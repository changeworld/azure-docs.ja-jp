<properties 
	pageTitle="App Services アプリケーションに Google 認証を構成する方法"
	description="App Services アプリケーションに Google 認証を構成する方法について説明します。" 
    services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Google ログインを使用するようにアプリケーションを構成する方法

このトピックでは、認証プロバイダーとして Google を使用するように Azure App Services を構成する方法を示します。 

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>. にアクセスしてください。

## <a name="register"> </a>Google にアプリケーションを登録する

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> Web サイトに移動して、Google アカウント資格情報でサインインします。**[Create Project]** をクリックして**プロジェクト名**を入力し、**[Create]** をクリックします。

2. 左側のナビゲーション バーで、**[API & Auth]** をクリックします。次に、**[Consent screen]** をクリックします。**電子メール アドレス**を選択し、**製品名**を入力します。その後、**[保存]** をクリックします。 

3. また、**[API & Auth]** で **[APIs]** 選択して、**Google + API** を有効にします。

4. もう一度 **[API & Auth]** で、**[Credentials]**、**[Create new Client ID]** の順に選択します。

5. **[Web application]** を選択します。App Services ゲートウェイの URL を **[Authorized JavaScript Origins]** に入力します。次に **[Authorized Redirect URI]** の生成された URL をパス _/signin-google_ が末尾に追加されたゲートウェイの URL で置き換えます。例:  `https://contoso.azurewebsites.net/signin-google`.HTTPS スキームを使用していることを確認します。**[Create client ID]** をクリックします。

     ![][0]

6. 次の画面で、**[ウェブ アプリケーションのクライアント ID]** の **[Client ID]** と **[Client secret]** の値を書き留めます。 

    > [AZURE.IMPORTANT] クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。


## <a name="secrets"> </a>Google の情報をモバイル アプリに追加します。

7. [Azure の管理ポータル]にログオンし、App Services ゲートウェイに移動します。

8. **[設定]** で、**[ID]**、**[Google]** の順にクリックします。以前に入手したクライアント ID とクライアント シークレットの値を貼り付けます。その後、**[保存]** をクリックします。

     ![][1]


これで、アプリケーションで認証に Google を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-how-to-configure-google-authentication/app-service-google-redirect.png
[1]: ./media/app-service-how-to-configure-google-authentication/app-service-google-settings.png

<!-- URLs. -->

[Google API]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 管理ポータル]: https://portal.azure.com/

<!--HONumber=49-->