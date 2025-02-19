---
title: Command Line Reference
sidebar_position: 3
description: "Discover the Tailcall CLI, a crucial tool for developers to manage and optimize GraphQL configurations from the command line. Learn commands like 'check', 'start', 'init', and 'gen' to validate specs, launch servers, bootstrap projects, and generate configurations. Detect N+1 issues, display schemas, format inputs, and more. Simplify GraphQL composition and enhance your development workflow with Tailcall CLI."
slug: tailcall-graphql-cli
sidebar_label: Command Line
---

import Tabs from "@theme/Tabs"
import TabItem from "@theme/TabItem"

<!-- ❕❕❕ Don't forget to update Fig auto complete spec upon adding of new sub-commands or modifying existing ones. https://fig.io/docs/getting-started -->

The Tailcall CLI (Command Line Interface) allows developers to manage and optimize GraphQL configurations directly from the command line.

## check

The `check` command validates a composition spec. Notably, this command can detect potential N+1 issues. To use the `check` command, follow this format:

```
tailcall check [OPTIONS] <FILE_PATHS>...
```

The `check` command offers options that control settings such as the display of the generated schema, n + 1 issues etc.

### --n-plus-one-queries

This flag triggers the detection of N+1 issues.

- Type: Boolean
- Default: false

```
tailcall check --n-plus-one-queries <FILE_PATHS> ...
```

### --schema

This option enables the display of the schema of the composition spec.

- Type: Boolean
- Default: false

```bash
tailcall check --schema <file1> <file2> ... <fileN>
```

The `check` command allows for files. Specify each file path, separated by a space, after the options.

**Example:**

```bash
tailcall check --schema ./path/to/file1.graphql ./path/to/file2.graphql
```

### --verify-ssl

Controls SSL/TLS certificate verification when loading remote configuration files.

- Type: Boolean
- Default: `true`

```bash
tailcall check --verify-ssl false < FILE_PATHS > ...
```

:::warning
Disabling SSL verification is not recommended for production environments as it makes your connections vulnerable to man-in-the-middle attacks.
:::

## start

The `start` command launches the GraphQL Server for the specific configuration.

To start the server, use the following command:

```bash
tailcall start <file1> <file2> ... <fileN> <http_path1> <http_path2> .. <http_pathN>
```

The `start` command allows for files and supports loading configurations over HTTP. You can mix file system paths with HTTP paths. Specify each path, separated by a space, after the options.

**Example:**

```bash
tailcall start ./path/to/file1.graphql ./path/to/file2.graphql http://example.com/file2.graphql
```

### --verify-ssl

Controls SSL/TLS certificate verification when loading remote configuration files.

- Type: Boolean
- Default: `true`

```bash
tailcall start --verify-ssl false < FILE_PATHS > ...
```

:::warning
Disabling SSL verification is not recommended for production environments as it makes your connections vulnerable to man-in-the-middle attacks.
:::

## init

The `init` command bootstraps a new Tailcall project. It creates the necessary GraphQL schema files in the provided file path.

```bash
tailcall init <file_path>
```

This command prompts for file creation and configuration, creating the following files:

| File Name | Description |
| --------: | ----------- |

<!-- TODO: uncomment when the Taillcall configuration will in separate file -->
<!-- | [.tailcallrc.schema.json] | Provides autocomplete in your editor for the tailcall configuration written in `json` or `yml` format.                                       | -->

| [.graphqlrc.yml] | An IDE configuration that references your GraphQL schema and the following `.tailcallrc.graphql`. |
| [.tailcallrc.graphql] | Contains Tailcall specific auto-completions for `.graphql` format. |

<!-- [.tailcallrc.schema.json]: https://github.com/tailcallhq/tailcall/blob/main/generated/.tailcallrc.schema.json -->

[.graphqlrc.yml]: https://the-guild.dev/graphql/config/docs
[.tailcallrc.graphql]: https://github.com/tailcallhq/tailcall/blob/main/generated/.tailcallrc.graphql

## gen

The `gen` command in the Tailcall CLI is designed to generate GraphQL configurations from various sources, such as protobuf files and REST endpoints.

**usage:**

```bash
tailcall gen path_to_configuration_file.json
```

To generate a Tailcall GraphQL configuration, provide a configuration file to the `gen` command like done above. This configuration file should be in JSON or YAML format, as illustrated in the example below:

<Tabs>
<TabItem value="json" label="JSON">

```json
{
  "llm": {
    "model": "gemini-1.5-flash-latest",
    "secret": "API_KEY"
  },
  "inputs": [
    {
      "curl": {
        "src": "https://jsonplaceholder.typicode.com/posts/1",
        "fieldName": "post",
        "headers": {
          "Content-Type": "application/json",
          "Accept": "application/json",
          "Authorization": "Bearer {{.env.AUTH_TOKEN}}"
        }
      }
    },
    {
      "curl": {
        "src": "https://jsonplaceholder.typicode.com/posts",
        "method": "POST",
        "body": {
          "title": "Tailcall - Modern GraphQL Runtime",
          "body": "Tailcall - Modern GraphQL Runtime",
          "userId": 1
        },
        "headers": {
          "Content-Type": "application/json",
          "Accept": "application/json"
        },
        "isMutation": true,
        "fieldName": "createPost"
      }
    },
    {
      "proto": {
        "src": "./news.proto",
        "url": "http://127.0.0.1:8080/rpc"
      }
    }
  ],
  "output": {
    "path": "./output.graphql"
  },
  "schema": {
    "query": "Query",
    "mutation": "Mutation"
  },
  "preset": {
    "mergeType": 1,
    "treeShake": true,
    "unwrapSingleFieldTypes": true,
    "inferTypeNames": true
  }
}
```

</TabItem>

<TabItem value="yml" label="YML">

```yaml
llm:
  model: "gemini-1.5-flash-latest"
  secret: "API_KEY"
inputs:
  - curl:
      src: "https://jsonplaceholder.typicode.com/posts/1"
      fieldName: "post"
      headers:
        Content-Type: "application/json"
        Accept: "application/json"
        Authorization: "Bearer {{.env.AUTH_TOKEN}}"
  - curl:
      src: "https://jsonplaceholder.typicode.com/posts"
      method: "POST"
      body:
        title: "Tailcall - Modern GraphQL Runtime"
        body: "Tailcall - Modern GraphQL Runtime"
        userId: 1
      headers:
        Content-Type: "application/json"
        Accept: "application/json"
      isMutation: true
      fieldName: "createPost"
  - proto:
      src: "./news.proto"
      url: "http://127.0.0.1:8080/rpc"
output:
  path: "./output.graphql"
schema:
  query: "Query"
  mutation: "Mutation"
preset:
  mergeType: 1
  treeShake: true
  unwrapSingleFieldTypes: true
  inferTypeNames: true
```

</TabItem>
</Tabs>

### Inputs

The `inputs` section specifies the sources from which the GraphQL configuration should be generated. Each source can be either a REST endpoint or a protobuf file.

1.  **REST:** When defining REST endpoints, the configuration should include the following properties.

    1. **src (Required):** The URL of the REST endpoint. In this example, it points to a specific post on `jsonplaceholder.typicode.com`.
    2. **fieldName (Required):** A unique name that should be used as the field name, which is then used in the operation type. In the example below, it's set to `post`.
       :::important
       Ensure that each field name is **unique** across the entire configuration to prevent overwriting previous definitions.
       :::
    3. **headers (Optional):** Users can specify the required headers to make the HTTP request in the headers section.
       :::info
       Ensure that secrets are not stored directly in the configuration file. Instead, use templates to securely reference secrets from environment variables. For example, see the following configuration where AUTH_TOKEN is referenced from the environment like `{{.env.AUTH_TOKEN}}`.
       :::

    4. **body (Optional):** This property allows you to specify the request body for methods like POST or PUT. If the endpoint requires a payload, include it here.
    5. **method (Optional):** Specify the HTTP method for the request (e.g. GET, POST, PUT, DEL). If not provided, the default method is `GET`.
    6. **isMutation (Optional):** This flag indicates whether the request should be treated as a GraphQL Mutation. Set `isMutation` to `true` to configure the request as a `Mutation`. If not specified or set to false, the request will be treated as a `Query by default`.

2.  **Query Operation:** To define a GraphQL Query, either omit the isMutation property or set it to false. By default, if isMutation is not provided, the request will be configured as a Query.

    <Tabs>
    <TabItem value="json" label="JSON">

    ```json title="sample input example for generating Query type"
    {
      "curl": {
        "src": "https://jsonplaceholder.typicode.com/posts/1",
        "fieldName": "post",
        "headers": {
          "Authorization": "Bearer {{.env.AUTH_TOKEN}}"
        }
      }
    }
    ```

      </TabItem>
      <TabItem value="yml" label="YML">
      ```yml title="sample input example for generating Query type"
      - curl:
          src: "https://jsonplaceholder.typicode.com/posts/1"
          fieldName: "post"
          headers:
            Content-Type: "application/json"
            Accept: "application/json"
            Authorization: "Bearer {{.env.AUTH_TOKEN}}"
      ```
      </TabItem>
      </Tabs>

    For the above input configuration, the following field will be generated in the operation type:

    ```graphql {2} showLineNumbers title="Generated Configuration"
    type Query {
      # field name is taken from the above JSON config
      post(p1: Int!): Post
        @http(
          url: "https://jsonplaceholder.typicode.com/posts/{{arg.p1}}"
        )
    }
    ```

3.  **Mutation Operation:** To define a GraphQL Mutation, set `isMutation` to `true` and provide the necessary` request body, method, isMutation and headers.`

    <Tabs>
    <TabItem value="json" label="JSON">

    ```json title="sample input example for generating Mutation type"
    {
      "curl": {
        "src": "https://jsonplaceholder.typicode.com/posts",
        "method": "POST",
        "body": {
          "title": "Tailcall - Modern GraphQL Runtime",
          "body": "Tailcall - Modern GraphQL Runtime",
          "userId": 1
        },
        "headers": {
          "Content-Type": "application/json",
          "Accept": "application/json"
        },
        "isMutation": true,
        "fieldName": "createPost"
      }
    }
    ```

      </TabItem>
      <TabItem value="yml" label="YML">
      ```yml title="sample input example for generating Mutation type"
      - curl:
          src: "https://jsonplaceholder.typicode.com/posts"
          method: "POST"
          body:
            title: "Tailcall - Modern GraphQL Runtime"
            body: "Tailcall - Modern GraphQL Runtime"
            userId: 1
          headers:
            Content-Type: "application/json"
            Accept: "application/json"
          isMutation: true
          fieldName: "createPost"
      ```
      </TabItem>
      </Tabs>

    For the above input configuration, the following field will be generated in the operation type:

    ```graphql {2} showLineNumbers title="Generated Configuration"
    input PostInput {
      title: String
      body: String
      userId: ID
    }

    type Mutation {
      # field name is taken from the above JSON config
      createPost(createPostInput: PostInput!): Post
        @http(
          url: "https://jsonplaceholder.typicode.com/posts/{{arg.p1}}"
          method: "POST"
        )
    }
    ```

4.  **Proto:**

    - Specify the **path to the proto file** (`src`) to help Tailcall create a schema and understand the gRPC methods to call when a field is queried.
    - Specify the **gRPC URL** (`url`) where the gRPC service is hosted.
    - Include a **boolean parameter** `connectRPC` (optional). If set to `true`, the proto file will be used to generate the schema, but the communication between Tailcall and the upstream will happen using the [Connect-RPC protocol](https://connectrpc.com/docs/protocol/).

        <Tabs>
          <TabItem value="json" label="JSON">
          ```json
          {
            "proto": {
              "src": "./path/to/file.proto",
              "url": "http://127.0.0.1:8080/rpc"
            }
          }
          ```
          </TabItem>
          <TabItem value="yml" label="YML">
          ```yml
          - proto:
              src: "./news.proto"
              url: "http://127.0.0.1:8080/rpc"
          ```
          </TabItem>
        </Tabs>

### Output

The `output` section specifies the path for the generated GraphQL configuration.

- **path**: The file path where the output will be saved.

### Preset

The config generator provides a set of tuning parameters that can make the generated configurations more readable by reducing duplication. This can be configured using the `preset` section.

<Tabs>
<TabItem value="json" label="JSON">

```json title="Presets with default values"
{
  "preset": {
    "mergeType": 1,
    "treeShake": true,
    "unwrapSingleFieldTypes": true,
    "inferTypeNames": true
  }
}
```

</TabItem>

<TabItem value="yml" label="YML">
```yml title="Presets with default values"
preset:
    mergeType: 1
    treeShake: true
    unwrapSingleFieldTypes: true
    inferTypeNames: true
```
</TabItem>
</Tabs>

1. **mergeType:** This setting merges types in the configuration that satisfy the threshold criteria. It takes a threshold value between `0.0` and `1.0` to determine if two types should be merged or not. The default is `1.0`.

   For example, the following types `T1` and `T2` are exactly similar, and with a threshold value of `1.0`, they can be merged into a single type called `M1`:

   ```graphql {14} showLineNumbers title="Merging type T1 and T2 into M1"
   # BEFORE
   type T1 {
     id: ID
     firstName: String
     lastName: String
   }

   type T2 {
     id: ID
     firstName: String
     lastName: String
   }

   # AFTER: T1 and T2 are merged into M1.
   type M1 {
     id: ID
     firstName: String
     lastName: String
   }
   ```

2. **treeShake:** This setting removes unused types from the configuration. When enabled, any type that is defined in the configuration but not referenced anywhere else (e.g., as a field type, union member, or interface implementation) will be removed. This helps to keep the configuration clean and free from unnecessary definitions.

   ```graphql showLineNumbers title="Before applying treeShake, the configuration might look like this."
   type Query {
     foo: Foo
   }

   type Foo {
     bar: Bar
   }

   # Type not used anywhere else
   type UnusedType {
     baz: String
   }

   type Bar {
     a: Int
   }
   ```

   ```graphql showLineNumbers title="After enabling treeShake, the UnusedType will be removed."
   type Query {
     foo: Foo
   }

   type Foo {
     bar: Bar
   }

   type Bar {
     a: Int
   }
   ```

3. **unwrapSingleFieldTypes:** This setting instructs Tailcall to flatten out types with single field.

   ```graphql showLineNumbers title="Before applying the setting"
   type Query {
     foo: Foo
   }

   # Type with only one field
   type Foo {
     bar: Bar
   }

   # Type with only one field
   type Bar {
     a: Int
   }
   ```

   ```graphql showLineNumbers title="After applying setting"
   type Query {
     foo: Int
   }
   ```

   This helps in flattening out types into single field.

4. **inferTypeNames:** This setting enables the automatic inference of type names based on their schema and it's usage. For it to work reliably it depends on an external secure AI agent.

   ```graphql title="Before enabling inferTypeNames setting"
   type T1 {
     id: ID
     name: String
     email: String
     post: [T2]
   }

   type T2 {
     id: ID
     title: String
     body: String
   }

   type Query {
     users: [T1]
       @http(
         url: "https://jsonplaceholder.typicode.com/users"
       )
   }
   ```

   - **Type T1:** T1 is used as the output type for the `user` field in the Query type. We recognize that T1 is associated with users in the users field of Query. Therefore, it infers that T1 should be named `User` to indicate that it represents user data.

   - **Type T2:** T2 is used as the output type for the `post` field within T1. We recognize that T2 is associated with posts in the post field of User. Therefore, it infers that T2 should be named `Post` to indicate that it represents post data.

   ```graphql title="After enabling inferTypeNames setting"
   type User {
     id: ID
     name: String
     email: String
     post: [Post]
   }

   type Post {
     id: ID
     title: String
     body: String
   }

   type Query {
     user: User
       @http(
         url: "https://jsonplaceholder.typicode.com/users"
       )
   }
   ```

   By leveraging field names to derive type names, the schema becomes more intuitive and aligned with the data it represents, enhancing overall readability and understanding. You can learn more about config autogen [here](./config-generation.md).

### LLM

Tailcall leverages LLM to improve the quality of configuration files by suggesting better names for types, fields, and more. The `llm` section in the configuration allows you to specify the [LLM model](./llm.md) and secret (API key) that will be used for generating the configuration.

Example:

- Using Gemini. Set TAILCALL_LLM_API_KEY to your Gemini API key.

  ```json
  "llm": {
      "model": "gemini-1.5-flash-latest",
      "secret": "{{.env.TAILCALL_LLM_API_KEY}}"
  }
  ```

- Using Ollama. Don't need secret.

  ```json
  "llm": {
      "model": "gemma2",
  }
  ```

:::important
Ensure that secrets are not stored directly in the configuration file. Instead, use templates to securely reference secrets from environment variables. For example, you can write secret as `{{.env.TAILCALL_SECRET}}`, where `TAILCALL_SECRET` is referenced from the running environment.
:::
