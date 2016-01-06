<properties
	pageTitle="App Services アプリケーションに Facebook 認証を構成する方法"
	description="App Services アプリケーションに Facebook 認証を構成する方法について説明します。"
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
	ms.date="11/20/2015"
	ms.author="mahender"/>

# App Service アプリケーションを Facebook ログインを使用するように構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、認証プロバイダーとして Facebook を使用するように Azure App Service を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、[facebook.com] にアクセスしてください。

> [AZURE.NOTE]このトピックでは、App Service 認証/承認の使用例を示します。ほとんどのアプリケーションで、これが App Service ゲートウェイに取って代わっています。ゲートウェイを使用する場合との相違点は、トピック全体を通してメモに記載しています。


## <a name="register"> </a>Facebook にアプリケーションを登録する

1. [Azure ポータル]にログオンし、目的のアプリケーションに移動します。**[URL]** をコピーします。この URL は、Facebook アプリの構成で使用します。
 
2. 他のブラウザー ウィンドウで、[Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

3. (省略可能) まだ登録していない場合は、**[Apps]**、**[Register as a Developer]** の順にクリックし、ポリシーに同意して、登録手順に従います。

4. **[My Apps]**、**[Add a New App]**、**[Website]** の順にクリックし、アプリの一意の名前を入力して、**[Create New Facebook App ID]** をクリックします。

6. ドロップダウンからアプリケーションのカテゴリを選択し、**[Create App ID]** をクリックします。次のページで **[Skip Quick Start]** をクリックします。これで、アプリケーションの開発者向けダッシュボードに移動します。

8. **[App Secret]** フィールドで **[Show]** をクリックし、要求された場合はパスワードを入力して、**[App ID]** と **[App Secret]** の値をメモしておきます。後で、これらを使用するようにアプリケーションを構成します。

	> [AZURE.NOTE]**セキュリティ上の注意** アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

9. 左側のナビゲーション バーの **[Settings]** をクリックします。**[App Domains]** にモバイル アプリの **URL** を入力し、**[Contact Email]** に連絡先のメール アドレスを入力します。

    ![][0]

10. 下に Web サイト セクションが表示されていない場合は、**[Add Platform]**、**[Website]** の順にクリックし、**[Site URL]** フィールドにモバイル アプリの **URL** を入力して、**[Save Changes]** をクリックします。

11. **[Advanced]** タブをクリックし、**[Valid OAuth redirect URIs]** にアプリケーションの**リダイレクト URI** を追加して、**[Save Changes]** をクリックします。リダイレクト URI は、アプリケーションの URL にパス _/.auth/login/facebook/callback_ を追加したものです。たとえば、「`https://contoso.azurewebsites.net/.auth/login/facebook/callback`」のように入力します。HTTPS スキームを使用していることを確認します。


> [AZURE.NOTE]App Service の認証/承認機能ではなく、App Service ゲートウェイを使用している場合、リダイレクト URL では、ゲートウェイ URL とパス _/signin-facebook_ を使用します。


12. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。この時点では、管理者のみがこのアプリケーションにサインインできます。他の Facebook アカウントを認証するには、左側のナビゲーション バーの **[Status & Review]** をクリックします。その後 **[はい]** をクリックして汎用パブリック アクセスを有効にします。


## <a name="secrets"> </a>Facebook の情報をアプリケーションに追加する

> [AZURE.NOTE]App Service ゲートウェイを使用する場合は、このセクションの内容は無視し、ポータル内で目的のゲートウェイに移動します。**[設定]**、**[ID]**、**[Facebook]** の順に選択します。前に取得した値を貼り付け、**[保存]** をクリックします。


13. [Azure ポータル]に戻り、目的のアプリケーションに移動します。**[設定]**、**[認証/承認]** の順にクリックし、**[App Service 認証]** が **[オン]** になっていることを確認します。

15. **[Facebook]** をクリックし、前の手順で取得した App ID と App Secret の値を貼り付けます。必要に応じて、アプリケーションで必要なスコープを有効にし、**[OK]** をクリックします。

    ![][1]
	
	App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。アプリケーション コードでユーザーを承認する必要があります。

17. (省略可能) Facebook によって認証されたユーザーしかサイトにアクセスできないように制限するには、**[要求が認証されていないときに実行するアクション]** を **[Facebook]** に設定します。この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Facebook にリダイレクトされます。

17. **[保存]** をクリックします。

これで、アプリケーションで認証に Facebook を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /ja-JP/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure ポータル]: https://portal.azure.com/

<!----HONumber=AcomDC_1203_2015-->