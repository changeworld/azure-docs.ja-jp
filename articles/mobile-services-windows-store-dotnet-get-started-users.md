<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey"></tags>

# モバイル サービスでの認証の使用

<p>[WACOM.INCLUDE <a href="../includes/mobile-services-selector-get-started-users.md">mobile-services-selector-get-started-users</a>]</p>
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。</p>
<p>このチュートリアルのビデオ バージョンを見るには、右側のクリップをクリックします。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">10:04:00</span></div>
</div>


> [WACOM.NOTE]現在、モバイル サービス JavaScript クライアント ライブラリを使用する場合、Windows Phone Store 8.1 アプリでは認証はサポートされていません。JavaScript クライアントを使用するユニバーサル Windows アプリ プロジェクトがある場合、現時点では、Windows Phone ではユーザーを認証できません。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する][アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する][テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する][アプリケーションに認証を追加する]
4.  [クライアントに認証トークンを保存する][クライアントに認証トークンを保存する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

> [WACOM.NOTE]このチュートリアルでは、さまざまな ID プロバイダーを使用してモバイル サービスで管理する認証フローを示します。この方法は構成が容易で、複数のプロバイダーをサポートしています。クライアントによって管理される認証で Live Connect を使用し、Windows Phone アプリケーションでシングル サインオンできるようにするには、トピック「[Live Connect を使用した Windows Phone アプリケーションへのシングル サインオン][Live Connect を使用した Windows Phone アプリケーションへのシングル サインオン]」を参照してください。クライアントによって管理される認証を使用すると、ID プロバイダーが維持する別のユーザー データにアプリでアクセスできます。サーバー スクリプトで **user.getIdentities()** 関数を呼び出せば、モバイル サービス内にある同じユーザー データを取得できます。詳細については、[この投稿][この投稿]を参照してください。

## <a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

1.  (省略可能)「[Windows ストア アプリ パッケージを Microsoft 認証に登録する][Windows ストア アプリ パッケージを Microsoft 認証に登録する]」の手順を実行します。

    <div class="dev-callout">

    <b>注</b>
<br/>
<br/>
    この手順は Microsoft アカウント ログイン プロバイダーのみに適用されるため、省略可能です。Windows ストア アプリケーションのパッケージ情報をモバイル サービスに登録すると、クライアントはシングル サインオン サービス エクスペリエンスを実現するために Microsoft アカウント ログイン資格情報を再利用できます。この操作を行わない場合、login メソッドが呼び出されるたびに、Microsoft アカウント ログイン ユーザーにログイン プロンプトが表示されます。Microsoft アカウント ID プロバイダーを使用する場合は、この手順を実行してください。

    </div>

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  Visual Studio 2012 Express for Windows 8 で、チュートリアル「[モバイル サービスの使用][1]」を実行したときに作成したプロジェクトを開きます。

2.  F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

    この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app][mobile-services-windows-dotnet-authenticate-app]]

## <a name="tokens"></a>クライアントに認証トークンを保存する

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token][mobile-services-windows-store-dotnet-authenticate-app-with-token]]

## <a name="next-steps"> </a>次のステップ

次の[モバイル サービス ユーザーのサービス側の認証][モバイル サービス ユーザーのサービス側の認証]チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。.NET でモバイル サービスを使用する方法の詳細については、「[モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]」を参照してください。



  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [チュートリアルを見る]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services
  [<span class="icon">ビデオを再生する</span>]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services
  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [クライアントに認証トークンを保存する]: #tokens
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started/
  [Live Connect を使用した Windows Phone アプリケーションへのシングル サインオン]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [この投稿]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Windows ストア アプリ パッケージを Microsoft 認証に登録する]: /ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started
  [mobile-services-windows-dotnet-authenticate-app]: ../includes/mobile-services-windows-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [モバイル サービス ユーザーのサービス側の認証]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
