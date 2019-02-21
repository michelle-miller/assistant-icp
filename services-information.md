---

copyright:
  years: 2015, 2019
lastupdated: "2019-02-21"

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

# Service information
{: #services-information}

The assistant that you build is hosted by {{site.data.keyword.icpfull}}.
{: shortdesc}

## Limits by artifact
{: #limits}

For information about artifact limits per plan, see these topics:

- [Workspaces](/docs/services/assistant-icp/configure-workspace.html#workspace-limits)
- [Dialog nodes](/docs/services/assistant-icp/dialog-build.html#dialog-node-limits)
- [Intents](/docs/services/assistant-icp/intents.html#intent-limits)
- [Entities](/docs/services/assistant-icp/entities.html#entity-limits)

## Service API Versioning
{: shortdesc}

API requests require a version parameter that takes a date in the format `version=YYYY-MM-DD`. Whenever we change the API in a backwards-incompatible way, we release a new minor version of the API.

Send the version parameter with every API request. The service uses the API version for the date you specify, or the most recent version before that date. Don't default to the current date. Instead, specify a date that matches a version that is compatible with your app, and don't change it until your app is ready for a later version.

The current version is `2018-09-20`.

The "Try it out" pane in the {{site.data.keyword.conversationshort}} tooling is using version `2018-07-10`.

## Authenticating API calls on private cloud version 3.1.0
{: #authenticate-api-calls-310}

The authentication mechanism used by your service instance impacts how you must provide credentials when making an API call.

**Note**: The following instructions describe how to authenticate calls when using {{site.data.keyword.icpfull}} version 3.1.0. If you are using {{site.data.keyword.icpfull}} version 2.1.0.3, then see [Authenticating API calls on private cloud version 2.1.0.3](#authenticate-api-calls-2103) instead.

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

    - The base URL uses the syntax `https://{global.icp.proxyHostname}{global.icp.ingress.path}/api`. For example: `https://myproxy/myrelease/assistant/api`.
    - Provide the API key when you call the service. The following example shows an API key being used.

      ```curl
      curl -k -H "apikey:{API_KEY}" https://{icp_url}/assistant/api/v1/workspaces?version=2018-09-20
      ```
      {: codeblock}

    To get a workspace ID, go to the **Workspaces** tab of the tool, find the workspace you want to access programmatically, and then from the menu, choose **View details**.

See the [{{site.data.keyword.icpfull}} overview ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/getting_started/introduction.html){: new_window} for more information about {{site.data.keyword.icpfull_notm}}.

## Authenticating API calls on private cloud version 2.1.0.3
{: #authenticate-api-calls-2103}

The authentication mechanism used by your service instance impacts how you must provide credentials when making an API call.

**Note**: The following instructions describe how to authenticate calls when using {{site.data.keyword.icpfull}} version 2.1.0.3. If you are using {{site.data.keyword.icpfull}} version 3.1.0, then see [Authenticating API calls on private cloud version 3.1.0](#authenticate-api-calls-310) instead.

1.  Get the service credentials.

    1.  From a new tab in the web browser, go to a URL with the following syntax:

      `https://{icp-url}:8443/console/configuration/secrets`

    1.  Search for `-serviceid-secret`

        The search result includes a secret with a name that has the following syntax:

        `wcs-{release-name}-serviceid-secret`

    1.  Click the secret.
    1.  Click the unlock icon ![Unlock icon](images/locked-api-key.png) for `api_key`.
    1.  Copy the key.

1.  Use these credentials in your API call.

    - The base URL uses the syntax `http://{{ global.icp.proxyHostname }}/{{ ingress.config.backendService.ingressPath }}`

      where the default value for `ingress.config.backendService.ingressPath` is `/assistant/ap1`. For example, `https://mycluster.icp/assistant/api`.
    - Provide the API key when you call the service. The following example shows an API key being used.

      ```curl
      curl -X GET \
      -u "apikey:{api_key}"\
      'https://myicp.net/assistant/api/v1/workspaces?version=2018-09-20' \
      ```
      {: codeblock}

    To get a workspace ID, go to the **Workspaces** tab of the tool, find the workspace you want to access programmatically, and then from the menu, choose **View details**.

See the [{{site.data.keyword.icpfull}} overview ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/getting_started/introduction.html){: new_window} for more information about {{site.data.keyword.icpfull_notm}}.