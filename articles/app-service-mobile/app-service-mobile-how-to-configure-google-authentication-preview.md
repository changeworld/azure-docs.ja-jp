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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Google ログインを使用するようにアプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Mobile Apps を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。

## <a name="register"> </a>Google にアプリケーションを登録する


1. [Azure の管理ポータル]にログオンし、モバイル アプリに移動します。**[URL]** をコピーします。この URL は、後で Google アプリで使用します。
 
2. **[設定]**、**[ユーザー認証]**、**[Google]** の順にクリックします。**[リダイレクト URI]** をコピーします。この URI は、Google アプリの構成に使用します。

3. <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

4. 左側のナビゲーション バーで、**[API と認証]** をクリックします。次に、**[同意画面]** をクリックします。**[メール アドレス]** を選択し、**サービス名**を入力します。その後、**[保存]** をクリックします。

5. また、**[API と認証]** で **[API]** を選択して、**[Google+ API]** を有効にします。**ソーシャル API** の下にあります。**Google + API** を検索することもできます。

6. もう一度 **[API と認証]** で、**[認証情報]**、**[新しいクライアント ID を作成]** の順に選択します。

7. **[ウェブ アプリケーション]** を選択します。**[Authorized JavaScript Origins]** に先ほどコピーした **[URL]** を貼り付け、**[Authorized Redirect URI]** で生成された URL を先ほどコピーしたモバイル アプリの **[リダイレクト URI]** に置換します。この URI は、モバイル アプリ ゲートウェイの後にパス _/signin-google_ を追加したフォーマットです。たとえば、「`https://contosogateway.azurewebsites.net/signin-google`」のように入力します。HTTPS スキームを使用していることを確認します。**[クライアント ID を作成]** をクリックします。

     ![][0]

8. 次の画面で、**[ウェブ アプリケーションのクライアント ID]** の **[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

    > [AZURE.IMPORTANT]クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。


## <a name="secrets"> </a>Google の情報をモバイル アプリに追加する

7. [Azure の管理ポータル]に戻り、モバイル アプリの [Google 設定] ブレードに、以前に入手したクライアント ID とクライアント シークレットの値を貼り付けます。その後、**[保存]** をクリックします。

     ![][1]


これで、アプリケーションで認証に Google を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure の管理ポータル]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->