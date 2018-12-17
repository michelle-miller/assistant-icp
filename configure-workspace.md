---

copyright:
  years: 2015, 2018
lastupdated: "2018-12-17"

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

# Configuring a {{site.data.keyword.conversationshort}} workspace

The natural-language processing for the {{site.data.keyword.conversationshort}} service happens inside a *workspace*, which is a container for all of the artifacts that define the conversation flow for an application.
{: shortdesc}

A single {{site.data.keyword.conversationshort}} service instance can contain multiple workspaces. A workspace contains the following types of artifacts:

- [**Intents**](intents.html): An *intent* represents the purpose of a user's input, such as a question about business locations or a bill payment. You define an intent for each type of user request you want your application to support. In the tool, the name of an intent is always prefixed with the `#` character. To train the workspace to recognize your intents, you supply lots of examples of user input and indicate which intents they map to.
- [**Entities**](entities.html); An *entity* represents a term or object that is relevant to your intents and that provides a specific context for an intent. For example, an entity might represent a city where the user wants to find a business location, or the amount of a bill payment. In the tool, the name of an entity is always prefixed with the `@` character. To train the workspace to recognize your entities, you list the possible values for each entity and synonyms that users might enter.
- [**Dialog**](dialog-build.html): A *dialog* is a branching conversation flow that defines how your application responds when it recognizes the defined intents and entities. You use the dialog builder in the tool to create conversations with users, providing responses based on the intents and entities that you recognize in their input.

The *content catalog* contains prebuilt common intents and entities that you can add to your application rather than building your own. For example, most applications require a greeting intent that starts a dialog with the user. You can add the **General** content catalog to add a group of intents that recognize and respond to utterances that are commonly used to start and end a conversation, among other things.

As you add information, the workspace uses this unique data to build a machine learning model that can recognize these and similar user inputs. Each time you add or change the training data, the training process is triggered to ensure that the underlying model stays up-to-date as your customer needs and the topics they want to discuss change.

## Workspace limits
{: #workspace-limits}

| Workspaces per service instance |
|---------------------------------|
|                              50 |

The sample workspace does not count toward your workspace limit unless you edit or duplicate the sample.

## Creating workspaces
{: #creating-workspaces}

You can create a workspace from scratch, use the provided sample workspace, or import a workspace from a JSON file. You can also duplicate an existing workspace within the same service instance.

You use the {{site.data.keyword.conversationshort}} tool to create workspaces. 

1.  [Launch the tool](install.html#launch-tool).
1.  Click the **Workspaces** tab.
1.  From the tool, do one of the following things:
    - To create a workspace from scratch, click **Create**.
    - To use the sample as a starting point for your own workspace, edit the sample workspace. If you want to use it for multiple workspaces, then duplicate it instead.
    - To import a workspace from a JSON file, click the ![Import workspace](images/workspace_import.png) icon, and select the JSON file you want to import from.

        **Important:**

        - The imported JSON file must use UTF-8 encoding.
        - The maximum size for a workspace JSON file is 10MB. If you need to import a larger workspace, consider importing the intents and entities separately after you have imported the workspace. (You can also import larger workspaces using the REST API. For more information, see the [API Reference ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/apidocs/assistant?curl=#create-workspace){: new_window}.)
        - The JSON cannot contain tabs, newlines, or carriage returns.

        Specify the data you want to include:

        - Select **Everything (Intents, Entities, and Dialog)** if you want to import a complete copy of the exported workspace, including the dialog.
        - Select **Intents and Entities** if you want to use the intents and entities from the exported workspace, but you plan to build a new dialog.

        **Note**: If you get an error message when importing a JSON file for a workspace that you created in the public cloud environment, then it might mean that your workspace uses features that are not supported in the private cloud environment. For example, it might contain `@sys-person` or `@sys-location` references, which are system entities that are available in public cloud only.

1.  Specify the details for the new workspace:
    - **Name**: A name no more than 64 characters in length. This value is required.
    - **Description**: A description no more than 128 characters in length.
    - **Language**: The language of the user input the workspace will be trained to understand.

After you create the workspace, it appears as a tile on the Workspaces page.

## Exporting and copying workspaces
{: #exporting-and-copying-workspaces}

You can use the Workspaces page to export workspaces and to copy a workspace to a new workspace.

Exporting a workspace saves a copy of all workspace data in a JSON file. The workspace data includes intents and entities that you defined as part of the training data. It also includes counter examples that are created when you identify inaccurate classifications by marking them as irrelevant. Use this option if you want to import the workspace into a different service instance or save a copy of the workspace data offline. When you import a workspace, you can choose to import only the intents and entities, which can be useful if you want to build a new dialog using the same training data.

Copying a workspace makes a complete copy of the workspace within the same service instance. Use this option if you want to adapt an existing workspace while preserving the original version.

- To export an existing workspace to a JSON file, click the menu icon in the workspace tile and then select **Download as JSON**.
- To create a copy of an existing workspace within the same service instance, click the menu icon in the workspace tile and then select **Duplicate**.

    Specify the name, description, and language for the new workspace. All data (including intents, entities, and dialog) is included in the copy.

You can export a workspace by using the API also. Include the `export=true` parameter with the request. See the [API reference](https://cloud.ibm.com/apidocs/assistant?curl=#get-information-about-a-workspace) for more details.

## Sharing the workspace with team members
{: #invite-others}

After you create the service instance, you can give other people access to it. Together, you can define the training data and build the dialog.

**Important**: Only one person can edit an intent, entity, or a dialog node at a time. If multiple people work on the same item at the same time, then the changes made by the person who saves their changes last are the only changes applied. Changes that are made during the same time frame by someone else and are saved first are not retained. Coordinate the updates that you plan to make with your team members to prevent anyone from losing their work.

To share a workspace with other people, you must give them access to the service instance that hosts the workspace.

**Note**: New service instances created on IBM Cloud are using IBM Cloud Identity and Access Management (IAM) instead of being managed by Cloud Foundry. This transition is being rolled out at different times in different locations. During the transition, keep the following things in mind:

  - If you are using a service instance that is managed by Cloud Foundry, then your steps will be slightly different from those described in the procedure below. For Cloud Foundry-managed instances, you must choose your organization from the *Cloud Foundry access* section, and then assign the person to the **Developer** space role.

1.  Go to the {{site.data.keyword.watson}} Developer Console [Projects ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.{DomainName}/developer/watson/projects) page, and log in. Click **Manage > Account > Users** from the header menu.
1.  Click **Invite users**, and then enter the email addresses of the people on your team to whom you want to give access.

    If you gave someone access to a service instance in Cloud Foundry, then the person might be listed as an invited user already. Click the person's name to open the access management settings for the user. Click **Assign access**, and then choose **Assign access to resources**.
1.  In the *Services* section, make the following selections at a minimum:

    - **Services**: {{site.data.keyword.conversationshort}}
    - **Assign platform access roles**: Operator

    For more information about platform management roles, see [IAM access ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/docs/iam/users_roles.html). (Service access roles are not leveraged by {{site.data.keyword.conversationshort}}.)
1.  Click **Invite users**.

When the people you invite next log in to {{site.data.keyword.cloud_notm}}, your account will be included in their list of accounts. If they select your account, they can see your service instance, and open and edit your workspace.

With more people contributing to workspace development, unintended changes can occur, including workspace deletions. Consider creating backup copies of your workspace on a regular basis, so you can roll back to an earlier version if necessary. To create a backup, simply [export the workspace as a JSON file](#exporting-and-copying-workspaces).
