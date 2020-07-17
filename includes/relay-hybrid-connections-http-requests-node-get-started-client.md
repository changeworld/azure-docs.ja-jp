---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181413"
---
### <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

リレーの作成時に [クライアント認証が必要] オプションを無効にしていた場合には、ブラウザーを問わずハイブリッド接続 URL に要求を送信できます。 保護されているエンドポイントにアクセスするには、ここに示したように `ServiceBusAuthorization` ヘッダーでトークンを作成して渡す必要があります。

最初に、`sender.js` という新しい JavaScript ファイルを作成します。

### <a name="add-the-relay-npm-package"></a>Relay NPM パッケージを追加する

プロジェクト フォルダーの Node のコマンド プロンプトから `npm install hyco-https` を実行します。 このパッケージでは、通常の `https` パッケージもインポートされます。 クライアントにとって重要な違いは、このパッケージにはリレーの URI とトークンを構築するための関数が用意されているという点です。

### <a name="write-some-code-to-send-messages"></a>メッセージを送信するコードを記述する

1. `sender.js` ファイルの先頭に次の `constants` を追加します。
   
    ```js
    const https = require('hyco-https');
    ```

2. ハイブリッド接続の詳細に関する次の定数を `sender.js` ファイルに追加します。 中かっこ内のプレースホルダーを、ハイブリッド接続の作成時に取得した値に置き換えます。
   
   1. `const ns` - Relay 名前空間。 必ず完全修飾名前空間名を使用してください (たとえば、`{namespace}.servicebus.windows.net`)。
   2. `const path` - ハイブリッド接続の名前。
   3. `const keyrule` - SAS キーの名前。
   4. `const key` - SAS キーの値。

3. 次のコードを `sender.js` ファイルに追加します。 ご覧のとおり、コードは Authorization ヘッダーが追加されているだけで、通常どおり Node.js HTTPS クライアントを使用した場合と大きく変わりません。
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    sender.js ファイルは次のようになります。
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

