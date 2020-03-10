---
title: インクルード ファイル
description: インクルード ファイル
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202481"
---
| | Standard Load Balancer | Basic Load Balancer |
| --- | --- | --- |
| [バックエンド プールのサイズ](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | 最大 1,000 インスタンスをサポート。 | 最大 300 インスタンスをサポート。 |
| バックエンド プール エンドポイント | 単一の仮想ネットワーク内の任意の仮想マシンまたは仮想マシン スケール セット。 | 単一の可用性セットまたは仮想マシン スケール セット内の仮想マシン。 |
| [正常性プローブ](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP、HTTP、HTTPS | TCP、HTTP |
| [正常性プローブ ダウン動作](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | インスタンス プローブがダウンし、__かつ__ すべてのプローブがダウンしても TCP 接続は存続。 | インスタンス プローブがダウンしても TCP 接続は存続。 すべてのプローブがダウンした場合、すべての TCP 接続は終了。 |
| 可用性ゾーン | 受信トラフィックと送信トラフィック用のゾーン冗長およびゾーン フロントエンド。 | 使用不可 |
| 診断 | [Azure Monitor 多次元メトリック](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Azure Monitor ログ](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA ポート | [内部ロード バランサーで使用可能](../articles/load-balancer/load-balancer-ha-ports-overview.md) | 使用不可 |
| 既定でのセキュリティ保護 | ネットワーク セキュリティ グループで許可されている場合を除き、受信フローは禁止されます。 VNet から内部ロードバランサーへの内部トラフィックは、許可されていることに注意してください。 | 既定で開いています。 ネットワーク セキュリティ グループはオプションです。 |
| 送信の規則 | [宣言型の送信 NAT 構成](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | 使用不可 |
| アイドル時の TCP リセット | [任意のルールで利用可能](../articles/load-balancer/load-balancer-tcp-reset.md) | 使用不可 |
| [複数のフロントエンド](../articles/load-balancer/load-balancer-multivip-overview.md) | 受信および[送信](../articles/load-balancer/load-balancer-outbound-connections.md) | 受信のみ |
| 管理操作 | ほとんどの操作は 30 秒未満 | 一般に 60 ～ 90 秒以上 |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | 使用不可 | 
