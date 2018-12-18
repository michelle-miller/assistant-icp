---

copyright:
  years: 2015, 2018
lastupdated: "2018-12-13"

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

# Service information
{: #services-information}

The assistant that you build is hosted by {{site.data.keyword.icpfull}}.
{: shortdesc}

## Limits by artifact
{: #limits}

For information about artifact limits per plan, see these topics:

- [Workspaces](configure-workspace.html#workspace-limits)
- [Dialog nodes](dialog-build.html#dialog-node-limits)
- [Intents](intents.html#intent-limits)
- [Entities](entities.html#entity-limits)

## Service API Versioning
{: shortdesc}

API requests require a version parameter that takes a date in the format `version=YYYY-MM-DD`. Whenever we change the API in a backwards-incompatible way, we release a new minor version of the API.

Send the version parameter with every API request. The service uses the API version for the date you specify, or the most recent version before that date. Don't default to the current date. Instead, specify a date that matches a version that is compatible with your app, and don't change it until your app is ready for a later version.

The current version is `2018-09-20`.

The "Try it out" pane in the {{site.data.keyword.conversationshort}} tooling is using version `2018-07-10`.

## Authenticating API calls
{: #authenticate-api-calls}

The authentication mechanism used by your service instance impacts how you must provide credentials when making an API call.

1.  Get the service credentials by using the Kubernetes command line interface.

    1.  You should have already installed the Kubernetes CLI (kubectl(), and configured access to your cluster. If not, see [Accessing your cluster from the kubectl CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_cluster/cfc_cli.html).

    1.  Log in to IBM Cloud Private.

        ```bash
        cloudctl login -a https://{icp_url}:8443
        ```

    1.  To get the API key for the secret, run the following command:

        ```bash
        kubectl -n [namespace-name} get secret wcs-{release-name}-serviceid-secret -o go-template='{{ index .data "api_key" | base64decode }}'
        ```

        The key is returned. For example: `icp-CXTvuAA2QwXZbETadG3zIpvqmi3djUmGBBBzV4803C6D`.
    1.  Copy the key.

1.  Use these credentials in your API call.

    - The base URL uses the syntax `{icp-url}/assistant/api`. (Yours might have a different syntax if you changed the Ingress path for your deployment during the installation.)
    - Provide the API key when you call the service. The following example shows an API key being used.

      ```curl
      curl -k -H "api-key:{API_KEY}" https://{icp_url}/assistant/api/v1/workspaces?version=2018-09-20
      ```
      {: codeblock}

    To get a workspace ID, go to the **Workspaces** tab of the tool, find the workspace you want to access programmatically, and then from the menu, choose **View details**.
