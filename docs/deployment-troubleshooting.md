# Azure Web App Node.js Deployment Error Resolution Guide

## Issue Description
The Node.js web application failed to deploy on Azure Web App Service with two main errors:

1. **App Container Failed to Start**
   ```
   Error: Cannot find module 'express'
   ```

2. **Container HTTP Response Error**
   ```
   Container didn't respond to HTTP pings on port: 8080, failing site start
   ```

## Root Causes
1. Missing NPM dependencies in the deployed environment
2. Incorrect port configuration for Azure Web App
3. Build process not executing during deployment

## Solution Steps

### 1. Enable Automatic Build During Deployment
In Azure Portal, add the following Application Setting (Web App --> App Setting --> Environment Variables):
- Name: `SCM_DO_BUILD_DURING_DEPLOYMENT`
- Value: `true`

This ensures that `npm install` runs during the deployment process.

### 2. Configure Correct Port
Add the port configuration in Azure Portal:
- Name: `WEBSITES_PORT`
- Value: `8080`

### 3. Update Server Configuration
Ensure your `server.js` includes proper port handling:
```javascript
const port = process.env.PORT || process.env.WEBSITES_PORT || 8080;
app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
});
```

### 4. Update package.json
Ensure your package.json includes proper scripts and dependencies:
```json
{
    "scripts": {
        "start": "node server.js",
        "prestart": "npm install"
    },
    "dependencies": {
        "express": "^4.16.3"
    }
}
```

## How to Apply the Fix

### In Azure Portal
1. Navigate to your Web App resource
2. Go to "Configuration" under Settings in the left sidebar
3. Select "Application settings" tab
4. Click "+ New application setting"
5. Add both required settings
6. Save changes and allow the app to restart

### Verification
1. After applying changes, monitor the application logs
2. Verify that:
   - npm install executes during deployment
   - The application starts successfully
   - The website is accessible

## Prevention
To prevent similar issues in future deployments:
1. Always include a proper `start` script in package.json
2. Ensure all dependencies are listed in package.json
3. Use environment variables for port configuration
4. Test the application locally with the same Node.js version as Azure

## Additional Resources
- [Azure App Service Linux Container Timeout Documentation](https://azureossd.github.io/2023/02/15/Whats-the-difference-between-PORT-and-WEBSITES_PORT/index.html)
- [Node.js on Azure App Service Documentation](https://learn.microsoft.com/en-us/azure/app-service/configure-language-nodejs)