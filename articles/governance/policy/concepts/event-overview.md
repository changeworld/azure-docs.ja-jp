---
title: Azure Policy 状態変更イベントに対応する
description: Azure Event Grid を使用してアプリ ポリシーのイベントにサブスクライブすると、複雑なコードを必要とせずに、アプリケーションが状態の変化に対応できます。
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105734923"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Azure Policy 状態変更イベントに対応する

Azure Policy イベントによって、アプリケーションは状態の変化に対応可能になります。 この統合を実行するために、複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。 イベントは、[Azure Event Grid](../../../event-grid/index.yml) を通して、[Azure Functions](../../../azure-functions/index.yml)、[Azure Logic Apps](../../../logic-apps/index.yml) などのサブスクライバー、またはユーザー独自のカスタム HTTP リスナーにプッシュされます。
決定的な点は、使用した分だけ支払うということです。

Azure Policy のイベントは Azure Event Grid に送信されます。そこでは、豊富な再試行ポリシーと配信不能メッセージ配信を使用して、信頼性の高い配信サービスがアプリケーションに提供されます。 詳細については、「[Event Grid のメッセージの配信と再試行](../../../event-grid/delivery-and-retry.md)」を参照してください。

Azure Policy イベントのよくあるシナリオは、リソースのコンプライアンス状態がポリシーの評価中に変わるときに追跡することです。 イベント ベースのアーキテクチャは、リソースのコンプライアンス状態を決まった日程でスキャンすることなくこれらの変更に対応できる、効率的な方法です。

> [!NOTE]
> [評価トリガー](../how-to/get-compliance-data.md#evaluation-triggers)がリソースの評価を完了した後に、Azure Policy 状態変更イベントが Event Grid に送信されます。

詳細なチュートリアルについては、[Azure CLI を使用した Event Grid へのポリシー状態変更イベントのルーティング](../tutorials/route-state-change-events.md)に関する記事を参照してください。

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="ソースとハンドラーの Event Grid モデル" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>使用可能な Azure Policy イベント

Event Grid は、[イベント サブスクリプション](../../../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Azure Policy のイベント サブスクリプションは、3 種類のイベントを含むことができます。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | ポリシー コンプライアンス状態が作成されたときに発生します。 |
| Microsoft.PolicyInsights.PolicyStateChanged | ポリシー コンプライアンス状態が変更されたときに発生します。 |
| Microsoft.PolicyInsights.PolicyStateDeleted | ポリシー コンプライアンス状態が削除されたときに発生します。 |

## <a name="event-schema"></a>イベント スキーマ

Azure Policy イベントには、データの変更に対応するために必要なすべての情報が含まれます。 Azure Policy イベントは、`eventType` プロパティが "Microsoft.PolicyInsights" で始まるとき識別できます。
Event Grid イベントのプロパティの使用法について詳しくは、  
[Event Grid イベント スキーマ](../../../event-grid/event-schema.md)に関する記事をご覧ください。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `id` | string | イベントの一意識別子。 |
| `topic` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | コンプライアンス状態の変更の対象となるリソースの完全修飾 ID。リソース名とリソースの種類が含まれます。 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` の形式が使用されます。 |
| `data` | object | Azure Policy イベントのデータ。 |
| `data.timestamp` | string | リソースが Azure Policy によってスキャンされた時刻 (UTC)。 イベントを並べ替えるには、このプロパティを、最上位レベルの `eventTime` または `time` プロパティの代わりに使用します。 |
| `data.policyAssignmentId` | string | ポリシー割り当てのリソース ID。 |
| `data.policyDefinitionId` | string | ポリシー定義のリソース ID。 |
| `data.policyDefinitionReferenceId` | string | イニシアチブ定義内のポリシー定義の参照 ID (ポリシー割り当てがイニシアチブの場合)。 空の場合もあります。 |
| `data.complianceState` | string | ポリシー割り当てに関するリソースのコンプライアンス状態。 |
| `data.subscriptionId` | string | リソースのサブスクリプション ID。 |
| `data.complianceReasonCode` | string | コンプライアンスの理由コード 空の場合もあります。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

ポリシー状態変更イベントの例を、以下に示します。

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

詳細については、[Azure Policy イベントのスキーマ](../../../event-grid/event-schema-policy.md)に関する記事をご覧ください。

## <a name="practices-for-consuming-events"></a>イベントの使用に関する手法

Azure Policy イベントを処理するアプリケーションは、こちらの推奨プラクティスに従う必要があります。

> [!div class="checklist"]
> - 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションを構成できるため、イベントが特定のソースからのものであると想定しないでください。 代わりに、メッセージのトピックを調べて、状態変更イベントが発生しているポリシー割り当て、ポリシー定義、およびリソースを確認します。
> - `eventType` を確認します。受信するすべてのイベントが予期する種類であると想定してはいけません。
> - 最上位レベルの `eventTime` または `time` プロパティを使用する代わりに、`data.timestamp` を使用して Azure Policy 内のイベントの順序を確認します。
> - 件名フィールドを使用して、ポリシー状態が変更されたリソースにアクセスします。

## <a name="next-steps"></a>次のステップ

Event Grid の詳細について理解し、Azure Policy の状態変更イベントを試してみてください。

- [Azure CLI を使用した Event Grid へのポリシー状態変更イベントのルーティング](../tutorials/route-state-change-events.md)
- [Event Grid の Azure Policy スキーマの詳細](../../../event-grid/event-schema-policy.md)
- [Event Grid について](../../../event-grid/overview.md)