# DevStash n8n Workflow Setup

This directory contains the n8n workflow template for the DevStash ecosystem. The workflow uses AI-powered processing to intelligently analyze and store code snippets directly in Algolia.

<img width="1260" height="466" alt="Screenshot 2025-07-27 at 1 12 29 AM" src="https://github.com/user-attachments/assets/f17c0a7f-0e08-461b-a2c8-aefc7b3054d9" />

## Workflow Overview

The workflow performs the following operations:
1. **Receives** snippet data via authenticated webhook (POST/DELETE)
2. **Analyzes** content using AI models via OpenRouter (You can use any other model)
3. **Extracts** intelligent metadata (title, type, language, tags, notes)
4. **Stores** directly in Algolia using MCP (Model Context Protocol)
5. **Validates** operations and handles errors gracefully

## Prerequisites

- n8n instance (see [n8n-deploy.md](../render/n8n-deploy.md) for setup instructions)
- Remote Algolia MCP Server (see [algolia-mcp-deploy.md](../render/algolia-mcp-deploy.md) for setup instructions)
- Authentication token for webhook security (Optional but recomended)

## Installation Steps

### 1. Import Workflow

1. Open your n8n instance
2. Click "Import from File" or use Ctrl+O
3. Select `devstash-template.json`
4. The workflow will be imported with all nodes

### 2. Configure Credentials

Set up the following credentials in your n8n instance:

**Required Credentials:**
- **HTTP Header Auth**: For webhook authentication
- **OpenRouter API**: For AI model access
- **Algolia MCP Server**: Connection to MCP server endpoint

### 3. Set up MCP Server

The workflow uses an Algolia MCP server for database operations:


1. **MCP Server Endpoint**: `your_algolia_mcp_server_address`
2. **Purpose**: Provides AI agents with tools to interact with Algolia
3. **Operations**: Insert, delete, and manage Algolia index records

**Note**: The MCP server handles all Algolia operations, eliminating the need for manually calling APIs.


### 4. Activate Workflow

1. Import the workflow from `devstash-template.json`
2. Configure all required credentials
3. Click the workflow toggle to activate it
4. Note the webhook URL: `https://your-n8n-instance.com/webhook/stash`
5. Test the webhook endpoint

## Workflow Nodes Explained

### Webhook Trigger
- **Purpose**: Receives POST/DELETE requests from VS Code extension
- **Path**: `/stash`
- **Methods**: POST (save), DELETE (remove)
- **Authentication**: Header-based authentication
- **Expected POST payload**:
```json
{
  "id": "unique-snippet-id",
  "content": "console.log('Hello World');",
  "createdAt": "2025-01-26T12:00:00Z",
  "userTags": ["console", "logging"],
  "note": "Optional user note"
}
```

### Information Extractor (AI-Powered)
- **Purpose**: Analyzes content using AI to extract structured metadata
- **AI Model**: OpenRouter (configurable model) - you can use any other LLM as well instead of the OpenRouter chat model in the AI assistant
- **Extracts**:
  - **Title**: Descriptive title for the snippet
  - **Type**: Classification (code, command, query, config, note, other)
  - **Language**: Programming language detection/correction
  - **Tags**: Relevant tags based on content analysis
  - **Note**: Auto-generated summary if not provided

### Code Processor
- **Purpose**: Combines webhook data with AI-extracted metadata

### Insert Agent (AI-Powered)
- **Purpose**: Uses AI agent to insert data into Algolia via MCP
- **AI Model**: Google Gemini 2.0 Flash (via OpenRouter) - you can use any other LLM as well instead of the OpenRouter chat model in the AI assistant
- **MCP Tool**: Algolia MCP client for database operations
- **Index**: `stashes`
- **Returns**: Success/failure status with object ID

### Delete Agent (AI-Powered)
- **Purpose**: Uses AI agent to delete records from Algolia
- **Trigger**: DELETE webhook requests
- **AI Model**: You can use any other LLM as well instead of the OpenRouter chat model in the AI assistant
- **Operation**: Removes record by object ID
- **Returns**: Success/failure status

### Validation & Error Handling
- **If Nodes**: Check operation success/failure
- **Success Path**: No-op completion
- **Error Path**: Stop workflow with error message

## Testing the Workflow

### Manual Test

Send a POST request to your webhook URL:

```bash
curl -X POST "https://your-n8n-instance.com/webhook/stash" \
  -H "Content-Type: application/json" \
  -H "Authorization: your-token-here" \
  -d '{
    "id": "test-snippet-123",
    "content": "console.log(\"Test snippet\");",
    "createdAt": "2025-01-26T12:00:00Z",
    "userTags": ["test", "console"]
  }'
```

Expected response: The workflow will complete successfully if the snippet is processed and stored in Algolia.

### Verify Storage

1. **Check Algolia**: Search your `stashes` index in Algolia dashboard
2. **Check n8n Logs**: Review execution history for errors

## Troubleshooting

### Common Issues

1. **Webhook not receiving data**
   - Check n8n workflow is active
   - Verify webhook URL is correct

2. **MCP server connection errors**
   - Verify MCP server endpoint is accessible
   - Check MCP server is running and responding
   - Ensure Algolia credentials are configured in MCP server

3. **AI model errors**
   - Verify OpenRouter API key is valid
   - Check if you have sufficient credits
   - Ensure the selected models are available

4. **Authentication errors**
   - Check webhook authentication token is configured
   - Verify VS Code extension has matching token
   - Consider disabling auth for testing

### Debug Mode

Enable debug mode in n8n to see detailed execution logs:
1. Go to workflow settings
2. Enable "Save execution progress"
3. Run test executions and review logs

