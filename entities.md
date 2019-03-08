---

copyright:
  years: 2015, 2019
lastupdated: "2019-03-08"

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

# Defining entities

***Entities*** represent information in the user input that is relevant to the user's purpose.

If intents represent verbs (the action a user wants to do), entities represent nouns (the object of, or the context for, that action). For example, when the *intent* is to get a weather forecast, the relevant location and date *entities* are required before the application can return an accurate forecast.

Recognizing entities in the user's input helps you to craft more useful, targeted responses. For example, you might have a `#buy_something` intent. When a user makes a request that triggers the `#buy_something` intent, the assistant's response should reflect an understanding of what the *something* is that the customer wants to buy. You can add a `@product` entity, and then use it to extract information from the user input about the product that the customer is interested in. (The `@` prepended to the entity name helps to clearly identify it as an entity.)

Finally, you can add multiple responses to your dialog tree with wording that differs based on the `@product` value that is detected in the user's request.

<iframe class="embed-responsive-item" id="youtubeplayer" title="Working with entities" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/o-uhdw6bIyI" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

Note: The video was created in a test environment in which the workspace is referred to as a skill.

## Entity creation overview
{: #entity-described}

You can create the following types of entities:

- **Synonym entity**: You define a category of terms as an entity (`color`), and then one or more values in that category (`blue`). For each value you specify a bunch of synonyms (`aqua`, `navy`).

    At run time, the service recognizes terms in the user input that exactly match (or, if fuzzy matching is enabled, closely match) the values or synonyms that you defined for the entity as mentions of that entity.
- **Pattern entity**: You define a category of terms as an entity (`contact_info`), and then one or more values in that category (`email`). For each value, you specify a regular expression that defines the textual pattern of mentions of that value type. For an `email` entity value, you might want to define a pattern like, `text@text.com`, for example.

    At run time, the service looks for patterns matching your regular expression in the user input, and identifies any matches as mentions of that entity.
- **Contextual entity**: First, you define a category of terms as an entity (`product`), and then optionally one or more values (`handbag`). Next, you go to the *Intents* page and mine your existing intent user examples to find any mentions of the entity type, and label them as such. For example, you might go to the `#buy_something` intent, and find a user example that says, `I want to buy a Coach bag`. You can label `Coach bag` as a mention of the `@product:handbag` entity.

    At run time, the service evaluates the context in which the term is used in the sentence. If the structure of a user request that mentions the term matches the structure of a user example sentence in which a mention is labeled, then the service identifies the term as a mention of that entity. For example, the user input might include the utterance, `I want to buy a Gucci bag`. Due to the similarity of the structure of this sentence to the user example that you annotated (`I want to buy a Coach bag`), the service recognizes `Gucci bag` as a `@product:handbag` entity mention.
- **System entity**: Synonym entities that are prebuilt for you by IBM. They cover commonly used categories, such as numbers, dates, and times. You simply enable a system entity to start using it.

## Entity limits
{: #entity-limits}

| Entities per workspace | Entity values per workspace | Entity synonyms per workspace | Contextual entities and annotations |
|-----------------------:|----------------------------:|------------------------------:|------------------------------------:|
|                  1,000 |                     100,000 |                       100,000 | 30 contextual entities with 3000 annotations |
{: caption="Entity limits" caption-side="top"}

System entities that you enable for use count toward your plan usage totals.

## Creating entities
{: #creating-entities}

Use the {{site.data.keyword.conversationshort}} tool to create entities.

1.  In the {{site.data.keyword.conversationshort}} tool, open your workspace and then click the **Entities** tab. If **Entities** is not visible, use the ![Menu](images/Menu_16.png) menu to open the page.

1.  Click **Add entity**.

    You can also click **Use System Entities** to select from a list of common entities, provided by {{site.data.keyword.IBM_notm}}, that can be applied to any use case. See [Enabling system entities](#enable_system_entities) for more detail.

1.  In the **Entity name** field, type a descriptive name for the entity.

    The entity name can contain letters (in Unicode), numbers, underscores, and hyphens. For example:
    - `@location`
    - `@menu_item`
    - `@product`

    Do not include spaces in the name. The name cannot be longer than 64 characters. Do not begin the name with the string `sys-` because it is reserved for system entities.

    The tool automatically includes the @ character in the entity name, so you do not have to add one.
    {: tip}

1.  Select **Create entity**.

    ![Screen capture of creating an entity](images/create_entity.png)

1.  In the **Value name** field, type the text of a possible value for the entity and hit the `Enter` key. An entity value can be any string up to 64 characters in length.

    > **Important:** Don't include sensitive or personal information in entity names or values. The names and values can be exposed in URLs in an app.

1.  For **Fuzzy Matching**, click the button to select either on or off; fuzzy matching is off by default. This feature is available for languages noted in the [Supported languages](/docs/services/assistant-icp/lang-support.html) topic.

    ***Fuzzy matching***
    {: #fuzzy-matching}

    You can turn on fuzzy matching to improve the ability of the service to recognize user input terms with syntax that is similar to the entity, but without requiring an exact match. There are three components to fuzzy matching - stemming, misspelling, and partial matching:
    - *Stemming* - The feature recognizes the stem form of entity values that have several grammatical forms. For example, the stem of 'bananas' would be 'banana', while the stem of 'running' would be 'run'.
    - *Misspelling* - The feature is able to map user input to the appropriate corresponding entity despite the presence of misspellings or slight syntactical differences. For example, if you define *giraffe* as a synonym for an animal entity, and the user input contains the terms *giraffes* or *girafe*, the fuzzy match is able to map the term to the animal entity correctly.
    - *Partial match* - With partial matching, the feature automatically suggests substring-based synonyms present in the user-defined entities, and assigns a lower confidence score as compared to the exact entity match.

    **Note** - For English, fuzzy matching prevents the capturing of some common, valid English words as fuzzy matches for a given entity. This feature uses standard English dictionary words. You can also define an English entity value/synonym, and fuzzy matching will match only your defined entity value/synonym. For example, fuzzy matching may match the term `unsure` with `insurance`; but if you have `unsure` defined as a value/synonym for an entity like `@option`, then `unsure` will always be matched to `@option`, and not to `insurance`.

1.  Once you have entered a value name, you can then add any synonyms, or define specific patterns, for that entity value by selecting either `Synonyms` or `Patterns` from the *Type* drop-down menu.

    ![Type selector for Value](images/value_type.png)

    > **Note:** You can add *either* synonyms or patterns for a single entity value, you cannot add both.

    ***Synonyms***
    {: #synonyms}

    - In the **Synonyms** field, type any synonym for the entity value. A synonym can be any string up to 64 characters in length.

      ![Screen capture of defining an entity](images/define_entity.png)

    ***Patterns***
    {: #patterns}

    - The **Patterns** field lets you define specific patterns for an entity value. A pattern **must** be entered as a regular expression in the field.

      - For each entity value, there can be a maximum of up to 5 patterns.
      - Each pattern (regular expression) is limited to 512 characters.

      ![Screen capture of defining a pattern entity](images/patternents1.png)
      {: #pattern-entities}

      As in this example, for entity *ContactInfo*, the patterns for phone, email, and website values can be defined as follows:
      - Phone
        - `localPhone`: `(\d{3})-(\d{4})`, e.g. 426-4968
        - `fullUSphone`: `(\d{3})-(\d{3})-(\d{4})`, e.g. 800-426-4968
        - `internationalPhone`: `^(\(?\+?[0-9]*\)?)?[0-9_\- \(\)]*$`, e.g., +44 1962 815000
      - `email`: `\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b`, e.g. name@ibm.com
      - `website`: `(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$`, e.g. https://www.ibm.com

      Often when using pattern entities, it will be necessary to store the text that matches the pattern in a context variable (or action variable), from within your dialog tree. For additional information, see [Defining a context variable](/docs/services/assistant-icp/dialog-runtime.html#context-var-define).

      Imagine a case where you are asking a user for their email address. The dialog node condition will contain a condition similar to `@contactInfo:email`. In order to assign the user-entered email as a context variable, the following syntax can be used to capture the pattern match within the dialog node's response section:
      
      - Variable: email
      - Value: `<? @contactInfo.literal ?>`
      {: #capture-group}

      *Capture groups* - For regular expressions, any part of a pattern inside a pair of normal parentheses will be captured as a group. For example, the entity value `fullUSphone` contains three captured groups:

        - `(\d{3})` - US area code
        - `(\d{3})` - Prefix
        - `(\d{4})` - Line number

      Grouping can be helpful if, for example, you wanted the {{site.data.keyword.conversationshort}} service to ask users for their phone number, and then use only the area code of their provided number in a response.

      In order to assign the user-entered area code as a context variable, the following syntax can be used to capture the group match within the dialog node's response section:

      - Variable: area_code
      - Value: `<? @fullUSphone.groups[1] ?>` 

      For additional information about using capture groups in your dialog, see [Storing and recognizing entity pattern groups in input](/docs/services/assistant-icp/dialog-tips.html#get-pattern-groups).

      The pattern matching engine employed by the {{site.data.keyword.conversationshort}} service has some syntax limitations, which are necessary in order to avoid performance concerns which can occur when using other regular expression engines.
        - Entity patterns may not contain:
          - Positive repetitions (for example `x*+`)
          - Backreferences (for example `\g1`)
          - Conditional branches (for example `(?(cond)true)`)
        - When a pattern entity starts or ends with a Unicode character, and includes word boundaries, for example `\bš\b`, the pattern match does not match the word boundary correctly. In this example, for input `š zkouška`, the match returns `Group 0: 6-7 š` (`š zkou`_**`š`**_`ka`), instead of the correct `Group 0: 0-1 š` (_**`š`**_ `zkouška`).

      The regular expression engine is loosely based on the Java regular expression engine. The {{site.data.keyword.conversationshort}} service will produce an error if you try to upload an unsupported pattern, either via the API or from within the {{site.data.keyword.conversationshort}} service Tooling UI.

1.  Click **Add value** and repeat the process to add more entity values.

1.  When you are finished adding entity values, select ![Close arrow](images/close_arrow.png) to finish creating the entity.

The entity you created is added to the **Entities** tab, and the system begins to train itself on the new data.

## Defining contextual entities ![BETA](images/beta.png)
{: #defining-contextual-entities}

When you define specific values for an entity, the service finds entity mentions only when a term in the user input exactly matches (or closely matches if fuzzy matching is enabled) a value or synonym defined. When you define a contextual entity, a model is trained on both the *annotated term* and the *context* in which the term is used in the sentences you annotate. This new contextual entity model enables the service to calculate a confidence score that identifies how likely a word or phrase is to be an instance of an entity, based on how it is used in the user input.

The following video demonstrates how to annotate entity mentions.

<iframe class="embed-responsive-item" id="youtubeplayer0" title="Annotating entity mentions" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/3WjzJpLsnhQ" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

To walk through a tutorial that shows you how to define contextual entities before you add your own, go to [Tutorial: Defining contextual entities ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/cloud/garage/demo/try-watson-assistant-contextual-entities){: new_window}.

### Creating contextual entities from the **Intents** tab
{: #create-open-entities}

In order to train a contextual entity model, you can take advantage of your intent examples, which provide readily-available sentences to annotate. Using intent user examples to define contextual entities does not affect the classification of an intent.

1.  In the {{site.data.keyword.conversationshort}} tool, open your skill and then click the **Intents** tab. If **Intents** is not visible, use the ![Menu](images/Menu_16.png) menu to open the page.

1.  Select an intent. For this example, the intent `#place_order` defines the order function for an online retailer.

    ![Select #place_order intent](images/oe-intent.png)

1.  Review the intent examples for potential entity values. Highlight a potential entity value from the intent examples, in this case `computer`.

    ![Review intent examples](images/oe-intent-review.png)

    **Note**: To directly edit an intent example, select the Edit icon ![Edit icon](images/oe-intent-edit.png) instead of highlighting a value for annotation.

1.  A Search box opens, allowing you to search for an appropriate entity for the highlighted entity value.

    ![Search box initial state](images/oe-intent-search1.png)

1.  In this example, searching `prod` brings up matches for both the `@product` entity, and for entity values `shirt` and `pens`. This is an important distinction - `@product` is an entity that can contain multiple entity values, in this case `@product:pencil`, `@product:shirt` and `@product:pens`

    ![Search box with search parameter prod](images/oe-intent-search2.png)

    You can also create a new entity by choosing `@(create new entity)`.

1.  Select `@product` to add `computer` as a value for that entity.

    **NOTE**: You should create *at least* 10 annotations for each contextual entity; more annotations are recommended for production use.

1.  Now, click the annotation you just created. A box will appear with the words `Go to: @product` at the bottom. Clicking that link will take you directly to the entity.

    ![Verify value computer for product entity](images/oe-verify-value.png)

To see all of the mentions you annotated for a particular entity, from the entity's configuration page, click the **Annotations** tab.

### Working with counterexamples

If you have an intent example with an annotation, and a word in that example is part of your entity values, but the value is **not** annotated, it is considered a counterexample:

1.  The `#Customer_Care_Appointments` intent includes two intent examples with the word `visit`.

    ![Visit examples intent](images/oe-counter-1.png)

1.  In the first example, you want to annotate the word `visit` as an entity value of the `@meeting` entity. This makes `visit` equivalent to other `@meeting` entity values such as `appointment`, as in "I'd like to make an appointment" or "I'd like to schedule a visit".

    ![@meeting entity](images/oe-counter-2.png)

1.  For the second example, the word `visit` is being used in a different context than a meeting. In this case, you can select the word `appointment` from the intent example, and annotate it as an entity value of the `@meeting` entity. Because the word `visit` in the same example is not annotated, it will serve as a counterexample.

    ![Visit unselected](images/oe-counter-3.png)

### Viewing annotations from the **Entities** tab
{: #annotate-open-entities}

To see the intent examples you have used in annotating your contextual entities:

1.  From the **Entities** tab, open an entity, for example, `@cuisine`.

    ![Cuisine entity highlighted in list](images/oe-annotate1.png)

1.  Select the *Annotation* view.

    ![Annotation view selector highlighted](images/oe-annotate2.png)

    If you have already [created annotated entities from the Intents tab](#create-open-entities), you will see a list of user examples with their associated intents.

    **NOTE**: Contextual entities understand values that you have not explicitly defined. The system makes predictions about additional entity values based on how your user examples are annotated, and uses those values to train other entities. Any similar user examples are added to the *Annotation* view, so you can see how this option impacts training.

    If you do not want your contextual entities to use this expanded understanding of entity values, select all the user examples in the *Annotation* view for that entity and choose **Delete**.

    ![Examples and intents list](images/oe-annotate3a.png)

## Editing entities

You can click any entity in the list to open it for editing. You can rename or delete entities, and you can add, edit, or delete values, synonyms, or patterns.

> **Note**: If you change the entity type from `synonym` to `pattern`, or vice versa, the existing values are converted, but might not be useful as-is.

## Importing entities

If you have a large number of entities, you might find it easier to import them from a comma-separated value (CSV) file than to define them one by one in the {{site.data.keyword.conversationshort}} tool.

1.  Collect the entities into a CSV file, or export them from a spreadsheet to a CSV file. The required format for each line in the file is as follows:

    ```
    <entity>,<value>,<synonyms>
    ```
    {: screen}

    where &lt;entity&gt; is the name of an entity, &lt;value&gt; is a value for the entity, and &lt;synonyms&gt; is a comma-separated list of synonyms for that value.

    ```
    weekday,Monday,Mon
    weekday,Tuesday,Tue,Tues
    weekday,Wednesday,Wed
    weekday,Thursday,Thur,Thu,Thurs
    weekday,Friday,Fri
    weekday,Saturday,Sat
    weekday,Sunday,Sun
    month,January,Jan
    month,February,Feb
    month,March,Mar
    month,April,Apr
    month,May
    ```
    {: screen}

    Importing a CSV file also supports patterns. Any string wrapped with `/` will be considered a pattern (as opposed to a synonym).

    ```
    ContactInfo,localPhone,/(\d{3})-(\d{4})/
    ContactInfo,fullUSphone,/(\d{3})-(\d{3})-(\d{4})/
    ContactInfo,internationalPhone,/^(\(?\+?[0-9]*\)?)?[0-9_\- \(\)]*$/
    ContactInfo,email,/\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b/
    ContactInfo,website,/(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/
    ```
    {: screen}

    Save the CSV file with UTF-8 encoding and no byte order mark (BOM). The maximum CSV file size is 10MB. If your CSV file is larger, consider splitting it into multiple files and importing them separately.  In the {{site.data.keyword.conversationshort}} tool, open your workspace and then click the **Entities** tab.
    {: tip}

1.  Click ![Import](images/importGA.png) and then drag a file, or browse to select a file from your computer. The file is validated and imported, and the system begins to train itself on the new data.

You can view the imported entities on the Entities tab. You might need to refresh the page to see the new entities.

## Exporting entities
{: #export_entities}

You can export a number of entities to a CSV file, so you can then import and reuse them for another {{site.data.keyword.conversationshort}} application.

Exporting a CSV file supports patterns. Any string wrapped with `/` will be considered a pattern (as opposed to a synonym).
{: tip}

1.  Select the entities you want, then select **Export**.

    ![Export entity button](images/ExportEntity.png)

## Deleting entities
{: #delete_entities}

You can select a number of entities for deletion.

**IMPORTANT**: By deleting entities you are also deleting all associated values, synonyms, or patterns, and these items cannot be retrieved later. All dialog nodes that reference these entities or values must be updated manually to no longer reference the deleted content.

1.  Select the entities you want, then select **Delete**.

    ![Delete entity button](images/DeleteEntity.png)

## Enabling system entities
{: #enable_system_entities}

The {{site.data.keyword.conversationshort}} service provides a number of *system entities*, which are common entities that you can use for any application. Enabling a system entity makes it possible to quickly populate your workspace with training data that is common to many use cases.

System entities can be used to recognize a broad range of values for the object types they represent. For example, the `@sys-number` system entity matches any numerical value, including whole numbers, decimal fractions, or even numbers written out as words.

System entities are centrally maintained, so any updates are available automatically. You cannot modify system entities.

1.  On the Entities tab, click **System entities**.

    ![Screen capture of "System entities" tab](images/system_entities_1.png)

1.  Browse through the list of system entities to choose the ones that are useful for your application.
    - To see more information about a system entity, including examples of matching input, click the entity in the list.
    - For details about the available system entities, see [System entities](/docs/services/assistant-icp/system-entities.html).

1.  Click the toggle switch next to a system entity to enable or disable it.

After you enable system entities, the {{site.data.keyword.conversationshort}} service begins retraining. After training is complete, you can use the entities.
