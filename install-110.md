---

copyright:
  years: 2015, 2019
lastupdated: "2019-03-06"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:deprecated: .deprecated}
{:important: .important}
{:note: .note}
{:tip: .tip}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}
{:download: .download}
{:gif: data-image-type='gif'}

# Install 1.1 on private cloud 3.1.0
{: #install-110}

{{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} version 1.1.0 runs on {{site.data.keyword.icpfull_notm}} version 3.1.0.
{: shortdesc}

Version 1.1.0 is compatible with {{site.data.keyword.icp4dfull}} version 1.2, meaning that both {{site.data.keyword.conversationshort}} and {{site.data.keyword.icp4dfull_notm}} can run on the same instance of {{site.data.keyword.icpfull_notm}} version 3.1.0. See [Overview of IBM Cloud Private for Data ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs-icpdata.mybluemix.net/docs/content/SSQNUZ_current/com.ibm.icpdata.doc/zen/overview/overview.html) for more information about that offering, including installation instructions.

## Software requirements
{: #install-110-prereqs}

- {{site.data.keyword.icpfull_notm}} 3.1.0
- Kubernetes 1.11.1
- Helm 2.9.1
- Tiller (Helm server) 2.9.1+icp

## System requirements
{: #install-110-sys-reqs}

See [Hardware requirements and recommendations ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/supported_system_config/hardware_reqs.html#reqs_multi){:new_window} for information about what is required for {{site.data.keyword.icpfull_notm}} itself.

All nodes, with the exception of the worker nodes, host the {{site.data.keyword.icpfull_notm}} cluster infrastructure. The worker nodes host the {{site.data.keyword.conversationshort}} resources. The worker nodes must provide the following number of Virtual Private CPUs (VPCs) to support {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} at a minimum.

- **Development**: 21 CPU with 75 GB Memory across a minimum of 1 worker node
- **Production**: 27 CPU with 112 GB Memory across a minimum of 4 worker nodes

These numbers reflect the bare minimum requirements. In a cluster environment, where CPU and memory are assigned to containers dynamically, CPU and memory resources can become stranded on nodes, leaving insufficient resources to schedule subsequent workloads. In particular, the process of training a machine learning model requires at least one node to have 4 CPUs that can be dedicated to training. This capacity is only needed when training occurs, which happens after changes are made to the training data for an assistant.
{: important}

## Tested deployment configurations
{: #install-110-tested-sys-reqs}

Table 1. Hardware verified to support a development deployment

| Node type | Number of nodes | CPU per node | Memory per node (GB) | Disk per node (GB) | Description |
|-----------|-----------------|--------------|-----------------|---------------|----|
| boot, master, management, proxy | 1 | 16  | 128 | 600 | Supports {{site.data.keyword.icpfull_notm}} infrastructure |
| worker | 1  | 24 | 256 | 500 | Supports the {{site.data.keyword.conversationshort}} service |
{: caption="Non-production hardware requirements" caption-side="top"}

Table 2. Hardware verified to support a production deployment

| Node type | Number of nodes | CPU per node | Memory per node (GB) | Disk per node (GB) |
|-----------|-----------------|--------------|-----------------|---------------|
| boot, master, proxy | 1 | 16 | 32 | 250 |
| management | 1 | 16  | 32 | 250 |
| worker | 5 | 8 | 32 | 500 |
{: caption="Production hardware requirements" caption-side="top"}

The systems that host {{site.data.keyword.conversationshort}} must meet these requirements:

- {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} can run on Intel architecture nodes only.
- CPUs must have 2.4 GHz or higher clock speed
- CPUs must support Linux SSE 4.2
- CPUs must support the AVX instruction set extension See the [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions) Wikipedia page for a list of CPUs that include this support (most CPUs since 2012). The service cannot function properly without AVX support.

### Storage requirements
{: #install-110-storage-reqs}

The following table lists the storage resources that have been verified to support a deployment.

Table 3. Storage requirements

| Component | Number of replicas | Space per pod | Storage type |
|-----------|-----------------|--------------|
| Postgres  | 3 | 10 GB | local-storage |
| etcd      | 3 | 10 GB | local-storage |
| Minio     | 4 |  5 GB | local-storage |
| MongoDB   | 3 | 80 GB | local-storage |
{: caption="Resource requirements" caption-side="top"}

<!-- Consider evaluating [vSphere Cloud Provider](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/vsphere_land.html) -->

### Microservices
{: #install-110-microservices}

Microservices are individual components that together comprise the service. The {{site.data.keyword.conversationshort}} service consists of the following microservices:

- **CLU (Conversational Language Understanding)**: Interface for store to communicate with the back-end to initiate ML training.
- **Dialog**: Dialog runtime, or user-chat capability.
- **ed-mm**: Manages contextual entity capabilities.
- **Master**: Controls the lifecycle of underlying intent and entity models.
- **SIREG** - Manages tokenization and system entity capabilities.
- **SLAD**: Manages service training capabilities.
- **Store**: API endpoints.
- **TAS**: Manages services model inferencing.
- **Tooling**: Provides the developer user interface.

In addition to these microservices, the Helm chart installs the following resources:

- **PostgreSQL**: Stores training data.
- **MongoDB**: Stores word vectors.
- **Redis**: Used by the {{site.data.keyword.conversationshort}} tool to store web session-related data.
- **etcd**: Manages service registration and discovery.
- **Minio**: Stores CLU models.

### Language considerations
{: #install-110-lang-reqs}

The components that are necessary to process natural languages require significant amounts of data. The base set of supported languages require 40 GB of memory per node. The following languages require that you add additional resources to support them:

Table 5. Language resource requirements

The following resources are what you need to support a production deployment, where two pods are dedicated to each additional language. In a development deployment, only one pod is used per language, which means you need half the number of resources in a development deployment.

| Language | Additional memory requirements per pod | Additional VPCs required`*` |
|----------|-----------------------------|------|
| Chinese (Simplified or Traditional or both) | 8 GB | 1 |
| German | 1 GB | 1 |
| Japanese | 1 GB | 1 |
| Korean | 2 GB | 1 |
{: caption="Language resource requirements" caption-side="top"}

`*`Again, for a development environment, you only need a half of a VPC for each of these languages.

For the full list of supported languages, see [Supported languages](/docs/services/assistant-icp/lang-support.html).

### Overview of the steps
{: #install-110-task-overview}

Follow these steps to install {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}}

1.  [Purchase and download installation artifacts](#install-110-download-wa-icp)
1.  [Prepare the cloud environment](#install-110-install-icp)
1.  [Upload the Watson Assitant chart to IBM Cloud Private](#install-110-add-wa-chart-to-icp)
1.  [Create persistent volumes](#install-110-create-pvs)
1.  [Install the service from the catalog](#install-110-admin-install)
1.  [Verify that the installation was successful](#install-110-verify)
1.  [Launch the tool](#install-110-launch-tool)

## Step 1: Purchase and download installation artifacts
{: #install-110-download-wa-icp}

1.  Purchase {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} from [Passport Advantage ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/software/passportadvantage/index.html){:new_window}.

1.  Download the appropriate package for your environment.

    {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} includes {{site.data.keyword.icpfull_notm}} Foundation version 3.1.0.

    If you have {{site.data.keyword.icpfull_notm}} version 3.1.0 set up in your environment already, you can download the archive for {{site.data.keyword.conversationshort}} alone.

    If you do not have {{site.data.keyword.icpfull_notm}} or have a later major version of {{site.data.keyword.icpfull_notm}} set up in your organization (4.x, for example), you must install version 3.1.0. To get the {{site.data.keyword.icpfull_notm}} 3.1.0 archive included, choose the e-assembly that includes {{site.data.keyword.icpfull_notm}}.

    If you have {{site.data.keyword.icpfull_notm}} 2.1.0.3 set up and you do not want to upgrade to using version 3.1.0, then install version 1.0.1 of {{site.data.keyword.conversationshort}} instead. Download the PPA file named `IWAICP_V1.0.1.tar.gz`.

The Passport Advantage archive (PPA) file for {{site.data.keyword.conversationshort}} contains a Helm chart and images. Helm is the Kubernetes package management system that is used for application management inside an {{site.data.keyword.icpfull_notm}} cluster.

## Step 2: Prepare the cloud environment
{: #install-110-install-icp}

You must have cluster administrator or team administrator access to the systems in your cluster.

1.  If you do not have {{site.data.keyword.icpfull_notm}} version 3.1.0 set up, install it. See [Installing a standard IBM Cloud Private environment ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/install_containers.html).
1.  If you have not done so, install the IBM Cloud Private command line interface and log in to your cluster. See [Installing the IBM Cloud Private CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_cluster/install_cli.html).
1.  Configure authentication from your computer to the Docker private image registry host and log in to the private registry. See [Configuring authentication for the Docker CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html).
1.  If you are not a root user, ensure that your account is part of the `docker` group. See [Post-installation steps ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.docker.com/engine/installation/linux/linux-postinstall/#manage-docker-as-a-non-root-user) in the Docker documentation.
1.  Ensure that you have a stable network connection between your computer and the cluster.
1.  Install the Kubernetes command line tool, kubectl, and configure access to your cluster. See [Accessing your cluster from the kubectl CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_cluster/cfc_cli.html).
1.  Set up the Helm command line interface.

    See [Setting up the Helm CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/create_helm_cli.html) for details.

    1.  Initialize the Helm command line interface.

        ```bash
        helm init --client-only
        ```
        {: pre}

        **Important**: Do not use the `--upgrade` flag with the init command. Adding the `--upgrade` flag replaces the server version of Helm Tiller that is installed with {{site.data.keyword.icpfull_notm}}.

    1.  You can confirm the version numbers by running the following command:

        ```bash
        helm version --tls
        ```
        {: pre}

        The response indicates version numbers similar to these:

        ```bash
        Client: &version.Version{SemVer:"v2.9.1", ... }
        Server: &version.Version{SemVer:"v2.9.1+icp", ... }
        ```
        {: pre}

1.  When you install {{site.data.keyword.conversationshort}}, the {{site.data.keyword.conversationshort}} tool is added to a file path on the proxy node of the cluster. Unlike with the previous version, the tool does not have its own subdomain. If you do not take action, users who open the tool from a web browser will see security warnings. To prevent users from seeing such warnings, add a known CA certificate for the proxy node.

    For more information about how to do so, see the *Replace the authentication certificate for the IBM Cloud Private management console* section of the [Create a new certificate authority (CA) ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/user_management/refresh_certs.html) topic.

## Step 3: Upload the Watson Assistant chart to IBM Cloud Private
{: #install-110-add-wa-chart-to-icp}

Add the {{site.data.keyword.conversationshort}} Helm chart to {{site.data.keyword.icpfull_notm}}.

1.  Ensure that you have enough disk space to load the images in the compressed files to your computer.

    You need 60 GB of space on your local system to support the extraction and loading of the archive file. To add more disk space, take one of the following actions:

    - Remove old Docker images.
    - Increase the amount of storage that the Docker daemon uses. To increase the amount of storage that the Docker daemon uses, see the entry for `dm.basesize` in the [dockerd Docker documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.docker.com/engine/reference/commandline/dockerd).

1.  If you have not, log in to your cluster from the {{site.data.keyword.icpfull_notm}} CLI and log in to the Docker private image registry.

    ```bash
    cloudctl login -a https://`{icp_url}`:8443 [--skip-ssl-validation]
    docker login `{icp_url}`:8500 -u `{cluster_username}`
    ```
    {: pre}

    The `{icp-url}` is the certificate authority (CA) domain. If you did not specify a CA domain, the default value is `mycluster.icp`. See [Specifying your own certificate authority (CA) for {{site.data.keyword.icpfull_notm}} services ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/create_ca_cert.html).

    The `skip-ssl-validate` parameter is needed if your cluster is not set up with a certificate that you trust, such as a self-signed certificate.

    The credentials that you must provide to log in to Docker are the same administrative credentials that you use to log into the {{site.data.keyword.icpfull_notm}} admin console. You will be prompted to provide the password that is associated with the user name that you specify in `u` parameter.

## Step 4: Create persistent volumes
{: #install-110-create-pvs}

A PersistentVolume (PV) is a unit of storage in the cluster. In the same way that a node is a cluster resource, a persistent volume is also a resource in the cluster.

For an overview, see [Persistent Volumes in the Kubernetes documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

When you install the service, persistent volume claims are created for the components automatically. However, because the preferred storage class for the service is **local-storage**, you must explicitly create persistent volumes before you install the service. Create one persistent volumes for each replica specified in the [system requirements](#install-110-sys-reqs) table earlier. See [Creating a PersistentVolume ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/create_volume.html) for the steps to take to create one.

**Note**: You must be a cluster administrator to create local storage volumes.

To create the volumes, you can define each volume configuration in a YAML file, and then use the Kubectl command line to push the configuration changes to the cluster. Use the [apply ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/#kubectl-apply) command in a command with the following syntax:

```bash
kubectl apply -f {pv-yaml-file-name}
```
{: pre}

1.  Create 13 YAML files, one for each volume.

    Specify the following settings in the YAML files.

    ```bash
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      finalizers:
      - kubernetes.io/pv-protection
      name: {name}
    spec:
      accessModes:
      - ReadWriteOnce
      capacity:
        storage: {size}
      hostPath:
        path: {path}
        type: ''
      persistentVolumeReclaimPolicy: Recycle
      storageClassName: local-storage
    ```
    {: codeblock}

    Replace the variables in this snippet with the appropriate value for the volume.

    - `{name}`: If you use a naming convention that includes the storage size information, it will be easier to recognize the volumes later. For example, you could use names like these:

      - For volumes 1 through 6 that have a size of 10Gi, use `pv-10g-n` where n starts at 1 and goes up to 6.
      - For volumes 7-10 that have a size of 5Gi, use `pv-5g-n` where n starts at 1 and goes up to 4.
      - For volumes 11-13 that have a size of 80Gi, use `pv-80g-n` where n starts at 1 and goes up to 3.

    - `{size}`: Reflect the size specified in the [resource requirements table](#install-110-storage-reqs).
    - `{path}`: Path on the worker node where you create the persistent volume. For example, `/mnt/local-storage/storage/{dir-name}`. For `{dir-name}`, use the same value that you use for `{name}` so you can map the volume name to its physical location.

1.  Run the `apply` command on each YAML file that you create.

    For example: `kubectl apply -f pv_001.yaml`. Rerun this command for each file up to `kubectl apply -f pv_013.yaml`.

    The result is 13 volumes with names like these: `pv-10g-1`, `pv-10g-2`, `pv-10g-3`, `pv-10g-4`, `pv-10g-5`, `pv-10g-6`, `pv-5g-1`, `pv-5g-2`, `pv-5g-3`, `pv-5g-4`, `pv-80g-1`, `pv-80g-2`, and `pv-80g-3`.

## Step 5: Install the service from the catalog
{: #install-110-admin-install}

1.  From the Kubernetes command line tool, create the namespace in which to deploy the service. Use the following command to create the namespace:

    ```bash
    kubectl create namespace {namespace-name}
    ```
    {:codeblock}

    For example:

    ```bash
    kubectl create namespace conversation
    ```
    {:codeblock}

    If you do not have the Kubernetes command line tool set up, complete the steps in [Prepare the cloud environment]install-110-install-icp).

1.  Get a certificate from your {{site.data.keyword.icpfull_notm}} cluster and install it to Docker or add the {cluster_CA_domain} as a Docker Daemon insecure registry. You must do one or the other for Docker to be able to pull from your {{site.data.keyword.icpfull_notm}} cluster.

    See [Specifying your own certificate authority (CA) for {{site.data.keyword.icpfull_notm}} services ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/create_ca_cert.html)

1.  To load the file from Passport Advantage into {{site.data.keyword.icpfull_notm}}, enter the following command in the {{site.data.keyword.icpfull_notm}} command line interface.

    ```bash
    cloudctl catalog load-archive --archive `{result_ppa}` --registry `{icp_url}:8500`
    ```
    {: pre}

    - `{result_ppa}` is the name of the file that you downloaded from Passport Advantage. For example, `ibm-watson-assistant-prod-1.1.0.tar.gz`.
    - `{icp_url}:8500` is the ICP cluster docker registry. `{icp_url}` is the IBM Cloud Private cluster domain.

It is loaded to the default local-charts repo.

1.  View the charts in the {{site.data.keyword.icpfull_notm}} Catalog. From the {{site.data.keyword.icpfull_notm}} management console navigation menu, click **Manage** > **Helm Repositories**.

1.  Click **Sync Repositories**.

    You must have the *cluster administrator* user type or access level to sync repositories.

1.  From the navigation menu, select **Catalog**.
1.  Scroll to find the **ibm-watson-assistant-prod** package, and then click the **Configuration** tab.
1.  Specify values for the installation details fields.

    - Helm release name.

      The release name must start with an alphbetic character, end with an alphanumeric character, and consist of lower case alphanumeric characters or a hyphen (-). For example *my-110-wa*.
      {: important}
    - Target namespace

1.  Click **License agreement**.

    Click **Next** multiple times to read the full agreement, and then click **OK** to close the license terms. If you have read and agree to the license agreement, select the checkbox.

1.  When you install the service, many configuration settings are applied to it for you unless you override them with your own values.

    At a minimum, provide your own values for the following configurable setting:

    - Deployment Type
    - Languages
    - Hostname of the ICP cluster Master node

    If you did not define a domain name for the master node of your private cloud instance, you are using the default hostname `mycluster.icp`, for example, then you must also specify values for these configuration settings:

    - IP address of the master node
    - Host name of the proxy node

1.  You might want to change additional configuration settings at this time. **You cannot change these settings after you complete the installation.**

    See [Configuration details](#install-110-config-details) for help understanding the configuration choices.

1.  Click **Install**.

You can check the Helm releases page to find out the status of the installation. See [Verify that the installation was successful](#install-110-verify).

#### Configuration details
{: #install-110-config-details}

Currently, the service does not support the ability to provide your own instances of resources, such as Postgres or MongoDB. There are configuration settings that suggest you can do so. However, do not change these settings from their default value of `true`.

Table 6. Configuration settings

| Setting | Description |
|---------|-------------|
| Helm release name | A unique ID for this deployment. The release name must start with an alphbetic character, end with an alphanumeric character, and consist of lower case alphanumeric characters or a hyphen (-). For example *my-110-wa*. When you install the service from the command line, you set this value by using the --name parameter. |
| Target namespace | Namespace in the cluster where {{site.data.keyword.conversationshort}} will be installed. This is the namespace you created earlier, and to which you uploaded the product archive file. |
| Deployment Type |  Options are **Development** and **Production**. Development is a Private cloud environment that you can use for testing purposes. It contains a single pod for each microservice. Production is a Private cloud environment that you can use to host applications and services used in production. Contains two replicas of each microservice pod. Development is the default. |
| Hostname of the ICP cluster Master node | Required. Specify the cluster_CA_domain hostname of the master node of your private cloud instance. This is the domain where you log in to the cluster. For example: `my.company.name.icp.net`. Specify the hostname only, without a protocol prefix (`https://`) and without a port number (`:8443`). This unique URL is typically referred to as the `{icp-url}` in this documentation. Corresponds to the `global.icp.masterHostname` value in the *values.yaml* file. |
| IP (v4) address of the master node | Required only if the hostname of the master node is not a DNS-resolvable name, such as `mycluster.icp`. Specify the IP address of the master node. Corresponds to the `global.icp.masterIP` value in the *values.yaml* file. |
| Hostname of the ICP cluster proxy node | Specify the hostname of the proxy node from which the ingress and services are accessed. To discover this value, run the command: `kubectl get nodes --show-labels`, and then find the node that shows `proxy=true`, and get the hostname value. It might be an IP address instead of a typical hostname. Corresponds to the `global.icp.proxyHostname` value in the *values.yaml* file. |
| Ingress path | Specifies how to access the Watson Assistant. You can access the service from `https://{{ global.icp.proxyHostname }}{{ global.icp.ingress.path }}`. If left empty, the default value `/{{ .Release.Name }}/assistant` is used for the ingress path. - The API is available from `https://{{ global.icp.proxyHostname }}{{ global.icp.ingress.path }}/api`. - The tool is available from `https://{{ global.icp.proxyHostname }}{{ global.icp.ingress.path }}/ui`). |
| Languages |  Specify the languages you want to support in addition to. English is required; do not deselect it. See [Language considerations](#install-110-lang-reqs) for more information. |
| Create COS | Boolean. Indicates whether you want a cloud object storage instance to be automatically created for you during the installation or you want to provide your own  instance. If `true`, a Minio cloud object store is created. The default value is true. **Do not set to `false`. The service does not currently support providing your own data store.**  |
| COS Bucket Prefix | Prefix of the bucket names to be used. `icp` is the default prefix. |
| COS Access Protocol | Only used if Create COS is deselected. Specifies the protocol used to connect to COS. Options are **http** and **https**. Typical protocol is **http**. |
| COS Hostname | Only used if Create COS is deselected. Hostname to connect to the store. Typical hostname when COS is running in the cluster is `{{ COS ServiceName }}.{{ namespace}}.svc.cluster.local`. |
| COS Port | Only used if Create COS is deselected. Port where COS is listening. Typical port is `443` and `9000`. |
| COS Secret Name | Name of the secret you created to hold the keys (`accesskey` and `secretkey`) for Minio. If not specified, the secret is created for you with randomly generated keys. |
| Create Redis | Boolean. Specify `true` to have a Redis store created for you. Specify `false` to provide your own instance. The default value is true. **Do not set to `false`. The service does not currently support providing your own data store.** |
| Redis Secret | If you want to specify a custom password for Redis, create a secret named `password` and specify the password value for it. If not specified, a secrets is created for you with a randomly generated password. |
| Redis Hostname | Used only if Create Redis is deselected. Specifies the hostname of the running Redis service. |
| Redis Port | Used only if Create Redis is deselected. Specifies the port from which the running Redis service can be accessed. The port is typically `6379`. |
| Create Postgres | Boolean. Specify `true` to have a Postrgres database created for you. Specify `false` to provide your own instance. The default value is true. **Do not set to `false`. The service does not currently support providing your own data store.** |
| Postgres Hostname | Used only if Create Postgres is deselected. Specifies the hostname of the running Postgres database server.  |
| Postgres Port | Used only if Create Postgres is deselected. Specifies the port from which the running Postgres database server can be accessed. The port is typically `5432`. |
| Postgres Admin Name | User ID for a Postgres super user with rights to create databases and users in the Postgres database. If you use your own instance, then you must provide the correct admin name and, in a secret, its password. |
| Postgres Admin Secret Name | Secret that contains the password associated with the user ID for a Postgres super user with rights to create databases and users in the Postgres database. If you use your own instance, then specify the name of the secret that contains your password. If not specified, a secret is created for you with a randomly generated password. |
| Postgres Admin Database | Name of the database to connect to. The name is typically `postgres`. |
| SSL Mode | SSL mode to use for the Postgres connection. Options include **verify-ca** and **verify-full**. Do not change from the default value of `verify-full`. |
| Postgres SSL Secret Name | Name of the secret that holds the certificate for the SSL connection to the Postgres server (key `tls.crt`). When you use the Postgres database that is installed for you, but want to use a custom certificate, then you must specify the private key (`tls.key`) also. |
| Create Database  | Boolean. Specify `true` to have the database and user created for you. If you specify `false`, then you must create the database and database user yourself. The default value is `true`. **Do not set to `false`. The service does not currently support providing your own database.** |
| Create Schema | Boolean. Specify `true` to have the required tables, functions, and so on applied to the database that is created for you. The default value is true. **Do not set to `false`. The service does not currently support providing your own schema.** |
| Store Database Name | Database name that the store microservice uses. If left empty, the default value `conversation-icp-{{ .Release.Name }}` is used. |
| Postgres User Name | User name that the store miroservice uses to connect to the Postgres database. If left empty, the default value `store-icp-{{ .Release.Name }}` is used. |
| Postgres Secret Name | Name of the secret that holds the `password` key of the postgres user for store microservice. If not specified, a secret is created for you with a randomly generated password value. |
| Create Etcd Cluster | Boolean. Specify `true` to have the etcd cluster for the Watson Assistant service created for you. Specify `false` to provide your own etcd instance and the associated credentials. The default value is true. **Do not set to `false`. The service does not currently support providing your own cluster.** |
| Etcd User | User ID used to access etcd. The default value is root. |
| Etcd Secret Name | Name of the secret that holds the `password` key for the Redis (super)user. If not specified, the secrets is created for you with a randomly generated password. |
| Connections details for etcd | Used only if Create Etcd Cluster is deselected. Etcd connection details. The format corresponds to the `--endpoints` parameter of the `etcdctl` tool. The syntax used is `schema://hostname:port` where `schema` is either `http` or `https` and `port` is typically `2379`. If multiple endpoints are provided, they are separated by commas (`,`)`. |
| Create MongoDB | Boolean. Specify `true` to have a MongoDB collection created for you. Specify `false` to provide your own instance. The default value is true. **Do not set to `false`. The service does not currently support providing your own database.** |
| MongoDB Hostname | Used only if Create MongoDB is deselected. Specifies the hostname of the running MongoDB collection. |
| MongoDB Port | Used only if Create MongoDB is deselected. Specifies the port from which the running MongoDB collection can be accessed. |
| Enable Authentication | Boolean. If true, authentication is enabled. The default value is true. |
| MongoDB Admin User | User ID for a MongoDB super user. If you use your own instance, then you must change this name and its associated password. |
| MongoDB Admin Password | Password associated with the user ID for a MongoDB super user. If you use your own instance, then you must change this password and the associated name. |
| Enable TLS | Boolean. Indicates whether to enable MongoDB TLS support. The default value is true. |
| TLS Secret Name | Name of the secret that holds the certificate (key `tls.crt`) and private key. |
| COS Configuration PVC Size | Specifies the size of the persistent volume claim to be used by the cloud object store. Default size is 5 Gi. |
| COS Configuration Storage Class | Specifies the persistent volume storage class. Default class is `local-storage`. |
| etcd Configuration PVC Size | Specifies the size of the persistent volume claim to be used by the etcd data store. Default size is 10 Gi. |
| etcd Configuration Storage Class | Specifies the persistent volume storage class. Default class is `local-storage`. |
| PostgreSQL Datatore Storage Class | Specifies the storage class of the persistent volume for the PostgreSQL database. Default class is `local-storage`. |
{: caption="Configuration settings" caption-side="top"}

## Step 5: Verify that the installation was successful
{: #install-110-verify}

To check the status of the installation process:

1.  Log in to the {{site.data.keyword.icpfull_notm}} management console.
1.  From the main menu, expand **Workloads**, and then choose **Helm releases**.
1.  Find the release name you used for the deployment in the list.

To run a test Helm chart:

1.  From the Helm command line interface, run the following command:

    ```bash
    helm test --tls {release name} --timeout 900
    ```
    {: pre}

1.  If one of the tests fails, review the logs to learn more. To see the log, use a command with the syntax `kubectl logs {podname} -n {namespace-name} -f --timestamps`. For example:

    ```bash
    kubectl logs my-release-bdd-test -n conversation -f --timestamps
    ```
    {: pre}

1.  To run the test script again, first delete the test pods by using a command with the syntax `kubectl delete pod {podname} --namespace {namespace-name}`. For example:

    ```bash
    kubectl delete pod my-release-bdd-test --namespace conversation
    ```
    {: pre}

    You must delete all of the `{podname}` pods, not just the one that failed, or the other tests will fail also.

### Uninstalling the service
{: #install-110-uninstall}

If you need to start the deployment over, be sure to remove all trace of the current installation before you try to install again.

1.  To uninstall and delete the `my-release` deployment, run the following command from the Helm command line interface:

    ```bash
    helm delete --tls --no-hooks --purge my-release
    ```
    {: pre}

    This command removes all records for the deployment so that the name can be used for a subsequent installation.

1.  The PersistentVolumeClaims will not be deleted and will remain bound to persistent volumes. You must remove them manually. See [Deleting a PersistentVolumeClaim ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/delete_app_volume.html) for details.

1.  Remove all content from any persistent volumes that you used for the previous deployment before you restart the installation. See [Deleting a PersistentVolume ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/delete_volume.html) for more information.

### Installing from the command line
{: #install-110-cli}

If you have trouble when you install from the catalog, you can install by using the command line interface instead.

**Be sure to check that the installation from the catalog did not complete.** Even if you see an error message, the installation might complete successfully.

To install from the command line, complete these steps:

1.  From the Kubernetes command line tool, create the namespace in which to deploy the service. Use the following command to create the namespace:

    ```bash
    kubectl create namespace {namespace-name}
    ```
    {:codeblock}

    For example:

    ```bash
    kubectl create namespace conversation
    ```
    {:codeblock}

    If you do not have the Kubernetes command line tool set up, see [Accessing your cluster from the kubectl CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_cluster/cfc_cli.html) for instructions.

1.  Get a certificate from your {{site.data.keyword.icpfull_notm}} cluster and install it to Docker or add the {cluster_CA_domain} as a Docker Daemon insecure registry. You must do one or the other for Docker to be able to pull from your {{site.data.keyword.icpfull_notm}} cluster.

    See [Specifying your own certificate authority (CA) for {{site.data.keyword.icpfull_notm}} services ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/create_ca_cert.html).

1.  To load the file from Passport Advantage into {{site.data.keyword.icpfull_notm}}, enter the following command in the {{site.data.keyword.icpfull_notm}} command line interface.

    ```bash
    cloudctl catalog load-archive  --registry {icp_url}:8500 --archive  ibm-watson-assistant.1.1.0.tar.gz  --repo local-charts
    ```
    {: pre}

1.  If you have a pre-existing version of the service on your cluster, remove its TGZ file. For example:

    ```bash
    rm ibm-watson-assistant-prod-1.0.1.tgz
    ```
    {: pre}

1.  Run this command to download the chart from the IBM Cloud Private repository:

    ```bash
    wget --no-check-certificate https://{cluster_CA_domain}:8443/helm-repo/requiredAssets/ibm-watson-assistant-prod-1.1.0.tgz
    ```
    {: pre}

1.  Extract the TAR file from the TGZ file, and then extract files from the TAR file by using the following command:

    ```bash
    tar -xvzf /path/to/ibm-watson-assistant-prod-1.1.0.tgz
    ```
    {: pre}

1.  Extract the TAR file from the TGZ file, and then extract files from the TAR file by using the following command:

    ```bash
    tar -xvzf /path/to/ibm-watson-assistant-prod-1.1.0.tgz
    ```
    {: pre}

1.  Edit values in the `values.yaml` file.

    To do so, first make a copy of the values.yaml. The `values.yaml` file is stored with the chart.  Rename the file. For example, `cp ibm-watson-assistant-prod/values.yaml my-override.yaml`.

    In your copy of the file, comment out or remove all but the configuration settings that you want to replace with your own values.

    Validate the Docker image repository values in the file with values that reflect your environment. Each image repository value must have the syntax `{icp_url}:8500/{namespace-name}/{unique-path-value}` where `{unique-path-value}` reflects the path from the existing YAML file, such as `icp-wa-tooling-pathed`.

    At a minimum, you must provide your own values for the following configurable settings also:

    - `global.deploymentType`: Specify whether you want to set up a development or production instance.
    - `global.icp.masterHostname`: Specify the hostname of the master node of your private cloud instance. Do not include the protocol prefix (`https://`) or port number (`:8443`).  For example: `my.company.name.icp.net`.
    - `global.icp.masterIP`: If you did not define a domain name for the master node of your private cloud instance, you are using the default hostname `mycluster.icp`, for example, then you must also specify this IP address.
    - `global.icp.proxyHostname`: Specify the hostname (or IP address) of the proxy node of your private cloud instance.

    **Attention**: Currently, the service does not support the ability to provide your own instances of resources, such as Postgres or MongoDB. The values YAML file has `{resource-name}.create` settings that suggest you can do so. However, do not change these settings from their default value of `true`.

1.  After you define any custom configuration settings, you can install the chart from the Helm command line interface. Enter the following command from the directory where the package was loaded in your local system:

    ```bash
    helm install --tls --values {override-file-name} --namespace {namespace-name} --name {my-release} ibm-watson-assistant-prod-1.1.0.tgz
    ```
    {: pre}

    - Replace `{my-release}` with a name for your release. The release name must start with an alphbetic character, end with an alphanumeric character, and consist of lower case alphanumeric characters or a hyphen (-). For example *my-110-wa*.
    - Replace `{override-file-name}` with the path to the file that contains the values that you want to override from the values.yaml file provided with the chart package. For example: `my-override.yaml`
    - Replace `{namespace-name}` with the name of the Kubernetes namespace that hosts the Docker pods.
    - The `ibm-watson-assistant-prod-1.1.0.tgz` parameter represents the name of the downloaded file that contains the Helm chart.

After the installation finishes, [verify](#install-110-verify) that it was successful.

## Step 6: Launch the tool
{: #install-110-launch-tool}

1.  Open a new tab in a web browser, and then enter a URL with the syntax `https://{global.icp.proxyHostname}{global.icp.ingress.path}/ui`. For example: `https://myproxy.icp/my-release/assistant/ui`.
1.  Log in using the same credentials you used to log into the {{site.data.keyword.icpfull_notm}} dashboard.

## Troubleshooting installation issues
{: #install-110-troubleshoot}

### Investigating issues
{: #install-110-ts-get-logs}

The first step to take if you hit an installation issue, such as a cluster node is not starting as expected, is to get logs from the cluster which can provide more detail.

To get log files, complete the following steps:

1.  Log into the cluster with administrator credentials.

1.  Run the following command to get a list of the jobs that are currently running in the cluster and whether the job was successful:

    ```bash
    kubectl get jobs
    ```
    {: pre}

1.  For any jobs that show a success status of 0, get the log file for the job by entering the following command:

    ```bash
    kubectl log {job-name} -f
    ```
    {: pre}

#### To check the configuration
{: #install-110-check-config}

If you want to check what configuration settings were applied to a deployment when it was set up, you can run the following command:

```bash
helm get <release-name> --tls
```
{: pre}

The user-provided configuration values are listed at the start of the information that is returned.

## Next steps
{: #install-110-next-steps}

Use the {{site.data.keyword.conversationshort}} tool to build training data and a dialog that can be used by your assistant.

- To learn more about the service first, read the [overview](/docs/services/assistant-icp/index.html).
- To see how it works for yourself, follow the steps in the [getting started tutorial](/docs/services/assistant-icp/getting-started.html).
