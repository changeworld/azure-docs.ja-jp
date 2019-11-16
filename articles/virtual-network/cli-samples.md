---
title: 仮想ネットワーク用の Azure CLI サンプル
description: 仮想ネットワーク用の Azure CLI サンプル。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: ff1017c8a6bc3c2806da6ea2a53b931aaf28644f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092089"
---
# <a name="azure-cli-samples-for-virtual-network"></a>仮想ネットワーク用の Azure CLI サンプル

次の表には、Azure CLI コマンドを使用する Bash スクリプトへのリンクが含まれています。

| | |
|----|----|
| [多層アプリケーション用の仮想ネットワークの作成](./scripts/virtual-network-cli-sample-multi-tier-application.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへのトラフィックは HTTP と SSH に制限され、バックエンド サブネットへのトラフィックは MySQL、ポート 3306 に制限されます。 |
| [2 つの仮想ネットワークのピアリング](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | 同じリージョンに 2 つの仮想ネットワークを作成し、接続します。 |
| [ネットワーク仮想アプライアンス経由のトラフィックのルーティング](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークと、2 つのサブネット間でトラフィックをルーティングできる VM を作成します。 |
| [VM ネットワークの着信トラフィックおよび送信トラフィックのフィルター処理](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへの着信ネットワーク トラフィックは、HTTP、HTTPS、SSH に制限されます。 バックエンド サブネットからインターネットへの送信トラフィックは許可されません。 |
|[Basic Load Balancer を使用した IPv4 と IPv6 のデュアル スタック仮想ネットワークの構成](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|2 つの VM と IPv4 および IPv6 のパブリック IP アドレスを使用した Azure Basic Load Balancer によるデュアル スタック (IPv4+IPv6) の仮想ネットワークを展開します。 |
|[Standard Load Balancer を使用した IPv4 と IPv6 のデュアル スタック仮想ネットワークの構成](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|2 つの VM と IPv4 および IPv6 のパブリック IP アドレスを使用した Azure Standard Load Balancer によるデュアル スタック (IPv4+IPv6) の仮想ネットワークを展開します。 |