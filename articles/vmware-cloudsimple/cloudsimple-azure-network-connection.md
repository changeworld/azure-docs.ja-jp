---
title: CloudSimple による VMware ソリューション - Azure ネットワーク接続
description: Azure 仮想ネットワークを CloudSimple 領域ネットワークに接続する方法について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497620"
---
# <a name="azure-network-connections-overview"></a>Azure ネットワーク接続の概要

領域内で、CloudSimple サービスを作成すると、以下が行われます。

* Azure ExpressRoute 回線を作成し、そのリージョンでサービスに添付します。
* Azure 仮想ネットワークまたは Azure ExpressRoute を使用するオンプレミス ネットワークに CloudSimple リージョン ネットワークから接続できます。
* Azure サブスクリプションで稼働するサービス、またはオンプレミス ネットワークで実行されているサービスにプライベート クラウド環境からアクセスします。

この接続は次のとおりです。

* セキュリティ保護
* プライベート
* 高帯域幅
* 待ち時間の短縮

## <a name="benefits"></a>メリット

Azure のネットワーク接続により以下をすることができます。

* プライベート クラウド上の仮想マシンのバックアップ ターゲットとして Azure を使用します。
* プライベート クラウドの vSAN データストアを暗号化する Azure サブスクリプション内の KMS サーバーを展開します。
* ハイブリッド アプリケーションを使用します。アプリケーションの Web 層は、アプリケーションおよびデータベースの層がプライベート クラウドで実行する間にパブリック クラウドで実行します。

## <a name="azure-virtual-network-connection"></a>Azure 仮想ネットワーク接続

ExpressRoute を使用して、プライベート クラウドを Azure リソースに接続できます。  この接続を使用して、プライベート クラウドから Azure サブスクリプションで実行されているさまざまなリソースにアクセスできます。  この接続により、プライベート クラウド ネットワークを Azure 仮想ネットワークに拡張できます。

![仮想ネットワークへの Azure ExpressRoute 接続](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute のオンプレミス ネットワークへの接続。

CloudSimple リージョンに、既存の Azure ExpressRoute 回線を接続できます。 ExpressRoute Global Reach 機能により、2 つの回線を相互に接続します。  オンプレミスと CloudSimple ExpressRoute 回線の間に接続が確立されます。  この接続により、オンプレミス ネットワークをプライベート クラウド ネットワークに拡張できます。

![オンプレミスの ExpressRoute 接続 - Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>次の手順

* [Azure 仮想ネットワークから CloudSimple 接続へのピアリング情報を取得する](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [ExpressRoute を使用してオンプレミスから CloudSimple に接続する](https://docs.azure.cloudsimple.com/on-premises-connection)
