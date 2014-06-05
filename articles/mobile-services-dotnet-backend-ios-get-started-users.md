<properties pageTitle="認証の使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="認証, Facebook, Google, Twitter, Microsoft アカウント, ログイン" description="モバイル サービスを使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを介して Windows ストア アプリのユーザーを認証する方法について説明します。" metaCanonical="" services="mobile" documentationCenter="Mobile" title="モバイル サービスでの認証の使用" authors="Glenn Gailey" solutions="" manager="" editor="" />

# モバイル サービスでの認証の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS" class="current">iOS</a><!--<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/" title=".NET バックエンド" class="current">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-users/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

##<a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>Visual Studio でモバイル サービス プロジェクトの Web.config ファイルを開き、appSettings セクションで、アプリケーションの識別子と ID プロバイダーから取得した共有シークレット値を設定します。</p>
<p>これらの設定は、ローカル開発中に使用されます。モバイル サービス プロジェクトを Azure に発行すると、これらの設定はポータルで設定された値で上書きされます。</p></li>
</ol>

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>Xcode で、チュートリアル「<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started">モバイル サービスの使用</a>」を実行したときに作成したプロジェクトを開きます。</p></li>
<li><p><strong>[実行]</strong> を押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。<p>
   
   	<p>この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、<em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="next-steps"></a>次のステップ

次の[モバイル サービス ユーザーのサービス側の認証][Authorize users with scripts]チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[次のステップ]:#next-steps


<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[スクリプトを使用したユーザーの承認]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
[Windows ストア アプリ パッケージを Microsoft 認証に登録する]: /ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication

