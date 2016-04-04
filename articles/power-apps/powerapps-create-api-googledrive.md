<properties
	pageTitle="PowerApps Enterprise への Google Drive API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい Google Drive API を作成または構成します。"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# PowerApps Enterprise で新しい Google Drive API を作成する

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-googledrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-googledrive.md)

組織の (テナント) App Service 環境に新しい Google Drive API を追加する

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。
 
2. タスク バーの **[参照]** をクリックします。![][15]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。![][16]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Google Drive]** を選択します。![Google Drive API を選択する][3]

8. **[設定] - [必要な設定の構成]** を選択します。![Google Drive API の設定を構成する][4]

9. Google Drive アプリケーションの*アプリケーション キー*と*アプリケーション シークレット*を入力します。これらがない場合は、このトピックの「PowerApps で使用する Google Drive アプリの登録」を参照して、必要なキーとシークレットの値を作成します。

	> [AZURE.IMPORTANT] **リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい Google Drive API が追加されます。


## 省略可能: PowerApps で使用する Google Drive アプリの登録

キーとシークレットの値が割り当てられた既存の Google Drive アプリがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。

1. [Google Developers Console][5] にサインインします。 ![Google Developers Console][6]

2. **[Create an empty project]** を選択します。

3. アプリケーションの名前を入力し、利用規約に同意して、**[Create]** を選択します。 ![新しい Google Drive プロジェクトを作成する][7]

4. 新しいプロジェクトが正常に作成されたら、**[Use Google APIs]** を選択します。 ![Google API を使用する][8]

5. 概要ページで、**[Drive API]** を選択します。![Google Drive API の概要][9]

6. **[Enable API]** を選択します。 ![Google Drive API を有効にする][10]

7. Drive API を有効にするときは、**[Credentials]** および **[OAuth 2.0 Client ID]** を選択します。 ![資格情報を追加する][12]

8. **[Configure consent screen]** を選択します。

9. **[OAuth consent screen]** タブで、**[Product Name]** を入力し、**[Save]** を選択します。 ![Configure consent screen][13]

10. [Create Client ID] ページで次のようにします。

	1. **[Application type]** で **[Web application]** を選択します。  
	2.  クライアントの名前を入力します。  
	3. Redirect URL を、(このトピックで) Azure ポータルで新しい Google Drive API を追加したときに受け取ったリダイレクト URL に設定します。  
	4. **[作成]** を選択します。  

	![クライアント ID を作成する][14]

11. 登録したアプリケーションのクライアント ID とクライアント シークレットが表示されます。

新しい Google Drive アプリが作成されます。Azure ポータルの Google Drive API 構成でこのアプリケーションを使用できます。

## REST API に関するページを参照してください。

[Google Drive REST API](../connectors/create-api-googledrive.md) リファレンス。

## まとめと次のステップ
このトピックでは、Google Drive API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!---HONumber=AcomDC_0309_2016-->