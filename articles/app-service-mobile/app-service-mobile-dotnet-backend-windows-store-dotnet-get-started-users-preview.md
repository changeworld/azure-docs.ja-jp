<properties
	pageTitle="Windows Runtime 8.1 ユニバーサル アプリへの認証の追加 | Azure Mobile Apps"
	description="Azure App Service Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを使って Windows アプリのユーザーを認証する方法を説明します。"
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="glenga"/>

# Windows アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル アプリによって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル アプリのクイック スタートに基づいています。事前に、チュートリアル：[モバイル アプリの使用]を完了している必要があります。http://acom-sandbox.azurewebsites.net/documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/?rnd=1

##<a name="create-gateway"></a>App Service ゲートウェイを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>アプリケーションを認証に登録し、App Service を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4.Visual Studio で、クライアント アプリ プロジェクト内の共有の App.xaml.cs プロジェクト ファイルを開き、**MobileServiceClient** インスタンスが Mobile App バックエンドの URL とゲートウェイの両方を使用するように構成されていることを確認します。

&nbsp;&nbsp;5.スタートアップ プロジェクトとして設定された Windows アプリ プロジェクトの 1 つを使用して、F5 キーを押してアプリを実行します。アプリケーションの開始後、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

&nbsp;&nbsp;この問題は、アプリが認証されていないユーザーとしてモバイル アプリ コードにアクセスしようとしても、*TodoItem* テーブルで認証が要求されるために発生します。

次に、App Service のリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>クライアント側で認証トークンを保存する

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]

##次のステップ


<!-- URLs. -->
[モバイル アプリの使用]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
 

<!---HONumber=Oct15_HO4-->