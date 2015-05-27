<properties 
	pageTitle="Mobile Services と Azure Active Directory でのロール ベースのアクセス制御 (Windows ストア) | モバイル デベロッパー センター" 
	description="Windows ストア アプリケーションで Azure Active Directory ロールに基づいてアクセスを制御する方法について説明します。" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Mobile Services と Azure Active Directory でのロール ベースのアクセス制御

[AZURE.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]

#概要

ロール ベースのアクセス制御 (RBAC) は、ユーザーが保持できるロールにアクセス許可を割り当てる機能であり、特定のクラスのユーザーが実行できることとできないことの境界線を明確に定義します。このチュートリアルでは、Azure Mobile Services に基本的な RBAC を追加する方法について説明します。

このチュートリアルでは、ロール ベースのアクセス制御について示しながら、Azure Active Directory (AAD) で定義された Sales グループに対する各ユーザーのメンバーシップを確認します。アクセスの確認は、モバイル サービス バックエンドで Azure Active Directory 用の [Graph API] を使用し、JavaScript で行います。Sales ロールに属するユーザーのみが、データの照会を許可されます。


>[AZURE.NOTE]このチュートリアルは、認証方法を含めた認証の知識を深めることを目的としています。事前に、Azure Active Directory 認証プロバイダーを使用して、チュートリアル「[Mobile Services アプリへの認証の追加]」を完了しておく必要があります。このチュートリアルでは、チュートリアル「[Mobile Services アプリへの認証の追加]」で使用した TodoItem アプリケーションを引き続き更新します。

##前提条件

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013。
* Azure Active Directory 認証プロバイダーを使用して、チュートリアル「[Mobile Services アプリへの認証の追加]」を完了していること。
* 「[Store Server Scripts (サーバー スクリプトの格納)]」チュートリアルを完了し、Git リポジトリを使用してサーバー スクリプトを格納する方法に習熟していること。
 


##Sales グループとメンバーシップを作成する

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]


##統合アプリケーションのキーを生成する


チュートリアル「[Mobile Services アプリへの認証の追加]」では、手順「[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]」を完了したとき、統合アプリケーションに対する登録を作成しました。このセクションでは、その統合アプリケーションのクライアント ID でディレクトリ情報を読み取るときに使用するキーを生成します。

チュートリアル「[Azure Active Directory Graph 情報へのアクセス]」を完了している場合は、この手順を既に完了しているため、このセクションを省略できます。

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]





##メンバーシップを確認する共有スクリプトをモバイル サービスに追加する

このセクションでは、Git を使用して *rbac.js* という名前の共有スクリプト ファイルをモバイル サービスにデプロイします。この共有スクリプト ファイルには、[Graph API] を使用してユーザーのグループ メンバーシップを確認する関数が含まれています。

Git でモバイル サービスにスクリプトをデプロイすることに慣れていない場合は、このセクションを完了する前に、「[Store Server Scripts (サーバー スクリプトの格納)]」チュートリアルを見直してください。

1. モバイル サービスのローカル リポジトリの *./service/shared/* ディレクトリに *rbac.js* という名前の新しいスクリプト ファイルを作成します。
2. `getAADToken` 関数を定義するファイルの先頭に次のスクリプトを追加します。*tenant_domain*、統合アプリケーションの *client id*、およびアプリケーションの *key* を与えることで、この関数はディレクトリ情報の読み取りに使用する Graph アクセス トークンを提供します。

    >[AZURE.NOTE]アクセス確認のたびに新しいトークンを作成するのではなく、トークンをキャッシュする必要があります。その後、「[Windows Azure AD Graph のエラー コード]」に記載されているとおり、トークンの使用を試みたときに 401 Authentication_ExpiredToken 応答が返された場合は、キャッシュを更新します。これは、次に示すコードでは簡略化のために省かれていますが、Active Directory に対して余分なネットワーク トラフィックを軽減する効果があります。

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;
         
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


3. 次のコードを *rbac.js* に追加して `getGroupId` 関数を定義します。この関数は、アクセス トークンを使用して、フィルターで使用されたグループ名に基づいてグループ ID を取得します。
 
    >[AZURE.NOTE]このコードは、Active Directory グループを名前で検索します。多くの場合、モバイル サービス アプリケーションの設定としてグループ ID を格納し、そのグループ ID を使用する方が適切な場合があります。これは、グループ名は変更される場合がありますが、ID は同じままであるためです。ただし、グループ名が変更されると、通常、少なくともロールのスコープに変更が生じるため、モバイル サービスのコードも更新が必要になる場合があります。

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
	              "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }


4. 次のコードを *rbac.js* に追加して、Graph REST API の [IsMemberOf] エンドポイントを呼び出す `isMemberOf` 関数を定義します。

    この関数は、Graph REST API の[IsMemberOf] エンドポイントの thin ラッパーです。Graph アクセス トークンを使用して、ユーザーのディレクトリ オブジェクト ID が、グループ ID に基づくグループのメンバーシップを持っているかどうか確認します。

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

    

7. 次のエクスポートされた `checkGroupMembership` 関数を *rbac.js* に追加します。

    この関数は他のスクリプト関数の使用をラップし、他のスクリプトによって呼び出される共有スクリプトからエクスポートされ、実際にアクセス確認を実行します。モバイル サービスのユーザー オブジェクトおよびグループ ID が与えられると、このスクリプトはユーザーの ID に対する Azure Active Directory オブジェクト ID を取得して、そのグループのメンバーシップを確認します。

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
		        else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
       	                        if (err) errorHandler(err);
               	                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

8. 変更を *rbac.js* に保存します。

##データベース操作にロール ベースのアクセス確認を追加する


チュートリアル「[Mobile Services アプリへの認証の追加]」を完了すると、次に示すように、認証が必要になるテーブル操作を既に設定したことになります。

![][3]

認証が必要なデータベース操作それぞれに対して、ユーザー オブジェクトを使用してアクセスを確認するスクリプトを追加できます。

次の手順には、このスクリプトを使用してロール ベースのアクセス制御をモバイル サービスの各テーブル操作にデプロイする方法を示します。共有 *rbac.js* スクリプトを使用するスクリプトが、各テーブル操作に対して実行されます。

1. *todoitem.insert.js* という名前の新しいスクリプト ファイルを、モバイル サービスに対するローカル Git リポジトリの *./service/table/* ディレクトリに追加します。次のスクリプトをファイルに貼り付けます。

        function insert(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2. *todoitem.read.js* という名前の新しいスクリプト ファイルを、モバイル サービスに対するローカル Git リポジトリの *./service/table/* ディレクトリに追加します。次のスクリプトをファイルに貼り付けます。

        function read(query, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3. *todoitem.update.js* という名前の新しいスクリプト ファイルを、モバイル サービスに対するローカル Git リポジトリの *./service/table/* ディレクトリに追加します。次のスクリプトをファイルに貼り付けます。

        function update(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4. *todoitem.delete.js* という名前の新しいスクリプト ファイルを、モバイル サービスに対するローカル Git リポジトリの *./service/table/* ディレクトリに追加します。次のスクリプトをファイルに貼り付けます。

        function del(id, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5. Git リポジトリへのコマンド ライン インターフェイスで次のコマンドを実行することにより、追加したスクリプト ファイルの追跡を追加します。

        git add .

6. Git リポジトリへのコマンド ライン インターフェイスで次のコマンドを実行して、更新をコミットします。

        git commit -m "Added role based access control to table operations"
  
7. Git リポジトリへのコマンド ライン インターフェイスで次のコマンドを実行して、ローカル Git リポジトリへの更新をモバイル サービスにデプロイします。このコマンドは、*master* 分岐で更新を行ったと想定しています。

        git push origin master

8. [Azure の管理ポータル]では、モバイル サービスに対するテーブル操作に移動して、以下に示すような更新が行われたことを確認できます。

    ![][4]

##クライアントのアクセスをテストする

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[Mobile Services アプリへの認証の追加]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure の管理ポータル]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts (サーバー スクリプトの格納)]: mobile-services-store-scripts-source-control.md
[アプリケーションを登録して Azure Active Directory アカウント ログインを使用する]: mobile-services-how-to-register-active-directory-authentication.md
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Windows Azure AD Graph のエラー コード]: http://msdn.microsoft.com/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Azure Active Directory Graph 情報へのアクセス]: mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info.md
<!--HONumber=54-->