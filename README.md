# Lab Module 5: AKS Security and Identities

![](../content/lab5b-title.png)
> Estimated Duration: 60 minutes  

## Create an Azure Container Registry & AKS cluster using the Azure CLI

1. Open a command prompt.

1. Log in to your Azure account:

   ```azurecli
   az login
   ```

1. Choose your Azure Subscription:

   ```azurecli
   az account set -s <YourSubscriptionID>
   ```

1. Create a resource group for the Azure resources used in this tutorial.

   ```azurecli
   az group create -n demo -l eastus
   ```

1. Create a private Azure container registry in the resource group. The tutorial pushes the sample app as a Docker image to this registry in later steps. Replace `<my-acr-name>` with a unique name for your registry.

   ```azurecli
   az acr create -g demo -l eastus --name <my-acr-name> --sku  Basic
   ```

  > Note the id of the registry.

1. Create a Kubernetes cluster in Azure Kubernetes Service. The following command creates a *kubernetes* cluster in the *demo* resource group, with *demo-aks-cluster* as the cluster name, with Azure Container Registry (ACR) `my-acr-name` attached, and replace `my-dns-name` as the DNS prefix:

   ```azurecli
   az aks create -g demo -n demo-aks-cluster --attach-acr <my-acr-name> --dns-name <my-dns-name> --generate-ssh-keys
   ```

   This command may take a while to complete.

## Create the Spring Boot on Docker Getting Started web app

The following steps walk you through building a Spring Boot web application and testing it locally.

1. Open a command-prompt and create a local directory to hold your application, and change to that directory; for example:

   ```bash
   mkdir C:\SpringBoot
   cd C:\SpringBoot
   ```

   -- or --

   ```bash
   mkdir /users/$USER/SpringBoot
   cd /users/$USER/SpringBoot
   ```

1. Fork the [Spring Boot on Docker Getting Started] sample project into your private GitHub account.

1. Clone the forked sample project into the directory. Example:

   ```bash
   git clone https://github.com/<my-username>/gs-spring-boot-docker.git
   ```

1. Change directory to the completed project.

   ```bash
   cd gs-spring-boot-docker
   cd complete
   ```

1. Use Maven to build and run the sample app.

   ```bash
   mvn package spring-boot:run
   ```

1. Test the web app by browsing to `http://localhost:8080`, or with the following `curl` command:

   ```bash
   curl http://localhost:8080
   ```

1. You should see the following message displayed: **Hello Docker World**

## Create a Service Principal for GitHub Actions

1. Create a Service Principal for GitHub Actions

   ```azurecli
   az ad sp create-for-rbac --scope /subscriptions/<subscription-id>/resourceGroups/demo --role Contributor  --sdk-auth
   ```

  Copy the JSON object for your service principal.

1. Add the service principal as a GitHub secret

- In GitHub, go to your repository.

- Select Security > Secrets and variables > Actions.

- Screenshot of select Actions menu item.

- Select New repository secret.

- Paste the entire JSON output from the Azure CLI command into the secret's value field. Give the secret the name AZURE_CREDENTIALS.

- Select Add secret.
