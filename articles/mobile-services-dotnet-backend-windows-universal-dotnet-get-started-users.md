<properties 
	pageTitle="認証の使用 (Windows ストア) | モバイル デベロッパー センター" 
	description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows ストア アプリのユーザーを認証する方法について説明します。" 
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
	ms.date="04/29/2015" 
	ms.author="glenga"/>

# Mobile Services アプリへの認証の追加 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

## 概要

このトピックでは、ユニバーサル Windows アプリから Azure モバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

>[AZURE.NOTE]このチュートリアルでは、Windows ストア アプリおよび Windows Phone Store 8.1 アプリでユーザーを認証する方法について説明します。Windows Phone 8.0 アプリまたは Windows Phone Silverlight 8.1 アプリの場合、このバージョンの「[モバイル サービスでの認証の使用](mobile-services-dotnet-backend-windows-phone-get-started-users.md)」を参照してください。

##<a name="register"></a>アプリケーションを認証に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="5">
<li><p>Visual Studio で、TodoList アプリの Windows ストア プロジェクトを右クリックして、<strong>[スタートアップ プロジェクトに設定]</strong> をクリックします。</p></li>
<li><p>共有プロジェクトで、App.xaml.cs プロジェクト ファイルを開き、<a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> の定義を見つけ、Azure で実行されているモバイル サービスに接続するように構成されていることを確認します。</p>
<p>Visual Studio ツールを使用してアプリをモバイル サービスに接続する場合、ツールはクライアント プラットフォームごとに 1 つずつ、<strong>MobileServiceClient</strong> 定義を合計 2 セット生成します。この機会に、<code>#if...#endif</code> でラップされた <strong>MobileServiceClient</strong> 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することができます。Azure の管理ポータルからクイック スタート アプリをダウンロードした場合は、これを行う必要はありません。</p>
</li> 
<li><p>F5 キーを押して、Windows ストア アプリを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
   
   	<p>この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、<em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

>[AZURE.NOTE]Windows ストア アプリ パッケージ情報をモバイル サービスに登録している場合は、*useSingleSignOn* パラメーターに値 **true** を指定して <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> メソッドを呼び出す必要があります。この操作を行わない場合、login メソッドが呼び出されるたびに、引き続きユーザーにログイン プロンプトが表示されます。

##<a name="tokens"></a>クライアントに認証トークンを保存する

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>次のステップ

次の[モバイル サービス ユーザーのサービス側の認証][Authorize users with scripts]チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。.NET でモバイル サービスを使用する方法の詳細については、「[モバイル サービス .NET の使用方法の概念リファレンス]」を参照してください。

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
[Single sign-on for Windows Store apps by using Live Connect]: mobile-services-windows-store-dotnet-single-sign-on.md
[モバイル サービスの使用]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=54-->