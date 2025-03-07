# Deploy ClickHouse on an Azure Kubernetes Service (AKS) Cluster using Bitnami Marketplace K8s application

This guide demonstrates how to deploy ClickHouse on an Azure Kubernetes Service (AKS) cluster using the Bitnami ClickHouse Helm chart and includes instructions for basic usage.

ClickHouse is an open-source column-oriented OLAP database management system. It can boost your database performance while providing linear scalability and hardware efficiency.

## Prerequisites

Before you begin, ensure you have the following:

1. **Kubernetes cluster**: A running AKS Kubernetes cluster
2. **kubectl**: The Kubernetes command-line tool is installed and configured to interact with your cluster. You can use [az aks install-cli](https://learn.microsoft.com/en-us/cli/azure/aks?view=azure-cli-latest%22%20\l%20%22az-aks-install-cli) command to download and install [kubectl](https://kubernetes.io/docs/reference/kubectl/introduction/) and [kubelogin](https://learn.microsoft.com/en-us/azure/aks/kubelogin-authentication) tools.
3. **PV provisioner** support in the underlying infrastructure
4. **ReadWriteMany volumes** for deployment scaling

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

### Access ClickHouse

1. Get ClickHouse credentials:

```console
echo "Username: default"
echo "Password: $(kubectl get secret --namespace default my-clickhouse -o jsonpath="{.data.admin-password}" | base64 -d)"
```

1. Access one of the ClickHouse pods by running:

```console
kubectl exec my-clickhouse-shard0-0 -it -- /bin/bash
```

> [!IMPORTANT]
> Depending on the deployment parameters, you may need to adjust the above commands, such as changing the namespace or deployment name.

1. Once inside the clickhouse container, you can invoke the clickhouse-client binary. You will be prompted to enter the password (obtained above):

![console1](../images/clickhouse/image6.png)

### Create a table

Use `CREATE TABLE` to define a new table. Typical SQL DDL commands work in ClickHouse with one addition - tables in ClickHouse require an `ENGINE` clause. Use `MergeTree` to take advantage of the performance benefits of ClickHouse:

![console2](../images/clickhouse/image7.png)

### Insert data

You can use the familiar `INSERT INTO TABLE` command with ClickHouse, but it is important to understand that each insert into a MergeTree table causes a part (folder) to be created in storage. To minimize parts, bulk insert lots of rows at a time (tens of thousands or even millions at once).

![console3](../images/clickhouse/image8.png)

### Query your new table

You can write a `SELECT` query just like you would with any SQL database:

![console4](../images/clickhouse/image9.png)

## Configuration and installation details

### Resource requests and limits

Bitnami charts allow setting resource requests and limits for all containers inside the chart deployment. These are inside the `resources` value (check parameter table). Setting requests is essential for production workloads and these should be adapted to your specific use case.

To make this process easier, the chart contains the `resourcesPreset` values, which automatically sets the `resources` section according to different presets. Check these presets in [the bitnami/common chart](https://github.com/bitnami/charts/blob/main/bitnami/common/templates/_resources.tpl#L15). However, in production workloads using `resourcesPreset` is discouraged as it may not fully adapt to your specific needs. Find more information on container resource management in the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/).

### Pod affinity

This chart allows you to set your custom affinity using the `affinity` parameter. Find more information about Pod affinity in the [kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).

As an alternative, use one of the preset configurations for pod affinity, pod anti-affinity, and node affinity available at the [bitnami/common](https://github.com/bitnami/charts/tree/main/bitnami/common#affinities) chart. To do so, set the `podAffinityPreset`, `podAntiAffinityPreset`, or `nodeAffinityPreset` parameters.

### Scale horizontally

To horizontally scale the ClickHouse deployment once it has been deployed, two options are available:

- Use the `kubectl scale` command.
- Upgrade the chart by modifying the `replicaCount` parameter.

```yaml
replicaCount: 3
auth:
  password: "password"
```

> [!NOTE]
> It is mandatory to specify the password that was set the first time the chart was installed when upgrading the chart. Otherwise, new pods won't be able to join the cluster. See the above example where auth.password is set alongside with `replicaCount`.

### Update credentials

Bitnami charts configure credentials at first boot. Any further change in the secrets or credentials require manual intervention. Follow these instructions:

- Update the user password following [the upstream documentation](https://clickhouse.com/docs/en/sql-reference/statements/alter/user)
- Update the password secret with the new values (replace the SECRET_NAME, and PASSWORD placeholders)

```console
kubectl create secret generic SECRET_NAME --from-literal=admin-password=PASSWORD --dry-run -o yaml | kubectl apply -f -
```

### ClickHouse keeper support

You can set `keeper.enabled` to use ClickHouse keeper. If `keeper.enabled=true`, Zookeeper settings will be ignore.

### External Zookeeper support

You may want to have ClickHouse connect to an external zookeeper rather than installing one inside your cluster. Typical reasons for this are to use a managed database service, or to share a common database server for all your applications. To achieve this, the chart allows you to specify credentials for an external database with the `externalZookeeper` parameter. You should also disable the Zookeeper installation with the `zookeeper.enabled` option. Here is an example:

```yaml
zookeper:
  enabled: false
externalZookeeper:
  host: myexternalhost
  user: myuser
  password: mypassword
  database: mydatabase
  port: 3306
```

### Ingress without TLS

For using ingress (example without TLS):

```yaml
ingress:
  ## If true, ClickHouse server Ingress will be created
  ##
  enabled: true

  ## ClickHouse server Ingress annotations
  ##
  annotations: {}
  #   kubernetes.io/ingress.class: nginx
  #   kubernetes.io/tls-acme: 'true'

  ## ClickHouse server Ingress hostnames
  ## Must be provided if Ingress is enabled
  ##
  hosts:
    - clickhouse.domain.com
```

### Ingress TLS

If your cluster allows automatic creation/retrieval of TLS certificates, please refer to the documentation for that mechanism.

To manually configure TLS, first create/retrieve a key & certificate pair for the address(es) you wish to protect. Then create a TLS secret (named `clickhouse-server-tls` in this example) in the namespace. Include the secret's name, along with the desired hostnames, in the Ingress TLS section of your custom `values.yaml` file:

```yaml
ingress:
  ## If true, ClickHouse server Ingress will be created
  ##
  enabled: true

  ## ClickHouse server Ingress annotations
  ##
  annotations: {}
  #   kubernetes.io/ingress.class: nginx
  #   kubernetes.io/tls-acme: 'true'

  ## ClickHouse server Ingress hostnames
  ## Must be provided if Ingress is enabled
  ##
  hosts:
    - clickhouse.domain.com

  ## ClickHouse server Ingress TLS configuration
  ## Secrets must be manually created in the namespace
  ##
  tls:
    - secretName: clickhouse-server-tls
      hosts:
        - clickhouse.domain.com
```

### Enable TLS support

This chart facilitates the creation of TLS secrets for use with the Ingress controller (although this is not mandatory). There are several common use cases:

- Generate certificate secrets based on chart parameters.
- Enable externally generated certificates.
- Manage application certificates via an external service (like [cert-manager](https://github.com/jetstack/cert-manager/)).
- Create self-signed certificates within the chart (if supported).

In the first two cases, a certificate and a key are needed. Files are expected in `.pem` format.

Here is an example of a certificate file:

> [!NOTE]
> There may be more than one certificate if there is a certificate chain.

```text
-----BEGIN CERTIFICATE-----
MIID6TCCAtGgAwIBAgIJAIaCwivkeB5EMA0GCSqGSIb3DQEBCwUAMFYxCzAJBgNV
...
jScrvkiBO65F46KioCL9h5tDvomdU1aqpI/CBzhvZn1c0ZTf87tGQR8NK7v7
-----END CERTIFICATE-----
```

Here is an example of a certificate key:

```text
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvLYcyu8f3skuRyUgeeNpeDvYBCDcgq+LsWap6zbX5f8oLqp4
...
wrj2wDbCDCFmfqnSJ+dKI3vFLlEz44sAV8jX/kd4Y6ZTQhlLbYc=
-----END RSA PRIVATE KEY-----
```

- If using Helm to manage the certificates based on the parameters, copy these values into the `certificate` and `key` values for a given `*.ingress.secrets` entry.
- If managing TLS secrets separately, it is necessary to create a TLS secret with name `INGRESS_HOSTNAME-tls` (where INGRESS_HOSTNAME is a placeholder to be replaced with the hostname you set using the `*.ingress.hostname` parameter).
- If your cluster has a [cert-manager](https://github.com/jetstack/cert-manager) add-on to automate the management and issuance of TLS certificates, add to `*.ingress.annotations` the [corresponding ones](https://cert-manager.io/docs/usage/ingress/#supported-annotations) for cert-manager.
- If using self-signed certificates created by Helm, set both `*.ingress.tls` and `*.ingress.selfSigned` to `true`.

### Backup and restore

To back up and restore Helm chart deployments on Kubernetes, you need to back up the persistent volumes from the source deployment and attach them to a new deployment using [Velero](https://velero.io/), a Kubernetes backup/restore tool. Find the instructions for using Velero in [this guide](https://techdocs.broadcom.com/us/en/vmware-tanzu/application-catalog/tanzu-application-catalog/services/tac-doc/apps-tutorials-backup-restore-deployments-velero-index.html).

### Using custom scripts

For advanced operations, the Bitnami ClickHouse chart allows using custom init and start scripts that will be mounted in `/docker-entrypoint.initdb.d` and `/docker-entrypoint.startdb.d` . The `init` scripts will be run on the first boot whereas the `start` scripts will be run on every container start. For adding the scripts directly as values use the `initdbScripts` and `startdbScripts` values. For using Secrets use the `initdbScriptsSecret` and `startdbScriptsSecret`.

```yaml
initdbScriptsSecret: init-scripts-secret
startdbScriptsSecret: start-scripts-secret
```

### Additional environment variables

In case you want to add extra environment variables (useful for advanced operations like custom init scripts), you can use the `extraEnvVars` property.

```yaml
clickhouse:
  extraEnvVars:
    - name: LOG_LEVEL
      value: error
```

Alternatively, you can use a ConfigMap or a Secret with the environment variables. To do so, use the `extraEnvVarsCM` or the `extraEnvVarsSecret` values.

### Prometheus metrics

This chart can be integrated with Prometheus by setting `metrics.enabled` to `true`. This will expose Clickhouse native Prometheus endpoint in the service. It will have the necessary annotations to be automatically scraped by Prometheus.

#### Prometheus requirements

It is necessary to have a working installation of Prometheus or Prometheus Operator for the integration to work. Install the [Bitnami Prometheus helm chart](https://github.com/bitnami/charts/tree/main/bitnami/prometheus) or the [Bitnami Kube Prometheus helm chart](https://github.com/bitnami/charts/tree/main/bitnami/kube-prometheus) to easily have a working Prometheus in your cluster.

#### Integration with Prometheus Operator

The chart can deploy `ServiceMonitor` objects for integration with Prometheus Operator installations. To do so, set the value `metrics.serviceMonitor.enabled=true`. Ensure that the Prometheus Operator `CustomResourceDefinitions` are installed in the cluster or it will fail with the following error:

```text
no matches for kind "ServiceMonitor" in version "monitoring.coreos.com/v1"
```

Install the [Bitnami Kube Prometheus helm chart](https://github.com/bitnami/charts/tree/main/bitnami/kube-prometheus) for having the necessary CRDs and the Prometheus Operator.
