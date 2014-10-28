<properties linkid="develop-mobile-tutorials-javascript-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# Azure Active Directory Graph 情報へのアクセス

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="Windows ストア C#" class="current">Windows ストア C#</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET バックエンド">.NET バックエンド</a> |
<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>

Mobile Services で提供する他の ID プロバイダーと同様に、Azure Active Directory (AAD) プロバイダーも、ディレクトリにプログラムでアクセスできるリッチな [Graph API][Graph API] をサポートしています。このチュートリアルでは、[Graph API][Graph API] を使用してディレクトリから取得したユーザーの詳細情報に基づいて、認証されたユーザーのアプリケーション エクスペリエンスを個人用に設定できるように、ToDoList アプリケーションを更新します。

> [WACOM.NOTE] このチュートリアルは、Azure Active Directory での認証についての知識を深めることを目的としています。事前に、Azure Active Directory 認証プロバイダーを使用して、「[認証の使用][認証の使用]」チュートリアルを完了しておく必要があります。このチュートリアルでは、「[認証の使用][認証の使用]」チュートリアルで使用した TodoItem アプリケーションを引き続き更新します。

このチュートリアルでは、次の手順について説明します。

1.  [AAD でのアプリケーション登録のアクセス キーを生成する][AAD でのアプリケーション登録のアクセス キーを生成する]
2.  [GetUserInfo カスタム API を作成する][GetUserInfo カスタム API を作成する]
3.  [カスタム API を使用するようにアプリケーションを更新する][カスタム API を使用するようにアプリケーションを更新する]
4.  [アプリケーションをテストする][アプリケーションをテストする]

## 前提条件

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

-   [認証の使用][認証の使用]
    TodoList サンプル アプリケーションにログイン要件を追加します。

-   [Custom API Tutorial (カスタム API チュートリアル)][Custom API Tutorial (カスタム API チュートリアル)]
    カスタム API の呼び出し方法を示します。

## <a name="generate-key"></a>AAD でのアプリケーション登録のアクセス キーを生成する

「[認証の使用][認証の使用]」チュートリアルでは、手順「[Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)][Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)]」を完了したときに、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-api"></a>GetUserInfo カスタム API を作成する

このセクションでは、[Graph REST API][Graph REST API] を使用して AAD からユーザーについての追加情報を取得する GetUserInfo カスタム API を作成します。

Mobile Services でカスタム API をまったく使用したことがない場合は、このセクションを完了する前に、「[Custom API Tutorial (カスタム API チュートリアル)][Custom API Tutorial (カスタム API チュートリアル)]」を見直すことをお勧めします。

1.  [Azure の管理ポータル][Azure の管理ポータル]で、モバイル サービスのための新しい GetUserInfo カスタム API を作成して、**認証されたユーザーだけ**に get メソッドに対する許可を設定します。

    ![][]

2.  新しい GetUserInfo API に対するスクリプト エディターを開きます。次の変数がスクリプトの先頭に表示されます。

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;

3.  `getAADToken` 関数に次の定義を追加します。

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    *tenant\_domain*、統合アプリケーションの *client id*、およびアプリケーションの *key* を与えることで、この関数はディレクトリ情報の読み取りに使用する Graph アクセス トークンを提供します。

4.  Graph API を使用してユーザー情報を返す、次の `getUser` 関数を追加します。

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    この関数は、Graph REST API の[ユーザーの取得][ユーザーの取得]エンドポイントの thin ラッパーです。Graph アクセス トークンを使用して、ユーザーのオブジェクト ID を使用するユーザー情報を取得します。

5.  次のように、エクスポートされた `get` メソッドを更新し、他の関数を使用してユーザー情報を返します。

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };

## <a name="update-app"></a>GetUserInfo を使用するようにアプリケーションを更新する

このセクションでは、カスタム API を呼び出し、AAD からユーザーに関する追加情報を返すように、「[認証の使用][認証の使用]」チュートリアルに実装した `AuthenticateAsync` メソッドを更新します。

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app][mobile-services-aad-graph-info-update-app]]

## <a name="test-app"></a> アプリケーションをテストする

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app][mobile-services-aad-graph-info-test-app]]

## <a name="next-steps"></a>次のステップ

次のチュートリアル、「[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御][Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]」では、Azure Active Directory (AAD) でロール ベースのアクセス制御を使用して、アクセスを許可する前にグループ メンバーシップを確認します。



  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/ "Windows ストア C#"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "JavaScript バックエンド"
  [Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [AAD でのアプリケーション登録のアクセス キーを生成する]: #generate-key
  [GetUserInfo カスタム API を作成する]: #create-api
  [カスタム API を使用するようにアプリケーションを更新する]: #update-app
  [アプリケーションをテストする]: #test-app
  [Custom API Tutorial (カスタム API チュートリアル)]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Register to use an Azure Active Directory Login (Azure Active Directory のログインの使用を登録する)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [Graph REST API]: http://msdn.microsoft.com/ja-jp/library/azure/hh974478.aspx
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png
  [ユーザーの取得]: http://msdn.microsoft.com/ja-jp/library/azure/dn151678.aspx
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
