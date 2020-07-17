---
title: Azure Marketplace での仮想マシンのオファー
description: Azure Marketplace で VM オファーを発行するプロセスの概要です。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: dsindona
ms.openlocfilehash: b857cf8b7485beb884adf77d99f82d965f55a0ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142796"
---
# <a name="virtual-machine-offer"></a>仮想マシンのオファー

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure 仮想マシン オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[Azure 仮想マシン オファーを作成する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)」の手順に従って、移行されたオファーを管理します。

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| このセクションでは、仮想マシンの新しいオファーを [Azure Marketplace](https://azuremarketplace.microsoft.com) に発行する方法について説明します。 Windows ベースの仮想マシンと Linux ベースの仮想マシンの両方に対してサポートが提供されます。これにはオペレーティング システム仮想ハード ディスク (VHD) と 0 台以上のデータ VHD が含まれています。 | ![仮想マシン アイコン](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>公開の概要

この[ご利用の Azure Marketplace オファーの最適化](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player)に関するビデオでは、Azure Marketplace の概要をご覧になれます。たとえば、このマーケットプレース上に発行する方法 (仮想マシン ソリューションを使用)、ご利用の製品ページでのユーザー エクスペリエンスやオプションの体験版エクスペリエンスを最適化する方法、ユーザーの潜在顧客が生成される方法、これらを使用して顧客のエンゲージメントを最適化する方法などが説明されます。

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>VM 発行プロセスのフロー

次の図は、VM オファーの発行の概要手順を示したものです。 

![VM の発行プロセス](./media/publishvm_001.png)

1. オファーを作成する - オファーの説明、マーケティング資料、法務文書、サポート情報、資産の仕様など、オファーに関するすべての詳細と情報を構成します。

2. ビジネス資産と技術資産を作成する - 関連付けられたソリューション (ここでは、VM および接続されたディスク) に対するビジネス資産 (法務文書とマーケティング資料) と技術資産を作成します。 

3. SKU を作成する - オファリングに関連付けられた SKU を作成し、それらを送信します。  発行を計画しているイメージごとに固有の SKU が必要です。 
 
4. オファーを認定して発行する - オファーと技術資産が完成したら、オファーを送信できます。 この送信により、発行プロセスが開始されます。このプロセスでは、ソリューションがテスト、検証、認定された後、マーケットプレースで "ライブ状態" になります。  

## <a name="next-steps"></a>次のステップ

これらの手順を検討する前に、Microsoft Azure Marketplace への VM の発行に関する[技術要件とビジネス要件](./cpp-prerequisites.md)を満たしている必要があります。 
