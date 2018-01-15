This repo is forked from the original repo here: https://github.com/sdelements/lets-chat and is designed as a starting point for a hands-on-lab with Azure.

A self-hosted chat app for small teams built by [Security Compass][seccom].

![Screenshot](http://i.imgur.com/C4uMD67.png)



# 1: Hands on Lab
### Prerequisites 

 1. Install [Visual Studio Code](https://code.visualstudio.com/)
 2. Install [Nodejs](https://nodejs.org/en/download/) on your local machine 
 3. Install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) and to login `az login` follow on screen instructions.
 4.  Create resource group for this lab `az group  create -l eastus2 -n letschat -o table` 
 5. Set up a deployment account that will be used to push code to app service `az webapp deployment user set --user-name <user> --password <password>`. This username and password will be the same across all apps in all subscriptions associated with your Microsoft Azure account
 
## Part 1: Deploy Application on local machine
 - Clone or download this repository
 - Run npm install to install all required nodejs packages
 - [Create](https://portal.azure.com/#create/Microsoft.DocumentDB) a blank Azure CosmosDB account. Make sure to select API type as MongoDB. 
 - Get connection string mongodb:// database endpoint link from Quick Start for Node.Js section
 - On your local machine Set or export environment variable MONGODB_URI=mongodb://your-connection-string from above step
 - Run `npm install` 
 - Run `npm start`
 - Visit http://localhost:5000
 - Make sure you are able signup and login into chat app
 
 ## Part 2: Create App Service Web App 
 Use the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) to execute following commands.
 Below 1st and 2nd  steps are optional, if you already have resource group and/or App service plan created
1. Create a resource group : `az group  create -l eastus2 -n letschat -o table` 
2. Create a App Service Plan: `az appservice  plan create -g letschat --name ASPlan --location eastus --sku S1 -o table `
3. Create a Web App `az webapp create --name letschaton -g letschat --plan ASPlan  --deployment-local-git --runtime "node|6.2" -o table `
4. Set a environment variable thats required to run the app: `az webapp config appsettings set --name letschaton -g letschat --settings MONGODB_URI=mongodb://your-connection-string` This step sets MONGODB_URI environment variable in application settings
6. To browse the app run `az webapp browse -g letschat -n letschaton -o table` You will find and empty default app running there. This is where your production app will run.

## Part 3: Deployment Slots
Now lets create deployment slot and try deploying code to it and finally swap the slots.
1.  Create deployment slot named **Stage**. Execute  `az webapp deployment slot create --name letschaton  --resource-group letschat  --slot stage --configuration-source letschaton -o table`. 
2. Once the Stage slot is created. Visit it  at`http://<WebAppName>-<SlotName>.azurewebsites.net` You will find it empty app running there.  
3.  Now get the remote git url `az webapp deployment source config-local-git --resource-group letschat --name letschaton --slot stage -o tsv`
4. Add remote git repository to your local machine  `git remote add stage <link from from above step>`
5. Now push the code to this remote repository. `git push stage master` 
6. To browse the deployed app run `az webapp browse -g letschat -n letschaton -s stage`

## Part 4 : Swap the slots
Now once you are able to visit the app in stage and test its working, its time to move it to production. App service makes it easy by just swapping the production app with stage app.

 1.  Lets swap the production with stage app `az webapp deployment slot swap --resource-group letschat --name letschaton --slot stage --target-slot production`
 2. To browse the production app run `az webapp browse -g letschat -n letschaton -o table`
 3. To browse the stage app run `az webapp browse -g letschat -n letschaton -s stage` You should notice that the stage is swapped with production app.

## Part 5: Update the app and try swap again
We want enable file uploads on our chat server. Lets add new empty file in root app your app and name it settings.yml. We want to achieve this [Before](https://github.com/krishnaji/lets-chat/blob/master/Before.gif) and [After](https://github.com/krishnaji/lets-chat/blob/master/After.gif)
Add following to your settings.yml file. 
```
files:
 enable: true
 provider: local
 local:
  dir: uploads
   ```

Now add(`git add`) and commit(`git commit -m "added settings file"`) this file to your local git. 
Restart the app to check if the file upload is enabled.
Time to push the code to your stage application. (`git push stage master`)
Browse your stage app and ensure that the files upload is enabled.
Lets swap the production with stage app
 `az webapp deployment slot swap --resource-group letschat --name letschaton --slot stage --target-slot production`

## [2: Hands on Lab - CICD with VSTS](https://github.com/krishnaji/CICDLabsHOL/blob/master/README.md)

## Clean Up

Clean up by deleting resource group. It will delete all resources created under it.
`az group delete --name letschat`
Also if you followed Hands on Lab for CICD you have delete prject and account on VSTS.
