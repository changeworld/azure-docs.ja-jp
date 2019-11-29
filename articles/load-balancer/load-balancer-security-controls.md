---
title: Azure Load Balancer のセキュリティ コントロール
description: Load Balancer を評価するためのセキュリティ コントロールのチェックリスト
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214897"
---
# <a name="security-controls-for-azure-load-balancer"></a>Azure Load Balancer のセキュリティ コントロール

この記事では、Azure Load Balancer に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし | |
| VNet インジェクションのサポート| 該当なし | |
| ネットワークの分離とファイアウォールのサポート| 該当なし |  |
| 強制トンネリングのサポート| 該当なし | |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[パブリック Basic ロード バランサーの Azure Monitor ログ](load-balancer-monitor-log.md)」を参照してください。 |
| コントロールと管理プレーンのログ記録と監査| はい | 「[パブリック Basic ロード バランサーの Azure Monitor ログ](load-balancer-monitor-log.md)」を参照してください。 |
| データ プレーンのログ記録と監査 | 該当なし |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| 該当なし |  |
| Authorization| 該当なし |  |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | 該当なし | |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| 該当なし | |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | 該当なし | |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| 該当なし |  | 

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。