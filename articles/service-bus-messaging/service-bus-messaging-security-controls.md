---
title: Azure Service Bus Messaging のセキュリティ コントロール
description: Azure Service Bus Messaging を評価するためのセキュリティ コントロールのチェックリスト
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903246"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure Service Bus Messaging のセキュリティ コントロール

この記事では、Azure Service Bus Messaging に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|---|---|--|--|
| サービス エンドポイントのサポート| はい (Premium レベルのみ) | VNet サービス エンドポイントは、[Service Bus Premium レベル](service-bus-premium-messaging.md)でのみサポートされます。 |  |
| VNet インジェクションのサポート| いいえ | |  |
| ネットワークの分離とファイアウォールのサポート| はい (Premium レベルのみ) |  |  |
| 強制トンネリングのサポート| いいえ |  |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure Monitor とアラート](service-bus-metrics-azure-monitor.md)によってサポートされます。 |  |
| コントロールと管理プレーンのログ記録と監査| はい | 操作ログを利用できます。  | [Service Bus の診断ログ](service-bus-diagnostic-logs.md) |
| データ プレーンのログ記録と監査| いいえ |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| 認証| はい | [Azure Active Directory マネージド サービス ID](service-bus-managed-service-identity.md) を通じて管理されます。| [Service Bus の認証と承認](service-bus-authentication-and-authorization.md) |
| 承認| はい | [RBAC](authenticate-application.md) と SAS トークンを使用した承認をサポートしています。 | [Service Bus の認証と承認](service-bus-authentication-and-authorization.md) |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes | ドキュメント |
|---|---|--|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー |  はい (サーバー側の保存時の暗号化では既定で)。 |  |  |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい。 | Azure KeyVault 内のカスタマー マネージド キーを使用して、保存されている Service Bus 名前空間のデータを暗号化できます。 | [Azure portal を使用して Azure Service Bus 保存データの暗号化用のカスタマー マネージド キーを構成する](configure-customer-managed-key.md)  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |   |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | 標準の HTTPS/TLS メカニズムがサポートされます。 |   |
| API 呼び出しの暗号化| はい | API 呼び出しは、[Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用して行われます。 |   |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes| ドキュメント |
|---|---|--|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager API](/rest/api/resources/) によるリソース プロバイダーのバージョン管理がサポートされます。|   |

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。
