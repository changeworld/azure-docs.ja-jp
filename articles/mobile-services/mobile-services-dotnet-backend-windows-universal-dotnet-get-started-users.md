<properties 
	pageTitle="ユニバーサル Windows 8.1 アプリへの認証の追加 | Microsoft Azure" 
	description="Mobile Services を使用して、Google、Facebook、Twitter、および Microsoft などのさまざまな ID プロバイダーを通じてユニバーサル Windows 8.1 アプリのユーザーを認証する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="glenga"/>

# Mobile Services アプリへの認証の追加 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## 概要

このトピックでは、ユニバーサル Windows アプリから Azure Mobile Services のユーザーを認証する方法を示します。このチュートリアルでは、Mobile Services でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、Mobile Services のクイック スタートに基づいています。チュートリアルの [Mobile Services の使用]または[既存のアプリケーションへの Mobile Services の追加](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md)のいずれかを完了しておくことも必要です。

>[AZURE.NOTE]このチュートリアルでは、Windows ストア アプリおよび Windows Phone ストア 8.1 アプリで、ユーザー主導の認証によってユーザー認証する方法について説明します。クライアント主導の認証の詳細については、[Logging in with Google, Microsoft and Facebook SDKs to Azure Mobile Services (Google、、Microsoft、Facebook の SDK を使用して Azure Mobile Services にログインする)](http://azure.microsoft.com/blog/2014/10/27/logging-in-with-google-microsoft-and-facebook-sdks-to-azure-mobile-services/) を参照してください。

##<a name="register"></a>アプリケーションを認証に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;6.Visual Studio で、TodoList アプリの Windows ストア プロジェクトを右クリックして、**[スタートアップ プロジェクトに設定]** をクリックします。

&nbsp;&nbsp;7.共有プロジェクトで、App.xaml.cs プロジェクト ファイルを開き、[MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) の定義を見つけ、Azure で実行されているモバイル サービスに接続するように構成されていることを確認します。

>[AZURE.NOTE]Visual Studio ツールを使用してアプリを Mobile Services に接続する場合、ツールはクライアント プラットフォームごとに 1 つずつ、**MobileServiceClient** 定義を合計 2 セット生成します。この機会に、`#if...#endif` でラップされた **MobileServiceClient** 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することができます。Azure 管理ポータルからクイック スタート アプリをダウンロードした場合は、これを行う必要はありません。

&nbsp;&nbsp;8.F5 キーを押して、Windows ストア アプリを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。
   
&nbsp;&nbsp;この問題が発生するのは、認証されないユーザーとしてアプリケーションが Mobile Services にアクセスしようとすると、*TodoItem* テーブルで認証が要求されるようになったことが原因です。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

>[AZURE.NOTE]Windows ストア アプリ パッケージ情報をモバイル サービスに登録している場合は、*useSingleSignOn* パラメーターに値 **true** を指定して <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> メソッドを呼び出す必要があります。この操作を行わない場合、login メソッドが呼び出されるたびに、引き続きユーザーにログイン プロンプトが表示されます。

##<a name="tokens"></a>クライアントに認証トークンを保存する

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]


## <a name="next-steps"> </a>次のステップ

次の[モバイル サービス ユーザーのサービス側の認証][Authorize users with scripts]チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

##関連項目

+ [強化されたユーザー機能](http://azure.microsoft.com/blog/2014/10/02/custom-login-scopes-single-sign-on-new-asp-net-web-api-updates-to-the-azure-mobile-services-net-backend/)<br/> .NET バックエンドで **ServiceUser.GetIdentitiesAsync()** メソッドを呼び出すことによって、モバイル サービス内で ID プロバイダーが管理するユーザー データを追加取得できます。 

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
[Mobile Services の使用]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Servicesでの .NET の使用方法に関する概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
 

<!---HONumber=Oct15_HO3-->