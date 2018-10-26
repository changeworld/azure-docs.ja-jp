---
title: インクルード ファイル
description: インクルード ファイル
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 9/26/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 74835734a1dd37e07efa96fd3aa76fc0d349e47f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454530"
---
| | Standard SKU | Basic SKU |
| --- | --- | --- |
| バックエンド プールのサイズ | 最大 1000 インスタンスをサポート | 最大 100 インスタンスをサポート |
| バックエンド プール エンドポイント | 仮想マシン、可用性セット、仮想マシン スケール セットの組み合わせを含む、単一の仮想ネットワーク内の任意の仮想マシン。 | 単一の可用性セットまたは仮想マシン スケール セット内の仮想マシン。 |
| [正常性プローブ](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP、HTTP、HTTPS | TCP、HTTP |
| [正常性プローブ ダウン動作](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | インスタンス プローブがダウンし、__かつ__ すべてのプローブがダウンしても TCP 接続は存続。 | インスタンス プローブがダウンしても TCP 接続は存続。 すべてのプローブがダウンした場合、TCP 接続は終了。 |
| 可用性ゾーン | Basic SKU では、受信と送信に対するゾーン冗長とゾーン フロントエンド、送信フロー マッピングによりゾーン障害に耐久、ゾーン間の負荷分散。 | 利用不可 |
| 診断 | Azure Monitor、バイト カウンターとパケット カウンターを含む多次元メトリック、正常性プローブの状態、接続試行 (TCP SYN)、送信接続の正常性 (SNAT 成功および失敗のフロー)、アクティブなデータ プレーン測定 | パブリック ロード バランサーに対する Azure Log Analytics のみ、SNAT 枯渇アラート、バックエンド プール正常性カウント。 |
| HA ポート | 内部ロード バランサー | 使用できません。 |
| 既定でのセキュリティ保護 | パブリック IP とロード バランサー エンドポイントに対する着信は既定でクローズ、トラフィックが流れるためにはネットワーク セキュリティ グループを使ってホワイトリストへの明示的な登録が必要。 | 既定でオープン、ネットワーク セキュリティ グループは任意。 |
| [送信接続 (アウトバウンド接続)](../articles/load-balancer/load-balancer-outbound-connections.md) | [送信規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md)を使用して、プールに基づく送信 NAT を明示的に定義できます。 負荷分散規則のオプトアウトごとに複数のフロントエンドを使用できます。仮想マシンが送信接続 (アウトバウンド接続) を使用できるためには、送信シナリオを明示的に作成する "_必要があります_"。  仮想ネットワーク サービス エンドポイントには送信接続 (アウトバウンド接続) なしで到達でき、処理されたデータにはカウントされません。  VNet サービス エンドポイントとして使用できない Azure PaaS サービスなどのすべてのパブリック IP アドレスは、送信接続 (アウトバウンド接続) を介して到達する必要があり、処理されたデータにカウントされます。 内部ロード バランサーだけが仮想マシンに対応しているときは、既定の SNAT による送信接続は利用できません。代わりに [送信規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md)を使用してください。 送信 SNAT プログラミングは、受信負荷分散ルールのプロトコルに基づくトランスポート プロトコル固有です。 | 単一のフロントエンド。複数のフロントエンドが存在する場合は、ランダムに選ばれます。  内部ロード バランサーだけが仮想マシンに対応している場合は、既定の SNAT が使われます。 |
| [送信規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | 宣言型の NAT 構成、どのパブリック IP アドレスまたはパブリック IP プレフィックスを含めるか、構成可能な送信アイドル タイムアウト、カスタム SNAT ポートの割り当て | 使用できません。 |
|  [アイドルの TCP リセット](../articles/load-balancer/load-balancer-tcp-reset.md) | 任意のルールの TCP アイドル タイムアウト に対する TCP リセット (TCP RST) を有効にする | 使用できません。 |
| [複数のフロントエンド](../articles/load-balancer/load-balancer-multivip-overview.md) | 受信および[送信](../articles/load-balancer/load-balancer-outbound-connections.md) | 受信のみ |
| 管理操作 | ほとんどの操作は 30 秒未満 | 一般に 60 ～ 90 秒以上。 |
| SLA | 2 つの正常な仮想マシンが存在するデータ パスで 99.99%。 | [VM SLA で暗黙](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)。 | 
| 価格 | ルールの数、リソースに関連付けられた受信または送信で処理されたデータに基づいて課金。  | 課金なし |
|  |  |  |
