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

## Instructor-approved VM deployment

The October 5, 2026 announcement by Ramy Mohamed permits deploying the store-front on a VM when Azure student policy blocks Static Web Apps. This lab uses store-front-vm at http://74.152.33.106/ with Nginx. Backend services still run on Azure App Service and RabbitMQ runs on its dedicated VM.

On the VM, the frontend source is in /opt/cst8915-lab3/store-front. To deploy a reviewed update, fetch the repository, check out the desired commit, then build as azureuser with:

```sh
export VUE_APP_ORDER_SERVICE_URL=https://order-service-tajudeen-lab3.azurewebsites.net
export VUE_APP_PRODUCT_SERVICE_URL=https://product-service-tajudeen-lab3.azurewebsites.net
npm ci --no-audit --no-fund
npm run build
```

Nginx serves the dist directory using /etc/nginx/sites-available/cst8915-lab3. It starts automatically with the VM. The public frontend uses HTTP; requests to both backend APIs use HTTPS. No broker secret is included in the frontend.

GitHub Actions continues to validate the production build and save store-front-dist. It does not automatically update the VM. Keep AZURE_STATIC_WEB_APPS_DEPLOYMENT_ENABLED=false for this approved alternative.
