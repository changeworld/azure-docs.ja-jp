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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Facebook ログインを使用するアプリケーションを構成する方法

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、認証プロバイダーとして Facebook を使用するように Azure モバイル アプリを構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、[facebook.com] にアクセスしてください。

## <a name="register"> </a>Facebook にアプリケーションを登録する

1. [Azure の管理ポータル]にログオンし、モバイル アプリに移動します。**[URL]** をコピーします。この URL は、Facebook アプリの構成で使用します。
 
2. **[設定]**、**[ユーザー認証]**、**[Facebook]** の順にクリックします。次に、Facebook ブレードから **リダイレクト URI** をコピーします。リダイレクト URI を Facebook アプリで使用します。
 
3. 他のブラウザー ウィンドウで、[Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

4. (省略可能) まだ登録していない場合は、**[Apps]**、**[Register as a Developer]** の順にクリックし、ポリシーに同意して、登録手順に従います。

5. **[My Apps]** をクリックし、**[Add a New App]** をクリックします。

6. プラットフォームとして **[Website]** を選択します。アプリケーションの一意の名前を選択して、**[Create New Facebook App ID]** をクリックします。

7. ドロップダウン リストから、アプリケーションのカテゴリを選択します。**[Create App ID]** をクリックします。

8. 次のページで、右上の **[Skip Quick Start]** を選択します。これで、アプリケーションの開発者向けダッシュ ボードに移動します。

9. **[App Secret]** フィールドで **[Show]** をクリックし、要求された場合はパスワードを入力して、**[App ID]** と **[App Secret]** の値をメモしておきます。これらの設定はモバイル アプリの Facebook 認証設定ブレードにて行います。

	> [AZURE.NOTE]**セキュリティ上の注意** アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

10. 左側のナビゲーション バーで、**[Settings]** をクリックします。**[App Domains]** にモバイル アプリの **URL** を入力し、**[Contact Email]** に連絡先のメール アドレスを入力します。

    ![][0]

11. 下に Web サイト セクションが表示されない場合は、**[Add Platform]** をクリックして、**[Website]** を選択します。**[Site URL]** フィールドにモバイル アプリの **URL** を入力し、**[Save Changes]** をクリックします。

12. **[Advanced]** タブをクリックし、**[Valid OAuth redirect URIs]** にさきほどコピーしたモバイル アプリの**リダイレクト URI** を追加します。次に、**[Save Changes]** をクリックします。リダイレクト URI は、パス _/signin-facebook_ が末尾に追加されたモバイル アプリのゲートウェイの URL です。たとえば、「`https://contosogateway.azurewebsites.net/signin-facebook`」のように入力します。HTTPS スキームを使用していることを確認します。

13. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。この時点では、管理者のみがこのアプリケーションにサインインできます。他の Facebook アカウントを認証するには、左側のナビゲーション バーの **[Status & Review]** をクリックします。その後 **[はい]** をクリックして汎用パブリック アクセスを有効にします。


## <a name="secrets"> </a>Facebook の情報をモバイル アプリに追加する


12. [Azure 管理ポータル]に戻り、モバイル アプリのFacebook 設定ブレードに再び移動します。以前に入手したアプリ ID とアプリ シークレットの値を貼り付けます。その後、**[保存]** をクリックします。

    ![][1]

これで、アプリケーションで認証に Facebook を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure の管理ポータル]: https://portal.azure.com/
[Azure 管理ポータル]: https://portal.azure.com/
 

<!---HONumber=Oct15_HO3-->