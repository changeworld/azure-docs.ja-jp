<properties
	pageTitle="Facebook 認証用に登録する | Azure Mobile Services"
	description="Azure Mobile Services アプリケーションで Facebook 認証を使用する方法について説明します。"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# Mobile Services での Facebook 認証用のアプリケーションの登録

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)] &nbsp;


[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

このトピックでは、Facebook を使用して Azure Mobile Services で認証できるようにアプリケーションを登録する方法について説明します。このページはアプリケーションへユーザーをログインさせる方法を説明する「[認証の使用](mobile-services-ios-get-started-users.md)」チュートリアルをサポートしています。モバイル サービスを初めて使用する場合は、チュートリアル「[モバイル サービスの使用](mobile-services-ios-get-started.md)」を完了することをお勧めします。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントおよび携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、[facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285) にアクセスしてください。

1. [Facebook Developers](http://go.microsoft.com/fwlink/p/?LinkId=268285) の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

2. (省略可能) まだ登録していない場合は、[**My Apps**]、[**Register as a Developer**] の順にクリックし、ポリシーに同意して、登録手順に従います。

3. **[My Apps]**、**[Add a New App]**、**[Website]**、**[Skip and Create App ID]** の順にクリックします。

4. **[Display name]** にアプリケーションの一意の名前を入力し、**[Category]** でアプリケーションのカテゴリを選択します。次に、**[Create App ID]** をクリックし、セキュリティ チェックを完了します。これで、新しい Facebook アプリケーションの開発者向けダッシュボードに移動します。

5. **[App Secret]** フィールドで **[Show]** をクリックし、要求された場合はパスワードを入力して、**[App ID]** と **[App Secret]** の値をメモしておきます。後でアプリケーションを Azure で構成するときにこれらの値を使用します。

	> [AZURE.NOTE] **セキュリティ上の注意** アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

5. 左側のナビゲーション バーで、**[Settings]** をクリックします。**[App Domains]** にモバイル サービスのドメインを入力し、必要に応じて **[Contact Email]** に入力します。**[Add Platform]** をクリックし、**[Website]** を選択します。

   	![][3]

6. **[Site URL]** にモバイル サービスの URL を入力し、**[Save Changes]** をクリックします。

7. **[Show]** をクリックし、入力を求められたらパスワードを入力し、**[App ID]** と **[App Secret]** の値を書き留めておきます。

   	![][5] &nbsp;

    >[AZURE.IMPORTANT] アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。&nbsp;

8. **[Advanced]** タブをクリックし、次のいずれかの URL 形式を **[Valid OAuth redirect URIs]** に入力して、**[Save Changes]** をクリックします。

	+ **.NET バックエンド**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **JavaScript バックエンド**: `https://<mobile_service>.azure-mobile.net/login/facebook`

	 >[AZURE.NOTE]*HTTPS* スキームを使用して、Mobile Services バックエンドの種類に合ったリダイレクト URL パスの形式を使用してください。これが正しくない場合、認証は失敗します。


12. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。この時点では、管理者のみがこのアプリケーションにサインインできます。他の Facebook アカウントを認証するには、**[App Review]** をクリックし、**[Make todolist-complete-nodejs public]** を有効にして、Facebook 認証を使用した汎用パブリック アクセスを有効にします。

これで、App ID と App Secret の値を Mobile Services に渡すことにより、Facebook ログインを使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!---HONumber=AcomDC_0727_2016-->