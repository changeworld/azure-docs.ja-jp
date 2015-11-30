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
	ms.date="10/29/2015"
	ms.author="mahender"/>

# App Service アプリケーションを Facebook ログインを使用するように構成する方法

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このトピックでは、認証プロバイダーとして Facebook を使用するように Azure App Service を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、[facebook.com] にアクセスしてください。


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Facebook にアプリケーションを登録する

1. [Microsoft Azure 管理ポータル]にログオンし、目的のアプリケーションに移動します。**[URL]** をコピーします。この URL は、Facebook アプリの構成で使用します。
 
2. 他のブラウザー ウィンドウで、[Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

3. (省略可能) まだ登録していない場合は、**[Apps]**、**[Register as a Developer]** の順にクリックし、ポリシーに同意して、登録手順に従います。

4. **[My Apps]** をクリックし、**[Add a New App]** をクリックします。

5. プラットフォームとして **[Website]** を選択します。アプリケーションの一意の名前を選択して、**[Create New Facebook App ID]** をクリックします。

6. ドロップダウン リストから、アプリケーションのカテゴリを選択します。**[Create App ID]** をクリックします。

7. 次のページで、右上の **[Skip Quick Start]** を選択します。これで、アプリケーションの開発者向けダッシュボードに移動します。

8. **[App Secret]** フィールドで **[Show]** をクリックし、要求された場合はパスワードを入力して、**[App ID]** と **[App Secret]** の値をメモしておきます。後で、これらを使用するようにアプリケーションを構成します。

	> [AZURE.NOTE]**セキュリティ上の注意** アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

9. 左側のナビゲーション バーで、**[Settings]** をクリックします。**[App Domains]** にモバイル アプリの **URL** を入力し、**[Contact Email]** に連絡先のメール アドレスを入力します。

    ![][0]

10. 下に Web サイト セクションが表示されない場合は、**[Add Platform]** をクリックして、**[Website]** を選択します。**[Site URL]** フィールドにモバイル アプリの **URL** を入力し、**[Save Changes]** をクリックします。

11. **[Advanced]** タブをクリックし、**[Valid OAuth redirect URIs]** にアプリケーションの**リダイレクト URI** を追加します。次に、**[Save Changes]** をクリックします。リダイレクト URI は、アプリケーションの URL にパス _/.auth/login/facebook/callback_ を追加したものです。たとえば、「`https://contoso.azurewebsites.net/.auth/login/facebook/callback`」のように入力します。HTTPS スキームを使用していることを確認します。


	> [AZURE.NOTE]App Service の認証/承認機能ではなく、App Service ゲートウェイを使用している場合、リダイレクト URL では、ゲートウェイ URL とパス _/signin-facebook_ を使用します。


12. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。この時点では、管理者のみがこのアプリケーションにサインインできます。他の Facebook アカウントを認証するには、左側のナビゲーション バーの **[Status & Review]** をクリックします。その後 **[はい]** をクリックして汎用パブリック アクセスを有効にします。


## <a name="secrets"> </a>Facebook の情報をアプリケーションに追加する


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Facebook**. Paste in the values you obtained earlier and click **Save**.


13. [Microsoft Azure 管理ポータル]に戻り、目的のアプリケーションに移動します。**[設定]**、**[認証/承認]** の順にクリックします。

14. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。

15. **[Facebook]** をクリックします。前の手順で取得した App ID と App Secret の値を貼り付けます。アプリケーションで必要なスコープを有効にします (省略可能)。次に、 **[OK]** をクリックします

    ![][1]
	
16. 既定では、App Service はログインを提供しますが、サイトのコンテンツと API へのアクセスは制限しません。アクセスの制限はアプリケーション コードで行います。Facebook ログインからサイトを完全に保護する場合は、**[要求が認証されていないときに実行するアクション]** ドロップダウンを **[Facebook]** オプションを使用するように変更します。これにより、すべての要求で認証が必要になり、認証されていない要求は、Facebook を使用してログインするようにリダイレクトされます。

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
[Microsoft Azure 管理ポータル]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->