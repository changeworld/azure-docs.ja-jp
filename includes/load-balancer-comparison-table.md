---
title: インクルード ファイル
description: インクルード ファイル
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 8/8/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 6514bcdbe79db4a22b2a4bf13e5808bbb9abbb06
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40026179"
---
| | Standard SKU | Basic SKU |
| --- | --- | --- |
| バックエンド プールのサイズ | 最大 1000 インスタンスをサポート | 最大 100 インスタンスをサポート |
| バックエンド プール エンドポイント | 仮想マシン、可用性セット、仮想マシン スケール セットの組み合わせを含む、単一の仮想ネットワーク内の任意の仮想マシン。 | 単一の可用性セットまたは仮想マシン スケール セット内の仮想マシン。 |
| [正常性プローブ](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP、HTTP、HTTPS | TCP、HTTP |
| [正常性プローブ ダウン動作](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | インスタンス プローブがダウンし、__かつ__すべてのプローブがダウンしても TCP 接続は存続。 | インスタンス プローブがダウンしても TCP 接続は存続。 すべてのプローブがダウンした場合、TCP 接続は終了。 |
| 可用性ゾーン | Basic SKU では、受信と送信に対するゾーン冗長とゾーン フロントエンド、送信フロー マッピングによりゾーン障害に耐久、ゾーン間の負荷分散。 | 該当なし|
| 診断 | Azure Monitor、バイト カウンターとパケット カウンターを含む多次元メトリック、正常性プローブの状態、接続試行 (TCP SYN)、送信接続の正常性 (SNAT 成功および失敗のフロー)、アクティブなデータ プレーン測定 | パブリック ロード バランサーに対する Azure Log Analytics のみ、SNAT 枯渇アラート、バックエンド プール正常性カウント。 |
| HA ポート | 内部ロード バランサー | 該当なし |
| 既定でのセキュリティ保護 | パブリック IP とロード バランサー エンドポイントに対して既定でクローズ、トラフィックが流れるためにはネットワーク セキュリティ グループを使ってホワイトリストへの明示的な登録が必要。 | 既定でオープン、ネットワーク セキュリティ グループは任意。 |
| [送信接続](../articles/load-balancer/load-balancer-outbound-connections.md) | 負荷分散規則によるオプトアウトを使用する複数のフロントエンド。仮想マシンが送信接続を使用できるためには、送信シナリオを明示的に作成する "_必要があります_"。  仮想ネットワーク サービス エンドポイントには送信接続なしで到達でき、処理されたデータにはカウントされません。  VNet サービス エンドポイントとして使用できない Azure PaaS サービスなどのすべてのパブリック IP アドレスは、送信接続を介して到達する必要があり、処理されたデータにカウントされます。 内部ロード バランサーだけが仮想マシンに対応しているときは、既定の SNAT による送信接続は利用できません。 送信 SNAT プログラミングは、受信負荷分散ルールのプロトコルに基づくトランスポート プロトコル固有です。 | 単一のフロントエンド。複数のフロントエンドが存在する場合は、ランダムに選ばれます。  内部ロード バランサーだけが仮想マシンに対応している場合は、既定の SNAT が使われます。 |
| [複数のフロントエンド](../articles/load-balancer/load-balancer-multivip-overview.md) | 受信および[送信](../articles/load-balancer/load-balancer-outbound-connections.md) | 受信のみ |
| 管理操作 | ほとんどの操作は 30 秒未満 | 一般に 60 ～ 90 秒以上。 |
| SLA | 2 つの正常な仮想マシンが存在するデータ パスで 99.99%。 | VM SLA で暗黙。 | 
| 価格 | ルールの数、リソースに関連付けられた受信または送信で処理されたデータに基づいて課金。  | 課金なし |
|  |  |  |
