<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Windows ストア アプリ パッケージを Microsoft 認証に登録する

Azure のモバイル サービスでは、クライアント主導とサーバー主導の両方の認証方法がサポートされています。サーバー主導の認証では、Microsoft アカウントなどの ID プロバイダーを使用します。サーバー主導の認証で、アプリケーションをモバイル サービスに登録せずに Microsoft アカウントを使用すると、認証が必要になるたびに、ユーザーは資格情報を入力するように求められます。アプリケーションを登録すると、Microsoft アカウントのログイン資格情報がキャッシュされて認証に使われるようになるので、ユーザーが再度入力を求められることがありません。このトピックでは、認証に Azure のモバイル サービスを使用するときの Microsoft アカウントのログイン エクスペリエンスを向上させるために Windows ストア アプリケーション パッケージを登録する方法について説明します。

> [WACOM.NOTE] Visual Studio 2013 では、Windows ストア アプリ パッケージをモバイル サービスに簡単に登録できます。詳細については、Windows デベロッパー センターの[モバイル サービスのプッシュ通知の追加のクイック スタートに関するページ][モバイル サービスのプッシュ通知の追加のクイック スタートに関するページ]を参照してください。

クライアントが管理する認証を使用して、Windows デバイスで Live Connect を使用したシングル サインオン エクスペリエンスを提供することができます。Live Connect API を使用する場合は、このトピックの手順を行う必要はありません。詳細については、「[Live Connect シングル サインオンによる Windows ストア アプリケーションの認証][Live Connect シングル サインオンによる Windows ストア アプリケーションの認証]」を参照してください。

> [WACOM.NOTE] Live Connect を使用するクライアント管理の認証は .NET バックエンド モバイル サービスではまだサポートされません。

[WACOM.INCLUDE [mobile-services-register-windows-store-app][mobile-services-register-windows-store-app]]

アプリケーション パッケージの登録後、[LoginAsync][LoginAsync] メソッドを呼び出すときに、*useSingleSignOn* に値 **true** を指定してください。そうすることで、Microsoft アカウントを使用する場合に、向上したログイン エクスペリエンスをユーザーに提供できます。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [モバイル サービスのプッシュ通知の追加のクイック スタートに関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=309101
  [Live Connect シングル サインオンによる Windows ストア アプリケーションの認証]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [LoginAsync]: http://go.microsoft.com/fwlink/p/?LinkId=311594
