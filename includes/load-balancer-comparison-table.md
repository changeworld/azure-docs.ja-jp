---
title: インクルード ファイル
description: インクルード ファイル
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 4b419bd8c19cffaf84582cb4fa9e46592746da3f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752311"
---
| | Standard SKU | Basic SKU |
| --- | --- | --- |
| バックエンド プールのサイズ | 最大 1,000 インスタンスをサポート。 | 最大 100 インスタンスをサポート。 |
| バックエンド プール エンドポイント | 仮想マシン、可用性セット、仮想マシン スケール セットの組み合わせを含む、単一の仮想ネットワーク内の任意の仮想マシン。 | 単一の可用性セットまたは仮想マシン スケール セット内の仮想マシン。 |
| [正常性プローブ](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP、HTTP、HTTPS | TCP、HTTP |
| [正常性プローブ ダウン動作](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | インスタンス プローブがダウンし、__かつ__ すべてのプローブがダウンしても TCP 接続は存続。 | インスタンス プローブがダウンしても TCP 接続は存続。 すべてのプローブがダウンした場合、すべての TCP 接続は終了。 |
| 可用性ゾーン | 受信トラフィックと送信トラフィック用のゾーン冗長およびゾーン フロントエンド。 送信フローのマッピングは、ゾーンの障害が発生しても保持されます。 クロスゾーン負荷分散 | 使用不可 |
| 診断 | Azure Monitor。 バイト カウンターとパケット カウンターを含む多次元メトリック。 正常性プローブの状態。 接続試行 (TCP SYN)。 送信接続の正常性 (SNAT 成功および失敗のフロー)。 アクティブなデータ プレーン測定。 | パブリック ロード バランサーに対する Azure Log Analytics のみ。 SNAT 枯渇アラート。 バックエンド プール正常性カウント。 |
| HA ポート | 内部ロード バランサー | 使用不可 |
| 既定でのセキュリティ保護 | パブリック IP、パブリック ロード バランサー エンドポイント、内部ロード バランサー エンドポイントへのインバウンド フローは、ネットワーク セキュリティ グループによって許可されない限り禁止されます。 VNET から内部ロードバランサーへの内部トラフィックは、引き続き許可されていることに注意してください。 | 既定で開いています。 ネットワーク セキュリティ グループはオプションです。 |
| [送信接続 (アウトバウンド接続)](../articles/load-balancer/load-balancer-outbound-connections.md) | [送信規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md)を使用して、プールに基づく送信 NAT を明示的に定義できます。 負荷分散規則のオプトアウトごとに複数のフロントエンドを使用できます。仮想マシン、可用性セット、または仮想マシン スケール セットが送信接続 (アウトバウンド接続) を使用するためには、送信シナリオを明示的に作成する "_必要があります_"。 仮想ネットワーク サービス エンドポイントは、送信接続 (アウトバウンド接続) を定義しなくても到達でき、処理されたデータにはカウントされません。 仮想ネットワーク サービス エンドポイントとして使用できない Azure PaaS サービスなどのすべてのパブリック IP アドレスは、送信接続を使用して到達する必要があり、処理されたデータにカウントされます。 仮想マシン、可用性セット、または仮想マシン スケール セットの負荷分散を担うのが内部ロード バランサーだけであるときは、既定の SNAT による送信接続は利用できません。 代わりに[送信規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md)を使用してください。 送信 SNAT プログラミングは、受信負荷分散ルールのトランスポート プロトコルによって異なります。 | 単一のフロントエンド。複数のフロントエンドが存在する場合は、ランダムに選ばれます。 仮想マシン、可用性セット、または仮想マシン スケール セットの負荷分散を担うのが内部ロード バランサーだけであるときは、既定の SNAT が使用されます。 |
| [送信規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | パブリック IP アドレスまたはパブリック IP プレフィックスまたはその両方を使用した、宣言型の送信 NAT 構成。 構成可能な送信アイドル タイムアウト (4 ～ 120 分)。 ポートの割り当てをカスタマイズする | 使用不可 |
| [アイドルの TCP リセット](../articles/load-balancer/load-balancer-tcp-reset.md) | 任意のルールの TCP アイドル タイムアウト に対する TCP リセット (TCP RST) を有効にする | 使用不可 |
| [複数のフロントエンド](../articles/load-balancer/load-balancer-multivip-overview.md) | 受信および[送信](../articles/load-balancer/load-balancer-outbound-connections.md) | 受信のみ |
| 管理操作 | ほとんどの操作は 30 秒未満 | 一般に 60 ～ 90 秒以上 |
| SLA | 2 つの正常な仮想マシンが存在するデータ パスで 99.99%。 | 適用なし | 
| 価格 | ルールの数、リソースに関連付けられた受信および送信で処理されたデータに基づいて課金。 | 課金なし |
|  |  |  |
