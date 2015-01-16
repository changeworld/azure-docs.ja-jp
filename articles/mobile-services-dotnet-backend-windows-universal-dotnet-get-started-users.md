<properties pageTitle="認証の使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows ストア アプリのユーザーを認証する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey" />

# Mobile Services アプリへの認証の追加 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、ユニバーサル Windows アプリから Azure Mobile Services のユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]
4. [クライアント側で認証トークンを保存する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。 

>[WACOM.NOTE]このチュートリアルでは、Windows ストア アプリおよび Windows Phone Store 8.1 アプリでユーザーを認証する方法について説明します。Windows Phone 8.0 アプリまたは Windows Phone Silverlight 8.1 アプリの場合、このバージョンの「[Get started with authentication in Mobile Services (Mobile Services での認証の使用)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users)」を参照してください。

##<a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>Visual Studio で、TodoList アプリの Windows ストア プロジェクトを右クリックして、<strong>[スタートアップ プロジェクトに設定]</strong> をクリックします。</p></li>
<li><p>共有プロジェクトで、App.xaml.cs プロジェクト ファイルを開き、<a href="http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> の定義を見つけ、Azure で実行されているモバイル サービスに接続するように構成されていることを確認します。</p>
<div class="dev-callout"><strong>注</strong><p>Visual Studio ツールを使用してアプリをモバイル サービスに接続する場合、ツールはクライアント プラットフォームごとに 1 つずつ、<strong>MobileServiceClient</strong> 定義を合計 2 セット生成します。この機会に、<code>#if...#endif</code> でラップされた <strong>MobileServiceClient</strong> 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することができます。Azure の管理ポータルからクイック スタート アプリをダウンロードした場合は、これを行う必要はありません。</p></div>
</li> 
<li><p>F5 キーを押して、Windows ストア アプリを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
   
   	<p>この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、<em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

[WACOM.NOTE]Windows ストア アプリ パッケージ情報をモバイル サービスに登録している場合は、<em>useSingleSignOn</em> パラメーターに値 <strong>true</strong> を指定して <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> メソッドを呼び出す必要があります。この操作を行わない場合、login メソッドが呼び出されるたびに、引き続きユーザーにログイン プロンプトが表示されます。

##<a name="tokens"></a>クライアント側で認証トークンを保存する

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>次のステップ

次の「[Service-side authorization of Mobile Services users (Mobile Services ユーザーのサービス側の認証)][Authorize users with scripts]」チュートリアルでは、認証されたユーザーに基づいて Mobile Services によって提供されるユーザー ID 値を受け取り、それを使用して、Mobile Services から返されたデータをフィルター処理します。.NET でモバイル サービスを使用する方法の詳細については、「[モバイル サービス .NET の使用方法の概念リファレンス]」を参照してください。

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[クライアント側で認証トークンを保存する]: #tokens
[次のステップ]:#next-steps


<!-- URLs. -->
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect を使用した Windows ストア アプリへのシングル サインオン]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[スクリプトを使用したユーザーの承認]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript と HTML]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Windows ストア アプリ パッケージを Microsoft 認証に登録する]: /ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
