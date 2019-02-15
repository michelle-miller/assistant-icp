---

copyright:
  years: 2015, 2019
lastupdated: "2019-09-21"

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

# Defining intents
{: #intents}

***Intents*** are purposes or goals expressed in a customer's input, such as answering a question or processing a bill payment. By recognizing the intent expressed in a customer's input, the {{site.data.keyword.conversationshort}} service can choose the correct dialog flow for responding to it.
{: shortdesc}

<iframe class="embed-responsive-item" id="youtubeplayer" title="Working with intents" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/OPdOCUPGMIQ" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

Note: The video was created in a test environment in which the workspace is referred to as a skill.

## Intent creation overview
{: #intent-described}

- Plan the intents for your application.

  Consider what your customers might want to do, and what you want your application to be able to handle on their behalf. For example, you might want your application to help your customers make a purchase. If so, you can add a `#buy_something` intent. (The `#` prepended to the intent name helps to clearly identify it as an intent.)

- Teach Watson about your intents.

  Once you decide which business requests you want your application to handle for your customers, you must teach Watson about them. For each business goal (such as `#buy_something`), you must provide at least 10 examples of utterances that your customers typically use to indicate their interest in that goal. For example, `I want to make a purchase.` Ideally, go and find real-world user examples from existing business processes. The user examples should be tailored to your specific business. For example, if you are an insurance company, your user examples might look more like this, `I want to buy a new XYZ insurance plan.` These examples are used by the service to build a machine learning model that can recognize the same and similar types of utterances and map them to the appropriate intent.

Start with a few intents, and test them as you iteratively expand the scope of the application.

## Intent limits
{: #intent-limits}

| Intents per workspace | Examples per workspace |
|-----------------------|------------------------|
|                 2,000 |                 25,000 |
{: caption="Intent limits" caption-side="top"}

## Creating intents
{: #creating-intents}

Use the {{site.data.keyword.conversationshort}} tool to create intents.

1.  In the {{site.data.keyword.conversationshort}} tool, open your workspace and then select the **Intents** tab in the navigation bar. If **Intents** is not visible, use the ![Menu](images/Menu_16.png) menu to open the page.

1.  Select **Create new**.

1.  In the **Intent name** field, type a name for the intent.
    - The intent name can contain letters (in Unicode), numbers, underscores, hyphens, and periods.
    - The name cannot consist of `..` or any other string of only periods.
    - Intent names cannot contain spaces and must not exceed 128 characters. The following are examples of intent names:
        - `#weather_conditions`
        - `#pay_bill`
        - `#escalate_to_agent`

    The tooling automatically includes the `#` character in the intent names, so you do not have to add one.
    {: tip}

    Add a description of the intent in the **Description** field.

1.  Select **Create intent** to save your intent name.

    ![Screen capture shows an intent named #pay_bill](images/create_intent.png)

1.  Next, in the **Add user examples** field, type the text of a user example for the intent. An example can be any string up to 1024 characters in length. The following might be examples for the `#pay_bill` intent:
    - `I need to pay my bill.`
    - `Pay my account balance`
    - `make a payment`

    ***Referencing entity values and synonyms in intent examples***
    {: #related-entities}

    If you have defined, or plan to define, entities that are related to this intent, mention the entity values or synonyms in some of the examples. Doing so helps to establish a relationship between the intent and entities.

    ![Screen capture shows that the definition Pay my account balance is added as a user example.](images/define_intent.png)
    {: #entity-as-example}

    You can also add entity annotations directly from user examples. See [Defining contextual entities](/docs/services/assistant-icp/entities.html#defining-contextual-entities).

    *Important*:

      - Intent example data should be representative and typical of data that end users will provide. Examples can be collected from actual user data, or from people who are experts in your specific field. The representative and accurate nature of the data is important.
      - Both training and test data (for evaluation purposes) should reflect the distribution of intents in real usage. Generally, more frequent intents have relatively more examples, and better response coverage.
      - You can include punctuation in the example text, as long as it appears naturally. If you believe that some users will express their intents with examples that include punctuation, and some users will not, include both versions. Generally, the more coverage for various patterns, the better the response.

    ***Directly referencing an entity name in an intent example***
    {: #entity-as-example}

    You may also choose to directly reference entities in your intent examples. For instance, say you have an entity called `@PhoneModelName`, which contains values *Galaxy S8*, *Moto Z2*, *LG G6*, and *Google Pixel 2*. When you create an intent, for example `#order_phone`, you could then provide training data as follows:
    - Can I get a `@PhoneModelName`?
    - Help me order a `@PhoneModelName`.
    - Is the `@PhoneModelName` in stock?
    - Add a `@PhoneModelName` to my order.

    ![Screen capture shows the @PhoneModelName entity being referenced in a user example for the #order_phone intent](/docs/services/assistant-icp/images/define_intent_entity.png)

    **Note**: Currently, you can only directly reference synonym entities that you define (pattern values are ignored). You cannot use [system entities](/docs/services/assistant-icp/system-entities.html).

    **Important**: If you choose to reference an entity as an intent example (for example, `@PhoneModelName`) *anywhere* in your training data it cancels out the value of using a direct reference (for example, *Galaxy S8*) in an intent example anywhere else. All intents will then use the entity-as-an-intent-example approach; you cannot select this approach for a specific intent only.

    In practice, this means that if you have previously trained most of your intents based on direct references (*Galaxy S8*), and you now use entity references (`@PhoneModelName`) for just one intent, that would impact all your previous training. For example, if you also have an entity `@ServiceCarrier`, with values *Verizon*, *AT&T*, and *Sprint*, and you add a reference to `@ServiceCarrier` in one utterance of the intent training, the intent classifier will treat literal mentions (*Verizon*, *AT&T*, and *Sprint*) in the intent training differently. If you do choose to use `@Entity` references, you need to be careful to replace all previous direct references with `@Entity` references.

    Defining one example intent with an `@Entity` that has 10 values defined for it **does not** equate to specifying that example intent 10 times. The {{site.data.keyword.conversationshort}} service does not give that much weight to that one example intent syntax.

    **Important**: Intent names and example text can be exposed in URLs when an application interacts with the service. Do not include sensitive or personal information in these artifacts.

1.  Click **Add example** to save the example.

1.  Repeat the same process to add more examples. You can tab between each example. Provide at least 5 examples for each intent. The more examples you provide, the more accurate your application can be.

1.  When you have finished adding examples, select ![Close arrow](images/close_arrow.png) to finish creating the intent.

### Results
{: #creating-results}

The intent you created is added to the Intents tab, and the system begins to train itself on the new data.

## Editing intents

You can select any intent in the list to open it for editing. You can make the following changes:

- Rename the intent.
- Delete the intent.
- Add, edit, or delete examples.
- Move an example to a different intent.

You can tab from the intent name to each example, editing the examples if you choose.

To move or delete an example, select the example by selecting the check box and then select **Move** or **Delete**.

  ![Screen capture shows that the checkbox for a user example is selected and the Move and Delete icons are available.](images/move_example.png)

## Importing intents and examples

If you have a large number of intents and examples, you might find it easier to import them from a comma-separated value (CSV) file than to define them one by one in the {{site.data.keyword.conversationshort}} tool.

1.  Collect the intents and examples into a CSV file, or export them from a spreadsheet to a CSV file. The required format for each line in the file is as follows:

    ```
    <example>,<intent>
    ```
    {: screen}

    where `<example>` is the text of a user example, and `<intent>` is the name of the intent you want the example to match. For example:

    ```
    Tell me the current weather conditions.,weather_conditions
    Is it raining?,weather_conditions
    What's the temperature?,weather_conditions
    Where is your nearest location?,find_location
    Do you have a store in Raleigh?,find_location
    ```
    {: screen}

    > **Important:** Save the CSV file with UTF-8 encoding and no byte order mark (BOM).

1.  In the {{site.data.keyword.conversationshort}} tool, open your workspace and then select the **Intents** tab in the navigation bar. If **Intents** is not visible, use the ![Menu](images/Menu_16.png) menu to open the page.

1.  Select the *Import* icon ![Import icon](images/importGA.png). Then, drag a file or browse to select a file from your computer. The file is validated and imported, and the system begins to train itself on the new data.

    ![Screen shot shows where the Import icon is located on the Intents page](images/ImportIntent.png)

    > **Important:** The maximum CSV file size is 10MB. If your CSV file is larger, consider splitting it into multiple files and importing them separately.

### Results
{: #importing-results}

You can view the imported intents and the corresponding examples on the **Intents** tab. You might need to refresh the page in order to see the new intents and examples.

## Exporting intents
{: #export_intents}

You can export a number of intents to a CSV file, so you can then import and reuse them for another {{site.data.keyword.conversationshort}} application.

1.  On the Intents tab, select the intents you want from the list and choose *Export*.

    ![Screen shot shows where the Export icon is located on the Intents page](images/ExportIntent.png)

## Deleting intents
{: #delete_intents}

You can select a number of intents for deletion.

**IMPORTANT**: By deleting intents you are also deleting all associated examples, and these items cannot be retrieved later. All dialog nodes that reference these intents must be updated manually to no longer reference the deleted content.

1.  On the Intents tab, select the intents you want from the list and choose *Delete*.

    ![Screen shot shows that an intent selected and it highlights the Delete icon](images/DeleteIntent.png)

## Testing your intents
{: #testing-your-intents}

After you have finished creating new intents, you can test the system to see if it recognizes your intents as you expect.

1.  In the {{site.data.keyword.conversationshort}} tool, select the ![Try it](images/ask_watson.png) icon.

1.  In the *Try it out* pane, enter a question or other text string and press Enter to see which intent is recognized. If the wrong intent is recognized, you can improve your model by adding this text as an example to the correct intent.

    If you have recently made changes in your workspace, you might see a message indicating that the system is still retraining. If you see this message, wait until training completes before testing:
    {: tip}

    ![Screen capture shows the Watson is training message in the Try it out pane](images/training.png)

    The response indicates which intent was recognized from your input.

    ![Screen capture of the system identifying intents from user input in the Try it out pane](images/test_intents.png)

1.  If the system did not recognize the correct intent, you can correct it. To correct the recognized intent, select the displayed intent and then select the correct intent from the list. After your correction is submitted, the system automatically retrains itself to incorporate the new data.

    ![Screen capture of correcting a recognized intent](images/correct_intent.png)

1.  If the input is unrelated to your application, you can indicate that. Select the displayed intent and choose **Mark as irrelevant**.

    ![Mark as irrelevant screen capture](images/irrelevant.png)

If your intents are not being correctly recognized, consider making the following kinds of changes:

- Add the unrecognized text as an example to the correct intent.
- Move existing examples from one intent to another.
- Consider whether your intents are too similar, and redefine them as appropriate.

## Absolute scoring and Mark as irrelevant

As of February 2017, there is a new algorithm for scoring intent confidence and returning intents. You can also mark inputs as *irrelevant*. These changes might require you to [upgrade to your workspace ![External link icon](../../icons/launch-glyph.svg "External link icon")](/docs/services/assistant-icp/upgrading.html){: new_window}.

### Absolute scoring

The {{site.data.keyword.conversationshort}} service now scores each intent’s confidence on its own, not in relation to other intents. This allows the flexibility to have multiple intents returned. It also means the system may not return an intent at all. If the top intent has low confidence that any intents relate to the user’s input (less than 0.2), it will still get included in the intents array output by the API, but conditioning on that #intent will return false. If you want to detect the case when no intents were detected with good confidence, you can condition on `irrelevant`.

As intent confidence scores change, your dialogs may need restructuring. For example, if you conditioned your dialog with an intent that now has low confidence, the system’s response will no longer be correct.

### Mark as irrelevant
{: #mark-irrelevant}

Refer to [supported languages](/docs/services/assistant-icp/lang-support.html) for the availability of this feature.

After you upgrade your workspace, you can [test input](#testing-your-intents) in the *Try it out* pane to see the changes. You can use **Mark as irrelevant** to indicate that the input is not related to your application.

If you have an intent, such as #off_topic, for those inputs that are out of scope or off topic, delete the intent and test your workspace by marking the inputs as irrelevant.

**Important**: Intents that are marked as irrelevant are saved as counterexamples in the JSON workspace, and are included as part of the training data. Be sure that you want to make any changes.

- The inputs cannot be accessed or changed later in the tooling.
- The only way to remove the **Irrelevant** tag is to use the same input in the *Try it out* pane, and then change the intent.
