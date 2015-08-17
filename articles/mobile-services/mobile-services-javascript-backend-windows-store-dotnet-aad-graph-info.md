<properties 
	pageTitle="Azure Active Directory Graph 情報へのアクセス (Windows ストア) | モバイル デベロッパー センター" 
	description="Windows ストア アプリケーションで、Graph API を使用して Azure Active Directory の情報にアクセスする方法について説明します。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Azure Active Directory Graph 情報へのアクセス


[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]
##概要

Mobile Services で提供する他の ID プロバイダーと同様に、Azure Active Directory (AAD) プロバイダーも、ディレクトリにプログラムでアクセスできるリッチな Graph API をサポートしています。このチュートリアルでは、[Graph REST Library] を使用してディレクトリから取得したユーザーの詳細情報を返す、認証されたユーザーのアプリケーション エクスペリエンスを個人用に設定できるように、ToDoList アプリケーションを更新します。

Azure AD Graph API の詳細については、[Azure Active Directory Graph Team Blog (Azure Active Directory Graph チームのブログ)] を参照してください。

>[AZURE.NOTE]このチュートリアルは、Azure Active Directory での認証についての知識を深めることを目的としています。事前に、Azure Active Directory 認証プロバイダーを使用して、チュートリアル [Mobile Services アプリへの認証の追加]を完了しておく必要があります。このチュートリアルでは、[Mobile Services アプリへの認証の追加]で使用した TodoItem アプリケーションを引き続き更新します。



##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [Mobile Services アプリへの認証の追加] <br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [Custom API Tutorial (カスタム API チュートリアル)]<br/>カスタム API の呼び出し方法を示します。



##AAD でのアプリケーション登録のアクセス キーを生成する


チュートリアル [Mobile Services アプリへの認証の追加]では、手順[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]を完了したとき、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##GetUserInfo カスタム API を作成する

このセクションでは、[Graph REST API] を使用して AAD からユーザーについての追加情報を取得する GetUserInfo カスタム API を作成します。

Mobile Services でカスタム API をまったく使用したことがない場合は、このセクションを完了する前に、「[Custom API Tutorial (カスタム API チュートリアル)]」を見直すことをお勧めします。

1. [Azure の管理ポータル]で、モバイル サービスのための新しい GetUserInfo カスタム API を作成して、**認証されたユーザーだけ**に get メソッドに対する許可を設定します。

    ![][0]

2. 新しい GetUserInfo API に対するスクリプト エディターを開きます。次の変数がスクリプトの先頭に表示されます。

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. `getAADToken` 関数に次の定義を追加します。

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

4. Graph API を使用してユーザー情報を返す、次の `getUser` 関数を追加します。

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

    この関数は、Graph REST API の[ユーザーの取得]エンドポイントの thin ラッパーです。Graph アクセス トークンを使用して、ユーザーのオブジェクト ID を使用するユーザー情報を取得します。

5. 次のように、エクスポートされた `get` メソッドを更新し、他の関数を使用してユーザー情報を返します。

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


##GetUserInfo を使用するようにアプリケーションを更新する


このセクションでは、カスタム API を呼び出し、AAD からユーザーに関する追加情報を返すように、「認証の使用」チュートリアルで実装した `AuthenticateAsync` メソッドを更新します。

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]


 


##アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]




##次のステップ

次のチュートリアル、[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]では、Azure Active Directory (AAD) でロール ベースのアクセス制御を使用して、アクセスを許可する前にグループ メンバーシップを確認します。



<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Mobile Services アプリへの認証の追加]: ../mobile-services-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure の管理ポータル]: https://manage.windowsazure.com/
[Custom API Tutorial (カスタム API チュートリアル)]: mobile-services-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]: mobile-services-how-to-register-active-directory-authentication.md
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Graph REST Library]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[ユーザーの取得]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Mobile Services と Azure Active Directory でのロール ベースのアクセス制御]: mobile-services-javascript-backend-windows-store-dotnet-aad-rbac.md
[Azure Active Directory Graph Team Blog (Azure Active Directory Graph チームのブログ)]: http://go.microsoft.com/fwlink/?LinkId=510536

<!---HONumber=August15_HO6-->