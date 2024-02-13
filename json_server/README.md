## Install JSON server

[Json server doc](https://github.com/typicode/json-server)

1. Create new face server dir json_server
2. Change directory to json_server and run the next command
```shell
npm init -y
```
3. Install json-server
```shell
npm install json-server
```
4. Create new file db.json in json_server directory and 
add some data in it.
```json
{
  "posts": [
    {
      "title": "JSON SERVER",
      "author": "foo",
      "id": 1
    },
    {
      "title": "REACT",
      "author": "facebook",
      "id": 2
    },
    {
      "title": "BOOTSTRAP",
      "author": "css master",
      "id": 3
    },
    {
      "title": "MEDIUM",
      "author": "medium",
      "id": 4
    }
  ]
}
```
5. Run json fake server
```shell
npx json-server --watch db.json --port 3000
```