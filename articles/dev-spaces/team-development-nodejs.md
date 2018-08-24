---
title: VS Code を使用する Azure Dev Spaces でのチーム開発 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 6993712e6de3eb932c4d1e3331ff6028463ad0ec
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919810"
---
# <a name="team-development-with-azure-dev-spaces"></a>Azure Dev Spaces を使用したチーム開発

このチュートリアルでは、複数の開発スペースを使用して異なる開発環境で同時に作業し、個別の作業を同じクラスター内の独立した開発スペースに分離させておく方法を説明します。

## <a name="call-a-service-running-in-a-separate-container"></a>別のコンテナーで実行されているサービスを呼び出す

このセクションでは、2 つ目のサービス (`mywebapi`) を作成し、`webfrontend` でそのサービスを呼び出します。 各サービスは別々のコンテナーで実行されます。 その後、両方のコンテナーでデバッグします。

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>*mywebapi* のサンプル コードを開く
`samples` という名前のフォルダーに、このガイドの `mywebapi` のサンプル コードが既にあります (ない場合は、https://github.com/Azure/dev-spaces に移動し、**[Clone or download]** をクリックして GitHub リポジトリをダウンロードしてください)。このセクションのコードは、`samples/nodejs/getting-started/mywebapi` にあります。

### <a name="run-mywebapi"></a>*mywebapi* を実行する
1. "*別の VS Code ウィンドウ*" で、`mywebapi` フォルダーを開きます。
1. (**[表示 | コマンド パレット]** メニューを使用して) **コマンド パレット**を開き、オート コンプリートを使用して次のコマンドを入力および選択します: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 
1. F5 キーを押し、サービスがビルドされ、展開されるまで待ちます。 準備ができると、VS Code デバッグ バーが表示されます。
1. エンドポイント URL を書き留めます。これは、http://localhost:\<portnumber\> のように表示されます。 **ヒント: VS Code のステータス バーに、クリック可能な URL が表示されます。** コンテナーはローカルで実行されているように見えるかもしれませんが、実際には Azure の開発環境で実行されています。 localhost アドレスである理由は、`mywebapi` でパブリック エンドポイントが定義されておらず、Kubernetes インスタンス内からしかアクセスできないためです。 利便性を考慮し、また、ローカル コンピューターからのプライベート サービスとの対話を容易にするために、Azure Dev Spaces では、Azure で実行されているコンテナーへの一時的な SSH トンネルが作成されます。
1. `mywebapi` の準備ができたら、ブラウザーで localhost アドレスを開きます。 `mywebapi` サービスからの応答 ("Hello from mywebapi") が表示されます。


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend* から *mywebapi* に対して要求を行う
次に、`mywebapi` に対して要求を行う `webfrontend` のコードを記述しましょう。
1. `webfrontend` の VS Code ウィンドウに切り替えます。
1. `server.js` の先頭に次のコード行を追加します。
    ```javascript
    var request = require('request');
    ```

3. `/api` GET ハンドラーのコードを*置き換えます*。 要求を処理すると、`mywebapi` が呼び出され、両方のサービスからの結果が返されます。

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```

前述のコード例は、`azds-route-as` ヘッダーを受信要求から送信要求に転送します。 これがチームによる共同開発にどのように役立つかについては、後ほど説明します。

### <a name="debug-across-multiple-services"></a>複数のサービスでデバッグする
1. この時点で、`mywebapi` は引き続きデバッガーがアタッチされた状態で実行されています。 そうでない場合は、`mywebapi` プロジェクトで F5 キーを押します。
1. 既定の GET `/` ハンドラーにブレークポイントを設定します。
1. `webfrontend` プロジェクトで、GET 要求を `http://mywebapi` に送信する直前にブレークポイントを設定します。
1. `webfrontend` プロジェクトで F5 キーを押します。
1. Web アプリを開き、両方のサービスでコードをステップ実行します。 Web アプリには、2 つのサービスによって連結されたメッセージ ("Hello from webfrontend and Hello from mywebapi") が表示されます。

お疲れさまでした。 これで、各コンテナーを個別に開発して展開できる、複数コンテナー アプリケーションが作成されました。

## <a name="learn-about-team-development"></a>チーム開発について学ぶ

[!INCLUDE[](includes/team-development-1.md)]

実際の動作を見てみましょう。
1. `mywebapi` の VS Code ウィンドウに移動し、既定の GET `/` ハンドラーのコードを編集します。次に例を示します。

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]




