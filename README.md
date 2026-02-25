# APITable MCP Server

A Model Context Protocol server that provides read and write access to [APITable](https://github.com/apitable/apitable). This server enables LLMs to list spaces, search nodes, list records, create records and upload attachments in APITable.

## Tools

| Tool Name                 | Available | Description                                                                                                   |
| ------------------------- | --------- | ------------------------------------------------------------------------------------------------------------- |
| list_spaces               | ✅        | Fetches all workspaces that the currently authenticated user has permission to access.                       |
| search_nodes              | ✅        | Retrieve nodes based on specific types, permissions, and queries.                                            |
| list_records              | ✅        | Read the records from a specified database with support for pagination, field filtering, and sorting options. |
| get_fields_schema         | ✅        | Returns the JSON schema of all fields within the specified database                                           |
| create_record             | ✅        | Create a new record in the database.                                                                          |
| upload_attachment_via_url | ✅        | Upload an attachment to the APITable server using its web URL.                                                 |
| update_record             | ❌        | TODO                                                                                                          |

## Environment Variables

- `APITABLE_API_KEY`: Your APITable personal access token.
- `APITABLE_API_URL`: The base URL of the APITable API. 
  - For APITable Cloud: `https://apitable.com/fusion/v1`
  - For local/self-hosted deployment: `http://your-server-address/fusion/v1` (e.g., `http://localhost:8080/fusion/v1`)

## Usage

### Docker Deployment

You can run this MCP server using Docker:

```bash
docker build -t apitable-mcp-server .
docker run -e APITABLE_API_KEY=your_api_key -e APITABLE_API_URL=https://apitable.com/fusion/v1 apitable-mcp-server
```

For local deployment:
```bash
docker run -e APITABLE_API_KEY=your_api_key -e APITABLE_API_URL=http://host.docker.internal:8080/fusion/v1 apitable-mcp-server
```

### MCP Client Configuration

You can use this server in MCP client such as [Claude Desktop](https://claude.ai/download), [CherryStudio](https://www.cherry-ai.com/), etc.

#### Claude Desktop

In the case of Claude Desktop, you need to add the following configuration information to the "mcpServers" section of the `claude_desktop_config.json` file:

**For Linux, MacOS:**

```json
{
  "mcpServers": {
    "apitable": {
      "command": "npx",
      "args": [
        "-y",
        "/ABSOLUTE/PATH/TO/PARENT/FOLDER/apitable-mcp-server"
      ],
      "env": {
        "APITABLE_API_KEY": "YOUR_API_KEY",
        "APITABLE_API_URL": "https://apitable.com/fusion/v1"
      }
    }
  }
}
```

**For Windows:**

```json
{
  "mcpServers": {
    "apitable": {
      "command": "npx",
      "args": [
        "-y",
        "D:\\ABSOLUTE\\PATH\\TO\\PARENT\\FOLDER\\apitable-mcp-server"
      ],
      "env": {
        "APITABLE_API_KEY": "YOUR_API_KEY",
        "APITABLE_API_URL": "https://apitable.com/fusion/v1"
      }
    }
  }
}
```

Replace `YOUR_API_KEY` with your APITable personal access token and `/ABSOLUTE/PATH/TO/PARENT/FOLDER/apitable-mcp-server` with the absolute path to the parent folder of this repository.

For local deployment, set `APITABLE_API_URL` to your local server address:
```json
{
  "env": {
    "APITABLE_API_KEY": "YOUR_API_KEY",
    "APITABLE_API_URL": "http://localhost:8080/fusion/v1"
  }
}
```

#### CherryStudio

If you are using CherryStudio as MCP client and Windows system, your configuration should look like this:

```json
{
  "mcpServers": {
    "apitable": {
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "-y",
        "D:\\ABSOLUTE\\PATH\\TO\\PARENT\\FOLDER\\apitable-mcp-server"
      ],
      "env": {
        "APITABLE_API_KEY": "YOUR_API_KEY",
        "APITABLE_API_URL": "https://apitable.com/fusion/v1"
      }
    }
  }
}
```

#### Docker in MCP Client

You can also use the Docker image directly in your MCP client:

```json
{
  "mcpServers": {
    "apitable": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "APITABLE_API_KEY",
        "-e",
        "APITABLE_API_URL",
        "apitable-mcp-server"
      ],
      "env": {
        "APITABLE_API_KEY": "YOUR_API_KEY",
        "APITABLE_API_URL": "https://apitable.com/fusion/v1"
      }
    }
  }
}
```

## Debug

The [MCP inspector](https://github.com/modelcontextprotocol/inspector) is a developer tool for testing and debugging MCP servers.

To inspect an MCP server implementation, there's no need to clone the MCP inspector repo. Instead, use `npx`. For example, APITable MCP server is built at `dist/index.js`. Arguments are passed directly to your server, while environment variables can be set using the `-e` flag:

```bash
npx @modelcontextprotocol/inspector -e APITABLE_API_KEY={YOUR_API_KEY} -e APITABLE_API_URL={API_URL} node dist/index.js
```

The other way is to clone the MCP inspector repo and connect the APITable MCP server in the inspector interface.

```bash
cd path/to/inspector/
npm start -- -e APITABLE_API_KEY={YOUR_API_KEY} -e APITABLE_API_URL={API_URL}
```
