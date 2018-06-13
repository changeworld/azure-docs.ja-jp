---
title: 仮想ネットワーク用の Azure CLI サンプル | Microsoft Docs
description: 仮想ネットワーク用の Azure CLI サンプル。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: baa4dbc8f95e068eb1a939fdee53fb2a4ee8117f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841362"
---
# <a name="azure-cli-samples-for-virtual-network"></a>仮想ネットワーク用の Azure CLI サンプル

次の表には、Azure CLI コマンドを使用する Bash スクリプトへのリンクが含まれています。

| | |
|----|----|
| [多層アプリケーション用の仮想ネットワークの作成](./scripts/virtual-network-cli-sample-multi-tier-application.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへのトラフィックは HTTP と SSH に制限され、バックエンド サブネットへのトラフィックは MySQL、ポート 3306 に制限されます。 |
| [2 つの仮想ネットワークのピアリング](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | 同じリージョンに 2 つの仮想ネットワークを作成し、接続します。 |
| [ネットワーク仮想アプライアンス経由のトラフィックのルーティング](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークと、2 つのサブネット間でトラフィックをルーティングできる VM を作成します。 |
| [VM ネットワークの着信トラフィックおよび送信トラフィックのフィルター処理](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへの着信ネットワーク トラフィックは、HTTP、HTTPS、SSH に制限されます。 バックエンド サブネットからインターネットへの送信トラフィックは許可されません。 |