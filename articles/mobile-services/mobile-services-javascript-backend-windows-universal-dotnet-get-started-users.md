<properties 
	pageTitle="ユニバーサル Windows 8.1 アプリへの認証の追加 | Azure Mobile Services"
	description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows ストア アプリのユーザーを認証する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/14/2015" 
	ms.author="glenga"/>

# ユニバーサル Windows 8.1 アプリへの認証の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、ユニバーサル Windows 8.1 アプリから Azure モバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、Mobile Services のクイック スタートに基づいています。チュートリアルの「[Mobile Services を使い始める]」または「[既存のアプリに Mobile Services を追加する](mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md)」のいずれかを完了しておくことも必要です。

>[AZURE.NOTE]このチュートリアルでは、Windows ストア アプリおよび Windows Phone ストア 8.1 アプリでユーザーを認証する方法について説明します。Windows Phone 8.0 アプリまたは Windows Phone Silverlight 8.1 アプリの場合、このバージョンの「[モバイル サービスでの認証の使用](mobile-services-windows-phone-get-started-users.md)」を参照してください。

>このチュートリアルでは、さまざまな ID プロバイダーを使用した、サービスによって管理される認証フローについて説明します。この方法は構成が容易で、複数のプロバイダーをサポートしています。Windows Phone アプリで Live SDK を使用する、クライアントによって管理される認証フローを使用する場合は、トピック「[Microsoft アカウントを使用して、クライアントによって管理される認証で Windows ストア アプリを認証する](mobile-services-windows-store-dotnet-single-sign-on.md)」を参照してください。

##<a name="register"></a> アプリケーションを認証に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a> アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]
 
>[AZURE.NOTE]Visual Studio ツールを使用してアプリをモバイル サービスに接続する場合、ツールはクライアント プラットフォームごとに 1 つずつ、**MobileServiceClient** 定義を合計 2 セット生成します。この機会に、`#if...#endif` でラップされた **MobileServiceClient** 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することができます。Azure の管理ポータルからクイック スタート アプリをダウンロードした場合は、これを行う必要はありません。

##<a name="add-authentication"></a> アプリケーションに認証を追加する

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

これで、信頼できる ID プロバイダーによって認証されたユーザーは、*TodoItem* テーブルにアクセスできます。ユーザー固有データのセキュリティを強化するには、承認を実装する必要もあります。これを行うには、特定のユーザーのユーザー ID を取得します。この ID を使用することで、特定のリソースに対してユーザーが有する必要のあるアクセス権のレベルを特定できます。

##<a name="tokens"></a>クライアント側で認証トークンを保存する

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>次のステップ

次の[モバイル サービス ユーザーのサービス側の認証](mobile-services-javascript-backend-service-side-authorization.md)チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

##関連項目

+ [強化されたユーザー機能](http://go.microsoft.com/fwlink/p/?LinkId=506605)<br/> サーバー スクリプトの中で **user.getIdentities()** 関数を呼び出すことによって、モバイル サービス内で ID プロバイダーが管理する追加のユーザー データを取得できます、 

+ [Mobile Servicesでの .NET の使用方法に関する概念リファレンス]<br/>Mobile Services で .NET クライアントを使用する方法について説明します。


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Mobile Services を使い始める]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: mobile-services-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Servicesでの .NET の使用方法に関する概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
 

<!---HONumber=Sept15_HO3-->