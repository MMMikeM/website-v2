# Handling Path Params

## Table of Contents

* [Before You Get Started](#before-you-get-started)
* [Folder Structure End State](#folder-structure-end-state)
* [Steps](#steps)
* [Verification](#verification)

## Before You Get Started

You can get a value from a request's path params by using the following in a resource:

```typescript
const param = request.pathParam("param_name");
```

## Folder Structure End State

```text
▾ /path/to/your/project/
  app.ts
  deps.ts
```

## Steps

1. Create your `app.ts` file. Your resource in this file will check for the `:id` path param in the request's URL. If it exists and is a `number`, then it will return what was passed in. If it is `NaN`, then it will throw a `400 Bad Request` response.

  ```typescript
  // app.ts

  import { Drash } from "./deps.ts";

  // Create your resource
   
  class UsersResource extends Drash.Http.Resource {
   
    publi paths = ["/users/:id"];
   
    public GET(request: Drash.Request, response: Drash.Response): void {
      const userId = parseInt(request.pathParam("id"));
   
      if (isNaN(userId)) {
        throw new Drash.Errors.HttpError(
          400,
          "This resource requires the `:id` path param to be a number."
        );
      }
   
      return response.text(`You passed in the following user ID as the path param: ${userId}`);
    }
   
  }

  // Create and run your server

  const server = new Drash.Server({
    hostname: "0.0.0.0",
    port: 1447,
    protocol: "http",
    resources: [
      UsersResource
    ],
  });

  server.run();

  console.log(`Server running at ${server.address}.`);
  ```

## Verification

1. Run your app.

  ```shell
  $ deno run --allow-net app.ts
  ```

2. Using `curl` (or similar command), make a `GET` request to `http://localhost:1447/users/1`.

  ```text
  $ curl http://localhost:1447/users/1
  ```

  You should receive the following response:

  ```text
  You passed in the following user ID as the path param: 1
  ```

3. Make the same request, but change the `:id` path param to `one`.

  ```text
  $ curl http://localhost:1447/users/one
  ```

  You should receive the following response:

  ```text
  This resource requires the `:id` path param to be a number.
  ```