# Algonquin Pet Store — Store Front

Vue frontend for the product and order APIs.

## Local setup

Copy .env.example to .env and set the API base URLs:

```dotenv
VUE_APP_ORDER_SERVICE_URL=http://localhost:3000
VUE_APP_PRODUCT_SERVICE_URL=http://localhost:3030
```

Run `npm ci`, then `npm run serve`. Restart after changing .env.

## Azure Static Web Apps

The GitHub Actions workflow builds the app and deploys dist. Configure these repository variables in Settings → Secrets and variables → Actions → Variables:

- VUE_APP_ORDER_SERVICE_URL: the order App Service HTTPS base URL.
- VUE_APP_PRODUCT_SERVICE_URL: the product App Service HTTPS base URL.

Set the repository secret AZURE_STATIC_WEB_APPS_API_TOKEN to your Static Web App deployment token. API URLs are public build configuration; never put broker credentials in frontend variables.

Once the Azure resource and deployment token are configured, set repository variable AZURE_STATIC_WEB_APPS_DEPLOYMENT_ENABLED to true. While region access is unresolved, the workflow builds and saves a downloadable store-front-dist artifact and explicitly skips hosting deployment.

The workflow declares both URLs in the build job's env block. Push or rerun the workflow after changing URLs, because Vue embeds them during compilation. Trailing slashes are accepted.

Validate products load, place an order, and verify message activity in RabbitMQ's order_queue.
