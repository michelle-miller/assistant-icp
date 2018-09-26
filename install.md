---

copyright:
  years: 2015, 2018
lastupdated: "2018-09-26"

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
| Postgres  | 3 | 10 GB | local-storage |
| etcd      | 3 | 10 GB | local-storage |
| Minio     | 1 | 20 GB | local-storage |
| MongoDB   | 3 | 80 GB | local-storage |
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

- [ ] [Download service installation artifacts](#download-wa-icp)
- [ ] [Prepare the cloud environment](#install-icp)
- [ ] [Add the service chart to the cloud repository](#add-wa-chart-to-icp)
- [ ] [Install the service](#install-wa-from-catalog)
- [ ] [Launch the tool](#launch-tool)

## Step 1: Purchase and download installation artifacts
{: #download-wa-icp}

1.  Purchase {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} from from [Passport Advantage ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/software/passportadvantage/index.html).

1.  Download the appropriate package for your environment.

    {{site.data.keyword.conversationshort}} for {{site.data.keyword.icpfull_notm}} includes {{site.data.keyword.icpfull_notm}} Foundation version 2.1.0.3.

    If you have {{site.data.keyword.icpfull_notm}} version 2.1.0.3 set up in your environment already, you can download the archive for {{site.data.keyword.conversationshort}} alone.

    If you do not have {{site.data.keyword.icpfull_notm}} or have a later major version of {{site.data.keyword.icpfull_notm}} set up in your organization (3.x, for example), you must install version 2.1.0.3. To get the {{site.data.keyword.icpfull_notm}} 2.1.0.3 archive included, choose the e-assembly that includes {{site.data.keyword.icpfull_notm}}.

The Passport Advantage archive (PPA) file for {{site.data.keyword.conversationshort}} contains a Helm chart and images. Helm is the Kubernetes native package management system that is used for application management inside an {{site.data.keyword.icpfull_notm}} cluster.

## Step 2: Prepare the cloud environment
{: #install-icp}

1.  If you do not have {{site.data.keyword.icpfull_notm}} version 2.1.0.3 set up, install it.

1.  Go to [Installing bundled products ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/install_entitled_workloads.html).

    Complete any of the prerequisite steps that you need to complete before you load the chart. (Prerequisites only, and then return to this procedure.)

    For example, setting up the {{site.data.keyword.icpfull_notm}} command line interface, logging in to your cluster, configuring authentication from your computer to the Docker private image registry host, and logging in to the private registry.

## Step 3: Add the Helm chart to the cloud repository
{: #add-wa-chart-to-icp}

Add the {{site.data.keyword.conversationshort}} Helm chart to the {{site.data.keyword.icpfull_notm}} internal repository.

You need 30GB of space on your local system to support the extraction and load of the archive file.

1.  Complete steps 2 and 3 in [Installing bundled products ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/install_entitled_workloads.html).

    (Step 1 covers getting the archive file from the Passport Advantage site, which you have already done.)

## Step 4: Install the service
{: #install-wa-from-catalog}

- [Create persistent volumes](#create-pvs)
- [Gather information about your environment](#gather-info)
- [Install the service](#admin-install)

### Create persistent volumes
{: #create-pvs}

A PersistentVolume (PV) is a unit of storage in the cluster. In the same way that a node is a cluster resource, a persistent volume is also a resource in the cluster. Ensure that you have enough persistent volumes to accommodate the [system requirements](#sys-reqs) outlined earlier.

See [Persistent Volumes in the Kubernetes documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) for more information.

### Gather information about your environment
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

  Before you install, obtain a certificate for the tooling subdomain `assistant.{icp-url}`. The certificate should be signed by a trusted certificate authority. Because assistant tooling uses a subdomain the certificate must either be specifically for that subdomain, or a wildcard certificate for your ICP domain.
  
  After you get the certificate and private key, create a secret with keys named `tls.crt` and `tls.key` that contain the certificate and private key. See [TLS in the Kubernetes documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) for information about how to create the secret.
  
  After you create the secret, update the TLS Secret configuration setting (or override the configuraton value `ui.ingress.tlsSecret`) with the name of that secret. Ingress will use that certificate when users access the tooling.

### Install the service
{: #admin-install}

1.  From the Kubernetes command line tool, create the `conversation` namespace by using the following command:

    ```bash
    kubectl create namespace conversation
    ```
    {:codeblock}

    If you do not have the Kubernetes command line tool set up, see [Accessing your IBM Cloud Private cluster by using the kubectl CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/cfc_cli.html) for instructions.

1.  Get a certificate from your ICP cluster and install it to Docker or add the {cluster_CA_domain} as a Docker Daemon insecure registry. You must do one or the other for Docker to be able to pull from your ICP cluster.

    See [Specifying your own certificate authority (CA) for IBM Cloud Private services ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/create_ca_cert.html)

1.  Follow the steps for **Solution 2** in [Troubleshooting instructions ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SS8G7U_18.2.0/com.ibm.app.mgmt.doc/content/trouble_docker_login_time_ppa_load.htm).

    The solution basically helps you to load the images from the chart one at a time.

1.  After completing the Solution 2 steps, specify values for the Docker image registries. (The image registry settings are included in the values.yaml file but are not described in the README file.)

    When you use the load command that is provided in Solution 2, it does not dynamically update the Docker registry You must specify the image registry information yourself.

    See [Image registry details](#registry) for the steps to perform to provide the required registry information.

1.  To specify all of your custom configuration values at once, you can define your own YAML file and use the Helm command line tool to install and pull values from your file.

    Make a copy of the `values.yaml` file that is provided in the PPA archive. The archive file contains a TAR file that contains the Helm chart. The `values.yaml` file is stored with the chart.

    Replace the values in your version of the file with values that reflect your environment. The README has descriptions for each configurable setting.

    At a minimum, you must provide your own values for the following configurable settings:

    - `global.icpUrl`

    If this is the only settings that you want to replace, then you can pass the value for it in the command line with the following parameter instead of providing your own YAML file: `--global.icpUrl {your ICP url}`

1.  After you define any custom configuration settings and specify your Docker image registry details, you can install the chart from the Helm command line interface. Enter the following command:

    ```bash
    helm install --tls --values {override-file-name} --namespace conversation \
    --name {my-release} -f {my-values.yaml} ibm-watson-assistant-prod
    ```
    {: codeblock}

    - Replace `{my-release}` with a name for your release.
    - Replace `{my-values.yaml}` with the path to a YAML file that specifies the values that are to be used with the `install` command. (Specify the YAML file you created in the previous step here.)
    - Replace `{override-file-name}` with the name of the file that contains your Docker image registry details.
    - Note that the namespace `conversation` is used. Do not change it.

#### Image registry details
{: #registry}

To provide image registry details yourself, complete the following steps.

1.  Create an override file. For example, `image-details-override.yaml`.
1.  Copy the JSON block below into your override file.
1.  Replace any references to `{icp-url}:{port}` with the appropriate value for your environment. For example, `my.icp.net:8500`.

```json
#Images for ibm-watson-assistant-ui chart
ui:
  image:
    repository: "{icp-url}:{port}/conversation/icp-wa-tooling-pathed"
  imageOauth:
    repository: "{icp-url}:{port}/conversation/conan-tools"

cos:
  minio:
    image:
      repository: "{icp-url}:{port}/conversation/minio"
    mcImage:
      repository: "{icp-url}:{port}/conversation/mc"


etcd:
  config:
    image:
      repository: "{icp-url}:{port}/conversation/etcd"

postgres:
  config:
    #Images for ibm-watson-assistant-datastores-store-postgres chart 
    image: "{icp-url}:{port}/conversation/stolon"
  image:
    #Images for ibm-watson-assistant-store_db_schema chart 
    storeRepository: "{icp-url}:{port}/conversation/store"
    #Images for ibm-watson-assistant-create_slot_store_db chart
    repository: "{icp-url}:{port}/conversation/postgresql"

ingress:
  config:
    #Images for ibm-watson-assistant-ingress chart 
    # name of the auth service that we reference in `ingress.yaml`
    authService:
      # specify image if install is set to `true`
      images:
        repository: "{icp-url}:{port}/conversation/icp-auth"
  
    provisionPod:
      images: 
        repository: "{icp-url}:{port}/conversation/icp-provision"

  storeReadyCheck:
    image:
      repository: "{icp-url}:{port}/conversation/conan-tools"

bdd:
  images:
    repository: "{icp-url}:{port}/conversation/dvt-bdd"
 
redis:
  #Images for ibm-watson-assistant-datastores-redis chart
  config:
    image:
      repository: "{icp-url}:{port}/conversation/redis-ha"

slot:
  #Images for ibm-watson-assistant-datastores-etcd-authentication & ibm-watson-assistant-create_slot charts
  image:
    repository: "{icp-url}:{port}/conversation/conan-tools"

mongodb:
  #Images for ibm-watson-assistant-datastores-mongodb chart
  config:
    image:
      repository: "{icp-url}:{port}/conversation/mongo"
      busyboxRepository: "{icp-url}:{port}/conversation/busybox"
    imageTest:
      testFramework:
        repository: "{icp-url}:{port}/conversation/bats"
    installImage:
      repository: "{icp-url}:{port}/conversation/mongodb-install"

mongodbloadclu:
  #Images for ibm-watson-assistant-datastores-mongodb-load-clu
  image:
    repository: "{icp-url}:{port}/conversation/icp-load-wa-word-vectors"
    
tas:
  #Images for ibm-watson-assistant-tas chart
  image:
    repository: "{icp-url}:{port}/conversation/slad-tas-runtime"
    mongoloaderRepository: "{icp-url}:{port}/conversation/improve-recommendations-mongo-loader"

master:
  #Images for ibm-watson-assistant-master chart
  image:
    repository: "{icp-url}:{port}/conversation/master"
    mongoloaderRepository: "{icp-url}:{port}/conversation/improve-recommendations-mongo-loader"

nlu:
  #Images for ibm-watson-assistant-nlu chart
  image:
    repository: "{icp-url}:{port}/conversation/nlu"

environment:
  #Images for ibm-watson-assistant-environment chart
  image:
    repository: "{icp-url}:{port}/conversation/conan-tools"

ed:
  #Images for ibm-watson-assistant-ed chart
  image:
    runtimeRepository: "{icp-url}:{port}/conversation/model-mesh"
    mmRepository:      "{icp-url}:{port}/conversation/entities"
    py4jRepository:    "{icp-url}:{port}/conversation/objectstore-py4j-bridge"

sireg:
  #Images for ibm-watson-assistant-sireg chart
  image:
    repository:      "{icp-url}:{port}/conversation/sireg"
    modelRepository: "{icp-url}:{port}/conversation/sireg-model"

dialog:
  #Images for ibm-watson-assistant-dialog chart
  image:
    repository: "{icp-url}:{port}/conversation/dialog"

store:
  #Images for ibm-watson-assistant-store chart
  image:
    repository: "{icp-url}:{port}/conversation/store"

    # Store init images
    init:
      schemaCheck:
        repository: "{icp-url}:{port}/conversation/postgresql"
      cluStarted:
        repository: "{icp-url}:{port}/conversation/conan-tools"

image:
  #Images for ibm-watson-assistant-prod chart
minioRepository: "{icp-url}:{port}/conversation/minio-mc"
```
{: codeblock}

### Uninstalling the service

If you need to start the deployment over, be sure to remove all content from any persistent volumes that you used for the previous deployment before you restart the installation. The PersistentVolumeClaims will not be deleted and will remain bounded to Persistent Volumes. You must remove them manually.

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
1.  Find the deployment named `watson-assistant-ui`, and then click **Launch**.

    A new web browser tab opens and shows the {{site.data.keyword.conversationshort}} tool login page. For example: `https://assistant.{icp-url}`.
1.  Log in using the same credentials you used to log into the {{site.data.keyword.icpfull_notm}} dashboard.

## Next steps
{: #next-steps}

Use the {{site.data.keyword.conversationshort}} tool to build training data and a dialog that can be used by your assistant.

- To learn more about the service first, read the [overview](index.html).
- To see how it works for yourself, follow the steps in the [gettings started tutorial](getting-started.html).
