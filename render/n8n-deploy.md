# Deploy n8n on Render

This guide walks you through deploying n8n (workflow automation tool) on Render using a Blueprint configuration. 

**Note**: A comprehensive guide on deploying n8n on Render is also available in the [official Render documentation](https://render.com/docs/deploy-n8n).


## Prerequisites

- A GitHub account
- A Render account (free tier available)

## What You'll Get

- A fully functional n8n instance running on Render
- PostgreSQL database for workflow storage
- Free tier deployment (upgradeable to paid plans)

## Deployment Steps

### 1. Deploy Using Blueprint

1. **Create a new repository** called `n8n-render` on GitHub
2. **Create a file called `render.yaml`** in your repository and paste the [n8n blueprint configuration](https://github.com/bugcacher/devstash/blob/master/render/n8n-blueprint.yaml)
3. **Push the file** to your repository
4. **Login to Render** and navigate to your dashboard
5. **Click "New"** → **"Blueprint"**
6. **Connect your repository** (`n8n-render`) to Render
7. **Customize the service name** - make it unique by adding a UUID or your project name
8. **Click "Apply"** to start the deployment

**Learn More**: For detailed information about Render Blueprint deployments, check the [official documentation](https://render.com/docs/infrastructure-as-code#setup).


### 2. Wait for Deployment

- The deployment process typically takes 5-10 minutes
- Render will automatically:
  - Create a PostgreSQL database
  - Deploy the n8n Docker container
  - Generate encryption keys
  - Set up environment variables
- Configure Webhook URL (Important)
    For webhooks to function properly, you need to manually add an environment variable:

    1. **Go to your n8n service** in the Render dashboard
    2. **Navigate to the Environment tab**
    3. **Add a new environment variable**:
    - **Key**: `WEBHOOK_URL`
    - **Value**: Your Render service URL (e.g., `https://your-service-name.onrender.com`)
    4. **Save and redeploy** the service


    **Reference**: [Render n8n Documentation - Set Your Webhook URL](https://render.com/docs/deploy-n8n#set-your-webhook-url)

### 3. Access Your n8n Instance

1. Once deployed, find your service URL in the Render dashboard
2. Click on the URL to access your n8n instance
3. Complete the initial setup by creating an admin account

## Configuration Details

The blueprint includes:

- **Web Service**: n8n Docker container on free tier
- **Database**: PostgreSQL database for workflow storage
- **Environment Variables**: 
  - Auto-generated encryption key
  - Database connection details
  - PostgreSQL configuration

## Important Notes

- **Encryption Key**: Never modify the `N8N_ENCRYPTION_KEY` after deployment - you'll lose access to encrypted credentials

## Troubleshooting

### Service Won't Start
- Check the deployment logs in Render dashboard
- Ensure all environment variables are properly set

### Database Connection Issues
- Verify the database is running and healthy
- Check database connection environment variables