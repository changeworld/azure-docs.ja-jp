<properties
	pageTitle="PowerApps Enterprise への SharePoint Server API の追加 | Microsoft Azure"
	description="組織の App Service 環境での新しい SharePoint Server API の作成または構成"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/02/2016"
   ms.author="litran"/>

# PowerApps Enterprise で新しい SharePoint Server API を作成する

> [AZURE.IMPORTANT] このトピックはアーカイブされたため、間もなく削除されます。新しい [PowerApps](https://powerapps.microsoft.com) のページで Microsoft の取り組みをご覧ください。
> 
> - PowerApps の詳細を確認し、使ってみる場合は、[PowerApps](https://powerapps.microsoft.com) のページをご覧ください。  
> - PowerApps で利用可能な接続の詳細については、[利用可能な接続](https://powerapps.microsoft.com/tutorials/connections-list/)に関する記事をご覧ください。 

<!--Archived
Add the SharePoint Server API to your organization's (tenant) app service environment. 

## Create the API in the Azure portal

1. In the [Azure portal](https://portal.azure.com/), sign-in with your work account. For example, sign-in with *yourUserName*@*YourCompany*.com. When you do this, you are automatically signed in to your company subscription.
 
2. Select **Browse** in the task bar:  
![][14]

3. In the list, you can scroll to find PowerApps or type in *powerapps*:  
![][15]  

4. In **PowerApps**, select **Manage APIs**:  
![Browse to registered apis][5]

5. In **Manage APIs**, select **Add** to add the new API:  
![Add API][6]

6. Enter a descriptive **name** for your API.	
7. In **Source**, select **Available APIs** to select the pre-built APIs, and select **SharePoint Server**. 
8. Select **Settings - Configure required settings**.
9. Enter the *Client Id* and *App Key* for the SharePoint Server Azure Active Directory (AAD app), and the *SharePoint URL* and *Resource Id* of the AAD Proxy app. Follow the steps outlined in the following section to configure connectivity to your on-premises SharePoint Server.  

	> [AZURE.IMPORTANT] Save the **redirect URL**. You may need this value later in this topic.  
	
10. Select **OK** to complete the steps.

When finished, a new SharePoint Server API is added to your app service environment.


## Configure connectivity to an on-premises SharePoint Server

SharePoint Server use Active Directory for user authentication. APIs in the app serviced environments are authenticated using Azure Active Directory (AAD). You do need to exchange the user’s AAD token and convert it to the AD token. This AD token can then be used to connect to the on-premises service.

[Azure Application Proxy (AAD Proxy)](../active-directory/active-directory-application-proxy-publish.md) is used for this requirement. It is an Azure Service in GA, and it secures remote access and SSO to on-premises web applications. The steps to enable AAD Proxy is well documented in MSDN. At a high level, the steps include:  

1. [Enable Application Proxy Services](../active-directory/active-directory-application-proxy-enable.md) – This includes:  

	- Enable Application Proxy in Azure AD
	- Install and Register the Azure Application Proxy Connector

2. [Publish Applications with Application  Proxy](../active-directory/active-directory-application-proxy-publish.md) – This includes:  

	- Publish an Application Proxy app using the wizard. Note the external URL of the intranet sharepoint site once the Proxy app has been created.
	- Assign users and group to the application.
	- Enter advanced configuration like the SPN (Service Principal Name) that is used by the Application Proxy Connector to fetch the Kerberos token on-premises.

Once the Proxy app is created, you have to create another AAD app that delegates to the proxy application. This is required to obtain the access token and refresh token that are required for the consent flow. You can create a new AAD application by following [these instructions](../active-directory/active-directory-integrating-applications.md).

## Summary and next steps
In this topic, you added the Office 365 Outlook API to your PowersApps Enterprise. Next, give users access to the API so it can be added to their apps: 

[Add a connection and give users access](powerapps-manage-api-connection-user-access.md)
-->


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_0504_2016-->