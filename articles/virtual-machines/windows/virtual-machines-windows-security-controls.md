---
title: Azure Windows Virtual Machines のセキュリティ コントロール
description: Azure Windows Virtual Machines を評価するためのセキュリティ コントロールのチェックリスト
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455344"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Windows Virtual Machines のセキュリティ コントロール

この記事では、Windows Virtual Machines に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| サービス エンドポイントのサポート| はい | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| はい |  |
| 強制トンネリングのサポート| はい | 「[Azure Resource Manager デプロイ モデルを使用した強制トンネリングの構成](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)」を参照してください。 |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure で Windows 仮想マシンの監視と更新を行う](tutorial-monitoring.md)。 |
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査 | いいえ |  |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 認証| はい |  |
| 承認| はい |  |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | Notes |
|---|---|--|
| 保存時のサーバー側の暗号化: Microsoft のマネージド キー | はい | 「[Windows VM の仮想ディスクを暗号化する](/azure/virtual-machines/windows/disk-encryption-overview)」を参照してください。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Virtual Machines では、[ExpressRoute](/azure/expressroute) と VNet の暗号化がサポートされています。 「[VM での転送中の暗号化](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | はい | カスタマー マネージド キーは、サポートされている Azure の暗号化シナリオです。「[Azure の暗号化の概要](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS および TLS 経由。 |



## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | Notes|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  | 

## <a name="next-steps"></a>次のステップ

- [Azure サービス全体の組み込みセキュリティ コントロール](../../security/fundamentals/security-controls.md)について学習します。
