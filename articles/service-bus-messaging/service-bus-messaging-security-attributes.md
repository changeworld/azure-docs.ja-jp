---
title: Azure Service Bus Messaging のセキュリティ属性
description: Azure Service Bus Messaging を評価するためのセキュリティ属性のチェックリスト
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 875754282f9e7a2b8dd92b0eb2d7e277ac842696
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013107"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Azure Service Bus Messaging のセキュリティ属性

この記事では、Azure Service Bus Messaging に組み込まれているセキュリティ属性について説明します。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能)|  はい (サーバー側の保存時の暗号化では既定で)。 | 顧客管理キーと BYOK はまだサポートされていません。 クライアント側の暗号化はクライアントの責任です |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | 標準の HTTPS/TLS メカニズムがサポートされます。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |   |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | API 呼び出しは、[Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用して行われます。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい (Premium レベルのみ) | VNet サービス エンドポイントは、[Service Bus Premium レベル](service-bus-premium-messaging.md)でのみサポートされます。 |
| VNet インジェクションのサポート| いいえ | |
| ネットワークの分離とファイアウォールのサポート| はい (Premium レベルのみ) |  |
| 強制トンネリングのサポート| いいえ |  |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure Monitor とアラート](service-bus-metrics-azure-monitor.md)によってサポートされます。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | [Azure Active Directory マネージド サービス ID](service-bus-managed-service-identity.md)を使用して管理されます (「[Service Bus の認証と承認](service-bus-authentication-and-authorization.md)」を参照)。|
| Authorization| はい | [RBAC](authenticate-application.md) と SAS トークンによる承認がサポートされます (「[Service Bus の認証と承認](service-bus-authentication-and-authorization.md)」を参照)。 |



## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | 操作ログを使用できます (「[Service Bus 診断ログ](service-bus-diagnostic-logs.md)」を参照)。  |
| データ プレーンのログ記録と監査| いいえ |  |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager API](/rest/api/resources/) によるリソース プロバイダーのバージョン管理がサポートされます。|
