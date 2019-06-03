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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576813"
---
# <a name="azure-network-connection-overview"></a>Azure ネットワーク接続の概要

領域内で、CloudSimple サービスを作成すると、以下が行われます。

* Azure ExpressRoute 回線を作成し、そのリージョンでサービスに添付します。
* Azure 仮想ネットワークまたは Azure ExpressRoute を使用するオンプレミス ネットワークに CloudSimple リージョン ネットワークを接続します。
* Azure サブスクリプションで稼働するサービス、またはオンプレミス ネットワークで実行されている サービスにプライベート クラウド環境からアクセスします。

この接続は、待機時間が短く高帯域幅です。

## <a name="benefits"></a>メリット

Azure のネットワーク接続により以下をすることができます。

* プライベート クラウド上の仮想マシンのバックアップ ターゲットとして Azure を使用します。
* プライベート クラウドの vSAN データストアを暗号化する Azure サブスクリプション内の KMS サーバーを展開します。
* ハイブリッド アプリケーションを使用します。アプリケーションの Web 層は、アプリケーションおよびデータベースの層がプライベート クラウドで実行する間にパブリック クラウドで実行します。

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute のオンプレミス ネットワークへの接続。

CloudSimple リージョンに、既存の Azure ExpressRoute 回線を接続できます。 ExpressRoute Global Reach 機能により、2 つの回線を相互に接続します。  オンプレミスと CloudSimple ExpressRoute 回線の間に接続が確立されます。

このメソッドにより、2 つの環境間の接続を確立します。

* セキュリティ保護
* プライベート
* 高帯域幅
* 待ち時間の短縮

オンプレミス ネットワークへの ExpressRoute 接続を作成するには、[サポートにお問い合わせ](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)ください。

## <a name="next-steps"></a>次の手順

* [仮想ネットワーク接続の削除](https://docs.azure.cloudsimple.com/virtual-network-connection)