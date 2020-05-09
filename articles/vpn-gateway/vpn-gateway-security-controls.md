---
title: Azure VPN Gateway のセキュリティ コントロール
description: Azure VPN Gateway を評価するためのセキュリティ コントロールのチェックリスト
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127852"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Azure VPN Gateway のセキュリティ コントロール

この記事では、Azure VPN Gateway に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし | |
| VNet インジェクションのサポート| 該当なし | |
| ネットワークの分離とファイアウォールのサポート| はい | VPN ゲートウェイは、顧客の仮想ネットワークごとの専用の VM インスタンスです。  |
| 強制トンネリングのサポート| はい |  |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure Monitor ログ アラート](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor メトリック アラート](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)に関するページを参照してください。  |
| コントロールと管理プレーンのログ記録と監査| はい | Azure Resource Manager アクティビティ ログ。 |
| データ プレーンのログ記録と監査 | はい | VPN 接続のログ記録と監査のための [Azure Monitor アクティビティ ログ](../azure-resource-manager/management/view-activity-logs.md)。 |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 認証| はい | サービスを管理し、Azure VPN ゲートウェイを構成するための [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)。 |
| 承認| はい | [RBAC](../role-based-access-control/overview.md) 経由で承認をサポートします。 |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | 該当なし | VPN ゲートウェイは顧客データを格納するのではなく、顧客データを転送します。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | VPN ゲートウェイは、Azure VPN ゲートウェイと顧客のオンプレミスの VPN デバイス (S2S) または VPN クライアント (P2S) の間の顧客パケットを暗号化します。 VPN ゲートウェイはまた、VNet 間の暗号化もサポートします。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ | 顧客が指定した仮共有キーは保存時に暗号化されますが、まだ CMK には統合されていません。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用します。  |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | 管理操作では、Azure VPN ゲートウェイ構成の状態を Azure Resource Manager テンプレートとしてエクスポートしたり、一定期間にわたってバージョン管理したりできます。 |

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について学習します。
