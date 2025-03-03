# Deploy RabbitMQ on an Azure Kubernetes Service (AKS) Cluster using Bitnami Marketplace K8s application

This guide demonstrates how to deploy RabbitMQ on an Azure Kubernetes Service (AKS) cluster using the Bitnami RabbitMQ Helm chart and includes instructions for basic usage such as creating and consuming messages.

> [!NOTE]
> This guide is not applicable for use with [Azure Kubernetes Service (AKS) Automatic](https://learn.microsoft.com/en-us/azure/aks/intro-aks-automatic).

## Prerequisites

Before you begin, ensure you have the following:

1. **Kubernetes cluster**: A running AKS Kubernetes cluster
2. **kubectl**: The Kubernetes command-line tool is installed and configured to interact with your cluster. You can use [az aks install-cli](https://learn.microsoft.com/en-us/cli/azure/aks?view=azure-cli-latest%22%20\l%20%22az-aks-install-cli) command to download and install [kubectl](https://kubernetes.io/docs/reference/kubectl/introduction/) and [kubelogin](https://learn.microsoft.com/en-us/azure/aks/kubelogin-authentication) tools.
3. **Persistent Volume**: Optional, if you wish to persist RabbitMQ data.

## Deployment

### Installing the Bitnami package for RabbitMQ extension using Marketplace

- Navigate to Azure Portal: [https://portal.azure.com](https://portal.azure.com/)
- Navigate to the running Azure Kubernetes Service (AKS) cluster where you would like to install the extension. More details about AKS can be found in [What is Azure Kubernetes Service (AKS)? - Azure Kubernetes Service \| Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/what-is-aks).

1. For more information on how to create a Kubernetes (AKS) Cluster, navigate to [Kubernetes on Azure tutorial - Create an Azure Kubernetes Service (AKS) cluster - Azure Kubernetes Service \| Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/tutorial-kubernetes-deploy-cluster?tabs=azure-cli)

- Under the “Settings” tab, select “Extensions + Applications”.

![ui](../images/rabbitmq/image1.png)

- Select “Add” located at the top left portion of the “Extensions + Applications” page.
- In the “Search the Marketplace” bar, search for “Bitnami package for RabbitMQ” and select the extension.
- Under Plan, select “Bitnami package for RabbitMQ” and select “Create”

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

![ui2](../images/rabbitmq/image2.png)

If Creating a New Kubernetes Cluster

1. Kubernetes Cluster (AKS) Name: Define the name of a new Azure Kubernetes Service (AKS) cluster in which the extension will be installed. Use only allowed characters.
2. Kubernetes version: Choose the version of Kubernetes that should be used for this managed Kubernetes cluster. You will be able to upgrade this version after creating the cluster.
3. VM size: Select the size of virtual machine for the managed Kubernetes cluster nodes.
4. Enable auto scaling: Whether to enable auto-scaler for the number of nodes in the managed Kubernetes cluster.
5. Number of agents: Specify the number of agent nodes in the Azure Kubernetes Service (AKS) cluster.

![ui3](../images/rabbitmq/image3.png)

#### Under “Application Details” Tab

1. Cluster extension resource name: Create a name for your extension resource
2. The value must not be empty.
3. Only lowercase alphanumeric characters are allowed, and the value must be 6-30 characters long.
4. Installation namespace: The Kubernetes namespace in which the extension will be installed. If the namespace does not exist, it will be created.
5. Allow extension auto upgrades of minor versions: Whether to allow auto upgrades of minor versions for the extension.
6. Application parameters (optional): Parameters are optional, for a quick start, the application can be launched with the default configuration. The link below lists the parameters that can be configured during installation for advanced customization. [charts/bitnami/rabbitmq at main · bitnami/charts](https://github.com/bitnami/charts/tree/main/bitnami/rabbitmq#parameters)
7. Enable multi line values: Enable this option when some of the parameter values specified above need to be multi line strings. It will be activated or deactivated for new rows.

![ui4](../images/rabbitmq/image4.png)

#### Under “Usage Instructions” Tab

1. The deployment notes of this solution provide useful information regarding how to access the application and the different parameters that have been generated during the deployment. Please look at the following guide to know more about it.

[Usage instructions for Azure Marketplace Kubernetes Applications](https://docs.bitnami.com/azure/faq/get-started/usage-instructions-cnab/)

![ui5](../images/rabbitmq/image5.png)

#### Under “Review + Create”

1. Review the information and select “Create” at the bottom right portion of the page to install the Bitnami package for RabbitMQ extension.

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

### Installing the Bitnami package for RabbitMQ extension using Azure CLI

Prerequisites

Before you begin, ensure you have the following:

1. Kubernetes cluster: A running AKS Kubernetes cluster
2. Azure CLI: Azure command-line interface (Azure CLI) is a set of commands used to create and manage Azure resources. More information on Azure CLI can be found in [Azure Command-Line Interface (CLI) - Overview \| Microsoft Learn](https://learn.microsoft.com/en-us/cli/azure/).

To install the AKS extension, input the following command into the command line:

```console
az k8s-extension create
--name \[insert extension name\]
  --extension-type Bitnami.RabbitmqMain
  --scope namespace
  --cluster-name \[insert existing AKS cluster name\]
  --resource-group \[insert resource group name\]
  --cluster-type managedClusters
  --plan-name main
  --plan-product rabbitmq-cnab
  --plan-publisher bitnami
  --target-namespace rabbitmq
(optional) --configuration-settings \[insert configuration settings\]
```

Make sure to replace the placeholders (e.g., `[insert extension name]`, `[insert existing AKS cluster name]`, `[insert resource group name]`) with your specific details.

Example: `az k8s-extension create --name rabbitmq2 --extension-type Bitnami.RabbitmqMain --scope namespace --cluster-name myAKScluster --resource-group myResourceGroup --cluster-type managedClusters --plan-name main --plan-product rabbitmq-cnab --plan-publisher bitnami --target-namespace rabbitmq --configuration-settings replicaCount=2 memoryHighWatermark.enabled="true" memoryHighWatermark.type="absolute" memoryHighWatermark.value="512Mi"`

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

resource "azurerm_kubernetes_cluster_extension" "rabbitmq" {
  name              = "\[insert extension name\]"
  cluster_id        = azurerm_kubernetes_cluster.aks.id
  extension_type    = "Bitnami.RabbitmqMain"
  release_namespace = "rabbitmq"
  plan {
    name      = "main"
    product   = "rabbitmq-cnab"
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

### Access RabbitMQ

1. Get RabbitMQ credentials:

```console
echo "User: $(kubectl get secret --namespace default my-rabbitmq -o jsonpath="{.data.rabbitmq-username}" | base64 --decode)"
echo "Password: $(kubectl get secret --namespace default my-rabbitmq -o jsonpath="{.data.rabbitmq-password}" | base64 --decode)"
```

1. Access the RabbitMQ Management Interface:

To access the RabbitMQ management interface, you can set up a port forward:

```console
kubectl port-forward --namespace default svc/my-rabbitmq 15672:15672
```

Now, open your browser and go to <http://127.0.0.1:15672>. Log in using the credentials obtained earlier

### Send and Receive Messages in RabbitMQ

#### Create a queue

Once logged into the RabbitMQ management interface:

1. Navigate to the **Queues and Streams** tab.
2. In the **Add a new queue section**, provide a name for your queue, e.g., `myqueue`.
3. Click **Add queue**

![ui6](../images/rabbitmq/image6.png)

#### Publish a message to the queue

1. Go to the **Queues and Streams** tab and select your queue (`myqueue`).
2. Scroll down to the **Publish message** section.
3. In the **Payload** field, type the message you want to send, e.g., `Hello, RabbitMQ!`.
4. Click **Publish message**.

![ui7](../images/rabbitmq/image7.png)

#### Consume messages from the queue

1. In the same **Queues and Streams** tab, scroll to the **Get Messages** section.
2. Set the number of messages to fetch (e.g., `1`) and click **Get Message(s)**.
3. The message you published earlier will be displayed below in the message list.

![ui8](../images/rabbitmq/image8.png)

## Configuration and installation details

### Resource requests and limits

Bitnami charts allow setting resource requests and limits for all containers inside the chart deployment. These are inside the `resources` value (check parameter table). Setting requests is essential for production workloads and these should be adapted to your specific use case.

To make this process easier, the chart contains the `resourcesPreset` values, which automatically sets the `resources` section according to different presets. Check these presets in [the bitnami/common chart](https://github.com/bitnami/charts/blob/main/bitnami/common/templates/_resources.tpl#L15). However, in production workloads using `resourcesPreset` is discouraged as it may not fully adapt to your specific needs. Find more information on container resource management in the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/).

### Set pod affinity

This chart allows you to set your custom affinity using the `affinity` parameter. Find more information about Pod's affinity in the [kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).

As an alternative, you can use of the preset configurations for pod affinity, pod anti-affinity, and node affinity available at the [bitnami/common](https://github.com/bitnami/charts/tree/main/bitnami/common#affinities) chart. To do so, set the `podAffinityPreset`, `podAntiAffinityPreset`, or `nodeAffinityPreset` parameters.

### Scale horizontally

To horizontally scale this chart once it has been deployed, two options are available:

- Use the `kubectl scale` command.
- Upgrade the chart modifying the `replicaCount` parameter.

```text
replicaCount=3
auth.password="$RABBITMQ_PASSWORD"
auth.erlangCookie="$RABBITMQ_ERLANG_COOKIE"
```

> [!NOTE]
> It is mandatory to specify the password and Erlang cookie that was set the first time the chart was installed when upgrading the chart. Otherwise, new pods won't be able to join the cluster.

When scaling down the solution, unnecessary RabbitMQ nodes are automatically stopped, but they are not removed from the cluster. These nodes must be manually removed via the `rabbitmqctl forget_cluster_node` command.

For instance, if RabbitMQ was initially installed with three replicas and then scaled down to two replicas, run the commands below (assuming that the release name is `rabbitmq` and the clustering type is `hostname`):

```console
kubectl exec rabbitmq-0 --container rabbitmq -- rabbitmqctl forget_cluster_node rabbit@rabbitmq-2.rabbitmq-headless.default.svc.cluster.local
kubectl delete pvc data-rabbitmq-2
```

> [!NOTE]
> It is mandatory to specify the password and Erlang cookie that was set the first time the chart was installed when upgrading the chart.

### Enable TLS support

To enable TLS support, first generate the certificates as described in the [RabbitMQ documentation for SSL certificate generation](https://www.rabbitmq.com/ssl.html#automated-certificate-generation).

Once the certificates are generated, you have two alternatives:

- Create a secret with the certificates and associate the secret when deploying the chart
- Include the certificates in the *values.yaml* file when deploying the chart

Set the *auth.tls.failIfNoPeerCert* parameter to *false* to allow a TLS connection if the client fails to provide a certificate.

Set the *auth.tls.sslOptionsVerify* to *verify_peer* to force a node to perform peer verification. When set to *verify_none*, peer verification will be disabled and certificate exchange won't be performed.

This chart also facilitates the creation of TLS secrets for use with the Ingress controller (although this is not mandatory). There are several common use cases:

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

### Load custom definitions

It is possible to [load a RabbitMQ definitions file to configure RabbitMQ](https://www.rabbitmq.com/management.html#load-definitions). Follow the steps below:

Because definitions may contain RabbitMQ credentials, [store the JSON as a Kubernetes secret](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-files-from-a-pod). Within the secret's data, choose a key name that corresponds with the desired load definitions filename (i.e. `load_definition.json`) and use the JSON object as the value.

Next, specify the `load_definitions` property as an `extraConfiguration` pointing to the load definition file path within the container (i.e. `/app/load_definition.json`) and set `loadDefinition.enable` to `true`. Any load definitions specified will be available within in the container at `/app`.

> [!NOTE]
> Loading a definition will take precedence over any configuration done through other parameters.

If needed, you can use `extraSecrets` to let the chart create the secret for you. This way, you don't need to manually create it before deploying a release. These secrets can also be templated to use supplied chart values. Here is an example:

```yaml
auth:
  password: CHANGEME
extraSecrets:
  load-definition:
    load_definition.json: |
      {
        "users": [
          {
            "name": "{{ .Values.auth.username }}",
            "password": "{{ .Values.auth.password }}",
            "tags": "administrator"
          }
        ],
        "vhosts": [
          {
            "name": "/"
          }
        ]
      }
loadDefinition:
  enabled: true
  existingSecret: load-definition
extraConfiguration: |
  load_definitions = /app/load_definition.json
```

In order to load custom definitions with Key Vault and CSI Driver

1. Create the secret in Azure Key Vault.
2. Configure the CSI Driver to mount the secret in AKS.
3. Reference the secret in your RabbitMQ deployment:

```yaml
loadDefinition:
  enabled: true
  existingSecret: csi-vault-secret
  extraConfiguration: |
    load_definitions = /mnt/secrets-store/load_definition.json
```

### Configure LDAP support

LDAP support can be enabled in the chart by specifying the `ldap.*` parameters while creating a release. For example:

```text
ldap.enabled="true"
ldap.server="my-ldap-server"
ldap.port="389"
ldap.user_dn_pattern="cn=${username},dc=example,dc=org"
```

If `ldap.tls.enabled` is set to true, consider using `ldap.port=636` and checking the settings in the `advancedConfiguration` chart parameters.

### Configure memory high watermark

It is possible to configure a memory high watermark on RabbitMQ to define [memory thresholds](https://www.rabbitmq.com/memory.html#threshold) using the `memoryHighWatermark.*` parameters. To do so, you have two alternatives:

- Set an absolute limit of RAM to be used on each RabbitMQ node, as shown in the configuration example below:

```text
memoryHighWatermark.enabled="true"
memoryHighWatermark.type="absolute"
memoryHighWatermark.value="512Mi"
```

- Set a relative limit of RAM to be used on each RabbitMQ node. To enable this feature,  define the memory limits at pod level too. An example configuration is shown below:

```text
memoryHighWatermark.enabled="true"
memoryHighWatermark.type="relative"
memoryHighWatermark.value="0.4"
resources.limits.memory="2Gi"
```

### Add extra environment variables

In case you want to add extra environment variables (useful for advanced operations like custom init scripts), you can use the `extraEnvVars` property.

```yaml
extraEnvVars:
  - name: LOG_LEVEL
    value: error
```

Alternatively, you can use a ConfigMap or a Secret with the environment variables. To do so, use the `.extraEnvVarsCM` or the `extraEnvVarsSecret` properties.

### Configure the default user/vhost

If you want to create default user/vhost and set the default permission. you can use `extraConfiguration`:

```yaml
auth:
  username: default-user
extraConfiguration: |-
  default_vhost = default-vhost
  default_permissions.configure = .*
  default_permissions.read = .*
  default_permissions.write = .*
```

### Use plugins

The Bitnami Docker RabbitMQ image ships a set of plugins by default. By default, this chart enables `rabbitmq_management` and `rabbitmq_peer_discovery_k8s` since they are required for RabbitMQ to work on K8s.

To enable extra plugins, set the `extraPlugins` parameter with the list of plugins you want to enable. In addition to this, the `communityPlugins` parameter can be used to specify a list of URLs (separated by spaces) for custom plugins for RabbitMQ.

```text
communityPlugins="http://URL-TO-PLUGIN/"
extraPlugins="my-custom-plugin"
```

### Advanced logging

In case you want to configure RabbitMQ logging set `logs` value to false and set the log config in extraConfiguration following the [official documentation](https://www.rabbitmq.com/logging.html#log-file-location).

An example:

```yaml
logs: false # custom logging
extraConfiguration: |
  log.default.level = warning
  log.file = false
  log.console = true
  log.console.level = warning
  log.console.formatter = json
```

### How to Avoid Deadlocked Deployments After a Cluster-Wide Restart

RabbitMQ nodes assume their peers come back online within five minutes (by default). When the `OrderedReady` pod management policy is used with a readiness probe that implicitly requires a fully booted node, the deployment can deadlock:

- Kubernetes will expect the first node to pass a readiness probe
- The readiness probe may require a fully booted node
- The node will fully boot after it detects that its peers have come online
- Kubernetes will not start any more pods until the first one boots

The following combination of deployment settings avoids the problem:

- Use `podManagementPolicy: "Parallel"` to boot multiple cluster nodes in parallel
- Use `rabbitmq-diagnostics ping` for readiness probe

To learn more, please consult RabbitMQ documentation guides:

- [RabbitMQ Clustering guide: Node Restarts](https://www.rabbitmq.com/docs/clustering#restarting)
- [RabbitMQ Clustering guide: Restarts and Readiness Probes](https://www.rabbitmq.com/docs/clustering#restarting-readiness-probes)
- [Recommendations](https://www.rabbitmq.com/docs/cluster-formation#peer-discovery-k8s) for Operator-less (DIY) deployments to Kubernetes

#### Do Not Force Boot Nodes on a Regular Basis

Note that forcing nodes to boot is **not a solution** and doing so **can be dangerous**. Forced booting is a last resort mechanism in RabbitMQ that helps make remaining cluster nodes recover and rejoin each other after a permanent loss of some of their former peers. In other words, forced booting a node is an emergency event recovery procedure.

### Password management

Changing the password through RabbitMQ's UI can make the pod fail due to the default liveness probes. If you do so, remember to make the chart aware of the new password. Updating the default secret with the password you set through RabbitMQ's UI will automatically recreate the pods. If you are using your own secret, you may have to manually recreate the pods.

## Persistence

The [Bitnami RabbitMQ](https://github.com/bitnami/containers/tree/main/bitnami/rabbitmq) image stores the RabbitMQ data and configurations at the `/opt/bitnami/rabbitmq/var/lib/rabbitmq/` path of the container.

The chart mounts a [Persistent Volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) at this location. By default, the volume is created using dynamic volume provisioning. An existing PersistentVolumeClaim can also be defined.

### Use existing PersistentVolumeClaims

1. Create the PersistentVolume
2. Create the PersistentVolumeClaim
3. Deploy the solution using the `persistence.existingClaim=PVC_NAME` parameter

### Adjust permissions of the persistence volume mountpoint

As the image runs as non-root by default, it is necessary to adjust the ownership of the persistent volume so that the container can write data into it.

By default, the chart is configured to use Kubernetes Security Context to automatically change the ownership of the volume. However, this feature does not work in all Kubernetes distributions.
As an alternative, this chart supports using an `initContainer` to change the ownership of the volume before mounting it in the final destination.

You can enable this `initContainer` by setting `volumePermissions.enabled` to `true`.

## Prometheus Metrics

RabbitMQ has [built-in support](https://www.rabbitmq.com/docs/prometheus#default-endpoint) for Prometheus metrics
exposed at `GET /metrics`. However, these metrics are all cluster-wide, and do not show any per-queue or per-node
metrics.

To get per-object metrics, there is a
[second metrics endpoint](https://www.rabbitmq.com/docs/prometheus#detailed-endpoint) at `GET /metrics/detailed` that
accepts query parameters to choose which metric families you would like to see. For instance, you can pass
`family=node_coarse_metrics&family=queue_coarse_metrics` to see per-node and per-queue metrics, but with no need to see
Erlang, connection, or channel metrics.

Additionally, there is a [third metrics endpoint](https://www.rabbitmq.com/docs/prometheus#per-object-endpoint):
`GET /metrics/per-object`. which returns *all* per-object metrics. However, this can be computationally expensive on a
large cluster with many objects, and so RabbitMQ docs suggest using `GET /metrics/detailed` mentioned above to filter
your scraping and only fetch the per-object metrics that are needed for a given monitoring application.

Because they expose different sets of data, a valid use case is to scrape metrics from both `GET /metrics` and
`GET /metrics/detailed`, ingesting both cluster-level and per-object metrics. The `metrics.serviceMonitor.default` and
`metrics.serviceMonitor.detailed` values support configuring a ServiceMonitor that targets one or both of these metrics.
