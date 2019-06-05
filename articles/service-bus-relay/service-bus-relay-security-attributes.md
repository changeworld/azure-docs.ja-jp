---
title: Azure Service Bus Relay の一般的なセキュリティ属性
description: Azure Service Bus Relay を評価するための一般的なセキュリティ属性のチェックリスト
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000148"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Azure Service Bus Relay のセキュリティ属性

この記事では、Azure Service Bus Relay に組み込まれているセキュリティ属性について説明します。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>|  該当なし | Relay は Web ソケットであり、データは保持されません。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | Service には TLS が必要です。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ | Microsoft の TLS 証明書のみを使用します。  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |
| 強制トンネリングのサポート| 該当なし | Relay は TLS トンネルです  |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | SAS を使用。 |
| 承認|  はい | SAS を使用。 |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。 |
| データ プレーンのログ記録と監査| はい | 接続の成功/失敗およびエラーはログに記録されます。  |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。|
