<properties linkid="mobile-services-dotnet-backend-xamarin-android-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin Android apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin Android apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# モバイル サービスでの認証の使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android" class="current">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/" title=".NET バックエンド" class="current">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する][アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する][テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する][アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

## <a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Visual Studio または Xamarin Studio で、デバイスまたはシミュレーターを使用してクライアント プロジェクトを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

    この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

1.  **TodoActivity** クラスに次のプロパティを追加します。

            private MobileServiceUser user;

2.  **TodoActivity** クラスに次のメソッドを追加します。

            private async Task Authenticate()
            {
                try
                {
                    user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
            }

    これで、認証プロセスを処理する新しいメソッドが作成されます。ユーザーは、Facebook ログインを使用して認証されます。認証されたユーザーの ID を示すダイアログが表示されます。

    <div class="dev-callout"><b>注</b>
<p>Facebook 以外の ID プロバイダーを使用している場合は、上の <strong>LoginAsync</strong> メソッドに渡される値を <i>MicrosoftAccount</i>、<i>Twitter</i>、<i>Google</i>、<i>WindowsAzureActiveDirectory</i> のいずれかにします。</p>
</div>

3.  **OnCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。

        // Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

    この呼び出しは、認証プロセスを開始し、認証プロセスを非同期に待機します。

4.  **[実行]** メニューの **[実行]** をクリックしてアプリケーションを再開し、選択した ID プロバイダーでサインインします。

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

<!-- ## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.   -->  

  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
