<properties 
    pageTitle="Overview of schemas and the Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
    description="Learn how to use schemas with the Enterprise Integration Pack and Logic apps" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2016" 
    ms.author="deonhe"/>


# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Learn about schemas and the Enterprise Integration Pack  

## <a name="why-use-a-schema"></a>Why use a schema
You use schemas to confirm that XML documents you receive are valid, meaning that the documents contain the expected data in a predefined format.

## <a name="how-to-add-a-schema"></a>How to add a schema
From the Azure portal:  

1. Select **More Services**  
![](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    
2. Enter **integration** in the filter search box and select **Integration Accounts** from the results list     
![](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Select the **integration account** to which you add the schema    
![](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  
4. Select the **Schemas** tile  
![](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

#### <a name="to-add-schema-file-less-than-2-mb"></a>To add schema file less than 2 MB  

5. Select the **Add** button in the Schemas blade that opens  
![](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  
6. Enter a **Name** for your schema, then to upload the schema file, select the folder icon on the right side of the **Schema** text box. After the upload process is completed, select the **OK** button.    
![](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

#### <a name="to-add-schema-file-larger-than-2-mb-(max-8-mb)"></a>To add schema file larger than 2 MB (Max 8 MB)  

7. If blob security access level is **Public**  
  ![](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

  * Upload the schema to storage and copy the URI  
  ![](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

  * Select Large file in Add Schema and provide the URI in the Content URI  
  ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

8. If blob security access level is **No anonymous access**  
  ![](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

  * Upload the schema to storage  
  ![](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

  * Generate a Shared Access Signature for the schema  
  ![](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

  * Select Large file in Add Schema and provide the Shared Access Signature URI in the Content URI  
  ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

9. You should see the newly added schema  
![](./media/app-service-logic-enterprise-integration-schemas/schema-41.png) 

## <a name="how-to-use-schemas"></a>How to use schemas
- Schemas are used to validate messages that are exchanged in a B2B scenario.  

## <a name="how-to-edit-schemas"></a>How to edit schemas
1. Select the **Schemas** tile  
2. Select the schema you wish to edit from the Schemas blade that opens up
3. Select the **Upload** link on the Schemas blade  
![](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Select the schema file you wish to upload by using the file picker dialog that opens up.
5. Select **Open** in the file picker  
![](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. You receive a notification that indicates the upload was successful  

## <a name="how-to-delete-schemas"></a>How to delete schemas
1. Select the **Schemas** tile  
2. Select the schema you wish to delete from the Schemas blade that opens up  
3. Select the **Delete** link from the menu bar on the Schemas blade ![](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  
4. If you really wish to delete the schema you selected, choose **Yes** on the Delete schema dialog to confirm your choice  
![](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Finally, notice that the list of schemas in the Schemas blade refreshes and the schema you deleted is no longer listed  
![](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Next steps

- [Learn more about the Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Learn about the enterprise integration pack")  




<!--HONumber=Oct16_HO2-->


