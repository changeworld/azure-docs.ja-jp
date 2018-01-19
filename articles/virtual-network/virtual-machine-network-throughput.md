---
title: "Azure 仮想マシンのネットワーク スループット | Microsoft Docs"
description: "Azure 仮想マシンのネットワーク スループットについて説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: f22b6f361f0c5bea547721309bb0f75b62f18d92
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="virtual-machine-network-bandwidth"></a>仮想マシンのネットワーク帯域幅

Azure の VM には多様なサイズと種類があり、パフォーマンス機能の組み合わせもそれぞれ異なっています。 機能の 1 つがネットワーク スループット (帯域幅) で、メガビット/秒 (Mbps) で測定されます。 仮想マシンは共有ハードウェアでホストされているため、同じハードウェアを共有する仮想マシン間でネットワーク容量が公平に分配される必要があります。 大きな仮想マシンには、小さい仮想マシンよりも相対的に多くの帯域幅が割り当てられます。
 
各仮想マシンに割り当てられたネットワーク帯域幅は、仮想マシンからのエグレス (送信) トラフィックで測定されます。 仮想マシンから送信されるすべてのネットワーク トラフィックは、送信先に関係なく、割り当てられた制限に達するまでカウントされます。 たとえば、ある仮想マシンの制限が 1,000 Mbps である場合、送信トラフィックの送信先が同じ仮想ネットワーク内の仮想マシンであっても Azure 外部であっても、この制限が適用されます。
 
受信は、直接的には測定も制限もされません。 ただし、CPU やストレージの制限などの他の要素があり、仮想マシンの受信データ処理機能に影響する可能性はあります。

高速ネットワークは、ネットワーク パフォーマンス (待ち時間、スループット、CPU 使用率など) の向上を目的として設計された機能です。 高速ネットワークは仮想マシンのスループットを向上させますが、向上できるのは仮想マシンに割り当てられた帯域幅までです。 高速ネットワークの詳細については、[Windows](create-vm-accelerated-networking-powershell.md) または [Linux](create-vm-accelerated-networking-cli.md) 仮想マシンの高速ネットワークに関する記事を参照してください。
 
Azure の仮想マシンには、少なくとも 1 つ (複数可) のネットワーク インターフェイスが接続されている必要があります。 仮想マシンに割り当てられる帯域幅は、仮想マシンに接続されているすべてのネットワーク インターフェイス全体の送信トラフィックの合計です。 つまり、帯域幅は仮想マシンごとに割り当てられており、仮想マシンに接続されているネットワーク インターフェイスの数は関係ありません。 さまざまな Azure VM サイズがサポートするネットワーク インターフェイスの数については、[Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の Azure VM サイズに関するページを参照してください。 

## <a name="expected-network-throughput"></a>想定されるネットワーク スループット

VM の各サイズで想定される送信スループットとサポートされるネットワーク インターフェイスの数については、[Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の Azure VM サイズに関するページを参照してください。 型 (汎用など) を選択し、結果として表示されるページでサイズ シリーズ (Dv2 シリーズなど) を選択します。 各シリーズの表の最後に、**最大 NIC 数/想定ネットワーク パフォーマンス (Mbps)** というネットワーク仕様の列があります。 

このスループット制限が仮想マシンに適用されます。 スループットは、次の要因には影響されません。
- **ネットワーク インターフェイスの数**: 帯域幅の制限は、仮想マシンからのすべての送信トラフィックの累積です。
- **高速ネットワーク**: この機能はスループットを公開された制限まで向上させるためには役立ちますが、制限自体は変更されません。
- **トラフィックの送信先**: すべての送信先が、送信制限に達するまでカウントされます。
- **プロトコル**: すべてのプロトコルに対するすべての送信トラフィックが、制限に達するまでカウントされます。

## <a name="next-steps"></a>次の手順

- [仮想マシンのオペレーティング システムに対するネットワーク スループットを最適化する](virtual-network-optimize-network-bandwidth.md)
- 仮想マシンの[ネットワーク スループットをテストする](virtual-network-bandwidth-testing.md)
