---
title: Event Grid リソースでカスタム Azure ポリシーを設定して、セキュリティ ポスチャーを強化する
description: この記事では、カスタム Azure ポリシーを定義してセキュリティ コントロールを行う方法を説明します。
ms.topic: how-to
author: jfggdl
ms.author: jafernan
ms.date: 06/24/2021
ms.openlocfilehash: a9018150d75556f729db593ec1a76fb6aa174798
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992338"
---
# <a name="use-custom-azure-policies-to-enforce-security-controls"></a>カスタム Azure ポリシーでセキュリティ コントロールを行う

この記事では、Event Grid のイベント サブスクリプションで設定できる宛先を管理するカスタム Azure ポリシーのサンプルを提供します。 [Azure Policy](../governance/policy/overview.md) は、セキュリティ、コスト、リソースの整合性、経営などに関するさまざまな課題に対応するために、組織の基準を徹底し、規制を順守するのに役立ちます。そうした課題の中で特に重要なのは、セキュリティ コンプライアンス標準を守って、組織のセキュリティ ポスチャーを維持することです。 セキュリティ コントロールに役立てていただくため、この記事では、承認されていないエンドポイントや Azure サービスへのデータ流出やイベントの送信を防ぐためのポリシーを紹介します。

> [!NOTE]
> Azure Event Grid では、数種類のコンプライアンス基準に関連するセキュリティ コントロールのポリシーなど、コンプライアンス分野の組み込みポリシーを用意しています。 それらの組み込みポリシーは、Event Grid の [[Azure Security Benchmark]](security-controls-policy.md#azure-security-benchmark) にあります。

組織のデータ流出を防ぐには、Event Grid のイベント送信先を制限するといいかもしれません。 これを実行するには、ポリシーで承認されている宛先を有する[イベント サブスクリプション](concepts.md#event-subscriptions)の作成または更新を許可するポリシーを使用します。 リソースの要求を拒否するには、ポリシーの効果を [deny](../governance/policy/concepts/effects.md#deny) に設定します。

次のセクションでは、許可している宛先のリストを有効にするポリシー定義のサンプルを紹介します。 ポリシーを定義するときは、```destination``` を含む [プロパティのエイリアス](../governance/policy/concepts/definition-structure.md#aliases)を検索し、それを ```field``` で指定して、許可している宛先のリストと照合する必要があります。

Event Grid に対し定義されているプロパティのエイリアス (名前空間 ```Microsoft.EventGrid``` を使用) を確認するには、[別の記事のこのセクション](../governance/policy/concepts/definition-structure.md#aliases)で説明している CLI または PowerShell のコマンドを実行します。

ポリシー定義の詳細は「[Azure Policy の定義の構造](../governance/policy/concepts/definition-structure.md)」をご覧ください。

 
## <a name="define-an-azure-policy-with-a-list-of-allowed-destinations-for-a-webhook-destination"></a>許可している Webhook の宛先リストを使用して Azure Policy を定義する

次のポリシー定義では、システムのトピックに対するイベント サブスクリプションで Webhook の宛先に設定しているエンドポイントを制限します。

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/systemTopics/eventSubscriptions/destination.WebHook.endpointUrl",
        "in": "[parameters('allowedDestinationEndpointURLs')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedDestinationEndpointURLs": {
      "type": "Array",
      "metadata": {
        "description": "Allowed event destination endpoint URLs.",
        "displayName": "The list of allowed webhook endpoint destinations to which send events"
      },
        "allowedValues": [
          "https://www.your-valid-destination-goes-here-1.com",
          "https://www.your-valid-destination-goes-here-2.com",
          "https://www.your-valid-destination-goes-here-3.com"
        ]
    }
  }
}
```

## <a name="define-an-azure-policy-with-a-list-of-allowed-azure-service-resource-destinations"></a>Azure サービス リソースで許可している宛先のリストを使用して Azure Policy を定義する

次のポリシー定義では、カスタム トピックに対するイベント サブスクリプションで宛先に設定している特定の Event Hubs を制限します。 [サポートしている他の Azure サービスの宛先](event-handlers.md)も、同様の方法で管理できます。

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/eventSubscriptions/destination.EventHub.resourceId",
        "in": "[parameters('allowedResourceDestinations')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedResourceDestinations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed event delivery destinations.",
        "displayName": "Allowed event delivery destinations"
      },
        "allowedValues": [
          "/subscriptions/<your-event-subscription>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<event-hubs-namespace-name>/eventhubs/<your-event-hub-name>"
        ]
    }
  }
}
```

## <a name="next-steps"></a>次のステップ
- Azure Policy の詳細は、次の記事をご覧ください。 
    - [Azure Policy とは](../governance/policy/overview.md)
    - 「[Azure Policy の定義の構造](../governance/policy/concepts/definition-structure.md)」
    - 「[Azure Policy の効果について](../governance/policy/concepts/effects.md)」
    - 「[Azure Policy でのスコープについて](../governance/policy/concepts/scope.md)」
    - ｢[Visual Studio Code 用の Azure Policy 拡張機能を使用する](../governance/policy/how-to/extension-for-vscode.md)」
    - 「[プログラムによるポリシーの作成](../governance/policy/how-to/programmatically-create.md)」
- Azure Event Grid の詳細は、[Event Grid 用語集](concepts.md)に関する記事などの説明セクションをご覧ください。