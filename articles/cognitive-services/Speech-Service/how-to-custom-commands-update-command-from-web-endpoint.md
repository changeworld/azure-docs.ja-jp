---
title: Web エンドポイントからコマンドを更新する
titleSuffix: Azure Cognitive Services
description: Web エンドポイントからコマンドを更新します
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94572664"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Web エンドポイントからコマンドを更新する

クライアント アプリケーションから、音声入力を使用しないで、実行中のコマンドの状態を更新する必要がある場合は、Web エンドポイントの呼び出しを使用してコマンドを更新できます。

この記事では、Web エンドポイントから実行中のコマンドを更新する方法について説明します。

## <a name="prerequisites"></a>前提条件
> [!div class = "checklist"]
> * 以前に[作成したカスタム コマンド アプリ](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Azure Function の作成 

この例では、次の入力 (またはこの入力のサブセット) をサポートする、HTTP によってトリガーされる [Azure 関数](https://docs.microsoft.com/azure/azure-functions/)が必要です。

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

この入力のキー属性を確認します。

| 属性 | 説明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "conversationId" は、会話の一意の識別子です。クライアント アプリからこの ID を生成できることにご注意ください。 |
| **currentCommand** | "currentCommand" は、会話で現在アクティブになっているコマンドです。 |
| **name** | "name" はコマンドの名前で、"parameters" はパラメーターの現在の値とのマップです。 |
| **currentGlobalParameters** | "currentGlobalParameters" も "parameters" のようなマップですが、グローバル パラメーターに使用されます。 |

Azure 関数の出力で、次の形式がサポートされている必要があります。

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

この形式は、[クライアントからコマンドを更新する](./how-to-custom-commands-update-command-from-client.md)ときに使用したものと同じなので、見覚えがあるかもしれません。 

次に、NodeJS に基づく Azure 関数を作成し、次のコードをコピーして貼り付けます

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Custom Commands からこの Azure 関数を呼び出すと、会話の現在の値が送信され、更新するパラメーターまたは現在のコマンドをキャンセルする必要がある場合のパラメーターが返されます。

## <a name="update-the-existing-custom-commands-app"></a>既存の Custom Commands アプリを更新する

次に、Azure 関数と既存の Custom Commands アプリを接続します。

1. IncrementCounter という名前の新しいコマンドを追加します。
1. 値 "increment" を含むサンプル文を 1 つだけ追加します。
1. Counter という名前 (上の Azure 関数で指定したものと同じ名前) で数値型の新しいパラメーターを追加します。既定値は 0 です。
1. Azure 関数の URL を使用し、リモート更新を有効にして、IncrementEndpoint という名前の新しい Web エンドポイントを追加します。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="リモート更新を有効にした Web エンドポイントの設定":::
1. "IncrementRule" という名前の新しい相互作用ルールを作成し、Web エンドポイントの呼び出しアクションを追加します。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="増分ルール":::
1. アクションの構成で、IncrementEndpoint を選択し、成功したら値をカウンターにして音声応答を送信し、失敗したらエラー メッセージを送信するように構成します。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="インクリメント カウンター呼び出しエンドポイントの設定":::
1. ルールの実行後の状態をユーザー入力待機に設定します

## <a name="test-it"></a>テストする

1. アプリを保存してトレーニングします
1. [テスト] をクリックします
1. 数回 "increment" を送信します (IncrementCounter コマンドの例の文)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="インクリメント カウンターの例":::

Azure 関数によってターンごとに Counter パラメーターの値がインクリメントされることを確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム コマンド アプリケーションの CI/CD プロセスを有効にする](./how-to-custom-commands-deploy-cicd.md)