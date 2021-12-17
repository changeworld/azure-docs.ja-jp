---
title: Azure Container Apps でのスケーリング
description: Azure Container Apps でのアプリケーションのスケールインとスケールアウトの方法について説明します。
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: f404c8c41879dca2fe3b9223c8d7a60c1f943f0a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710215"
---
# <a name="set-scaling-rules-in-azure-container-apps"></a>Azure Container Apps でスケーリング ルールを設定する

Azure Container Apps では、宣言型スケーリング ルールのセットにより、自動水平スケーリングが管理されます。 コンテナー アプリがスケールアウトされるときは、コンテナー アプリの新しいインスタンスがオンデマンドで作成されます。 これらのインスタンスはレプリカと呼ばれます。

スケーリング ルールは、[構成](overview.md)の `resources.properties.template.scale` セクションで定義されます。 コンテナー アプリ内のすべてのルールに適用されるスケーリング プロパティが 2 つあります。

| スケーリング プロパティ | 説明 | 既定値 | 最小値 | 最大値 |
|---|---|---|---|---|
| `minReplicas` | コンテナー アプリで実行されるレプリカの最小数。 | 0 | 1 | 25 |
| `maxReplicas` | コンテナー アプリで実行されるレプリカの最大数。 | 該当なし | 1 | 25 |

- コンテナー アプリがゼロにスケーリングされた場合、課金されることはありません。
- 個々のスケーリング ルールは、`rules` 配列で定義されます。
- アプリケーションのインスタンスが常に必ず実行されているようにしたい場合は、`minReplicas` を 1 以上に設定します。
- 処理されていなくてもメモリに残っているレプリカは、"アイドル料金" のカテゴリで課金されます。
- スケーリング ルールの変更は、[リビジョン スコープ](overview.md)の変更です。
- HTTP 以外のイベント スケール ルールを使用する場合は、`activeRevisionMode` を `single` に設定することをお勧めします。

> [!IMPORTANT]
> レプリカの数は目標値であり、保証はされません。 `maxReplicas` を `1` に設定したとしても、スレッド セーフは保証されません。

## <a name="scale-triggers"></a>スケーリング トリガー

Container Apps では、多数のスケーリング トリガーがサポートされています。 サポートされているスケーリング トリガーについて詳しくは、[KEDA のスケーラー](https://keda.sh/docs/scalers/)に関するページをご覧ください。

KEDA のドキュメントでは YAML のコード例が示されていますが、Container Apps の ARM テンプレートは JSON です。 必要に応じて KEDA の例を変換する場合は、プロパティ名を[ケバブ](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Delimiter-separated_words) ケースから [キャメル](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Letter_case-separated_words) ケースに切り替えてください。

## <a name="http"></a>HTTP

HTTP スケーリング ルールを使うと、スケールアウトするタイミングを決定するしきい値を制御できます。

| スケーリング プロパティ | 説明 | 既定値 | 最小値 | 最大値 |
|---|---|---|---|---|
| `concurrentRequests`| 要求の数がこの値を超えると、さらにレプリカが追加されます (最大 `maxReplicas` 個まで)。 | 50 | 1 | 該当なし |

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": 0,
          "maxReplicas": 5,
          "rules": [{
            "name": "http-rule",
            "http": {
              "metadata": {
                  "concurrentRequests": "100"
              }
            }
          }]
        }
      }
    }
  }
}
```

この例のコンテナー アプリは、最大 5 レプリカまでスケールアウトし、ゼロ インスタンスにスケールダウンできます。 スケーリングしきい値は、1 秒あたり 100 個の同時要求に設定されています。

## <a name="event-driven"></a>イベント ドリブン

Container Apps は、さまざまな種類のイベントに基づいてスケーリングできます。 [KEDA](https://keda.sh/docs/scalers/) でサポートされているすべてのイベントが、Container Apps でサポートされます。

KEDA 定義の `metadata` セクションにあるプロパティは、イベントの種類ごとに異なります。 これらのプロパティを使って、Container Apps のスケーリング ルールを定義します。

次の例では、[Azure Service Bus](https://keda.sh/docs/scalers/azure-service-bus/) のトリガーに基づくスケーリング ルールを作成する方法を示します。

```json
{
  ...
  "resources": {
    ...
    "properties": {
      "configuration": {
        "secrets": [{
          "name": "servicebusconnectionstring",
          "value": "<MY-CONNECTION-STRING-VALUE>"
        }],
      },
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [
          {
            "name": "queue-based-autoscaling",
            "custom": {
              "type": "azure-servicebus",
              "metadata": {
                "queueName": "myServiceBusQueue",
                "messageCount": "20"
              },
              "auth": [{
                "secretRef": "servicebusconnectionstring",
                "triggerParameter": "connection"
              }]
        }
    }]
}
```

この例のコンテナー アプリは、次の動作に従ってスケーリングされます。

- キュー内のメッセージ数が 20 を超えると、新しいレプリカが作成されます。
- キューへの接続文字列は、構成ファイルへのパラメーターとして提供され、`secretRef` プロパティを使って参照されます。

## <a name="cpu"></a>CPU

CPU のスケーリングを使うと、CPU の使用量に応じて、アプリをスケールインまたはスケールアウトできます。 CPU のスケーリングでは、コンテナー アプリを 0 にスケーリングすることはできません。 このトリガーについて詳しくは、[KEDA の CPU スケーリング トリガー](https://keda.sh/docs/scalers/cpu/)に関するページをご覧ください。

次に示すのは、CPU スケーリング ルールを作成する方法の例です。

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [{
            "name": "cpuScalingRule",
            "custom": {
              "type": "cpu",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- この例のコンテナー アプリは、CPU 使用率が 50% を超えるとスケーリングされます。
- CPU 使用率に基づいてスケーリングされるアプリの場合、少なくとも 1 つのレプリカがメモリ内に残ります。

## <a name="memory"></a>メモリ

メモリのスケーリングを使うと、メモリの使用量に応じて、アプリをスケールインまたはスケールアウトできます。 メモリのスケーリングでは、コンテナー アプリを 0 にスケーリングすることはできません。 このスケーラーについて詳しくは、[KEDA のメモリ スケーラー](https://keda.sh/docs/scalers/memory/)に関するページをご覧ください。

次に示すのは、メモリ スケーリング ルールを作成する方法の例です。

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [{
            "name": "memoryScalingRule",
            "custom": {
              "type": "memory",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- この例のコンテナー アプリは、メモリの使用率が 50% を超えるとスケーリングされます。
- メモリ使用率に基づいてスケーリングされるアプリの場合、少なくとも 1 つのレプリカがメモリ内に残ります。


## <a name="considerations"></a>考慮事項

- 垂直方向のスケーリングはサポートされていません。
- レプリカの数は目標値であり、保証はされません。
  - `maxReplicas` を `1` に設定したとしても、スレッド セーフは保証されません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [コンテナー アプリをセキュリティで保護する](secure-app.md)
