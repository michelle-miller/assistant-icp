---

copyright:
  years: 2015, 2018
lastupdated: "2018-10-19"

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

    - The base URL uses the syntax `http://{{ global.icp.proxyHostname }}/{{ ingres.config.backendService.ingressPath }}`

      where the default value for `ingres.config.backednService.ingressPath` is `/assistant/ap1`. For example,   `https://mycluster.icp/assistant/api`.
    - Provide the API key when you call the service. The following example shows an API key being used.

      ```curl
      curl -X GET \
      -u "apikey:{api_key}"\
      'https://myicp.net/assistant/api/v1/workspaces?version=2018-09-20' \
      ```
      {: codeblock}

    To get a workspace ID, go to the **Workspaces** tab of the tool, find the workspace you want to access programmatically, and then from the menu, choose **View details**.

See [Platform overview ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/getting_started/introduction.html){: new_window} for more information about {{site.data.keyword.icpfull_notm}}.
