## How Create new project with PostgreSQL

1. Create new nestjs project with the name nestjs-pgdb-test
    ```bash
    npx -p @nestjs/cli nest new nestjs-pgdb-test
    ```

2. In the project root directory create DB file docker-compose.yml for testing
   with the next content
    ```yaml showLineNumbers
    version: "3.9" # https://docs.docker.com/compose/gettingstarted/
    services:
      postgres:
        image: postgres:16.1
        container_name: postgres
        restart: always
        environment:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: postgres
          POSTGRES_DB: testdb
        ports:
          - "5432:5432"

      pgadmin4:
        container_name: pgadmin4 # http://localhost:5050/
        image: dpage/pgadmin4
        restart: always
        environment:
          PGADMIN_DEFAULT_EMAIL: postgres@postgres.co
          PGADMIN_DEFAULT_PASSWORD: postgres
        ports:
          - "5050:80"
    ```

3. Run the next command to deploy and run DB and DB Admin containers
    ```shell
    docker-compose -p nestjs_db_test -f docker-compose.yml up postgres pgadmin4 -d
    ```
   if you need to stop services then run the next command

    ```shell
    docker stop pgadmin4 postgres
    ```
   see running containers
    ```shell
    docker ps
    ```

4. In new project directory run the next command<br>
   https://www.prisma.io/docs/getting-started/quickstart
   https://docs.nestjs.com/recipes/prisma#set-up-prisma

    ```shell
    npm install prisma --save-dev
    ```

5. Run the next command that creates a new prisma directory with your Prisma schema file
   and configures PostgreSQL as your database. You're now ready to model your data and create
   your database with some tables
    ```shell
    npx prisma init --datasource-provider postgresql
    ```

6. Open created .env file and change db settings
    ```shell
    DATABASE_URL="postgresql://postgres:postgres@localhost:5432/testdb?schema=public"
    ```

7. Open schema.prisma file and add your db model to the end of file
```prisma
model User {
  id    Int     @default(autoincrement()) @id
  email String  @unique
  name  String?
  posts Post[]
  createdAt DateTime  @default(now()) @map(name: "created_at")
  @@map(name: "user")
}

model Post {
  id        Int      @default(autoincrement()) @id
  title     String
  content   String?
  published Boolean? @default(false)
  author    User?    @relation(fields: [authorId], references: [id])
  authorId  Int? @map(name: "author_id")
  createdAt DateTime  @default(now()) @map(name: "created_at")
  @@map(name: "post")
}
```

8. Run migration to create database tables with Prisma Migrate
    ```shell
    npx prisma migrate dev --name init
    ```
   This command did three things:<br>
    * It created a new SQL migration file for this migration in the prisma/migrations directory.<br>
    * It executed the SQL migration file against the database.<br>
    * It ran prisma generate under the hood (which installed the @prisma/client package and generated a tailored Prisma Client API based on your models).<br>
      And defined via the environment variable in the .env file.


9. Generate Prisma Service for nestjs project
   https://docs.nestjs.com/recipes/prisma#set-up-prisma

    ```bash
    npx nest g s services/prisma
    ```

10. Change PrismaService

```ts showLineNumbers
import { Injectable, OnModuleInit } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit {
  async onModuleInit() {
    await this.$connect();
  }
}
```

11. Create User service
    ```bash
    npx nest g s repositories/user
    ```

12. Change User service

```ts
import {Injectable} from '@nestjs/common';
import {User, Prisma} from '@prisma/client';
import {PrismaService} from '../../services/prisma/prisma.service';

@Injectable()
export class UserService {
    constructor(private prisma: PrismaService) {
    }

    async user(
        userWhereUniqueInput: Prisma.UserWhereUniqueInput,
    ): Promise<User | null> {
        return this.prisma.user.findUnique({
            where: userWhereUniqueInput,
        });
    }

    async users(params: {
        skip?: number;
        take?: number;
        cursor?: Prisma.UserWhereUniqueInput;
        where?: Prisma.UserWhereInput;
        orderBy?: Prisma.UserOrderByWithRelationInput;
    }): Promise<User[]> {
        const {skip, take, cursor, where, orderBy} = params;
        return this.prisma.user.findMany({
            skip,
            take,
            cursor,
            where,
            orderBy,
        });
    }

    async usersWithPaging(params): Promise<{ pagination: { total: any }, users: any }> {
        const {skip, take, cursor, where, orderBy} = params;
        const [users, count] = await this.prisma.$transaction([
            this.prisma.user.findMany({
                skip,
                take,
                cursor,
                where,
                orderBy,
            }),
            this.prisma.user.count({
                cursor,
                where,
                orderBy,
            }),
        ]);
        return {
            pagination: {
                total: count,
            },
            users,
        };
    }

    async createUser(data: Prisma.UserCreateInput): Promise<User> {
        return this.prisma.user.create({
            data,
        });
    }

    async updateUser(params: {
        where: Prisma.UserWhereUniqueInput;
        data: Prisma.UserUpdateInput;
    }): Promise<User> {
        const {where, data} = params;
        return this.prisma.user.update({
            data,
            where,
        });
    }

    async deleteUser(where: Prisma.UserWhereUniqueInput): Promise<User> {
        return this.prisma.user.delete({
            where,
        });
    }
}
```


13. Create Post service
```bash
npx nest g s repositories/post
```

14. Change Post service

```ts

import {Injectable} from '@nestjs/common';
import {Post, Prisma} from '@prisma/client';
import {PrismaService} from '../../services/prisma/prisma.service';

@Injectable()
export class PostService {
    constructor(private prisma: PrismaService) {
    }

    async post(
        postWhereUniqueInput: Prisma.PostWhereUniqueInput,
    ): Promise<Post | null> {
        return this.prisma.post.findUnique({
            where: postWhereUniqueInput,
        });
    }

    async posts(params: {
        skip?: number;
        take?: number;
        cursor?: Prisma.PostWhereUniqueInput;
        where?: Prisma.PostWhereInput;
        orderBy?: Prisma.PostOrderByWithRelationInput;
    }): Promise<Post[]> {
        const {skip, take, cursor, where, orderBy} = params;
        return this.prisma.post.findMany({
            skip,
            take,
            cursor,
            where,
            orderBy,
        });
    }

    async createPost(data: Prisma.PostCreateInput): Promise<Post> {
        return this.prisma.post.create({
            data,
        });
    }

    async updatePost(params: {
        where: Prisma.PostWhereUniqueInput;
        data: Prisma.PostUpdateInput;
    }): Promise<Post> {
        const {data, where} = params;
        return this.prisma.post.update({
            data,
            where,
        });
    }

    async deletePost(where: Prisma.PostWhereUniqueInput): Promise<Post> {
        return this.prisma.post.delete({
            where,
        });
    }
}

```

15. Create User controller
    ```shell
    npx nest g co controllers/user
    ```

16.Change User controller

```ts
import {Body, Controller, Get, Post} from '@nestjs/common';
import {UserService} from '../../repositories/user/user.service';
import {User as UserModel} from '@prisma/client';

@Controller()
export class UserController {
    constructor(private readonly userService: UserService) {
    }

    @Get('users')
    async getUsers(): Promise<UserModel[]> {
        return this.userService.users({});
    }

    @Get('users')
    async getUsersWithPaging(
        @Query('search') search,
        @Query('page', new DefaultValuePipe(1), ParseIntPipe) page: number,
        @Query('pageSize', new DefaultValuePipe(10), ParseIntPipe) pageSize: number,
    ) {
        return this.userService.usersWithPaging({
            skip: (page - 1) * pageSize,
            take: pageSize,
        });
    }

    @Post('user')
    async signupUser(
        @Body() userData: { name?: string; email: string },
    ): Promise<UserModel> {
        return this.userService.createUser(userData);
    }
}

```

16. Create Post controller
    ```bash
    npx nest g co controllers/post
    ```

17. Change post controller

```ts
import { Body, Controller, Get, Param, Post } from '@nestjs/common';
import { PostService } from '../../repositories/post/post.service';
import { Post as PostModel } from '@prisma/client';

@Controller()
export class PostController {
  constructor(private readonly postService: PostService) {}

  @Get('post/:id')
  async getPostById(@Param('id') id: string): Promise<PostModel> {
    return this.postService.post({ id: Number(id) });
  }

  @Get('feed')
  async getPublishedPosts(): Promise<PostModel[]> {
    return this.postService.posts({
      where: { published: true },
    });
  }

  @Get('filtered-posts/:searchString')
  async getFilteredPosts(
    @Param('searchString') searchString: string,
  ): Promise<PostModel[]> {
    return this.postService.posts({
      where: {
        OR: [
          {
            title: { contains: searchString },
          },
          {
            content: { contains: searchString },
          },
        ],
      },
    });
  }

  @Post('post')
  async createDraft(
    @Body() postData: { title: string; content?: string; authorEmail: string },
  ): Promise<PostModel> {
    const { title, content, authorEmail } = postData;
    return this.postService.createPost({
      title,
      content,
      author: {
        connect: { email: authorEmail },
      },
    });
  }
}
```

18. Rest calls test<br>
    Create User
```shell
curl -X 'POST' \
  'http://localhost:3002/user' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "Lital the aaa",
  "email": "lital@mail.ru"
}'
```

Get all users
```shell
curl -X 'GET' \
  'http://localhost:3000/users' \
  -H 'accept: application/json'
```

Get all user pages
```shell
curl -X 'GET' \
  'http://localhost:3002/users/pages?search=aaaa&page=1&pageSize=5' \
  -H 'accept: application/json'
```

## Add simple validator

1. Create simple custom validator
```shell
nest g cl validators/string-param-validator
```

2. Change validator class
```ts
import { Injectable, PipeTransform, BadRequestException } from '@nestjs/common';

@Injectable()
export class StringParamValidator implements PipeTransform<any, string> {
  transform(value: { name?: string; email: string }): any {
    if (/\b(a|an|the)\b/i.test(value.name)) {
      throw new BadRequestException(
        'Invalid name parameter article in the name',
      );
    }
    return value;
  }
}
```

3. Add this validator to User controller

```ts
import { Body, Controller, Get, Post, UsePipes } from '@nestjs/common';
import { UserService } from '../../repositories/user/user.service';
import { User as UserModel } from '@prisma/client';
import { StringParamValidator } from '../../validators/string-param-validator/string-param-validator';

@Controller()
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Get('users')
  async getUsers(): Promise<UserModel[]> {
    return this.userService.users({});
  }

  @Post('user')
  @UsePipes(StringParamValidator)
  async signupUser(
    @Body() userData: { name?: string; email: string },
  ): Promise<UserModel> {
    return this.userService.createUser(userData);
  }
}
```

## Links to external documentation

[nestjs file upload](https://docs.nestjs.com/techniques/file-upload#example)

[git project example file upload](https://github.com/nestjs/nest/tree/master/sample/29-file-upload)

[Prisma ORM - quick start](https://www.prisma.io/docs/getting-started/quickstart)

[Prisma - generate](https://www.prisma.io/docs/orm/reference/prisma-cli-reference#generate)

[Prisma - migrate](https://www.prisma.io/docs/orm/prisma-migrate/understanding-prisma-migrate)