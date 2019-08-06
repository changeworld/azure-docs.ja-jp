---
title: Azure ExpressRoute のセキュリティ属性
description: Azure ExpressRoute を評価するためのセキュリティ属性のチェックリスト
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c9a46497c18b99ad7774036fd92e63d024b47045
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442194"
---
# <a name="security-attributes-for-azure-expressroute"></a>Azure ExpressRoute のセキュリティ属性

この記事では、Azure ExpressRoute に組み込まれているセキュリティ属性について説明します。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能)|  該当なし | ExpressRoute は顧客データを格納しません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| いいえ | |
| 暗号化キーの処理 (CMK や BYOK など)| 該当なし |  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用します。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし |  |
| VNet インジェクションのサポート| 該当なし | |
| ネットワークの分離とファイアウォールのサポート| はい | 各顧客は独自のルーティング ドメインに含まれ、独自の VNet にトンネリングされます |
| 強制トンネリングのサポート| 該当なし | Border Gateway Protocol (BGP) を使用。 |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[ExpressRoute の監視、メトリック、およびアラート](expressroute-monitoring-metrics-alerts.md)」を参照してください。|

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | Gateway for Microsoft (GWM) (コントローラー) 用のサービス アカウント。開発と運用のための Just-In-Time (JIT) アクセス。 |
| Authorization|  はい |Gateway for Microsoft (GWM) (コントローラー) 用のサービス アカウント。開発と運用のための Just-In-Time (JIT) アクセス。 |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ| 
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査| いいえ |   |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | ネットワーク リソース プロバイダー (NRP) を使用。 |
