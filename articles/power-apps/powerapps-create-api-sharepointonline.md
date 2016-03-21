<properties
	pageTitle="PowerApps Enterprise への SharePoint Online API の追加 | Microsoft Azure"
	description="組織の App Service 環境での新しい SharePoint Online API の作成または構成"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# 組織の App Service 環境での新しい SharePoint Online API の作成

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*ユーザー名*@*会社名*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。
 
2. タスク バーの **[参照]** をクリックします。![][14]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。![][15]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[SharePoint Online]** を選択します。![SharePoint Online API の選択][3]

8. **[設定] - [必要な設定の構成]** を選択します。![SharePoint Online API の設定の構成][4]

9. SharePoint Online Azure Active Directory (AAD) アプリケーションの *[クライアント ID]* と *[アプリケーション シークレット]* を入力します。これらがない場合は、このトピックの「PowerApps Office 365 Users API で使用する AAD アプリケーションの登録」を参照して、必要な ID とシークレットの値を作成します。

	> [AZURE.IMPORTANT]**リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい SharePoint Online API が追加されます。


## PowerApps SharePoint Online API で使用する AAD アプリケーションの登録

1. [Azure ポータル][5] を開きます。

2. **[参照]** をクリックし、**[Active Directory]** を選択します。

	> [AZURE.NOTE]これにより、Azure クラシック ポータルで Active Directory が開きます。

3. 組織のテナント名を選択します。![Azure Active Directory の起動][6]

4. **[アプリケーション]** タブを選択し、**[追加]** をクリックします。![AAD テナント アプリケーション][7]

5. **[アプリケーションの追加]** で次の操作を行います。

	a) アプリケーションの **[名前]** を入力します。b) アプリケーションの種類は **Web** のままにしておきます。c) **[次へ]** をクリックします。


	![AAD アプリケーションの追加 - アプリケーション情報][8]

6. **[アプリケーションのプロパティ]** で次の操作を行います。

	a) アプリケーションの **[サインオン URL]** を入力します。AAD を使用して PowerApps に対する認証を行うので、サインオン URL を _https://login.windows.net_ に設定します。b) アプリケーションの有効な **[アプリケーション ID/URI]** を入力します。c) **[OK]** をクリックします。

	![AAD アプリケーションの追加 - アプリケーションのプロパティ][9]

7. 操作が正常に完了すると、新しい AAD アプリケーションにリダイレクトされます。**[構成]** をクリックします。![Contoso AAD アプリケーション][10]

8. _OAuth 2_ セクションの **[応答 URL]** を、(このトピックで) Azure ポータルで新しい SharePont Online API を追加したときに受け取ったリダイレクト URL に設定します。**[アプリケーションの追加]** をクリックします。![Contoso AAD アプリケーションの構成][11]

9. **[他のアプリケーションに対するアクセス許可]** ウィンドウで、**[Office 365 Exchange Online]** を選択し、**[OK]** をクリックします。![Contoso アプリケーションでのデリゲート][12]

10. 構成ページに戻り、_[他のアプリケーションに対するアクセス許可]_ の一覧に _Office 365 Exchange Online_ が追加されていることを確認します。

11. _Office 365 Exchange Online_ の **[デリゲートされたアクセス許可]** を選択し、次のアクセス許可を選択します。

	- すべてのサイト コレクションのアイテムの読み書き
	- すべてのサイト コレクションのアイテムとリストの読み書き

	![Contoso アプリケーションでのアクセス許可のデリゲート][13]

新しい Azure Active Directory アプリケーションが作成されます。Azure ポータルの SharePoint Online API 構成でこのアプリケーションを使用できます。

## まとめと次のステップ
このトピックでは、SharePoint Online API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-sharepointonline/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-sharepointonline/add-api.PNG
[3]: ./media/powerapps-create-api-sharepointonline/select-sharepointonline-api.PNG
[4]: ./media/powerapps-create-api-sharepointonline/configure-sharepointonline-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-sharepointonline/launch-aad.PNG
[7]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline.PNG
[13]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline-permissions.PNG
[14]: ./media/powerapps-create-api-sharepointonline/browseall.png
[15]: ./media/powerapps-create-api-sharepointonline/allresources.png

<!---HONumber=AcomDC_1203_2015-->