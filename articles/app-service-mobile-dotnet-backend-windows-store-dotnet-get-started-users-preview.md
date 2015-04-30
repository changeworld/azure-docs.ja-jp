<properties 
	pageTitle="Windows でのモバイル アプリの認証の使用" 
	description="モバイル アプリを使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows アプリのユーザーを認証する方法について説明します。" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="mahender"/>

# Windows アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル アプリによって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル アプリのクイック スタートに基づいています。先に[モバイル アプリの使用]に関するチュートリアルを完了している必要があります。 

## <a name="register"></a>アプリを認証に登録し、App Service を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li>Visual Studio でクライアント アプリケーション プロジェクトを開き、App.xaml.cs で <b>MobileServiceClient</b> のインスタンスがモバイル アプリ リソースへのクラウド URL で使用されるように構成します。</li> 
<li><p>F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
   
   	<p>この問題は、認証されないユーザーとしてアプリケーションがモバイル アプリ コードにアクセスしようとしているのに、現在は <em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、App Service のリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)] 


[AZURE.NOTE] App Services に Windows ストア アプリ パッケージ情報を登録している場合は、<em>useSingleSignOn</em> パラメーターに値 <strong>true</strong> を指定して <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> メソッドを呼び出す必要があります。この操作を行わない場合、login メソッドが呼び出されるたびに、引き続きユーザーにログイン プロンプトが表示されます。


## <a name="tokens"></a>クライアント側で認証トークンを保存する

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)] 


<!-- Anchors. -->
[アプリを認証に登録し、App Service を構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[クライアント側で認証トークンを保存する]: #tokens
[次のステップ]:#next-steps


<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル アプリの使用]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md


[Azure 管理ポータル]: https://manage.windowsazure.com/
[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
[Windows ストア アプリ パッケージを Microsoft 認証に登録する]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md
[Live Connect を使用した Windows ストア アプリへのシングル サインオン]: mobile-services-windows-store-dotnet-single-sign-on.md

<!--HONumber=49-->