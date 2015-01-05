<properties urlDisplayName="Shared Access Signature Part 1" pageTitle="Windows ストア アプリ パッケージを Microsoft 認証に登録する" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Windows ストア アプリ パッケージを Microsoft 認証に登録する

Azure のモバイル サービスでは、クライアント主導とサーバー主導の両方の認証方法がサポートされています。サーバー主導の認証では、Microsoft アカウントなどの ID プロバイダーを使用します。サーバー主導の認証で、アプリケーションをモバイル サービスに登録せずに Microsoft アカウントを使用すると、認証が必要になるたびに、ユーザーは資格情報を入力するように求められます。アプリケーションを登録すると、Microsoft アカウントのログイン資格情報がキャッシュされて認証に使われるようになるので、ユーザーが再度入力を求められることがありません。このトピックでは、認証に Azure のモバイル サービスを使用するときの Microsoft アカウントのログイン エクスペリエンスを向上させるために Windows ストア アプリケーション パッケージを登録する方法について説明します。 

>[WACOM.NOTE]Visual Studio 2013 では、Windows ストア アプリ パッケージを Mobile Services に簡単に登録できます。詳細については、Windows デベロッパー センターで、「 <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">クイック スタート:モバイル サービスのプッシュ通知を追加</a> 」を参照してください。

クライアントが管理する認証を使用して、Windows デバイスで Live Connect を使用したシングル サインオン エクスペリエンスを提供することができます。Live Connect API を使用する場合は、このトピックの手順を行う必要はありません。詳細については、「[Live Connect シングル サインオンによる Windows ストア アプリの認証]」を参照してください。   

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

アプリ パッケージを登録した後、値  <strong>true</strong> を <em>useSingleSignOn</em> に、 <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> メソッドを呼び出すときに追加します。そうすることで、Microsoft アカウントを使用する場合に、向上したログイン エクスペリエンスをユーザーに提供できます。

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/
[Live Connect シングル サインオンによる Windows ストア アプリケーションの認証]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Get started with users C# (ユーザー C# の使用)]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with users JavaScript (ユーザー JavaScript の使用)]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=35.1-->
