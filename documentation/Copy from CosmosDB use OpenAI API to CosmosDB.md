Incremental copy from Cosmos DB using OpenAI API to Cosmos DB
==========================================

This article describes a solution model that you can use to copy items from a Cosmos DB Source Container by filtering via query the attributes through the Lookup activity and using the copy activity to iterate the items with OpenAI API to store the JSON in the container Cosmos DB destination.

About this solution template
----------------------------

This model retrieves items from a Cosmos DB source container through the Lookup activity, with the necessary attributes being selected via query, and the Lookup output is dynamically configured in the Foreach activity that iterates the items, passing each attribute to the copy activity that was configured with the sink defining the OpenAI parameters storing the resulting JSON in a Cosmos DB destination container.

For example:

-   Cosmos DB 
        -   Database
            -   Container
                -   Items

<img src="./media/CosmosSource.png" style="width:3.80106in;height:2.44509in" />

How to use this solution template
---------------------------------

1.  Go to the **Incremental copy from Cosmos DB using OpenAI API to Cosmos DB** template.

    <img src="./media/template.png" style="width:2.62292in;height:1.79097in" />

2.  Create a **New** or use an existing connection to the source
    Cosmos DB and REST linked service.

    <img src="./media/use_template_linked1.png" style="width:4.376in;height:2.78549in" />

3.  Create a **New** or use an existing connection to the Cosmos DB source.

    Follow these steps if you need to create a new REST linked service.
    
    1.  Select “+ New" from the **REST** dropdown list.
    
        <img src="./media/new_ls_rest.png" style="width:2.06135in;height:1.29089in" />
    
    2.  See [REST Linked Service](REST%20Linked%20Service.md) for information on setting up the REST linked service.

4.  Select **Use this template**.

5.  You will see a pipeline created as shown in the following example:

    <img src="./media/created_template.png" style="width:4.504in;height:3.13981in" />


Pipeline
--------

### Parameters

1.  **model:** The Model chosen for this template is gpt-3.5-turbo, other models can be checked in the official OpenAI API documentation.

2.  **token:** The OpenAI Token is an authentication key necessary for accessing OpenAI's services via its API. It's obtained upon registration and grants access to the API. This unique token must be included in every request to authenticate and authorize access to the services provided by OpenAI.

    <img src="./media/parameters.png" style="width:4.04in;height:1.39674in" />

Lookup Activity
-------------

### Settings

1.  **First row only:** Indicates whether to return just the first row or all rows. In our template we are using this unchecked option to get all items from the Cosmos DB container.

2.  **Use query:** Define in the query an attribute of the Cosmos DB container item that will be read by the Rest API later in the copy activity.

    <img src="./media/lookup_settings.png" style="width:4.04in;height:1.39674in" />


Copy Activity
-------------

### Source

1.  Dataset properties:

    1.  **Request body** - Using the Pipeline expression builder, the "content" property of the second "messages" array object must be changed with the Cosmos DB container item attribute defined in the Lookup Activity query.
    
    2.  **Additional columns** - Note that it is possible to add new attributes dynamically, where their values ​​can be obtained according to the Lookup activity query. These Attributes will be added to the JSON that will be copied to the Cosmos DB Container.
        
        <img src="./media/copy_source.png" style="width:4.624in;height:2.12279in" />


### Sink

1.  Dataset properties

    1.  FolderName – A concatenation of the OutputContainer and the EntityId.
    2.  FileName – A concatenation of the EntityId and a timestamp.
	
    <img src="./media/copyfrom_restapi_to_json_11.png" style="width:4.98747in;height:1.76in" />

### Mapping

Select **Mapping** tab to map the records result properties to the corresponding JSON column.

First click the **Import Schemas** button. You will be prompted to confirm the value of the pipeline variable for the EntityId. Click **OK**.

<img src="./media/copyfrom_restapi_to_json_12.png" style="width:3.19951in;height:2.28221in" />

After a couple of seconds, you will see a list of mapping fields
listed, as shown in the following example.

<img src="./media/copyfrom_restapi_to_json_13.png" style="width:6.208in;height:4.128in" />

Next, select **data** from the **Collection reference** drop down
list. The **data** property is the array of records.

<img src="./media/copyfrom_restapi_to_json_14.png" style="width:3.70779in;height:0.45753in" />

Unselect the Include checkboxes for the pageNbr, pageSize,
resultCount, totalPages, totalRecords, and nextPage properties as we
do not want to copy them to the file.

<img src="./media/copyfrom_restapi_to_json_15.png" style="width:5.29221in;height:1.43161in" />

After selecting the data collection reference, you need to correct the
Column names for each property you want to copy by removing the
**\[‘data’\]\[0\]** part of the column name.

From this

  <img src="./media/copyfrom_restapi_to_json_16.png" style="width:6.09583in;height:0.52778in" />

To this

  <img src="./media/copyfrom_restapi_to_json_17.png" style="width:4.76in;height:0.52228in" />

**Quick Tip:** Depending on the number of fields, correcting the
column name may take several minutes. Click the { } (Code) icon in the
right side of the pipeline toolbar.

  <img src="./media/copyfrom_restapi_to_json_18.png" style="width:5.152in;height:2.28592in" />

Scroll down to the mappings section. Then select **\[‘data’\]\[0\]**
and press Ctrl+H to replace this string with an empty string. Press
the Replace all button. Once done replacing, press the OK button.

  <img src="./media/copyfrom_restapi_to_json_19.png" style="width:4.864in;height:2.97037in" />

Go back into the Copy data Mapping tab and now your Column names should all be updated.

  <img src="./media/copyfrom_restapi_to_json_20.png" style="width:5.008in;height:2.94166in" />

Publish
-------

Once you are finished with all your changes, click **Publish All**.

<img src="./media/copyfrom_restapi_to_json_21.png" style="width:1.36994in;height:0.29043in" />

Triggering
----------

1.  To run the pipeline now, select **Add Trigger** and select **Trigger
    now**. Press **OK** at the Pipeline run prompt.

2.  Select **Monitor** tab in the left navigation panel and wait for
    about 20 seconds. Click **Refresh** to get the updated run status.

3.  When the pipeline run completes successfully, you would see results
    like the following example:

    <img src="./media/copyfrom_restapi_to_json_22.png" style="width:5.85833in;height:1.11597in" />

4.  You should also see the output file in the Container and Directory
    you entered.

    <img src="./media/copyfrom_restapi_to_json_23.png" style="width:3.6in;height:1.456in" />

Next steps
----------

-   [Introduction to Azure Data
    Factory](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/data-factory/introduction.md)
