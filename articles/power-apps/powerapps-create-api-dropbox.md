<properties
	pageTitle="PowerApps Enterprise への Dropbox API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい Dropbox API を作成または構成します。"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# 組織の App Service 環境での新しい Dropbox API の作成

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*ユーザー名*@*会社名*.com でサインインします。これにより、自動的に会社のサブスクリプションにサインインされます。
 
2. タスク バーの **[参照]** をクリックします。  
![][12]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「powerapps」と入力します。  
![][13]

4. **[PowerApps]** で、**[API の管理]** を選択します。  
![登録されている API の参照][4]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。  
![Add API][5]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を表示し、**[Dropbox]** を選択します。  
![Dropbox API の選択][6]

8. **[設定] - [必要な設定の構成]** を選択します。  
![Dropbox API 設定の構成][7]

9. Dropbox アプリケーションの**アプリケーション キー**と**アプリケーション シークレット**の値を入力します。これらがまだない場合は、このトピックの「PowerApps で使用する Dropbox アプリケーションの登録」を参照して、必要なキーとシークレットの値を作成します。

	> [AZURE.IMPORTANT]**リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。


完了すると、App Service 環境に新しい Dropbox API が追加されます。


## 省略可能: PowerApps で使用する Dropbox アプリケーションの登録

キーとシークレットの値が割り当てられた既存の Dropbox アプリケーションがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。

1. [Dropbox][1] に移動し、ご使用のアカウントでサインインします。

2. Dropbox 開発者向けサイトに移動し、**[My Apps]** を選択します。  
![Dropbox 開発者向けサイト][8]

3. **[Create app]** をクリックします。  
![Dropbox でのアプリケーションの作成][9]

4. **[Create a new app on the Dropbox platform]** で次の操作を行います。

	a) **[Choose API]** で **[Dropbox API]** を選択します。b) **[Choose the type of access you need]** で **[Full Dropbox...]** を選択します。c) アプリケーションの名前を入力します。

	![Dropbox のアプリケーション作成ページ 1][10]

5. アプリケーション設定ページで次の操作を行います。

	**[OAuth 2]** セクションで、**[Redirect URL]** を、(このトピックで) Azure ポータルで新しい Dropbox API を追加したときに受け取ったリダイレクト URL に設定します。**[Add]** をクリックします。b) **[Show]** リンクをクリックして、**アプリケーション シークレット**を表示します。

	![Dropbox のアプリケーション作成ページ 2][11]

新しい Dropbox アプリケーションが作成されます。Azure ポータルの Dropbox API 構成でこのアプリケーションを使用できます。


## まとめと次のステップ
このトピックでは、Dropbox API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

<!----HONumber=AcomDC_1203_2015-->