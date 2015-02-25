<properties pageTitle="Microsoft 認証用の登録 - モバイル サービス" description="Azure Mobile Services アプリケーションで Microsoft 認証用に登録する方法について説明します。" authors="ggailey777" services="mobile-services" documentationCenter="" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# Microsoft アカウント ログインを使用するためのアプリケーションの登録

このトピックでは、Azure のモバイル サービスの認証プロバイダーとして Live Connect を使用できるようにアプリケーションを登録する方法について説明します。 

>[AZURE.NOTE]ユニバーサル Windows アプリ用の Microsoft アカウント認証を構成する場合、または、Windows ストア アプリ用にシングル サインオン エクスペリエンスを提供する場合は、「[Windows ストア アプリ パッケージを Microsoft 認証に登録する](/ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication)」を参照してください。

1. Live Connect デベロッパー センター アプリケーション管理の <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">[マイ アプリケーション]</a> ページに移動し、必要な場合は Microsoft アカウントでログオンします。 

2. **[アプリケーションの作成]** をクリックし、**[アプリケーション名]** に名前を入力して、**[同意する]** をクリックします。

   	![][1] 

   	これでアプリケーションが Live Connect に登録されます。

3. **[API 設定]** をクリックして、**[リダイレクト URL]** に `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` の値を入力し、**[保存]** をクリックします。

	>[AZURE.NOTE]Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL は、ユーザーのモバイル サービスの URL の末尾に .NET サービスとしてパス _signin-microsoft_ を付加したものです。例: <code>https://todolist.azure-mobile.net/signin-microsoft</code>。  

	![][3]

	これで、アプリでの Microsoft アカウント認証が可能になります。

	>[AZURE.NOTE]既存の Live Connect アプリの登録には、まず **[リダイレクト セキュリティ強化]** を有効にする必要があります。

4. **[アプリ設定]** をクリックして、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。 

   	![][2]

    > [AZURE.NOTE] クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットは、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

これで、クライアント ID とクライアント シークレットの値をモバイル サービスに渡すことにより、Microsoft アカウントを使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Azure の管理ポータル]: https://manage.windowsazure.com/


<!--HONumber=42-->
