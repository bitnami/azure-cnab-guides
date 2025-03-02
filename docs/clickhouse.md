# Deploy ClickHouse on an Azure Kubernetes Service (AKS) Cluster using Bitnami Marketplace K8s application

This guide demonstrates how to deploy ClickHouse on an Azure Kubernetes Service (AKS) cluster using the Bitnami ClickHouse Helm chart and includes instructions for basic usage such as creating and consuming messages.  

## Deployment

### Installing the Bitnami package for ClickHouse extension using Marketplace

- Navigate to Azure Portal: [https://portal.azure.com](https://portal.azure.com/)
- Navigate to the running Azure Kubernetes Service (AKS) cluster where you would like to install the extension. More details about AKS can be found in [What is Azure Kubernetes Service (AKS)? - Azure Kubernetes Service \| Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/what-is-aks).

1. For more information on how to create a Kubernetes (AKS) Cluster, navigate to [Kubernetes on Azure tutorial - Create an Azure Kubernetes Service (AKS) cluster - Azure Kubernetes Service \| Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/tutorial-kubernetes-deploy-cluster?tabs=azure-cli)

- Under the “Settings” tab, select “Extensions + Applications”.

![ui](../images/clickhouse/image1.png)

- Select “Add” located at the top left portion of the “Extensions + Applications” page.
- In the “Search the Marketplace” bar, search for “Bitnami package for ClickHouse” and select the extension.
- Under Plan, select “Bitnami package for ClickHouse” and select “Create”

#### Under “Basics” Tab

##### Project Details

1. For “Subscriptions”, select the subscription your AKS cluster is under. All resources in an Azure subscription are billed together.
2. For “Resource Group”, select the resource group your AKS cluster is under. A resource group is a collection of resources that share the same lifecycle, permissions, and policies.

##### Instance Details

1. If you would like to create a new AKS Cluster, select “Yes”. If you would like to use an existing cluster, select “No”.
2. If Creating a New Kubernetes Cluster: For “Region” select the region to be used with the newly created AKS cluster.

#### Under “Kubernetes Cluster (AKS) Details” Tab

If Using an Existing Kubernetes Cluster

1. Select the name of an existing Azure Kubernetes Service (AKS) cluster from the "Kubernetes Cluster (AKS) Name" drop-down list. This cluster will serve as the installation destination for the extension.

![ui2](../images/clickhouse/image2.png)

If Creating a New Kubernetes Cluster

1. Kubernetes Cluster (AKS) Name: Define the name of a new Azure Kubernetes Service (AKS) cluster in which the extension will be installed. Use only allowed characters.
2. Kubernetes version: Choose the version of Kubernetes that should be used for this managed Kubernetes cluster. You will be able to upgrade this version after creating the cluster.
3. VM size: Select the size of virtual machine for the managed Kubernetes cluster nodes.
4. Enable auto scaling: Whether to enable auto-scaler for the number of nodes in the managed Kubernetes cluster.
5. Number of agents: Specify the number of agent nodes in the Azure Kubernetes Service (AKS) cluster.

![ui3](../images/clickhouse/image3.png)

#### Under “Application Details” Tab

1. Cluster extension resource name: Create a name for your extension resource
2. The value must not be empty.
3. Only lowercase alphanumeric characters are allowed, and the value must be 6-30 characters long.
4. Installation namespace: The Kubernetes namespace in which the extension will be installed. If the namespace does not exist, it will be created.
5. Allow extension auto upgrades of minor versions: Whether to allow auto upgrades of minor versions for the extension.
6. Application parameters (optional): Parameters are optional, for a quick start, the application can be launched with the default configuration. The link below lists the parameters that can be configured during installation for advanced customization. [charts/bitnami/clickhouse at main · bitnami/charts](https://github.com/bitnami/charts/tree/main/bitnami/clickhouse#parameters)
7. Enable multi line values: Enable this option when some of the parameter values specified above need to be multi line strings. It will be activated or deactivated for new rows.

![ui4](../images/clickhouse/image4.png)

#### Under “Usage Instructions” Tab

1. The deployment notes of this solution provide useful information regarding how to access the application and the different parameters that have been generated during the deployment. Please look at the following guide to know more about it.

[Usage instructions for Azure Marketplace Kubernetes Applications](https://docs.bitnami.com/azure/faq/get-started/usage-instructions-cnab/)

![ui5](../images/clickhouse/image5.png)

#### Under “Review + Create”

1. Review the information and select “Create” at the bottom right portion of the page to install the Bitnami package for ClickHouse extension.

### Deploying Azure Resource Manager (ARM) Template with Azure CLI

Prerequisites:

1. Kubernetes cluster: A running AKS Kubernetes cluster
2. Azure CLI: Azure command-line interface (Azure CLI) is a set of commands used to create and manage Azure resources. More information on Azure CLI can be found in [Azure Command-Line Interface (CLI) - Overview \| Microsoft Learn](https://learn.microsoft.com/en-us/cli/azure/).

Instructions

1. Create a resource group

```console
az group create  --name \[insert resource group name\] --location \[insert location\]
```

1. Deploy the ARM Template

```console
az deployment group create --resource-group \[insert resource group name\] --template-file \[path to ARM template file\]
```

### Installing the Bitnami package for ClickHouse extension using Azure CLI

Prerequisites

Before you begin, ensure you have the following:

1. Kubernetes cluster: A running AKS Kubernetes cluster
2. Azure CLI: Azure command-line interface (Azure CLI) is a set of commands used to create and manage Azure resources. More information on Azure CLI can be found in [Azure Command-Line Interface (CLI) - Overview \| Microsoft Learn](https://learn.microsoft.com/en-us/cli/azure/).

To install the AKS extension, input the following command into the command line:

```console
az k8s-extension create
  --name \[insert extension name\]
  --extension-type Bitnami.ClickHouseMain
  --scope namespace
  --cluster-name \[insert existing AKS cluster name\]
  --resource-group \[insert resource group name\]
  --cluster-type managedClusters
  --plan-name main
  --plan-product clickhouse-cnab
  --plan-publisher bitnami
  --target-namespace clickhouse
(optional) --configuration-settings \[insert configuration settings\]
```

Make sure to replace the placeholders (e.g., `[insert extension name]`, `[insert existing AKS cluster name]`, `[insert resource group name]`) with your specific details.

Example: `az k8s-extension create --name clickhouse2 --extension-type Bitnami.ClickHouseMain --scope namespace --cluster-name myAKScluster --resource-group myResourceGroup --cluster-type managedClusters --plan-name main --plan-product clickhouse-cnab --plan-publisher bitnami --target-namespace clickhouse --configuration-settings replicaCount=2 memoryHighWatermark.enabled="true" memoryHighWatermark.type="absolute" memoryHighWatermark.value="512Mi"`

### Terraform deployment

Prerequisites

1. **Terraform:** Feel free to refer to this guide for setup instructions: [Guide to Setting Up and Using Terraform and Azure CLI to Deploy a Kubernetes Cluster - Copy.docx](https://microsoft-my.sharepoint.com/:w:/r/personal/maanasagovi_microsoft_com/_layouts/15/Doc.aspx?sourcedoc=%7BE42D47A2-B605-47C8-AC8C-F612D3C11D7B%7D&file=Guide%20to%20Setting%20Up%20and%20Using%20Terraform%20and%20Azure%20CLI%20to%20Deploy%20a%20Kubernetes%20Cluster%20-%20Copy.docx&action=default&mobileredirect=true&share=IQGiRy3kBbbIR6yM9hLTwR17Ac4Au-OiH2pQPpFOnuOBMnA) **(or** [Install \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/install)**)**
2. **Azure CLI:** [How to install the Azure CLI \| Microsoft Learn](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)

#### Initialize Terraform

1. To initialize Terraform in the current directory where you have copied the k8s-extension-install sample run the following command:  

```console
terraform init  
```

1. Create a new directory for your Terraform configuration files and navigate to it.
2. Create a file named `main.tf` containing the following:

```text
provider "azurerm" {
  features {}
}

resource "azurerm_kubernetes_cluster" "aks" {
  name                = "\[insert existing AKS cluster name\]"
  location            = "\[insert location\]"
  resource_group_name = "\[insert resource group name\]"
  dns_prefix          = "aks"
  default_node_pool {
    name       = "default"
    node_count = 1
    vm_size    = "Standard_DS2_v2"
  }

  identity {
    type = "SystemAssigned"
  }
}

resource "azurerm_kubernetes_cluster_extension" "clickhouse" {  
  name                  = "\[insert extension name\]"  
  kubernetes_cluster_id = azurerm_kubernetes_cluster.aks.id  
  extension_type        = "Bitnami.ClickHouseMain"  
  scope {  
    namespace = "clickhouse"  
  }  
  plan {  
    name      = "main"  
    product   = "clickhouse-cnab"  
    publisher = "bitnami"  
  }  
  configuration_settings = {  
    \# Add any configuration settings here  
  }
}
```

1. Before you test run the **main.tf file,** you need to update the following in the tf file:

**AKS cluster name** - The name of the AKS cluster.
**Resource group name** - The name of the resource group where AKS cluster is located.
**Extension name** - Name of the extension

## Deploy the application

1. Deploy the application with default configuration for azure-vote.

```console
terraform apply 
```
