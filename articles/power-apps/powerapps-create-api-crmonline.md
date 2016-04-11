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
   ms.date="03/29/2016"
   ms.author="sameerch"/>

# PowerApps Enterprise で新しい Dynamics CRM Online API を作成する

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

組織の (テナント) App Service 環境に Dynamics CRM Online API を追加する

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。

2. タスク バーの **[参照]** をクリックします。  
![][1]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「powerapps」と入力します。  
![][2]

4. **[PowerApps]** で、**[API の管理]** を選択します。  
![登録されている API の参照][3]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。  
![Add API][4]

6. API のわかりやすい**名前**を入力します。

7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Dynamics CRM Online]** を選択します。  
![Dynamics CRM Online API の選択][5]

8. **[設定] - [必要な設定の構成]** を選択します。  
![Dynamics CRM Online API 設定の構成][6]

9. Dynamics CRM Online Azure Active Directory (AAD) アプリケーションの**クライアント ID** と**アプリケーション キー**を入力します。これらがない場合は、このトピックの「PowerApps Dynamics CRM Online API で使用する AAD アプリケーションの登録」を参照して、必要な ID とシークレットの値を作成します。

	> [AZURE.IMPORTANT] **リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい Dynamics CRM Online API が追加されます。

## PowerApps Dynamics CRM Online API で使用する AAD アプリケーションの登録

1. [Azure ポータル](https://portal.azure.com)を開きます。

2. **[参照]** をクリックし、**[Active Directory]** を選択します。

	> [AZURE.NOTE] これにより、Azure クラシック ポータルで Active Directory が開きます。

3. 組織のテナント名を選択します。  
![Azure Active Directory の起動][7]

4. **[アプリケーション]** タブを選択し、**[追加]** をクリックします。  
![AAD テナント アプリケーション][8]

5. **[アプリケーションの追加]** で次の操作を行います。

	1. アプリケーションの **[名前]** を入力します。  
	2. アプリケーションの種類は **[Web]** のままにします。  
	3. **[次へ]** を選択します。

	![AAD アプリケーションの追加 - アプリケーション情報][9]

6. **[アプリケーションのプロパティ]** で次の操作を行います。

	1. アプリケーションの**サインオン URL** を入力します。PowerApps の AAD に対して認証するので、サインオン URL を _https://login.windows.net_ に設定します。
	2. アプリの有効な**アプリ ID URI** を入力します。  
	3. **[OK]** を選択します。  

	![AAD アプリケーションの追加 - アプリケーションのプロパティ][10]

7. 操作が正常に完了すると、新しい AAD アプリケーションにリダイレクトされます。**[構成]** をクリックします。  
![Contoso AAD アプリケーション][11]

8. _OAuth 2_ セクションの **[応答 URL]** を、(このトピックで) Azure ポータルで新しい Dynamics CRM Online API を追加したときに受け取ったリダイレクト URL に設定します。  
![Contoso AAD アプリケーションの構成][12]

9. [**保存**] を選択します。

新しい Azure Active Directory アプリケーションが作成されます。Azure ポータルの Dynamics CRM Online API 構成でこのアプリケーションを使用できます。

## REST API に関するページを参照してください。

[Dynamics CRM Online REST API](../connectors/connectors-create-api-crmonline.md) リファレンス。


## まとめと次のステップ
このトピックでは、Dynamics CRM Online API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)

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

<!---HONumber=AcomDC_0330_2016-->