---

copyright:
  years: 2015, 2019
lastupdated: "2019-02-14"

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

# About
{: #index}

With {{site.data.keyword.conversationfull}} for {{site.data.keyword.icpfull}}, you can build a solution that understands natural-language input and uses machine learning to respond to customers in a way that simulates a conversation between humans.
{: shortdesc}

## How it works

This diagram shows the overall architecture of a complete solution:![Flow diagram of the service](images/arch-overview0.png)

- Users interact with your application through the user **interface** that you implement. For example, a simple chat window or a mobile app, or even a robot with a voice interface. The client application can be hosted either inside or outside the private cloud infrastructure.

- The **application** sends the user input to the {{site.data.keyword.conversationshort}} service.
    - The application connects to a *workspace*, which is a container for your dialog flow and training data.
    - The service interprets the user input, directs the flow of the conversation and gathers information that it needs.

- The application can interact with the following resources:

    - **Other IBM services**: Connect with additional {{site.data.keyword.watson}} services to analyze user input, such as {{site.data.keyword.toneanalyzershort}} or {{site.data.keyword.speechtotextshort}}.
    - **Back-end systems**: Based on the user's intent and additional information, extract information or perform transactions by interacting with your back-end systems. For example, answer question, open tickets, update account information, or place orders. There is no limit to what you can do.

The tool does not currently include integrations for deploying the finished assistant nor metrics for analyzing conversations that your assistant is having with your users. Such deployment and metrics features are available from the public cloud version of the service only.

## Implementation

Here's how you will implement your conversation:

- **Configure a workspace.** With the easy-to-use graphical environment, set up the training data and dialog for your conversation.

    The training data consists of the following artifacts:

    - **Intents**: Goals that you anticipate your users will have when they interact with the service. Define one intent for each goal that can be identified in a user's input. For example, you might define an intent named *store_hours* that answers questions about store hours. For each intent, you add sample utterances that reflect the input customers might use to ask for the information they need, such as, `What time do you open?`

      Or use prebuilt **content catalogs** provided by IBM to get started with data that addresses common customer goals.

    - **Entities**: An entity represents a term or object that provides context for an intent. For example, an entity might be a city name that helps your dialog to distinguish which store the user wants to know store hours for.

      As you add training data, a natural language classifier is automatically added to the workspace, and is trained to understand the types of requests that you have indicated the service should listen for and respond to.

    Use the dialog tool to build a dialog flow that incorporates your intents and entities. The dialog flow is represented graphically in the tool as a tree. You can add a branch to process each of the intents that you want the service to handle. You can then add branch nodes that handle the many possible permutations of a request based on other factors, such as the entities found in the user input or information that is passed to the service from your application or another external service.

- **Deploy your workspace.** Deploy the configured workspace to users by connecting it to a front-end user interface that you build.

Read more about these implementation steps by following these links:

- [Intent creation overview](/docs/services/assistant-icp/intents.html#intent-described)
- [Dialog overview](/docs/services/assistant-icp/dialog-overview.html)
- [Entity creation overview](/docs/services/assistant-icp/entities.html#entity-described)
- [Building a client application](/docs/services/assistant-icp/develop-app.html)

## Browser support

The {{site.data.keyword.conversationshort}} service tooling requires the same level of browser software as is required by {{site.data.keyword.icpfull_notm}}. See [Supported browsers ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/supported_system_config/supported_browsers.html){: new_window} for details.

## Language support
{: #index-lang-support}

Language support by feature is detailed in the [Supported languages](/docs/services/assistant-icp/lang-support.html) topic.

## Next steps
{: index-next steps}

- [Get started](/docs/services/assistant-icp/getting-started.html) with the service
- Try out some [demos](/docs/services/assistant-icp/sample-applications.html).
- View the list of [developer resources ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/watson/developer-resources/){: new_window}.

Still have questions? Contact [IBM Sales ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www-01.ibm.com/marketing/iwm/dre/signup?source=urx-20970){: new_window}.

## Terms and notices
{: #notices}

See [IBM Cloud Terms and Notices ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/docs/overview/terms-of-use/notices.html) for information about the terms of service.

## Trademarks
{: #trademarks}

IBM, the IBM logo, and ibm.com are trademarks or registered trademarks of International Business Machines Corp., registered in many jurisdictions worldwide. Other product and service names might be trademarks of IBM or other companies. A current list of IBM trademarks is available on the web at [Copyright and trademark information ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/legal/us/en/copytrade.shtml).

Java and all Java-based trademarks and logos are trademarks or registered trademarks of Oracle and/or its affiliates.

![Java integrated logo.](images/Java_Compatible.png)