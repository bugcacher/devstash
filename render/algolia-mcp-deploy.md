# Deploying Algolia MCP Server on Render

This guide walks you through deploying the Algolia Model Context Protocol (MCP) server on Render using the provided blueprint. The MCP server acts as a bridge between AI models and Algolia search, enabling intelligent search and data management capabilities for the DevStash ecosystem.

## Prerequisites

Before starting, ensure you have:
- A [Render account](https://render.com) (free tier works)
- An [Algolia account](https://www.algolia.com) with an application and index set up
- Your Algolia credentials ready:
  - Application ID
  - API Key (Admin API Key for write operations)
  - Index Name

## Quick Start with Blueprint

The fastest way to deploy is using render blueprint:

### Deploy Using Blueprint

1. **Create a new repository** called `algolia-mcp-render` on GitHub.
2. Create a file called `render.yaml` and paste the content from [algolia-mcp-blueprint.yaml](https://github.com/bugcacher/devstash/blob/master/render/algolia-mcp-blueprint.yaml).
3. **Push the file** to your repository
4. **Login to Render** and navigate to your dashboard
5. **Click "New"** → **"Blueprint"**
6. **Connect your repository** (`algolia-mcp-render`) to Render
7. **Customize the service name** - make it unique by adding a UUID or your project name
8. **Click "Apply"** to start the deployment

**Learn More**: For detailed information about Render Blueprint deployments, check the [official documentation](https://render.com/docs/infrastructure-as-code#setup).


### Blueprint Customization

Before applying, you can modify the blueprint:

```yaml
services:
  - type: web
    name: your-algolia-mcp-server-name # Make this unique
    runtime: go
    repo: your-repo-name-here
    plan: free # or starter/standard for more resources
    region: oregon # or your preferred region
```

## Environment Variables Configuration

After deployment, configure these essential environment variables in your Render service:

### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `ALGOLIA_APP_ID` | Your Algolia Application ID | `ABC123DEF4` |
| `ALGOLIA_API_KEY` | Admin API Key for write operations | `abc123...` |
| `ALGOLIA_WRITE_API_KEY` | Same as API_KEY (legacy support) | `abc123...` |
| `ALGOLIA_INDEX_NAME` | Target index name | `stashes` |
| `MCP_SERVER_TYPE` | Server type identifier | `sse` |
| `MCP_ENABLED_TOOLS` | Comma-separated list of enabled tools | `search_read,search_write` |

**Note**: The Algolia MCP Server (golang) has a bug where if you didn't specify any tool or only specify `search` tool the credentials are not picked from env variables and leads to connection issues.


### Setting Environment Variables

1. **Go to your service** in the Render dashboard
2. **Navigate to "Environment"** tab
3. **Add each variable** with its corresponding value
4. **Save changes** - this will trigger a redeploy

**Security Note**: Never commit API keys to your repository. Always use Render's environment variables for sensitive data.



## Testing the Deployment

### Using MCP Inspector

MCP Inspector is the best tool for testing your deployed server:

```bash
# Replace YOUR_RENDER_URL with your actual Render service URL
npx @modelcontextprotocol/inspector https://your-algolia-mcp-server.onrender.com/sse
```

### Troubleshooting Common Issues

**Connection Failed**
- Verify your Render service is running (check service logs)
- Ensure environment variables are set correctly
- Check if your service URL is accessible

**Authentication Errors**
- Verify `ALGOLIA_API_KEY` has write permissions
- Confirm `ALGOLIA_APP_ID` matches your Algolia application
- Check that `ALGOLIA_INDEX_NAME` exists in your Algolia dashboard

**Build Failures**
- Check Render build logs for Go compilation errors
- Verify the repository structure matches expected paths


Your Algolia MCP server should now be running on Render and ready to power your DevStash ecosystem!