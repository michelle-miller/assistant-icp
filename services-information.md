---

copyright:
  years: 2015, 2018
lastupdated: "2018-09-25"

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

### Limits by artifact
{: #limits}

For information about artifact limits per plan, see these topics:

- [Workspaces](configure-workspace.html#workspace-limits)
- [Dialog nodes](dialog-build.html#dialog-node-limits)
- [Intents](intents.html#intent-limits)
- [Entities](entities.html#entity-limits)

## Authenticating API calls
{: #authenticate-api-calls}

The authentication mechanism used by your service instance impacts how you must provide credentials when making an API call.

1.  Get the service credentials.

    1.  From a new tab in the web browser, go to a URL with the following syntax:

      `https://{icp-url}:8443/console/configuration/secrets`

      **Note**: The `{icp-url}` is the segment that comes after `assistant.` in the URL for the tool.

    1.  Search for `-serviceid-secret`

        The search result includes a secret with a name that has the following syntax:

        `wcs-{release-name}-serviceid-secret`

    1.  Click the secret.
    1.  Click the unlock icon ![Unlock icon](images/unlock-api-icon.png) for `api_key`.
    1.  Copy the key.

1.  Use these credentials in your API call.

    - The base URL uses the syntax `{icp-url}/assistant/api`. (Yours might have a different syntax if you edited the Ingress path when you customized your installation.)
    - Provide the API key when you call the service. The following example shows an API key being used.

      ```curl
      curl -X GET \
      -u "apikey:{api_key}"\
      'https://myicp.net/assistant/api/v1/workspaces?version=2018-09-20' \
      ```
      {: codeblock}

See [Platform overview ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/cloud/private){: new_window} for more information about {{site.data.keyword.icpfull_notm}}.
