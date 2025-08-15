# Railway Deployment Guide

## Prerequisites
1. Create a [Railway account](https://railway.app)
2. Install [Railway CLI](https://docs.railway.app/deploy/cli) (optional)
3. Push your code to GitHub

## Step 1: Create Railway Project

1. Go to [Railway Dashboard](https://railway.app/dashboard)
2. Click "New Project"
3. Select "Deploy from GitHub repo"
4. Choose your safe-transaction-service repository

## Step 2: Add Database Services

### PostgreSQL Database
1. In your Railway project, click "Add Service"
2. Select "PostgreSQL"
3. Railway will automatically create DATABASE_URL environment variable

### Redis Cache
1. Click "Add Service" again
2. Select "Redis"
3. Railway will automatically create REDIS_URL environment variable

## Step 3: Configure Environment Variables

In your Railway web service, add these environment variables:

```
PYTHONPATH=/app/
DJANGO_SETTINGS_MODULE=config.settings.production
DEBUG=0
ETH_L2_NETWORK=1
DJANGO_SECRET_KEY=your-super-secret-key-here-make-it-long-and-random
ETHEREUM_NODE_URL=https://proportionate-long-frog.arbitrum-sepolia.quiknode.pro/9fbe70897eac392557aac7ac5dbeb29c9d12974e/
CELERY_BROKER_URL=${{REDIS_URL}}
```

Railway automatically provides:
- `DATABASE_URL` (from PostgreSQL service)
- `REDIS_URL` (from Redis service)

## Step 4: Configure Build & Deploy

1. In your web service settings:
   - **Root Directory**: leave empty
   - **Build Command**: leave empty (uses Dockerfile)
   - **Start Command**: `docker/web/run_web.sh`

## Step 5: Deploy

1. Railway will automatically deploy when you push to your main branch
2. You can also manually trigger deployments from the Railway dashboard

## Step 6: Add Worker Services (Optional for development)

For full functionality, add separate services for workers:

1. **Indexer Worker**:
   - Same environment variables as web service
   - Start command: `docker/web/celery/worker.sh`

2. **Scheduler**:
   - Same environment variables as web service  
   - Start command: `docker/web/celery/scheduler.sh`

## Accessing Your Service

Once deployed, Railway will provide you with a URL like:
`https://your-app-name.railway.app`

## Cost Optimization for Free Tier

To stay within the $5 free limit:

1. **Use only web + database + redis** for basic functionality
2. **Add workers only when needed** for indexing
3. **Monitor usage** in Railway dashboard
4. **Scale down** when not actively developing

## Troubleshooting

- Check logs in Railway dashboard
- Verify environment variables are set correctly
- Ensure your QuickNode RPC URL is working
- Database migrations run automatically on deployment

## Development Workflow

1. Make changes locally
2. Test with `docker-compose up`
3. Push to GitHub
4. Railway auto-deploys
5. Monitor via Railway dashboard

Your development Safe Transaction Service will be live and accessible to test your frontend integration!
