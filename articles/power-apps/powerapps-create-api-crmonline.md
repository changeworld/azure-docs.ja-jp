<properties
	pageTitle="PowerApps Enterprise への Dynamics CRM Online API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい Dynamics CRM Online API を作成または構成します。"
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="schabungbam"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/02/2016"
   ms.author="sameerch"/>

# PowerApps Enterprise で新しい Dynamics CRM Online API を作成する

> [AZURE.IMPORTANT] このトピックはアーカイブされたため、間もなく削除されます。新しい [PowerApps](https://powerapps.microsoft.com) のページで Microsoft の取り組みをご覧ください。
> 
> - PowerApps の詳細を確認し、使ってみる場合は、[PowerApps](https://powerapps.microsoft.com) のページを参照してください。  
> - PowerApps で利用可能な接続の詳細については、[利用可能な接続](https://powerapps.microsoft.com/tutorials/connections-list/)に関する記事を参照してください。 

<!--Archived
Add the Dynamics CRM Online API to your organization's (tenant) app service environment. 

## Create the API in the Azure portal

1. In the [Azure portal](https://portal.azure.com), sign-in with your work account. For example, sign-in with *yourUserName*@*YourCompany*.com. When you do this, you are automatically signed in to your company subscription.

2. Select **Browse** in the task bar:  
![][1]

3. In the list, you can scroll to find PowerApps or type in *powerapps*:  
![][2]  

4. In **PowerApps**, select **Manage APIs**:  
![Browse to registered apis][3]

5. In **Manage APIs**, select **Add** to add the new API:  
![Add API][4]

6. Enter a descriptive **name** for your API.  

7. In **Source**, select **Available APIs** to select the pre-built APIs, and select **Dynamics CRM Online**:  
![Select Dynamics CRM Online API][5]

8. Select **Settings - Configure required settings**:  
![Configure Dynamics CRM Online API settings][6]

9. Enter **Client Id** and **App Key** of your Dynamics CRM Online Azure Active Directory (AAD) application.  If you don't have one, see the "Register an AAD app for use with PowerApps" section in this topic to create the ID and secret values you need.  

	> [AZURE.IMPORTANT] Save the **redirect URL**. You may need this value later in this topic.

10. Select **OK** to complete the steps.

When finished, a new Dynamics CRM Online API is added to your app service environment.

## Register an AAD app for use with PowerApps Dynamics CRM Online API

1. Open the [Azure Portal](https://portal.azure.com).

2. Select **Browse** and then select **Active Directory**:  

	> [AZURE.NOTE] This opens Active Directory in the Azure classic portal.  

3. Select your organization's tenant name:  
![Launch Azure Active Directory][7]

4. Select the **Applications** tab, and select **Add**:  
![AAD tenant applications][8]

5. In **Add application**:  

	1. Enter a **Name** for your application.  
	2. Leave the application type as **Web**.  
	3. Select **Next**.

	![Add AAD application - app info][9]

6. In **App Properties**:  

	1. Enter the **SIGN-ON URL** of your application.  Since you are going to authenticate with AAD for PowerApps, set the sign-on url to _https://login.windows.net_.  
	2. Enter a valid **APP ID URI** for your app.  
	3. Select **OK**.  

	![Add AAD application - app properties][10]

7. On successful completion, you are redirected to the new AAD app. Select **Configure**:  
![Contoso AAD app][11]

8. Set the **Reply URL** under _OAuth 2_ section to the redirect URL you received when you added the new Dynamics CRM Online API in the Azure Portal (in this topic):  
![Configure Contoso AAD app][12]

9. Select **Save**.

A new Azure Active Directory app is created. You can use this app in your Dynamics CRM Online API configuration in the Azure portal.

## See the REST APIs

[Dynamics CRM Online REST API](../connectors/connectors-create-api-crmonline.md) reference.


## Summary and next steps
In this topic, you added the Dynamics CRM Online API to your PowersApps Enterprise. Next, give users access to the API so it can be added to their apps:

[Add a connection and give users access](powerapps-manage-api-connection-user-access.md)
-->


<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0504_2016-->