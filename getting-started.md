---

copyright:
  years: 2015, 2019
lastupdated: "2019-02-21"

subcollection: assistant-private


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

# Getting started tutorial
{: #getting-started}

In this short tutorial, we introduce the {{site.data.keyword.conversationshort}} tool and go through the process of creating your first assistant.
{: shortdesc}

## Before you begin
{: #prerequisites}

You'll need a service instance to start. The administrator must install the service for you. Details are provided in the [installation checklist](/docs/services/assistant-icp/install.html).

If a {{site.data.keyword.conversationshort}} service instance is already set up in your {{site.data.keyword.BluOpenStackDed_full}} environment, then you're all set with these prerequisites. Go to [Step 1](#launch-tool).

## Step 1: Open the tool
{: #launch-tool}

1.  Log in to the {{site.data.keyword.icpfull_notm}} management console.
1.  From the main menu, expand **Workloads**, and then choose **Deployments**.

    ![Shows the ICP menu choices to get to the deployment launch button.](images/gs-icp-admin-menu.png)
1.  Filter the list of deployments by the namespace you specified for your instance.

    ![Shows the ICP namespace filter.](images/gs-convo-namespace.png)
1.  Find the deployment named `{release-name}-ui`.

    If you don't know the `{release-name}`, search on `-ui` to find it.
1.  Click **Launch**.

    A new web browser tab opens and shows the {{site.data.keyword.conversationshort}} tool login page.
1.  Log in using the same credentials you used to log into the {{site.data.keyword.icpfull_notm}} dashboard.

## Step 2: Create a workspace
{: #create-workspace}

Your first step in the {{site.data.keyword.conversationshort}} tool is to create a workspace.

A [*workspace*](/docs/services/assistant-icp/configure-workspace.html) is a container for the artifacts that define the conversation flow.

1.  From the home page of the {{site.data.keyword.conversationshort}} tool, click the **Workspaces** tab.
1.  Click **Create**.

    ![Shows the service with no workspaces created yet.](images/gs-ass-service-created.png)
1.  Give your workspace the name `{{site.data.keyword.conversationshort}} tutorial`. If the dialog you plan to build will use a language other than English, then choose the appropriate language from the list. Click **Create**. Youʼll land on the **Intents** tab of your new workspace.

![Shows an animation of a user creating a {{site.data.keyword.conversationshort}} tutorial workspace.](images/gs-ass-create-workspace.gif){: gif}

## Step 3: Add intents from a content catalog
{: #add-catalog}

Add training data that was built by IBM to your workspace by adding intents from a content catalog. In particular, you will give your assistant access to the `General` content catalog so your dialog can greet users, and end conversations with them.

1.  In the {{site.data.keyword.conversationshort}} tool, click the **Content Catalog** tab.
1.  Find **General** in the list, and then click **Add to workspace**.

    ![Shows the Content Catalog and highlights the Add to workspace button for the General catalog.](images/gs-ass-add-general-catalog.png)
1.  Open the **Intents** tab to review the intents and associated example utterances that were added to your training data. You can recognize them because each intent name begins with the prefix `#General_`. You will add the `#General_Greetings` and `#General_Ending` intents to your dialog in the next step.

    ![Shows the intents that are displayed in the Intents tab after the General catalog is added.](images/gs-ass-general-added.png)

You have successfully started to build your training data by adding prebuilt content from IBM to your workspace.

## Step 4: Build a dialog
{: #build-dialog}

A [dialog](/docs/services/assistant-icp/dialog-build.html) defines the flow of your conversation in the form of a logic tree. Each node of the tree has a condition that triggers it, based on user input.

We'll create a simple dialog that handles the `#General_Greetings` and `#General_Ending` intents, each with a single node.

1.  In the {{site.data.keyword.conversationshort}} tool, click the **Dialog** tab.

    ![Shows the empty Dialog tab.](images/gs-ass-empty-dialog.png)
1.  Click **Create**.

    You'll see two nodes:
    - **Welcome**: Contains a greeting that is displayed to your users when they first engage with the bot.
    - **Anything else**: Contains phrases that are used to reply to users when their input is not recognized.

    ![Shows the dialog with the two default nodes, Welcome and Anything else.](images/gs-ass-dialog-init-nodes.png)
1.  Click the **Welcome** node to open it in the edit view.
1.  Replace the default response with the text, `Welcome to the {{site.data.keyword.conversationshort}} tutorial!`.

    ![Shows the Welcome node open in edit view and that a revised response has been added.](images/gs-ass-edited-welcome-node.png)
1.  Click ![Close](images/close.png) to close the edit view.

    Now let's add nodes to handle our intents between the `Welcome` node and the `Anything else` node.

1.  Click the More icon ![More options](images/kabob.png) on the **Welcome** node, and then select **Add node below**.
1.  Type `#General_Greetings` in the **Enter a condition** field of this node. Then select the **`#General_Greetings`** option.
1.  Add the response, `Good day to you!`
1.  Click ![Close](images/close.png) to close the edit view.

   ![Adding a general greeting node to the dialog.](images/gs-ass-add-general-greeting.gif){: gif}
1.  Click the More icon ![More options](images/kabob.png) on this node, and then select **Add node below** to create a peer node. In the peer node, specify `#General_Ending` as the condition, and `OK. See you later.` as the response.

1.  Click ![Close](images/close.png) to close the edit view.

   ![Shows that a general ending node was also added to the dialog.](images/gs-ass-general-ending-added.png)

## Step 5: Test the dialog

You built a simple dialog to recognize and respond to both hello and goodbye inputs. Let's see how well it works.

1.  Click the ![Ask Watson](images/ask_watson.png) icon to open the "Try it out" pane. The welcome message that you added is displayed.

   ![Shows the welcome message being displayed in the Try it out pane.](images/gs-ass-welcome-message.png)
1.  At the bottom of the pane, type `Hello` and press Enter. The output indicates that the `#General_Greetings` intent was recognized, and the appropriate response (`Good day to you.`) appears.
1.  Try the following input:
    - `bye`
    - `howdy`
    - `see ya`
    - `good morning`
    - `sayonara`

  ![Shows an animation of the user testing the dialog in the Try it out pane by entering the inputs listed above and getting the appropriate responses.](images/gs-ass-dialog-test.gif){: gif}

{{site.data.keyword.watson}} can recognize your intents even when your input doesn't exactly match the examples you included. The dialog uses intents to identify the purpose of the user's input regardless of the precise wording used, and then responds in the way you specify.

## Step 6: Add a business function to the dialog
{: #add-ecommerce}

Add the *Customer Care* content catalog to your training data, so your dialog can address user requests for contact information.

1.  In the {{site.data.keyword.conversationshort}} tool, click the **Content Catalog** tab.
1.  Find **Customer Care** in the list, and then click **Add to workspace**.
1.  Open the **Intents** tab to review the intents and associated example utterances that were added to your training data. You can recognize them because each intent name begins with the prefix `#Customer_Care_`. You will add the `#Customer_Care_Contact_Us` intent to your dialog in a later step.

    ![Shows the customer care intents that were added from the catalog in the Intents page.](images/gs-ass-customer-care-intents.png)
1.  Open the **Dialog** tab. Click the More icon ![More options](images/kabob.png) on the `#General_Greetings` node, and then select **Add node below** to create a peer node. In the peer node, specify `#Customer_Care_Contact_Us` as the condition.
1.  Add the following text as the response:

    ```
    Contact us by phone 24 hours a day, 7 days a week at 958-234-3456. To give us feedback, submit a feedback form through our <a href="https://www.example.com/feedback.html" target="_blank">website</a>.
    ```
    {: codeblock}

    ![Shows the contact us intent node being added to the dialog node.](images/gs-ass-add-customer-care-node.png)
1.  Click ![Close](images/close.png) to close the edit view.

    ![Shows that a contact us node was added to the dialog.](images/gs-ass-full-dialog-tree.png)
1.  Test the node you just added. Click the ![Try it](images/ask_watson.png) icon to open the "Try it out" pane, and then enter `How can I contact you?`

    The `#Customer_Care_Contact_Us` intent is recognized, and the response that you specified for it to return is displayed.

    ![Shows an animation of the user entering, how can i contact you?, into the Try it out pane and the bot answering with a phone number and link to a website.](images/gs-ass-test-dialog-contact-us.gif){: gif}

You have successfully added a node to the dialog that addresses the type of business-related question that real users might ask.

## Step 7: Review the sample workspace
{: #review-sample-workspace}

Open the sample workspace to see intents similar to the ones you just created plus many more, and see how they are used in a more complex dialog.

1.  Go back to the Workspaces page.
   You can click the ![Back to workspaces button](images/workspaces-button.png) button from the navigation menu.
1.  On the **Customer Service - Sample** workspace tile, click the **Edit sample** button.

## Next steps
{: #next-steps}

This tutorial is built around a simple example. For a real application, you'll need to define some more interesting intents, some entities, and a more complex dialog.

- Try the advanced [tutorial](/docs/services/assistant-icp/tutorial.html) to add entities and clarify a user's purpose.
- Check out the [sample apps](/docs/services/assistant-icp/sample-applications.html).
