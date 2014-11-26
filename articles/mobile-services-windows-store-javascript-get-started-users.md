<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# モバイル サービスでの認証の使用

> [AZURE.SELECTOR-LIST (プラットフォーム | バックエンド)]
- [(iOS | .NET)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/)
- [(iOS | JavaScript)](/ja-jp/documentation/articles/mobile-services-ios-get-started-users/)
- [(Windows C# | .NET)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)
- [(Windows C# | Javascript)](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/)
- [(Windows JavaScript | .NET)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/)
- [(Windows JavaScript | Javascript)](/ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
- [(Windows Phone | .NET)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/)
- [(Windows Phone | Javascript)](/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-users/)
- [(Android | .NET)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/)
- [(Android | Javascript)](/ja-jp/documentation/articles/mobile-services-android-get-started-users/)
- [(Xamarin iOS | .NET)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/)
- [(Xamarin iOS | Javascript)](/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/)
- [(HTML | Javascript)](/ja-jp/documentation/articles/mobile-services-html-get-started-users/)
- [(Xamarin Android | .NET)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/)
- [(Xamarin Android | Javascript)](/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/)
- [(Appcelerator | Javascript)](/ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/)

このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する]
4.  [クライアントに認証トークンを保存する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

> [WACOM.NOTE]このチュートリアルでは、さまざまな ID プロバイダーを使用してモバイル サービスで管理する認証フローを示します。この方法は構成が容易で、複数のプロバイダーをサポートしています。クライアントによって管理される認証で Live Connect を使用し、Windows Phone アプリケーションでシングル サインオンできるようにするには、トピック「[Live Connect を使用した Windows Phone アプリケーションへのシングル サインオン]」を参照してください。クライアントによって管理される認証を使用すると、ID プロバイダーが維持する別のユーザー データにアプリでアクセスできます。サーバー スクリプトで **user.getIdentities()** 関数を呼び出せば、モバイル サービス内にある同じユーザー データを取得できます。詳細については、[この投稿](http://go.microsoft.com/fwlink/p/?LinkId=506605)を参照してください。

## <a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

1.  (省略可能)<a href="/ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">「Windows ストア アプリ パッケージを Microsoft 認証に登録する」</a>の手順を実行します。

    <div class="dev-callout">

    <b>注</b>
    <p>この手順は Microsoft アカウント ログイン プロバイダーのみに適用されるため、省略可能です。Windows ストア アプリケーションのパッケージ情報をモバイル サービスに登録すると、クライアントはシングル サインオン サービス エクスペリエンスを実現するために Microsoft アカウント ログイン資格情報を再利用できます。この操作を行わない場合、login メソッドが呼び出されるたびに、Microsoft アカウント ログイン ユーザーにログイン プロンプトが表示されます。Microsoft アカウント ID プロバイダーを使用する場合は、この手順を実行してください。</p>

    </div>

これで、モバイル サービスとアプリケーションの両方が、選択した認証プロバイダーと連係するように構成されました。

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

1.  Visual Studio 2012 Express for Windows 8 で、チュートリアル<a href="/ja-jp/develop/mobile/tutorials/get-started/">「モバイル サービスの使用」</a>を実行したときに作成したプロジェクトを開きます。

2.  F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

    この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

## <a name="tokens"></a>クライアントに認証トークンを保存する

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>次のステップ

次の[モバイル サービス ユーザーのサービス側の認証][モバイル サービス ユーザーのサービス側の認証]チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。


  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [クライアントに認証トークンを保存する]: #tokens
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started/
  [Live Connect を使用した Windows Phone アプリケーションへのシングル サインオン]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
  [この投稿]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [モバイル サービス ユーザーのサービス側の認証]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts
