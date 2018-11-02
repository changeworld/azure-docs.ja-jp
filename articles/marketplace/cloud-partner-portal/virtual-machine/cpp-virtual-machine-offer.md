---
title: Azure Marketplace での仮想マシンのオファー | Microsoft Docs
description: Azure Marketplace で VM オファーを発行するプロセスの概要です。
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639130"
---
# <a name="virtual-machine-offer"></a>仮想マシンのオファー

このセクションは仮想マシン (VM) の発行の要素についての概要であり、[Azure Marketplace](https://azuremarketplace.microsoft.com) への発行元のためのガイドとして提供されています。  この観点から、次の主要な部分に分かれています。

- [前提条件](./cpp-prerequisites.md) - VM オファーを作成または発行する前の、技術要件およびビジネス要件の一覧を示します。
- [VM オファーを作成する](./cpp-create-offer.md) - この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com)を使用して新しい VM オファー エントリを作成するために必要な手順を示します。
- [VM の技術資産を作成する](./cpp-create-technical-assets.md) - VM ソリューション用の技術資産を作成する方法、およびこのパッケージを Azure Marketplace での VM オファーとして構成する方法を説明します
- [VM オファーを発行する](./cpp-publish-offer.md) - Azure Marketplace に発行するためにオファーを送信する方法について説明します


## <a name="vm-publishing-process-flow"></a>VM 発行プロセスのフロー

次の図は、VM オファーの発行の概要手順を示したものです。 

![VM の発行プロセス](./media/publishvm_001.png)

1. オファーを作成する - オファーの説明、マーケティング資料、法務文書、サポート情報、資産の仕様など、オファーに関するすべての詳細と情報を構成します。

2. ビジネス資産と技術資産を作成する - 関連付けられたソリューション (ここでは、VM および接続されたディスク) に対するビジネス資産 (法務文書とマーケティング資料) と技術資産を作成します。 

3. SKU を作成する - オファリングに関連付けられた SKU を作成し、それらを送信します。  発行を計画しているイメージごとに固有の SKU が必要です。 
 
4. オファーを認定して発行する - オファーと技術資産が完成したら、オファーを送信できます。 この送信により、発行プロセスが開始されます。このプロセスでは、ソリューションがテスト、検証、認定された後、マーケットプレースで "ライブ状態" になります。  

## <a name="next-steps"></a>次の手順

これらの手順を検討する前に、Microsoft Azure Marketplace への VM の発行に関する[技術要件とビジネス要件](./cpp-prerequisites.md)を満たしている必要があります。 
