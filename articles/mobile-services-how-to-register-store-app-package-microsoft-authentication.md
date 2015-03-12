<properties 
	pageTitle="Windows ストア アプリ パッケージを Microsoft 認証に登録する" 
	description="Azure Mobile Services アプリケーションで Microsoft 認証用に Windows ストア アプリを登録する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Windows ストア アプリ パッケージを Microsoft 認証に登録する

Azure のモバイル サービスでは、クライアント主導とサーバー主導の両方の認証方法がサポートされています。サーバー主導の認証では、Microsoft アカウントなどの ID プロバイダーを使用します。サーバー主導の認証で、アプリケーションをモバイル サービスに登録せずに Microsoft アカウントを使用すると、認証が必要になるたびに、ユーザーは資格情報を入力するように求められます。アプリケーションを登録すると、Microsoft アカウントのログイン資格情報がキャッシュされて認証に使われるようになるので、ユーザーが再度入力を求められることがありません。このトピックでは、認証に Azure のモバイル サービスを使用するときの Microsoft アカウントのログイン エクスペリエンスを向上させるために Windows ストア アプリケーション パッケージを登録する方法について説明します。 

>[AZURE.NOTE]Visual Studio 2013 では、Windows ストア アプリ パッケージを Mobile Services に簡単に登録できます。詳細については、Windows デベロッパー センターの<a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">モバイル サービスのプッシュ通知の追加のクイック スタートに関するページ</a>を参照してください。

クライアントが管理する認証を使用して、Windows デバイスで Live Connect を使用したシングル サインオン エクスペリエンスを提供することができます。Live Connect API を使用する場合は、このトピックの手順を行う必要はありません。詳細については、[Live Connect シングル サインオンによる Windows ストア アプリの認証]に関するトピックを参照してください。   

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

アプリケーション パッケージの登録後、<a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> メソッドを呼び出すときに、<em>useSingleSignOn</em> に値 <strong>true</strong> を指定してください。そうすることで、Microsoft アカウントを使用する場合に、向上したログイン エクスペリエンスをユーザーに提供できます。

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/
[Live Connect シングル サインオンによる Windows ストア アプリの認証]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[ユーザー C# の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[ユーザー JavaScript の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js/


<!--HONumber=42-->
