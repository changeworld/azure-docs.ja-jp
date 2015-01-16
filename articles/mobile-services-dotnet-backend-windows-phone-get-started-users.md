<properties pageTitle="認証の使用 (Windows Phone) | モバイル デベロッパー センター" metaKeywords="authentication, Facebook, GOogle, Twitter, Microsoft Account, login" description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows Phone アプリのユーザーを認証する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey" />

# Mobile Services アプリへの認証の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、Windows Phone アプリケーションから Azure Mobile Services のユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

>[AZURE.NOTE] このチュートリアルが対象とするのは、Windows Phone 8.0 および Windows Phone 8.1 Silverlight のアプリケーションです。Windows Phone 8.1 アプリまたはユニバーサル Windows アプリについては、[このトピックのユニバーサル Windows アプリ バージョン](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)を参照してください。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]
4. [クライアント側で認証トークンを保存する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。 


##<a name="register"></a> アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>Visual Studio でクライアント アプリケーション プロジェクトを開き、App.xaml.cs で **MobileServiceClient** のインスタンスがモバイル サービスへのクラウド URL で使用されるように設定します。</li> 
<li><p>F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
   
   	<p>この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、<em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>クライアント側で認証トークンを保存する

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>次のステップ

次の「[Service-side authorization of Mobile Services users (Mobile Services ユーザーのサービス側の認証)][Authorize users with scripts]」チュートリアルでは、認証されたユーザーに基づいて Mobile Services によって提供されるユーザー ID 値を受け取り、それを使用して、Mobile Services から返されたデータをフィルター処理します。.NET でモバイル サービスを使用する方法の詳細については、「[モバイル サービス .NET の使用方法の概念リファレンス]」を参照してください。

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[クライアント側で認証トークンを保存する]: #tokens
[次のステップ]: #next-steps


<!-- URLs. -->
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect を使用した Windows Phone アプリケーションへのシングル サインオン]: /ja-jp/documentation/articles/mobile-services-windows-phone-single-sign-on
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[スクリプトを使用したユーザーの承認]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts
[JavaScript と HTML]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
[Windows ストア アプリ パッケージを Microsoft 認証に登録する]: /ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
