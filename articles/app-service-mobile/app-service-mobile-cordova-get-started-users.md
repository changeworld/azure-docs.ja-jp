<properties
	pageTitle="Mobile Apps を使用した Apache Cordova での認証の追加 | Azure App Service"
	description="Azure App Service で Mobile Apps を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Apache Cordova アプリのユーザーを認証する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="ggailey777"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Apache Cordova アプリへの認証の追加

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## 概要

このチュートリアルでは、サポートされている ID プロバイダーを使用して、Apache Cordova で todolist クイック スタート プロジェクトに認証を追加します。最初に、このチュートリアルの基になっている [Mobile Apps の使用]チュートリアルを完了しておく必要があります。

##<a name="register"></a>アプリケーションを認証に登録し、App Service を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Visual Studio で、[Mobile Apps の使用]チュートリアルを実行したときに作成したプロジェクトを開き、**Google Android エミュレーター**でアプリケーションを実行します。さらに、アプリケーションの起動後に、予期しない接続エラーが表示されることを確認します。

    これは、アプリが認証されていないユーザーとしてバックエンドにアクセスしようとするために生じます。バックエンドは、OAuth を使用して認証ページにユーザーをリダイレクトします。ただし、アプリケーションでは OAuth エンドポイントを信頼していません。

次に、Mobile App バックエンドのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

1. **Visual Studio** でプロジェクトを開き、編集する `www/index.html` ファイルを開きます。

2. head セクションで `Content-Security-Policy` メタ タグを見つけます。許可されているソースの一覧に OAuth ホストを追加する必要があります。

    | プロバイダー | SDK プロバイダー名 | OAuth ホスト |
    | :--------------------- | :---------------- | :-------------------------- |
    | Azure Active Directory | aad | https://login.windows.net |
    | Facebook | facebook | https://www.facebook.com |
    | Google | google | https://accounts.google.com |
    | Microsoft | microsoftaccount | https://login.live.com |
    | Twitter | twitter | https://api.twitter.com |

    サンプルの Content-Security-Policy (Azure Active Directory に実装されている) は次のようになっています。

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
			data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    `https://login.windows.net` を上の表の OAuth ホストに置き換える必要があります。このメタ タグの詳細については、[Content-Security-Policy に関するドキュメント]をご覧ください。

    一部の認証プロバイダーについては、適切なモバイル デバイスで使用する場合、Content-Security-Policy を変更する必要がありません。たとえば、Android デバイスで Google 認証を使用する場合は、Content-Security-Policy を変更する必要はありません。

3. 編集する `www/js/index.js` ファイルを開き、`onDeviceReady()` メソッドを見つけて、クライアント作成コードの下に次のコードを追加します。

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    テーブル参照を作成し、UI を更新する既存のコードはこのコードで置き換えられることに注意してください。

    login() メソッドは、プロバイダーでの認証を開始します。login() メソッドは、JavaScript Promise を返す非同期関数です。初期化の残りの部分は promise の応答内に配置されているため、login() メソッドが完了するまで実行されません。

4. 先ほど追加したコードで、`SDK_Provider_Name` を実際のログイン プロバイダーの名前に置き換えます。たとえば、Azure Active Directory の場合、`client.login('aad')` を使用します。

4. プロジェクトを実行します。プロジェクトの初期化が終了すると、アプリケーションにより、選択した認証プロバイダーの OAuth ログイン ページが表示されます。

##<a name="next-steps"></a>次のステップ

* Azure App Service を使用した[認証の詳細]を確認します。
* このチュートリアルの続きとして、Apache Cordova アプリに[プッシュ通知]を追加します。

SDK の使用方法を確認してください。

* [Apache Cordova SDK]
* [ASP.NET サーバー SDK]
* [Node.js サーバー SDK]

<!-- URLs. -->
[Mobile Apps の使用]: app-service-mobile-cordova-get-started.md
[Content-Security-Policy に関するドキュメント]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[プッシュ通知]: app-service-mobile-cordova-get-started-push.md
[認証の詳細]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-codova-how-to-use-client-library.md
[ASP.NET サーバー SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js サーバー SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0323_2016-->