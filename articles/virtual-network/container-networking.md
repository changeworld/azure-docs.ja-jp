---
title: "コンテナー用の Azure 仮想ネットワーク | Microsoft Docs"
description: "仮想ネットワークでコンテナーが別のコンテナーや他のリソースと通信できるようにする、Kubernetes クラスター用の CNI プラグインについて説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>コンテナー ネットワーク

Azure では、独自の Kubernetes クラスターをデプロイおよび監視できるネイティブの Azure ネットワーク機能を備えた [Container Networking Interface (CNI) プラグイン](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) を提供しています。 [Azure Container Service エンジン](https://github.com/Azure/acs-engine) (ACS エンジン) を備えた Kubernetes クラスターをデプロイする場合、このプラグインは既定で有効になります。

## <a name="networking-capabilities"></a>ネットワーク機能

コンテナーでは、仮想ネットワークが提供する以下のような多くの機能を使用できます。
-   クラスター用に仮想ネットワークを別途作成したり、既存の仮想ネットワークにクラスターをデプロイしたりできます。 
-   クラスター内のすべてのポッドで仮想ネットワーク内から IP アドレスを受信し、クラスター内の他のポッドや仮想ネットワーク内のあらゆる仮想マシンと直接通信できます。 
-   ポッドは、ExpressRoute 接続やサイト間 VPN 接続経由で、ピアリングされた仮想ネットワーク内の他のポッドや仮想マシンに接続したり、オンプレミス ネットワークと接続したりできます。 オンプレミス リソースはポッドと通信できます。 
-   Azure Load Balancer を使用して、Kubernetes サービスをインターネットに公開できます。  
-   サービス エンドポイントが有効なサブネット内のポッドは、Azure サービス (Azure Storage や SQL Database など) に安全に接続できます。
-   ユーザー定義のルートを使用して、ポッドのトラフィックをネットワーク仮想アプライアンスにルーティングできます。 
-   ポッドはインターネット上のパブリック リソースにアクセスできます。
-   ポッドにパブリック IP アドレスを割り当て、それに DNS 名を関連付けることができます。
 
## <a name="limits"></a>制限
このプラグインを使用する場合、Kubernetes クラスターにデプロイできるノードの最大数は 4,000 で、ノードごとのポッドの最大数は 250 となります。全体としては、ノードごとのポッド数は 16,000 に制限されます。

## <a name="constraints"></a>制約
- [Azure Container Service (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を備えた Kubernetes クラスター、または Kubernetes を備えた [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) をデプロイする場合、このプラグインは有効になりません。
- Kubernetes ネットワーク ポリシー (DNS ポリシーやアクセス ポリシーを含む) に対するネイティブ サポートはありません。
- このプラグインでは、ポッドごとのネットワーク ポリシーはサポートされません。

## <a name="pricing"></a>価格
CNI プラグインは無料で使用できます。

## <a name="next-steps"></a>次の手順

[独自の仮想ネットワーク](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni)に [Kubernetes クラスターをデプロイする](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md)方法を学習します。
