<properties pageTitle="認証の使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows ストア アプリのユーザーを認証する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey" />

# Mobile Services アプリへの認証の追加 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このトピックでは、アプリケーションから Azure のモバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。</p>
<p>このチュートリアルのビデオ バージョンを見るには、右側のクリップをクリックします。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">10:04:00</span></div>
</div> 

>[WACOM.NOTE]Mobile Services JavaScript クライアント ライブラリを使用する場合、現時点では Windows Phone Store 8.1 アプリで認証はサポートされません。JavaScript クライアントを使用するユニバーサル Windows アプリがある場合、現時点では Windows Phone でユーザーを認証できません。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]
5. [クライアント側で認証トークンを保存する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。 

>[WACOM.NOTE]このチュートリアルでは、さまざまな ID プロバイダーを使用した、Mobile Services によって管理される認証フローについて説明します。この方法は構成が容易で、複数のプロバイダーをサポートしています。クライアントによって管理される認証による Live Connect を使用して、Windows Phone アプリでシングル サインオンできるようにするには、トピック「[Live Connect を使用した Windows Phone アプリへのシングル サインオン]」を参照してください。クライアントによって管理される認証を使用することにより、アプリは ID プロバイダーが保持する追加のユーザー データにアクセスできます。サーバー スクリプトで **user.getIdentities()** 関数を呼び出すことにより、同じユーザー データをモバイル サービスで取得できます。　詳細については、[この記事](http://go.microsoft.com/fwlink/p/?LinkId=506605)を参照してください。

##<a name="register"></a> アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(省略可能)「<a href="/ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Windows ストア アプリケーション パッケージを Microsoft 認証に登録する</a>」の手順を実行します。</p>

    <div class="dev-callout"><b>注</b>
	<p>この手順は Microsoft アカウント ログイン プロバイダーのみに適用されるため、省略可能です。Windows ストア アプリケーションのパッケージ情報をモバイル サービスに登録すると、クライアントはシングル サインオン サービス エクスペリエンスを実現するために Microsoft アカウント ログイン資格情報を再利用できます。この操作を行わない場合、login メソッドが呼び出されるたびに、Microsoft アカウント ログイン ユーザーにログイン プロンプトが表示されます。Microsoft アカウント ID プロバイダーを使用する場合は、この手順を実行してください。</p>
    </div>
</li>
</ol>

##<a name="permissions"></a> アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p> Visual Studio 2012 Express for Windows 8 で、チュートリアル「<a href="/ja-jp/documentation/articles/mobile-services-windows-store-get-started">モバイル サービスの使用</a>」を実行したときに作成したプロジェクトを開きます。</p></li> 
<li><p>F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
   
   	<p>この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、<em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a> アプリケーションに認証を追加する

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>クライアント側で認証トークンを保存する

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>次のステップ

次のチュートリアル「[モバイル サービス ユーザーのサービス側の認証][Authorize users with scripts]」では、認証されたユーザーに基づいて Mobile Services で提供されるユーザー ID 値を受け取り、それを使用して、Mobile Services から返されたデータをフィルター処理します。.NET でモバイル サービスを使用する方法の詳細については、「[モバイル サービス .NET の使用方法の概念リファレンス]」を参照してください。

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
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/
[スクリプトを使用したユーザーの承認]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript と HTML]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
[Windows ストア アプリ パッケージを Microsoft 認証に登録する]: /ja-jp/develop/mobile/how-to-guides/register-windows-store-app-package
