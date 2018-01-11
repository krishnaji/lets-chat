This repo is forked from the original repo here: https://github.com/sdelements/lets-chat and is designed as a starting point for a hands-on-lab with Azure.

A self-hosted chat app for small teams built by [Security Compass][seccom].

![Screenshot](http://i.imgur.com/C4uMD67.png)



# Hands on Lab

## Part 1: Deploy Application on local machine
 - Clone or download this repository
 - Run npm install to install all required nodejs packages
 - [Create](https://portal.azure.com/#create/Microsoft.DocumentDB) a blank Azure CosmosDB account. Make sure to select API type as MongoDB. 
 - Get mongodb:// database endpoint link from Settings section
 - On your local machine Set or export enviroment variable MONGODB_URI=mongodb://your-connection-string from above step
 - Run `npm install` 
 - Run `npm start`
 - Visit http://localhost:5000
 - Make sure you are able signup and login into chat app
 
 ## Part 2: Deploy Application on Azure 
 - Option 1:  Using [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Execute following on command line.
	 1. `az group  create -l eastus2 -n letschat -o table `
	 2. `az appservice  plan create -g letschat --name ASPlan --location eastus --sku S1 -o table `
	 3. `az webapp create --name letschaton -g letschat --plan ASPlan  --deployment-local-git --runtime "node|6.2" -o table `
	 4. `az webapp config appsettings set --name letschaton -g letschat --settings MONGODB_URI=mongodb://your-connection-string` This step sets MONGODB_URI enviroment variable 
	 5. `az webapp deployment source config-local-git    -g letschat -n letschaton -o tsv`
	 6. `git remote add azure <link from from above step>`
	 7. `git push azure master` The code should be pushed to App Service
	 9. To browse the app run `az webapp browse -g letschat -n letschaton -o table`
