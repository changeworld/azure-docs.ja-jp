<properties 
	pageTitle="Azure Active Directory Graph 情報へのアクセス (Windows ストア) | モバイル デベロッパー センター" 
	description="Windows ストア アプリケーションで、Graph API を使用して Azure Active Directory の情報にアクセスする方法について説明します。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc"/>

# Azure Active Directory Graph 情報へのアクセス

[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]


Mobile Services で提供する他の ID プロバイダーと同様に、Azure Active Directory (AAD) プロバイダーも、ディレクトリにプログラムでアクセスできるリッチな [Graph API] をサポートしています。このチュートリアルでは、[Graph API] を使用してディレクトリから取得したユーザーの詳細情報に基づいて、認証されたユーザーのアプリケーション エクスペリエンスを個人用に設定できるように、ToDoList アプリケーションを更新します。

>[AZURE.NOTE] このチュートリアルは、Azure Active Directory での認証についての知識を深めることを目的としています。事前に、Azure Active Directory 認証プロバイダーを使用して、「[認証の使用]」チュートリアルを完了しておく必要があります。このチュートリアルでは、「[認証の使用]」チュートリアルで使用した TodoItem アプリケーションを引き続き更新します。



このチュートリアルでは、次の手順について説明します。


1. [AAD でのアプリケーション登録のアクセス キーを生成する]
2. [GetUserInfo カスタム API を作成する]
3. [カスタム API を使用するようにアプリケーションを更新する]
4. [アプリケーションをテストする]

##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [カスタム API に関するチュートリアル]<br/>カスタム API の呼び出し方法を示します。 



## <a name="generate-key"></a>AAD でのアプリケーション登録のアクセス キーを生成する


「[認証の使用]」チュートリアルでは、[登録して Azure Active Directory のログインを使用する]の手順を完了したときに、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-api"></a>GetUserInfo カスタム API を作成する

このセクションでは、[Graph REST API] を使用して AAD からユーザーについての追加情報を取得する GetUserInfo カスタム API を作成します。

Mobile Services でカスタム API をまったく使用したことがない場合は、このセクションを完了する前に、[カスタム API に関するチュートリアル]を見直すことをお勧めします。

1. [Azure の管理ポータル]で、モバイル サービスのための新しい GetUserInfo カスタム API を作成して、**認証されたユーザーだけ**に get メソッドに対する許可を設定します。

    ![][0]

2. 新しい GetUserInfo API に対するスクリプト エディターを開きます。次の変数がスクリプトの先頭に表示されます。

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

     *tenant_domain*、統合アプリケーションの  *client id*、およびアプリケーションの  *key* を与えることで、この関数はディレクトリ情報の読み取りに使用する Graph アクセス トークンを提供します。

4. Graph API を使用してユーザー情報を返す、次の  `getUser` 関数を追加します。

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

    この関数は、Graph REST API の [ユーザーの取得] エンドポイントの thin ラッパーです。Graph アクセス トークンを使用して、ユーザーのオブジェクト ID を使用するユーザー情報を取得します。

5. 次のように、エクスポートされた  `get` メソッドを更新し、他の関数を使用してユーザー情報を返します。

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


このセクションでは、カスタム API を呼び出し、AAD からユーザーに関する追加情報を返すように、「[認証の使用]」チュートリアルに実装した  `AuthenticateAsync` メソッドを更新します。 

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


## <a name="test-app"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##<a name="next-steps"></a>次のステップ

次のチュートリアル、「[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]」では、Azure Active Directory (AAD) でロール ベースのアクセス制御を使用して、アクセスを許可する前にグループ メンバーシップを確認します。 


<!-- Anchors. -->
[AAD でのアプリケーション登録のアクセス キーを生成する]: #generate-key
[GetUserInfo カスタム API を作成する]: #create-api
[カスタム API を使用するようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test-app
[次のステップ]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Azure Active Directory 認証用の登録]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure の管理ポータル]: https://manage.windowsazure.com/
[カスタム API に関するチュートリアル]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[ソース管理へのプロジェクト コードの保存]: /ja-jp/documentation/articles/mobile-services-store-scripts-source-control/
[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph API] に関するリファレンス: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph REST API] に関するリファレンス: http://msdn.microsoft.com/library/azure/hh974478.aspx
[ユーザーの取得]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/


<!--HONumber=42-->
