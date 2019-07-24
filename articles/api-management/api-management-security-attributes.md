---
title: Azure API Management のセキュリティ属性
description: API Management を評価するための一般的なセキュリティ属性のチェックリスト
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b5826d472b80179c5eb76e0e3a6b1c7ee282487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001091"
---
# <a name="security-attributes-for-api-management"></a>API Management のセキュリティ属性

この記事では、API Management に組み込まれているセキュリティ属性について説明します。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい (サービス側の暗号化のみ) | 証明書、キー、およびシークレットという名前付きの値などの機密データは、サービスで管理される、サービス インスタンスごとのキーで暗号化されます。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | [Express Route](../expressroute/index.yml) と VNet 暗号化は、[Azure ネットワーク](../virtual-network/index.yml)によって提供されます。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ | すべての暗号化キーはサービス インスタンスごとに存在し、サービスで管理されます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | 管理プレーン呼び出しは、TLS 経由で [Azure Resource Manager](../azure-resource-manager/index.yml) によって行われます。 有効な JSON Web トークン (JWT) が必要です。  データ プレーン呼び出しは、TLS と、サポートされる認証メカニズムのいずれか (例: クライアント証明書や JWT など) で保護できます。
 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| はい | それぞれ、ネットワーク セキュリティ グループ (NSG) と Azure Application Gateway (またはその他のソフトウェア アプライアンス) を使用します。 |
| 強制トンネリングのサポート| はい | Azure ネットワークでは、強制トンネリングを提供します。 |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | |
| Authorization| はい | |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | [Azure Monitor のアクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md) |
| データ プレーンのログ記録と監査| はい | [Azure Monitor 診断ログ](../azure-monitor/platform/diagnostic-logs-overview.md)と (必要に応じて) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)。  |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure API Management DevOps リソース キット](https://aka.ms/apimdevops)を使用 |

## <a name="vulnerability-scans-false-positives"></a>脆弱性スキャンの偽陽性

このセクションでは一般的な脆弱性について説明します。これらは Azure API Management には影響しません。

| 脆弱性               | 説明                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed は、一部の F5 製品で検出された TLS SessionTicket 拡張機能の実装に含まれる脆弱性です。 初期化されていないメモリから最大 31 バイトのデータがリーク ("流出") する可能性があります。 これは、クライアントから渡される TLS スタックをセッション ID でパディングして、データを 32 ビットの長さにすることが原因で発生します。 |
