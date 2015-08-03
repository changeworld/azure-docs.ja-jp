<properties
	pageTitle="App Services アプリケーションに Google 認証を構成する方法"
	description="App Services アプリケーションに Google 認証を構成する方法について説明します。"
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Google ログインを使用するようにアプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Azure App Services を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

## <a name="register"> </a>Google にアプリケーションを登録する

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

2. 左側のナビゲーション バーで、**[API と認証]** をクリックします。次に、**[同意画面]** をクリックします。**[メール アドレス]** を選択し、**サービス名**を入力します。その後、**[保存]** をクリックします。

3. また、**[API と認証]** で **[API]** を選択して、**[Google+ API]** を有効にします。

4. もう一度 **[API と認証]** で、**[認証情報]**、**[新しいクライアント ID を作成]** の順に選択します。

5. **[ウェブ アプリケーション]** を選択します。App Services ゲートウェイの URL を **[承認済みの JavaScript 生成元]** に入力します。次に **[承認済みのリダイレクト URI]** の生成された URL を、パス _/signin-google_ が末尾に追加されたゲートウェイの URL で置き換えます。たとえば、「`https://contoso.azurewebsites.net/signin-google`」のように入力します。HTTPS スキームを使用していることを確認します。**[クライアント ID を作成]** をクリックします。

     ![][0]

6. 次の画面で、**[ウェブ アプリケーションのクライアント ID]** の **[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

    > [AZURE.IMPORTANT]クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。


## <a name="secrets"> </a>Google の情報をモバイル アプリに追加する

7. [Azure の管理ポータル]にログオンし、App Services ゲートウェイに移動します。

8. **[設定]** で、**[ID]**、**[Google]** の順にクリックします。以前に入手したクライアント ID とクライアント シークレットの値を貼り付けます。その後、**[保存]** をクリックします。

     ![][1]


これで、アプリケーションで認証に Google を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure の管理ポータル]: https://portal.azure.com/
 

<!---HONumber=July15_HO4-->