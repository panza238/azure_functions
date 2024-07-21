# Azure Fuctions with Python
Azure Functions from scratch with Python, for enterprise production environments.

## How to use this repository
This repository is a step-by-step guide to create Azure Functions with Python. Each step is a branch in this repository. To follow along, clone this repository and checkout the branch that corresponds to the step you want to follow.

## Table of Contents
- [Prerequisites](#00---prerequisites)
- [Introduction to Azure Functions](#01---introduction)

# 00 - Prerequisites
1. Azure Account: [Create a free account](https://azure.microsoft.com/en-us/free)
2. Azure CLI: [Install Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
3. Azure Functions Core Tools: [Install Azure Functions Core Tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=macos%2Cisolated-process%2Cnode-v4%2Cpython-v2%2Chttp-trigger%2Ccontainer-apps&pivots=programming-language-python)
4. Python: virtual environment. (We'll be using Python 3.11.6)
5. Visual Studio Code + Azure Functions Extension for VSCode.

*Note: Visual Studio Code and the Azure Functions Extension are NOT strict prerequisites. They are a recommendation*

# 01 - Introduction
## The Setup
In order to create a Function App (the Azure resource that will host our functions), we need to create the following resources first:
1. Resource Group
2. Storage Account
3. App Service Plan

In order to do this, we need to have the Azure CLI installed (see prerequisites) and be logged in to our Azure account.
You can check if you are logged in by running the following command:
```bash
az account show
```
If you are not logged in, you can log in by running:
```bash
az login
```
You will be prompted to open a browser and log in to your Azure account.
Once you are logged in, you can start creating the resources.


## Creating these Resources
All commands have a similar structure. It should not be hard to follow along!
For a detailed explanation of each command, please refer to the YouTube video.

1. Create a Resource Group
```bash
az group create --name <resource-group-name> --location <location>
```

2. Create a Storage Account
If this is your first time creating a storage account, you may need to register the Microsoft.Storage resource provider. You can do this by running:
```bash
az provider register --namespace Microsoft.Storage
```
You can check the status of the registration by running:
```bash
az provider show --namespace Microsoft.Storage --query "registrationState"
```

Then, you can create the storage account by running:
```bash
az storage account create -n <storage-account-name> -l <location> -g <resource-group-name> --sku Standard_LRS
```
*note that we are using shorthand notation for this command (e.g. `-n` instead of `--name`)*
One thing worth mentioning is that the storage account's name has to be unique across all Azure (not just your account!). If you get an error saying that the name is already taken, try a different name.


3. Create an App Service Plan
If this is your first time creating an app service plan, you may need to register the Microsoft.Web resource provider. You can do this in the same way that you registered the `Microsoft.Storage` resource provider.

You can create the app service plan by running:
```bash
az appservice plan create -n <app-service-plan-name> -g <resource-group-name> --sku B1 --is-linux
```

A few things worth mentioning:
- We are using the `--is-linux` flag because we are going to use Python. If you are using another language, check the documentation to see whether you need a linux-based or windows-based app service plan.
- We are using the `--sku B1` flag to specify the pricing tier. 
    - `B1` is the cheapest tier for dedicated plans. This plan is NOT free, and it is NOT truly serverless.
    - There is a free dedicated plan (`F1`), but it does not support Azure Functions.
    - Later on, we will see how to create a consumption plan (`Y1`), which is truly serverless and offers a limited amount of free requests.

## Creating the Function App
Once the resources mentioned above are created, we can create the Function App by running:
```bash
az functionapp create -n <function-app-name> -g <resource-group-name> -s <storage-account-name> -p <app-service-plan-name> --runtime python --runtime-version 3.11 --functions-version 4
```

At the moment of writing this, the latest version of Azure Functions is 4. If you are using this repo in the future, you may want to check the latest version of Azure Functions and update the `--functions-version` flag accordingly.

## Destroying the Resources
So as not to incur in unwanted costs, you may want to delete the resources we created. You can do this by running:
```bash
az group delete -n <resource-group-name>
```
This will delete the resource group and all resources within it.
