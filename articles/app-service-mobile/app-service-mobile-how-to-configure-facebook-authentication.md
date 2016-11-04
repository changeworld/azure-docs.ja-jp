---
title: App Services アプリケーションに Facebook 認証を構成する方法
description: App Services アプリケーションに Facebook 認証を構成する方法について説明します。
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/22/2016
ms.author: mahender

---
# App Service アプリケーションを Facebook ログインを使用するように構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Facebook を使用するように Azure App Service を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントと携帯電話番号が必要になります。新しい Facebook アカウントを作成するには、[facebook.com] にアクセスしてください。

## <a name="register"> </a>Facebook にアプリケーションを登録する
1. [Azure ポータル]にログオンし、目的のアプリケーションに移動します。**[URL]** をコピーします。この URL は、Facebook アプリの構成で使用します。
2. 他のブラウザー ウィンドウで、[Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。
3. (省略可能) まだ登録していない場合は、**[Apps]**、**[Register as a Developer]** の順にクリックし、ポリシーに同意して、登録手順に従います。
4. **[My Apps]**、**[Add a New App]**、**[Website]**、**[Skip and Create App ID]** の順にクリックします。
5. **[Display Name]** にアプリの一意の名前を入力し、**[Contact Email]** を入力して、**[Category]** でアプリのカテゴリを選択します。次に、**[Create App ID]** をクリックし、セキュリティ チェックを完了します。これで、新しい Facebook アプリケーションの開発者向けダッシュボードに移動します。
6. "Facebook ログイン" で **[Get Started]** をクリックします。**[Valid OAuth redirect URIs]** にアプリケーションの**リダイレクト URI** を追加して、**[Save Changes]** をクリックします。
   
   > [!NOTE]
   > リダイレクト URI は、アプリケーションの URL にパス */.auth/login/facebook/callback* を追加したものです。たとえば、「`https://contoso.azurewebsites.net/.auth/login/facebook/callback`」のように入力します。HTTPS スキームを使用していることを確認します。
   > 
   > 
7. 左側のナビゲーションで、クリックして **[Settings]** をクリックします。**[App Secret]** フィールドで **[Show]** をクリックし、要求された場合はパスワードを入力して、**[App ID]** と **[App Secret]** の値をメモしておきます。後でアプリケーションを Azure で構成するときにこれらの値を使用します。
   
   > [!IMPORTANT]
   > アプリケーション シークレットは、重要なセキュリティ資格情報です。このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
   > 
   > 
8. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。この時点では、管理者のみがこのアプリケーションにサインインできます。他の Facebook アカウントを認証するには、**[App Review]** をクリックし、**[Make <アプリケーション名> public]** を有効にして、Facebook 認証を使用した汎用パブリック アクセスを有効にします。

## <a name="secrets"> </a>Facebook の情報をアプリケーションに追加する
1. [Azure ポータル]に戻り、アプリケーションに移動します。**[設定]**、**[認証/承認]** の順にクリックし、**[App Service 認証]** が **[オン]** になっていることを確認します。
2. **[Facebook]** をクリックし、前の手順で取得した App ID と App Secret の値を貼り付けます。必要に応じて、アプリケーションで必要なスコープを有効にし、**[OK]** をクリックします。
   
    ![][0]
   
    App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。アプリケーション コードでユーザーを承認する必要があります。
3. (省略可能) Facebook によって認証されたユーザーしかサイトにアクセスできないように制限するには、**[要求が認証されていないときに実行するアクション]** を **[Facebook]** に設定します。この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Facebook にリダイレクトされます。
4. 認証の構成が終了したら、**[保存]** をクリックします。

これで、アプリケーションで認証に Facebook を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /ja-JP/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure ポータル]: https://portal.azure.com/

<!---HONumber=AcomDC_0824_2016-->