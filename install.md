---

copyright:
  years: 2015, 2018
lastupdated: "2018-10-10"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
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

# Installation checklist
{: #install}

Learn how to install the {{site.data.keyword.conversationshort}} tool into {{site.data.keyword.icpfull}}.
{: shortdesc}

The {{site.data.keyword.icpfull_notm}} environment is a Kubernetes-based container platform that can help you quickly modernize and automate workloads that are associated with the applications and services you use. You can develop and deploy on your own infrastructure and in your data center which helps to mitigate risk and minimize vulnerabilities.

## Software requirements
{: #prereqs}

- {{site.data.keyword.icpfull_notm}} 2.1.0.3
- Kubernetes 1.10.0
- Helm 2.7.3
- Tiller (Helm server) 2.7.3

{{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} can run on Intel architecture nodes only.

## System requirements
{: #sys-reqs}

The following table lists the system resources that have been verified to support a deployment.

Table 1. Resource requirements

| Component | Number of replicas | Space per pod | Storage type |
|-----------|-----------------|--------------|
| Postgres  | 3 | 10 Gi | local-storage |
| etcd      | 3 | 10 Gi | local-storage |
| Minio     | 1 | 20 Gi | local-storage |
| MongoDB   | 3 | 80 Gi | local-storage |
{: caption="Resource requirements" caption-side="top"}

## Microservices

Microservices are individual components that together comprise the service. The {{site.data.keyword.conversationshort}} service consists of the following microservices:

- **Dialog**: Dialog runtime, or user-chat capability.
- **Store**: API endpoints.
- **CLU (Conversational Language Understanding)**: Interface for store to communicate with the back-end to initiate ML training.
- **Master**: Controls the lifecycle of underlying intent and entity models.
- **TAS**: Manages services model inferencing.
- **SLAD**: Manages service training capabilities.
- **SIREG** - Manages tokenization and system entity capabilities.
- **ed-mm**: Manages contextual entity capabilities.
- **Tooling**: Provides the developer user interface.

In addition to these microservices, the Helm chart installs the following resources:

- **PostgreSQL**: Stores training data.
- **MongoDB**: Stores word vectors.
- **Redis**: Caches data.
- **etcd**: Manages service registration and discovery.
- **Minio**: Stores CLU models.

### Language considerations

The components that are necessary to process different natural languages require significant amounts of data. You can install another instance of {{site.data.keyword.conversationshort}} to add support for another language. However, each language you add increases the amount of resources you need to support it. English is always provided. If you specify a different language during the installation process, you get resources that support English plus the specified language.

Table 2. Language resource requirements

| Language | Required Virtual Private CPUs | Memory requirements per pod |
|----------|-----------------------|-----------------------------|
| Base + English |              60 |                       40 GB |
| Each language you add |        1 | 10 GB (12 GB for Portuguese and Chinese) |
{: caption="Language resource requirements" caption-side="top"}

### Overview of the steps

1.  [Download service installation artifacts](#download-wa-icp)
1.  [Prepare the cloud environment](#install-icp)
1.  [Add the service chart to the cloud repository](#add-wa-chart-to-icp)
1.  [Install the service](#install-wa-from-catalog)
1.  [Launch the tool](#launch-tool)

## Step 1: Purchase and download installation artifacts
{: #download-wa-icp}

1.  Purchase {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} from [Passport Advantage ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/software/passportadvantage/index.html).

1.  Download the appropriate package for your environment.

    {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} includes {{site.data.keyword.icpfull_notm}} Foundation version 2.1.0.3.

    If you have {{site.data.keyword.icpfull_notm}} version 2.1.0.3 set up in your environment already, you can download the archive for {{site.data.keyword.conversationshort}} alone.

    If you do not have {{site.data.keyword.icpfull_notm}} or have a later major version of {{site.data.keyword.icpfull_notm}} set up in your organization (3.x, for example), you must install version 2.1.0.3. To get the {{site.data.keyword.icpfull_notm}} 2.1.0.3 archive included, choose the e-assembly that includes {{site.data.keyword.icpfull_notm}}.

The Passport Advantage archive (PPA) file for {{site.data.keyword.conversationshort}} contains a Helm chart and images. Helm is the Kubernetes native package management system that is used for application management inside an {{site.data.keyword.icpfull_notm}} cluster.

**Attention**: If you downloaded the PPA file between 26 September 2018 and 4 October 2018, then you have version 1.0.0.0 of the service. The installation process was simplified with the PPA file version 1.0.0.1 made available on 5 October 2018. For a simpler installation experience, download the later version of the PPA file, named `IWAICP_V1.0.0.1.tar.gz`.

## Step 2: Prepare the cloud environment
{: #install-icp}

1.  If you do not have {{site.data.keyword.icpfull_notm}} version 2.1.0.3 set up, install it.

1.  Go to [Installing bundled products ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/install_entitled_workloads.html).

    Complete any of the prerequisite steps that you need to complete before you load the chart. (Prerequisites only, and then return to this procedure.)

    For example, setting up the {{site.data.keyword.icpfull_notm}} command line interface, logging in to your cluster, configuring authentication from your computer to the Docker private image registry host, and logging in to the private registry.

## Step 3: Add the Helm chart to the cloud repository
{: #add-wa-chart-to-icp}

Add the {{site.data.keyword.conversationshort}} Helm chart to the {{site.data.keyword.icpfull_notm}} internal repository.

You need 30 GB of space on your local system to support the extraction and loading of the archive file.

1.  Complete steps 2 and 3 only in [Installing bundled products ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/install_entitled_workloads.html), and then return to this procedure.

    (Step 1 covers getting the archive file from the Passport Advantage site, which you have already done.)

## Step 4: Install the service
{: #install-wa-from-catalog}

- [4.1 Create persistent volumes](#create-pvs)
- [4.2 Set up a DNS subdomain for the tool](#create-subdomain)
- [4.3 Gather information about your environment](#gather-info)
- [4.4 Install the service](#admin-install)

### 4.1 Create persistent volumes
{: #create-pvs}

A PersistentVolume (PV) is a unit of storage in the cluster. In the same way that a node is a cluster resource, a persistent volume is also a resource in the cluster.

For an overview, see [Persistent Volumes in the Kubernetes documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

When you install the service, persistent volume claims are created for the components automatically. However, because the preferred storage class for the service is local-storage, you must explicitly create persistent volumes before you install the service. Create 10 persistent volumes, one to accommodate each replica specified in the [system requirements](#sys-reqs) table earlier. See [Creating a PersistentVolume ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/create_volume.html) for the steps to take to create one.

**Note**: You must be a cluster administrator to create local storage volumes.

Specify the following choices for {{site.data.keyword.conversationshort}}.

Table 3. Persistent volume settings

| Field | Value |
|-------|-------|
| Name | Specify a name that is unique across the 10 volumes |
| Type | Hostpath |
| Capacity | Check the system requirements table |
| Access mode | ReadWriteOnce (RWO) |
| Reclaim policy | Recycle |
| Path | Specify a directory location on a worker node of the cluster where there is enough space for data. The directory must be unique across the 10 volumes. For example, `/mnt/local-storage/storage/pv_10gi-postgres1` |
{: caption="Persistent volume settings" caption-side="top"}

### 4.2 Create a subdomain for the tool user interface
{: #create-subdomain}

Work with your DNS provider to create a subdomain on your cluster named `assistant` that can be used by the {{site.data.keyword.conversationshort}} tool user interface.

For example, if you are using SoftLayer, log in to the SoftLayer portal, and go to Network > DNS > Forward Zones. In the DNS Forwarding Zone for your {{site.data.keyword.icpfull_notm}} cluster, add a new record with the host name 'assistant' that points to the IP address of your {{site.data.keyword.icpfull_notm}} cluster.

You must be able to ping `assistant.{icp-url}` and get a reply.

### 4.3 Gather information about your environment
{: #gather-info}

When you install the service, many configuration settings are applied to it for you unless you override them with your own values. You might want to change things like user names and passwords for databases or stores that are created for you, for example. **You cannot change these settings after you complete the installation.**

Other actions you might want to take before starting the installation include:

- **Generate a MongoDB TLS certificate**: If you create your own certificate authority, you can replace the default values for the CA with details for your own certificate and key.

  1.  Generate your own TLS certificate authority for MongoDB.

      `$ openssl genrsa -out ca.key 2048`

      `$ openssl req -x509 -new -nodes -key ca.key -days 10000 -out ca.crt -subj "/CN=mydomain.com"`

  1.  Encode it in base64 format.

      `$ cat ca.key | base64 -w0`

  1.  Update the base64 encoded certificate and base64 encoded key configuration settings (or override the configuraton values `global.mongodb.tls.cacert` and `global.mongodb.tls.cakey`) with the new certificate and key values.

- **Create a TLS secret for the tool**: To prevent users from seeing browser security warnings when using the tooling, you can install a certificate.

  Before you install, obtain a certificate for the tooling subdomain `assistant.{icp-url}`. The certificate should be signed by a trusted certificate authority. Because assistant tooling uses a subdomain the certificate must either be specifically for that subdomain, or a wildcard certificate for your {{site.data.keyword.icpfull_notm}} domain.
  
  After you get the certificate and private key, create a secret with keys named `tls.crt` and `tls.key` that contain the certificate and private key. See [TLS in the Kubernetes documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) for information about how to create the secret.
  
  After you create the secret, update the TLS Secret configuration setting (or override the configuraton value `ui.ingress.tlsSecret`) with the name of that secret. Ingress will use that certificate when users access the tooling.

### 4.4 Install the service
{: #admin-install}

1.  From the Kubernetes command line tool, create the `conversation` namespace by using the following command:

    ```bash
    kubectl create namespace conversation
    ```
    {:codeblock}

    If you do not have the Kubernetes command line tool set up, see [Accessing your IBM Cloud Private cluster by using the kubectl CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/cfc_cli.html) for instructions.

1.  Get a certificate from your {{site.data.keyword.icpfull_notm}} cluster and install it to Docker or add the {cluster_CA_domain} as a Docker Daemon insecure registry. You must do one or the other for Docker to be able to pull from your {{site.data.keyword.icpfull_notm}} cluster.

    See [Specifying your own certificate authority (CA) for IBM Cloud Private services ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/create_ca_cert.html)

1.  To load the file from Passport Advantage into {{site.data.keyword.icpfull_notm}}, enter the following command in the {{site.data.keyword.icpfull_notm}} command line interface.

    ```bash
    bx pr load-ppa-archive --archive {compressed_file_name} 
    --clustername {cluster_CA_domain} --namespace conversation
    ```
    {: codeblock}

    - `{compressed_file_name}` is the name of the file that you downloaded from Passport Advantage.
    - `{cluster_CA_domain}` is the {{site.data.keyword.icpfull_notm}} cluster domain, often referred to in this documentation as the {icp-url}.
    - `namespace` is the Docker namespace that hosts the Docker image and must be set to `conversation`.

1.  View the charts in the {{site.data.keyword.icpfull_notm}} Catalog. From the {{site.data.keyword.icpfull_notm}} management console navigation menu, click **Manage** > **Helm Repositories**.
1.  Click **Sync Repositories**.

    You must have the *cluster administrator* user type or access level to sync repositories.

1.  From the navigation menu, select **Catalog**.
1.  Scroll to find the **ibm-watson-assistant-prod** package, and then click **Configure**.
1.  Specify values for the configurable fields.

    When you install the service, many configuration settings are applied to it for you unless you override them with your own values. You might want to change things like user names and passwords for databases or stores that are created for you, for example. **You cannot change these settings after you complete the installation.**

    See [Configuration details](#config-details) for help understanding the configuration choices. At a minimum, you must provide your own values for the following configurable settings:

    - Release name
    - Deployment Type
    - ICP Cluster URL
    - Languages: If you do not need to support Czech, deselect it.

1.  Click **license agreements**.

    Click **Next** multiple times to read the full agreement, and then click **Accept** to accept the license terms.

1.  Click **Install**.

    **Attention**: You might get a message that a timeout occurred during the installation process. However, the message can be ignored; the installation continues in the background. Give it time to complete. Check the Helm releases page for the status.

#### Configuration details
{: #config-details}

Currently, the service does not support the ability to provide your own instances of resources, such as Postgres or MongoDB. There are configuration settings that suggest you can do so. However, do not change these settings from their default value of `true`.

Table 4. Configuration settings

| Setting | Description |
|---------|-------------|
| Release name | A unique ID for this deployment. When you install the service from the command line, you set this value by using the --name parameter. |
| Target namespace | Namespace used within the cluster to identify this service. You must choose `conversation` as the namespace. |
| Deployment Type |  Options are **Development** and **Production**. Development is a Private cloud environment that you can use for testing purposes. It contains a single pod for each microservice. Production is a Private cloud environment that you can use to host applications and services used in production. Contains two replicas of each microservice pod. Development is the default. |
| ICP Cluster URL | Specify the cluster_CA_domain hostname of your private cloud instance. For example: `my.company.name.icp.net`. Specify the hostname only, without a protocol prefix (`https://`) and without a port number (`:8443`). This unique URL is typically referred to as `{icp-url}` in this documentation. |
| Languages |  Specify the languages you want to support in addition to. English is required; do not deselect it. For more information about language options, see [Supported languages](lang-support.html). |
| Create COS | Boolean. Indicates whether you want to provide your own cloud object store or have one created for you. If `true`, a Minio cloud object store is created. The default value is true. **Do not set to `false`. The service does not currently support providing your own store.**  |
| COS Access Key | Credential to access the store. |
| COS Secret Key | Access key to the store used by CLU components. |
| Bucket Prefix | Prefix of the bucket names to be used. |
| COS Access Protocol | Only used if Create COS is deselected. Specifies the protocol used to connect to COS. Options are **http** and **https**. Typical protocol is **http**. |
| COS Hostname | Only used if Create COS is deselected. Hostname to connect to the store. Typical hostname when COS is running in the cluster is `cos.namespace.svc.cluster.local`. |
| COS Port | Only used if Create COS is deselected. Port where COS is listening. Typical port is `443`. |
| Create Redis | Boolean. Specify `true` to have a Redis store created for you. Specify `false` to provide your own instance. The default value is true. **Do not set to `false`. The service does not currently support providing your own store.** |
| Redis Hostname | Used only if Create Redis is deselected. Specifies the hostname of the running Redis service. |
| Redis Password | Password for accessing Redis. (User is root.) |
| Redis Port | Used only if Create Redis is deselected. Specifies the port from which the running Redis service can be accessed. |
| Create Postgres | Boolean. Specify `true` to have a Postrgres store created for you. Specify `false` to provide your own instance. The default value is true. **Do not set to `false`. The service does not currently support providing your own store.** |
| Postgres Hostname | Used only if Create Postgres is deselected. Specifies the hostname of the running Postrgres service.  |
| Postgres Port | Used only if Create Postgres is deselected. Specifies the port from which the running Postrgres service can be accessed. |
| Postgres Admin Name | User ID for a Postgres super user with rights to create databases and users in the Postgres database. If you use your own instance, then you must change this name and its associated password. |
| Postgres Admin Password | Password associated with the user ID for a Postgres super user with rights to create databases and users in the Postgres database. If you use your own instance, then you must change this password and the associated name. |
| Postgres Admin Database | Name of the database to connect to. |
| SSL Mode | SSL mode to use for the Postgres connection. Options such as **verify-ca** or **verify-full** are not currently supported by the store microservice. Currently, only **SSL** is supported. Do not change from the default value. The default value is `allow`. |
| Postgres Server Certificate | Used only if Create Postgres is deselected. Server certificate details. |
| Create Database  | Boolean. Specify `true` to have the database and user created for you. If you specify `false`, then you must create the database and database user yourself. The default value is true. **Do not set to `false`. The service does not currently support providing your own database.** |
| Create Schema | Boolean. Specify `true` to have the required tables, functions, and so on applied to the database that is created for you. The default value is true. **Do not set to `false`. The service does not currently support providing your own schema.** |
| Store Database Name | Database name that the store microservice uses. If left empty, the default value  "conversation_icp_{{ .Release.Name }}" is used. |
| Postgres User Name | User name that the store miroservice uses to connect to the Postgres database. If left empty, the default value "store_icp_{{ .Release.Name }}" is used. |
| Postgres User Password | Password associated with the user ID specified in Postgres User Name.|
| Create Etcd Cluster | Boolean. Specify `true` to have the etcd cluster for the Watson Assistant service created for you. Specify `false` to provide your own etcd instance and the associated credentials. The default value is true. **Do not set to `false`. The service does not currently support providing your own cluster.** |
| Enable Etcd Authentication | Boolean. If set to `true` the authentication is enabled in etcd. Watson Assistant requires authentication. Set to `false` only if you provide your own etcd cluster where authentication is already enabled. The default value is true. |
| Etcd User | User ID used to access etcd. The default value is root. |
| Etcd Password | Password associated with the Etcd User.  |
| Connections details for etcd | Used only if Create Etcd Cluster is deselected. Etcd connection details. If you do not specify a connection, these default connection details are used: shema:http, host:etcd-ibm-wcd-etcd.default.svc.cluster.local, port:2379. No connection details are specified by default. |
| Create MongoDB | Boolean. Specify `true` to have a MongoDB database created for you. Specify `false` to provide your own instance. The default value is true. **Do not set to `false`. The service does not currently support providing your own database.** |
| MongoDB Hostname | Used only if Create MongoDB is deselected. Specifies the hostname of the running MongoDB database service. |
| MongoDB Port | Used only if Create MongoDB is deselected. Specifies the port from which the running MongoDB database service can be accessed. |
| Enable Authentication | Boolean. If true, authentication is enabled. The default value is true. |
| MongoDB Admin User | User ID for a MongoDB super user with rights to create databases and users in the MongoDB database. If you use your own instance, then you must change this name and its associated password. |
| MongoDB Admin Password | Password associated with the user ID for a MongoDB super user with rights to create databases and users in the MongoDB database. If you use your own instance, then you must change this password and the  associated name. |
| Enable TLS | Boolean. Indicates whether to enable MongoDB TLS support. The default value is true. |
| base64 encoded certificate | Replace the certificate with your own. See the Configuration page for certificate details. |
| base64 encoded key | Replace the key with your own. See the Configuration page for key details. |
| Subdomain | URL from which to access the Watson Assistant tool. The url syntax is typically `https://{{ subdomain }}.{{ icp-url }}/{{ applicationContext }}`. The default value is assistant. |
| TLS Secret | Name of the secret that has the certificate and private key for the subdomain `{{ subdomain }}.{{ icp-url }}`. If empty, a secret with a self-signed certificate is created. By default, this setting is empty. |
{: caption="Configuration settings" caption-side="top"}

### Uninstalling the service

If you need to start the deployment over, be sure to remove all content from any persistent volumes that you used for the previous deployment before you restart the installation. See [Deleting a PersistentVolume ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/delete_volume.html) for more information.

The PersistentVolumeClaims will not be deleted and will remain bound to persistent volumes. You must remove them manually. See [Deleting a PersistentVolumeClaim ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/delete_app_volume.html) for details.

To uninstall and delete the `my-release` deployment, run the following command from the Helm CLI:

```bash
$ helm delete --tls my-release
```
{: codeblock}

To irrevocably uninstall and delete the `my-release` deployment, run the following command:

```bash
$ helm delete --purge --tls my-release
```
{: codeblock}

If you omit the `--purge` option, Helm deletes all resources for the deployment but retains the record with the release name. This allows you to roll back the deletion. If you include the `--purge` option, Helm removes all records for the deployment so that the name can be used for another installation.

## Step 5: Launch the tool
{: #launch-tool}

1.  Log in to the {{site.data.keyword.icpfull_notm}} management console.
1.  From the main menu, expand **Workloads**, and then choose **Deployments**.
1.  Find the deployment named `{release-name}-ui`.

    If you don't know the `{release-name}`, filter the list of deployments to include only those associated with the `conversation` namespace, and then search on `-ui` to find it.

1.  Click **Launch**.

    A new web browser tab opens and shows the {{site.data.keyword.conversationshort}} tool login page. For example: `https://assistant.{icp-url}`.
1.  Log in using the same credentials you used to log into the {{site.data.keyword.icpfull_notm}} dashboard.

## Next steps
{: #next-steps}

Use the {{site.data.keyword.conversationshort}} tool to build training data and a dialog that can be used by your assistant.

- To learn more about the service first, read the [overview](index.html).
- To see how it works for yourself, follow the steps in the [gettings started tutorial](getting-started.html).
