---
title: Azure CDN SDK for Node.js の概要 | Microsoft Docs
description: Azure CDN のプロファイルとエンドポイントの作成と管理を自動化する方法を示すシンプルな Node.js コンソール アプリケーションを作成する方法について説明します。
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/02/2021
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: 386a424e45d1b718b68cbbf53322fd704317a06b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285226"
---
# <a name="get-started-with-azure-cdn-development"></a>Azure CDN 開発の概要
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

CDN のプロファイルとエンドポイントの作成と管理は、[Azure CDN SDK for JavaScript](https://www.npmjs.com/package/@azure/arm-cdn) を使用して自動化できます。  このチュートリアルでは、単純な Node.js コンソール アプリケーションを作成しながら、使用可能な操作のいくつかを紹介します。  このチュートリアルは、Azure CDN SDK for JavaScript のすべての側面を詳細に説明することを目的としていません。

このチュートリアルを完了するには、[Node.js](https://www.nodejs.org) **6.x.x** 以降をインストールし、構成を済ませておく必要があります。  Node.js アプリケーションの作成には、好きなテキスト エディターを使用してかまいません。  このチュートリアルは [Visual Studio Code](https://code.visualstudio.com)を使って執筆されています。  


[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>プロジェクトの作成と NPM の依存関係の追加
これまでの手順で、CDN プロファイルのリソース グループを作成し、そのグループ内の CDN プロファイルとエンドポイントを管理するための Azure AD アプリケーション アクセス許可を割り当てました。この段階で、アプリケーションの作成を開始できます。

アプリケーションの保存先となるフォルダーを作成します。  Node.js のツールが置かれているパスのコンソールから、この新しいフォルダーに移動して次のコマンドを実行し、プロジェクトを初期化します。

```console
npm init
```

プロジェクトを初期化するための一連の指定項目が表示されます。  このチュートリアルでは、 **entry point** に *app.js* を使用します。  その他の項目については、次のスクリーンショットを参照してください。

![NPM init output](./media/cdn-app-dev-node/cdn-npm-init.png)

プロジェクトが *packages.json* ファイルで初期化されました。  このプロジェクトでは、NPM パッケージに含まれているいくつかの Azure ライブラリを使用します。  Node.js の Azure Active Directory 認証用のライブラリ (@azure/ms-rest-nodeauth) と JavaScript 用の Azure CDN クライアント ライブラリ (@azure/arm-cdn) を使用します。  それらを依存関係としてプロジェクトに追加しましょう。

```console
npm install --save @azure/ms-rest-nodeauth
npm install --save @azure/arm-cdn
```

パッケージのインストールが完了した後の *package.json* ファイルは次のようになります (バージョン番号は異なる場合があります)。

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "@azure/arm-cdn": "^5.2.0",
    "@azure/ms-rest-nodeauth": "^3.0.0"
  }
}
```

最後にテキスト エディターを使用して空のテキスト ファイルを作成し、プロジェクト フォルダーのルートに *app.js* という名前で保存します。  コードを記述する準備ができました。

## <a name="requires-constants-authentication-and-structure"></a>ライブラリの読み込み、定数、認証、構造
テキスト エディターで *app.js* を開き、プログラムの基本構造を記述していきましょう。

1. まず NPM パッケージの "require" を次のように追加します。
   
    ``` javascript
    var msRestAzure = require('@azure/ms-rest-nodeauth');
    const { CdnManagementClient } = require('@azure/arm-cdn');
    ```
2. 次に、メソッドで使用するいくつかの定数を定義します。  以降の内容を追加してください。  **&lt;山かっこ&gt;** などのプレースホルダーは、必要に応じて自分の環境に合わせて置き換えます。
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. 次に、CDN 管理クライアントをインスタンス化し、必要な資格情報を割り当てます。
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new CdnManagementClient(credentials, subscriptionId);
    ```

4. Node.js コンソール アプリケーションには、いくつかのコマンド ライン パラメーターがあります。  少なくとも 1 つのパラメーターが渡されたことを検証しましょう。
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. ここからがプログラムの核となる部分です。渡されたパラメーターに応じて、他の関数に分岐します。
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. プログラム内のいくつかの場所で、適切な数のパラメーターが渡されたことを確認しなければなりません。正しくないようであれば何らかのヒントを表示する必要があります。  そのための関数を作成しましょう。
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. CDN 管理クライアントで使用する関数は非同期的に実行されるので、その完了時にコールバックするメソッドが必要となります。  CDN 管理クライアントからの出力 (ある場合) を表示し、プログラムを適切に終了できるようにしましょう。
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

これで、プログラムの基本的な構造が作成されました。次に、パラメーターに基づいて呼び出される関数を作成する必要があります。

## <a name="list-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントのリストの取得
最初に、既存のプロファイルとエンドポイントをリストするコードを作成します。  コード コメントは、各パラメーターの流れを把握しやすいように正しい構文を示しています。

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(resourceGroupName, parms[2], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントの作成
次に、プロファイルとエンドポイントを作成するための関数を作成します。

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create( resourceGroupName, parms[2], standardCreateParameters, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(resourceGroupName, parms[2], parms[3], endpointProperties, callback);
}
```

## <a name="purge-an-endpoint"></a>エンドポイントの消去
エンドポイントが作成されたと仮定して、プログラムで実行する一般的なタスクの 1 つに、エンドポイントの内容を消去することがあります。

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(resourceGroupName, parms[2], parms[3], purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントの削除
最後に、エンドポイントとプロファイルを削除する関数を追加します。

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteMethod(resourceGroupName, parms[2], callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteMethod(resourceGroupName, parms[2], parms[3], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>プログラムの実行
これで、使い慣れたデバッガーやコンソールを使用して Node.js プログラムを実行できる状態になりました。

> [!TIP]
> Visual Studio Code をデバッガーとして使用する場合、コマンド ライン パラメーターを渡すための設定を環境に対して行う必要があります。  Visual Studio Code では、この設定を **launch.json** ファイルで行います。  **args** という名前のプロパティを探して、パラメーターの一連の文字列値を追加します (例: `"args": ["list", "profiles"]`)。
> 
> 

まず、プロファイルを一覧表示してみましょう。

![List profiles](./media/cdn-app-dev-node/cdn-list-profiles.png)

空の配列が返されました。  リソース グループにはプロファイルがまったく存在しないので、これは正しい動作です。  早速プロファイルを作成しましょう。

![Create profile](./media/cdn-app-dev-node/cdn-create-profile.png)

次にエンドポイントを追加します。

![エンドポイントを作成する](./media/cdn-app-dev-node/cdn-create-endpoint.png)

最後に、プロファイルを削除します。

![Delete profile](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>次の手順

Azure CDN SDK for JavaScript のリファレンスを確認するには、この[リファレンス](https://docs.microsoft.com/javascript/api/@azure/arm-cdn)を参照します。

Azure SDK for JavaScript に関するその他のドキュメントを探すには、[完全なリファレンス](https://docs.microsoft.com/javascript/api/?view=azure-node-latest)を参照します。

[PowerShell](cdn-manage-powershell.md) で CDN リソースを管理します。
