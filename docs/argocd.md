# Deploy ArgoCD on an Azure Kubernetes Service (AKS) Cluster using Bitnami Marketplace K8s application.

This guide demonstrates how to deploy ArgoCD on an Azure Kubernetes Service (AKS) cluster using the Bitnami ArgoCD Helm chart and includes instructions for basic usage such as creating and consuming messages.

Argo CD is a continuous delivery tool for Kubernetes based on GitOps.

## Prerequisites

Before you begin, ensure you have the following:

1.  **Kubernetes cluster**: A running AKS Kubernetes cluster
2.  **kubectl**: The Kubernetes command-line tool is installed and configured to interact with your cluster.
3.  **PV provisioner** support in the underlying infrastructure
4.  **ReadWriteMany volumes** for deployment scaling

## Deployment and quick start

### Installing the Bitnami package for ArgoCD extension using Marketplace

- Navigate to Azure Portal: [https://portal.azure.com](https://portal.azure.com/) 
- Navigate to the running Azure Kubernetes Service (AKS) cluster where you would like to install the extension. More details about AKS can be found in [What is Azure Kubernetes Service (AKS)? - Azure Kubernetes Service \| Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/what-is-aks). 

1.  For more information on how to create a Kubernetes (AKS) Cluster, navigate to [Kubernetes on Azure tutorial - Create an Azure Kubernetes Service (AKS) cluster - Azure Kubernetes Service \| Microsoft Learn](https://learn.microsoft.com/en-us/azure/aks/tutorial-kubernetes-deploy-cluster?tabs=azure-cli) 

- Under the “Settings” tab, select “Extensions + Applications”.

<img src="images/argocd/image1.png" />

- Select “Add” located at the top left portion of the “Extensions + Applications” page. 
- In the “Search the Marketplace” bar, search for “Bitnami package for ArgoCD” and select the extension. 
- Under Plan, select “Bitnami package for ArgoCD” and select “Create” 

#### Under “Basics” Tab

##### Project Details 

1.  For “Subscriptions”, select the subscription your AKS cluster is under. All resources in an Azure subscription are billed together. 
2.  For “Resource Group”, select the resource group your AKS cluster is under. A resource group is a collection of resources that share the same lifecycle, permissions, and policies. 

##### Instance Details

1.  If you would like to create a new AKS Cluster, select “Yes”. If you would like to use an existing cluster, select “No”.  
2.  If Creating a New Kubernetes Cluster: For “Region” select the region to be used with the newly created AKS cluster. 

#### Under “Kubernetes Cluster (AKS) Details” Tab

If Using an Existing Kubernetes Cluster
1.  Kubernetes Cluster (AKS) Name: The resource name of the existing managed Kubernetes cluster (AKS) in which the extension will be installed. 

If Creating a New Kubernetes Cluster 
1.  Kubernetes Cluster (AKS) Name: The name of a new managed Kubernetes cluster (AKS) in which the extension will be installed. Use only allowed characters. 
2.  Kubernetes version: The version of Kubernetes that should be used for this managed Kubernetes cluster. You will be able to upgrade this version after creating the cluster. 
3.  VM size: The size of virtual machine for the managed Kubernetes cluster nodes. 
4.  Enable auto scaling: Whether to enable auto-scaler for the number of nodes in the managed Kubernetes cluster. 
5.  Number of agents: Number agent nodes in the managed Kubernetes cluster. 

#### Under “Application Details” Tab

1.  Cluster extension resource name: Create a name for your extension resource 
3.  The value must not be empty. 
4.  Only lowercase alphanumeric characters are allowed, and the value must be 6-30 characters long. 
4.  Installation namespace: The Kubernetes namespace in which the extension will be installed. If the namespace does not exist, it will be created. 
5.  Allow extension auto upgrades of minor versions: Whether to allow auto upgrades of minor versions for the extension. 
6.  Application parameters (optional): Parameters are optional, for a quick start, the application can be launched with the default configuration. The link below lists the parameters that can be configured during installation for advanced customization. [charts/bitnami/clickhouse at main · bitnami/charts](https://github.com/bitnami/charts/tree/main/bitnami/clickhouse) 
7.  Enable multi line values: Enable this option when some of the parameter values specified above need to be multi line strings. It will be activated or deactivated for new rows. 

#### Under “Usage Instructions” Tab

1.  The deployment notes of this solution provide useful information regarding how to access the application and the different parameters that have been generated during the deployment. Please take a look at the following guide to know more about it. 

[Usage instructions for Azure Marketplace Kubernetes Applications](https://docs.bitnami.com/azure/faq/get-started/usage-instructions-cnab/) 

#### Under “Review + Create”

1.  Review the information and select “Create” at the bottom right portion of the page to install the Bitnami package for ArgoCD extension. 

### Deploying Azure Resource Manager (ARM) Template with Azure CLI

Prerequisites

1.  Kubernetes cluster: A running AKS Kubernetes cluster 
2.  Azure CLI: Azure command-line interface (Azure CLI) is a set of commands used to create and manage Azure resources. More information on Azure CLI can be found in [Azure Command-Line Interface (CLI) - Overview \| Microsoft Learn](https://learn.microsoft.com/en-us/cli/azure/). 

Instructions 

1.  Create a resource group 
```
az group create  --name \[insert resource group name\] --location \[insert location\] 
```
2.  Deploy the ARM Template 
```
az deployment group create --resource-group \[insert resource group name\] --template-file \[path to ARM template file\] 
```

### Installing the Bitnami package for ArgoCD extension using Azure CLI

Prerequisites 

Before you begin, ensure you have the following: 

1. Kubernetes cluster: A running AKS Kubernetes cluster
2. Azure CLI: Azure command-line interface (Azure CLI) is a set of commands used to create and manage Azure resources. More information on Azure CLI can be found in [Azure Command-Line Interface (CLI) - Overview \| Microsoft Learn](https://learn.microsoft.com/en-us/cli/azure/). 

To install the AKS extension, input the following command into the command line: 
```
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

1.  **Terraform:** Feel free to refer to this guide for setup instructions: [Guide to Setting Up and Using Terraform and Azure CLI to Deploy a Kubernetes Cluster - Copy.docx](https://microsoft-my.sharepoint.com/:w:/r/personal/maanasagovi_microsoft_com/_layouts/15/Doc.aspx?sourcedoc=%7BE42D47A2-B605-47C8-AC8C-F612D3C11D7B%7D&file=Guide%20to%20Setting%20Up%20and%20Using%20Terraform%20and%20Azure%20CLI%20to%20Deploy%20a%20Kubernetes%20Cluster%20-%20Copy.docx&action=default&mobileredirect=true&share=IQGiRy3kBbbIR6yM9hLTwR17Ac4Au-OiH2pQPpFOnuOBMnA) **(or** [Install \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/install)**)** 
2.  **Azure CLI:** [How to install the Azure CLI \| Microsoft Learn](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) 

#### Initialize Terraform

1.  To initialize Terraform in the current directory where you have copied the k8s-extension-install sample run the following command:  
```
terraform init  
```
2.  Create a new directory for your Terraform configuration files and navigate to it. 
3.  Create a file named main.tf containing the following: 
```
provider "azurerm" { 
  features {} 
} 
 
resource "azurerm_kubernetes_cluster" "aks" { 
  name                = "\[insert existing AKS cluster name\]" 
  location            = "\[insert location\]" 
  resource_group_name = "\[insert resource group name\]" 
  dns_prefix          = "aks" 
 
  default_node_pool { 
    name       = "default" 
    node_count = 1 
    vm_size    = "Standard_DS2_v2" 
  } 
 
  identity { 
    type = "SystemAssigned" 
  } 
} 
 
resource "azurerm_kubernetes_cluster_extension" "clickhouse" { 
  name                 = "\[insert extension name\]" 
  kubernetes_cluster_id = azurerm_kubernetes_cluster.aks.id 
  extension_type       = "Bitnami.ClickHouseMain" 
  scope { 
    namespace = "clickhouse" 
  } 
  plan { 
    name      = "main" 
    product   = "clickhouse-cnab" 
    publisher = "bitnami" 
  } 
  configuration_settings = { 
    \# Add any configuration settings here 
  } 
} 
```
1.  Before you test run the main.tf file, you need to update the following in the tf file:  

**AKS cluster name** - The name of the AKS cluster.  
**Resource group name** - The name of the resource group where AKS cluster is located.  
**Extension name** - Name of the extension 
**Deploy the application**  

1.  Deploy the application with default configuration for azure-vote.  
```
terraform apply 
```
 
### Step 3: Access ArgoCD

1.  Get ArgoCD credentials:
```
echo "Username: \"admin\""
echo "Password: $(kubectl -n default get secret argocd-secret -o jsonpath="{.data.clearPassword}" \| base64 -d)"
```

2.  Access ArgoCD UI with the above-obtained credentials:

<img src="images/argocd/image2.png" /> 
<img src="images/argocd/image3.png" /> 

## Configuration and installation details

### Resource requests and limits

Bitnami charts allow setting resource requests and limits for all containers inside the chart deployment. These are inside the `resources` value (check parameter table). Setting requests is essential for production workloads and these should be adapted to your specific use case.

To make this process easier, the chart contains the `resourcesPreset` values, which automatically sets the `resources` section according to different presets. Check these presets in [the bitnami/common chart](https://github.com/bitnami/charts/blob/main/bitnami/common/templates/_resources.tpl#L15). However, in production workloads using `resourcesPreset` is discouraged as it may not fully adapt to your specific needs. Find more information on container resource management in the [official Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/).

### Set pod affinity

This chart allows you to set your custom affinity using the `affinity` parameter. Find more information about Pod affinity in the [kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).

As an alternative, use one of the preset configurations for pod affinity, pod anti-affinity, and node affinity available at the [bitnami/common](https://github.com/bitnami/charts/tree/main/bitnami/common#affinities) chart. To do so, set the `podAffinityPreset`, `podAntiAffinityPreset`, or `nodeAffinityPreset` parameters.

### Default config maps and secrets

The chart has hardcoded names for some ConfigMaps and Secrets like `argocd-ssh-known-hosts-cm`, `argocd-repo-server-tls` or `argocd-ssh-known-hosts-cm`. Argo CD will search for those specific names when the chart installed, so installing the chart twice in the same namespaces is not possible due to this restriction. For more information about each configmap or secret check the references at the corresponding YAML files.

### Using SSO

In order to use SSO you need to enable Dex by setting `dex.enabled=true`. You can follow [this guide](https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/#1-register-the-application-in-the-identity-provider) to configure your Argo CD deployment into your identity provider. After that, you need to configure Argo CD like described [here](https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/#2-configure-argo-cd-for-sso). You can set the Dex configuration at `server.config.dex\.config` that will populate the `argocd-cm` config map.

> [!NOTE]
> `dex.config` is the key of the object. IF you are using the Helm CLI to set the parameter you need to scape the `.` like `--set server.config.dex\.config`.
> [!IMPORTANT]
> If you enable Dex without configuring it you will get an error similar to `msg="dex is not configured"`, and the Dex pod will never reach the running state.

### Installing a Config Management Plugin

In order to install a Config Management Plugin as described in the official [documentation](https://argo-cd.readthedocs.io/en/stable/operator-manual/config-management-plugins/), it's recommended to run a sidecar container & mount the plugin configuration from a ConfigMap. You can achieve so by using `repoServer.extraVolumes`, `repoServer.sidecars` and `extraDeploy` parameters as shown in the example below:

```yaml
repoServer:
  extraVolumes:
    - name: plugin-config
      configMap:
        name: my-plugin-config
    - name: plugins
      emptyDir: {}
    - name: plugin-tmp
      emptyDir: {}
    - name: var-files
      emptyDir: {}
  sidecars:
    - name: plugin-sidecar
      command: [argocd-cmp-server, --config-dir-path, /app/config]
      image: "{{ include \"argocd.image\" . }}"
      env:
        - name: ARGOCD_PLUGINSOCKFILEPATH
          value: /app/plugins
      securityContext:
        runAsNonRoot: true
        runAsUser: 1001
      volumeMounts:
        - mountPath: /app/plugins
          name: plugins
        - mountPath: /tmp
          name: plugin-tmp
        - mountPath: /var/run/argocd
          name: var-files
        # Insert plugin configuration
        - mountPath: /app/config/plugin.yaml
          subPath: plugin.yaml
          name: plugin-config
extraDeploy:
- apiVersion: v1
  kind: ConfigMap
  metadata:
    name: my-plugin-config
  data:
    plugin.yaml: |
      apiVersion: argoproj.io/v1alpha1
      kind: ConfigManagementPlugin
      metadata:
        name: my-plugin
      spec:
        version: v1.0
        (...)
```

### Allowing multi namespace tendancy

In case you would like to allow applications / application sets in multiple namespaces, you can use the following to configure Argo-CD.

Upstream docs:

- [Reconfigure Argo CD to allow certain namespaces for apps](https://argo-cd.readthedocs.io/en/stable/operator-manual/app-any-namespace/#reconfigure-argo-cd-to-allow-certain-namespaces)
- [Reconfigure Argo CD to allow certain namespaces for appset](https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Appset-Any-Namespace/#change-workload-startup-parameters)

```yaml
controller:
  # Default is true
  clusterAdminAccess: true
  extraArgs:
  # Refer to documentation to allow specific namespaces:
  # https://argo-cd.readthedocs.io/en/stable/operator-manual/app-any-namespace/#change-workload-startup-parameters
  - --application-namespaces=*
  # Refer to documentation if you are enabling notifications
  # https://argo-cd.readthedocs.io/en/stable/operator-manual/notifications/#namespace-based-configuration
  - --self-service-notification-enabled

server:
  # Default is true
  clusterAdminAccess: true
  # Refer to recommended documentation for config:
  # https://argo-cd.readthedocs.io/en/stable/operator-manual/app-any-namespace/#switch-resource-tracking-method
  config:
    application.resourceTrackingMethod: annotation

repoServer:
  # Default is false
  clusterAdminAccess: true

notifications:
  # Enable if you would like notifications to be used, default false
  enabled: true
  # Default is false
  clusterAdminAccess: true

applicationSet:
  # Enable if you would like applicationSets to be used, default false
  enabled: true
  # Default is false
  clusterAdminAccess: true

  # Refer to documentation for SCM providers:
  # https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Appset-Any-Namespace/#scm-providers-secrets-consideration
  extraEnv:
  - name: ARGOCD_APPLICATIONSET_CONTROLLER_ENABLE_SCM_PROVIDERS
    value: true
  - name: ARGOCD_APPLICATIONSET_CONTROLLER_ALLOWED_SCM_PROVIDERS
    value: https://git.mydomain.com/,https://gitlab.mydomain.com/

  # Refer to documentation to allow specific namespaces:
  # https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Appset-Any-Namespace/#change-workload-startup-parameters
  extraArgs:
  - --applicationset-namespaces=*
```

### Ingress

This chart provides support for Ingress resources. If you have an ingress controller installed on your cluster, such as [nginx-ingress-controller](https://github.com/bitnami/charts/tree/main/bitnami/nginx-ingress-controller) or [contour](https://github.com/bitnami/charts/tree/main/bitnami/contour) you can utilize the ingress controller to serve your application.To enable Ingress integration, set `server.ingress.enabled` to `true` for the http ingress or `server.grpcIngress.enabled` to `true` for the gRPC ingress.

The most common scenario is to have one host name mapped to the deployment. In this case, the `xxx.ingress.hostname` property can be used to set the host name. The `xxx.ingress.tls` parameter can be used to add the TLS configuration for this host.

However, it is also possible to have more than one host. To facilitate this, the `xxx.ingress.extraHosts` parameter (if available) can be set with the host names specified as an array. The `xxx.ingress.extraTLS` parameter (if available) can also be used to add the TLS configuration for extra hosts.

> [!NOTE]  
> For each host specified in the `xxx.ingress.extraHosts` parameter, it is necessary to set a name, path, and any annotations that the Ingress controller should know about. Not all annotations are supported by all Ingress controllers, but [this annotation reference document](https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/annotations.md) lists the annotations supported by many popular Ingress controllers.

Adding the TLS parameter (where available) will cause the chart to generate HTTPS URLs, and the  application will be available on port 443. The actual TLS secrets do not have to be generated by this chart. However, if TLS is enabled, the Ingress record will not work until the TLS secret exists.

[Learn more about Ingress controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/).

### Enable TLS support

This chart facilitates the creation of TLS secrets for use with the Ingress controller (although this is not mandatory). Apart from the Ingress TLS certificates, Argo CD repo server will auto-generate a secret named `argocd-repo-server-tls`. This secret contains the TLS configuration for the Argo CD components. The secret will be created only if it does not exist, so if you want to add custom TLS configuration you can create a secret with that name before installing the chart. There are several common use cases for the TLS secrets generation:

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

### Additional environment variables

In case you want to add extra environment variables (useful for advanced operations like custom init scripts), you can use the `extraEnvVars` property.

```yaml
argo-cd:
  extraEnvVars:
    - name: LOG_LEVEL
      value: error
```

Alternatively, you can use a ConfigMap or a Secret with the environment variables. To do so, use the `extraEnvVarsCM` or the `extraEnvVarsSecret` values.

### Deploying extra resources

There are cases where you may want to deploy extra objects, such as a ConfigMap containing your app's configuration or some extra deployment with a microservice used by your app. To cover this case, the chart allows adding the full specification of other objects using the extraDeploy parameter.

## Prometheus Metrics

This chart can be integrated with Prometheus by setting `*.metrics.enabled` (under the `repoServer`, `controller`, `applicationSet`, `server`, `notification` and `dex` sections) to `true`. This will expose the Argo CD native Prometheus ports, as well as create a special metrics service, which can be configured under the `metrics.service` section. This `metrics` service will have the necessary annotations to be automatically scraped by Prometheus.

### Prometheus requirements

It is necessary to have a working installation of Prometheus or Prometheus Operator for the integration to work. Install the [Bitnami Prometheus helm chart](https://github.com/bitnami/charts/tree/main/bitnami/prometheus) or the [Bitnami Kube Prometheus helm chart](https://github.com/bitnami/charts/tree/main/bitnami/kube-prometheus) to easily have a working Prometheus in your cluster.

### Integration with Prometheus Operator

The chart can deploy `ServiceMonitor` objects for integration with Prometheus Operator installations. To do so, set the value `*.metrics.serviceMonitor.enabled=true` (under the `repoServer`, `controller`, `applicationSet`, `server`, `notifications` and `dex` sections). Ensure that the Prometheus Operator `CustomResourceDefinitions` are installed in the cluster or it will fail with the following error:

```text
no matches for kind "ServiceMonitor" in version "monitoring.coreos.com/v1"
```

Install the [Bitnami Kube Prometheus helm chart](https://github.com/bitnami/charts/tree/main/bitnami/kube-prometheus) for having the necessary CRDs and the Prometheus Operator.
