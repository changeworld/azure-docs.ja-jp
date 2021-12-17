---
title: Kubernetes 上の Azure Event Grid - 機能
description: この記事では、Kubernetes 上の Event Grid と Azure 上の Event Grid の機能を比較します。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 3e8d2259c4ae41f5f0433151cef8c6f447e49418
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768105"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-features"></a>Azure Arc を使用した Kubernetes 上の Event Grid の機能
Kubernetes 上の Event Grid には、Kubernetes ワークロードの統合とハイブリッド アーキテクチャの実現に役立つ豊富な機能セットが用意されています。 同じ [REST API](/rest/api/eventgrid/version2021-06-01-preview/topics) (バージョン 2020-10-15-preview 以降)、[Event Grid CLI](/cli/azure/eventgrid)、Azure portal エクスペリエンス、[管理 SDK](../sdk-overview.md#management-sdks)、[データ プレーン SDK](../sdk-overview.md#data-plane-sdks) が、Azure Event Grid (同じサービスの他のエディション) と共有されます。 イベントを発行する準備ができたら、[各種言語で提供されているデータ プレーン SDK のサンプル](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)を使用できます。これは、Event Grid の両方のエディションで動作します。

Kubernetes 上の Event Grid と Azure Event Grid は多くの機能を共有しており、目標は同じユーザー エクスペリエンスを提供することですが、それらが満たそうとする固有の要件と、それらが置かれているソフトウェア ライフサイクルの段階にいくつか違いがあります。 たとえば、Kubernetes 上の Event Grid で使用できるトピックの種類は Event Grid トピックのみです。これはカスタム トピックと呼ばれる場合もあります。 その他の種類のトピック (下記参照) は適用できないか、それらのサポートがまだ利用できません。 下の表に、Event Grid の 2 つのエディションの主な相違点を示します。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="event-grid-on-kubernetes-vs-event-grid-on-azure"></a>Kubernetes 上の Event Grid と Azure 上の Event Grid

| 機能 | Kubernetes 上の Event Grid | Azure Event Grid |
|:--|:-:|:-:|
| [Event Grid トピック](/rest/api/eventgrid/version2021-06-01-preview/topics) | ✔ | ✔ |
| [CNCF Cloud Events スキーマ](https://github.com/cloudevents/spec/blob/master/spec.md) | ✔ | ✔ |
| Event Grid とカスタム スキーマ | ✘* | ✔ |
| 信頼性の高い配信 | ✔ | ✔ |
| メトリック  | ✔** | ✔ |
| Azure Monitor  | ✘ | ✔ |
| [配信不能の場所](../manage-event-delivery.md#set-dead-letter-location) | ✘ | ✔ |
| [別の Event Grid トピックへのイベントの転送](event-handlers.md#azure-event-grid) | ✔ | ✘ |
| [システム トピック](../system-topics.md) | ✘ | ✔ |
| [ドメイン トピック](../event-domains.md) | ✘ | ✔ |
| [パートナー イベント](../partner-events-overview.md) | ✘ | ✔ |
| [宛先エンドポイントの検証](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events) | ✘ | ✔ |
| [Azure Functions の Azure Event Grid トリガー](../../azure-functions/functions-bindings-event-grid-trigger.md) | ✘ | ✔ |
| 宛先としての Azure Relay のハイブリッド接続 | ✘ | ✔ |
| [高度なフィルター処理](filter-events.md) | ✔*** | ✔ |
| [AAD での Webhook AuthN/AuthZ](../secure-webhook-delivery.md) | ✘ | ✔ |
| [リソース ID を使用したイベント配信](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update) | ✘ | ✔ |
| データ プレーン SDK の同じセット | ✔ | ✔ |
| 管理 SDK の同じセット | ✔ | ✔ |
| 同じ Event Grid CLI | ✔ | ✔ |

\* Cloud Events 1.0 スキーマは、機能拡張メカニズムを提供し、オープン標準です。 これらの品質や機能は、Event Grid やカスタム スキーマでは提供されません。 Cloud Events 1.0 スキーマは、Event Grid スキーマが発展したものです。

\** トピックとイベント サブスクリプションのメトリックは、[Prometheus の公開形式](https://prometheus.io/docs/instrumenting/exposition_formats/)を使用して提供されます。 プレビュー バージョンでは、Azure portal のメトリックやその他の監視機能は現在使用できません。

\*** Kubernetes 上の Event Grid は、Azure 上の Event Grid と同様に、イベント データの値に基づいたイベントの高度なフィルター処理をサポートしていますが、Kubernetes 上の Event Grid がサポートしていない機能と演算子がいくつかあります。 詳細については、[高度なフィルター処理](filter-events.md#filter-by-values-in-event-data)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
Kubernetes 上の Event Grid について詳しく学習するために、[Azure Arc を使用した Kubernetes 上の Event Grid (プレビュー) - 概要](overview.md)に関する記事を参照してください。
