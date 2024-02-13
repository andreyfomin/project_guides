## Create client server apps project

1. Initialize the Project: <br>Start by creating a new directory for your project and navigate into it:
    ```shell
    mkdir react-nestjs-project
    cd react-nestjs-project
    ```

2. Create a NestJS App: <br> Initialize a NestJS application using the Nest CLI:
    ```shell
    npx -p @nestjs/cli nest new backend
    ```

3. Create a React App: <br> Within the same project directory, create a React app using Create React App:
    ```shell
   npx create-react-app frontend
    ```
   If you need generate frontend project based on
   **typescript** then run the next command
    ```shell
    npx create-react-app frontend --template typescript
    ```

4. Organize the Project Structure: <br> Your project structure might look like this:
    ```scss
    your-project-name/
    ├── backend/
    │   ├── src/
    │   └── ... (NestJS files and directories)
    ├── frontend/
    │   ├── public/
    │   ├── src/
    │   └── ... (React files and directories)
    ├── package.json
    ├── tsconfig.json (for TypeScript if used)
    └── ...
    ```

5. Update Scripts: <br> Update the scripts in the root package.json to run both the backend and frontend:
    ```json
    {
      "scripts": {
        "start": "cd backend && npm run start:dev & cd frontend && npm start",
        "start:backend": "cd backend && npm run start:dev",
        "start:frontend": "cd frontend && npm start"
        // ... other scripts
      }
    }
    ```

6. Run the Project: <br> Start both the backend and frontend with a single command:
    ```shell
    npm start
    ```
   This will launch both the NestJS backend and the React frontend.


7. Adjust CORS settings (Optional):
   <br> If your frontend and backend are on different origins, you might need to configure <br>
   CORS in your NestJS application.<br> [Enable CORS](https://docs.nestjs.com/security/cors)
    ```typescript
    const app = await NestFactory.create(AppModule, { cors: true })
    ```
   Adjust the CORS configuration according to your requirements.


8. To change the NestJS app port and get it from a .env file if it exists, you can use the <br>
   dotenv package to load environment variables and then configure the NestJS <br> application accordingly. Here are the
   steps:
   <br> Install the dotenv package to load environment variables from a .env file.
    ```shell
    cd backend
    npm install dotenv
    ```
9. Create a .env file:<br>
   Create a .env file in the root of your NestJS project and define the PORT variable:
   ```dotenv
    PORT=3002
   ```
   Adjust the port number according to your preferences.


10. Update NestJS Configuration:<br>
    In your NestJS application (typically in the main.ts file), load the environment <br>
    variables using dotenv and configure the app to use the specified port. Here's an
    example:

```ts
// main.ts
import {NestFactory} from '@nestjs/core';
import {AppModule} from './app.module';
import * as dotenv from 'dotenv';

async function bootstrap() {
    // Load environment variables from .env file
   dotenv.config();

    // Get the port from the environment variable or use a default value (e.g., 3000)
   const port = process.env.PORT || 3000;

   const app = await NestFactory.create(AppModule, { cors: true} );

    // Configure the app to listen on the specified port
   await app.listen(port);

   console.log(`Application is running on: http://localhost:${port}`);
}

bootstrap();
```
