---
title: Azure アプリケーション ソリューション テンプレートのオファー発行ガイド
description: この記事では、Marketplace でソリューション テンプレートを発行するための要件を説明します
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057884"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure アプリケーション (ソリューション テンプレート) のオファー発行ガイド

ソリューション テンプレートは、Marketplace でソリューションを発行するための 1 つの手段です。 このガイドでは、このオファーの要件について説明します。 

これらは、Marketplace を通じてデプロイされ課金されるトランザクションのオファーです。 ユーザーに表示される行動喚起は、"今すぐ入手する" です。

デプロイと構成に関して、単純な VM を超える特別なオートメーションが必要となるソリューションの場合、オファーの種類として Azure アプリ (ソリューション テンプレート) を使用してください。 Azure アプリ (ソリューション テンプレート) を使用して、VM のプロビジョニングを自動化することができます。 ネットワーク リソースやストレージ リソースをプロビジョニングすることもできます。 Azure アプリ (ソリューション テンプレート) タイプのオファーでは、単一の VM および IaaS ベースのソリューション全体に関して、オートメーション ベネフィットが提供されます。

## <a name="requirements-for-solution-templates"></a>ソリューション テンプレートの要件

|必要条件 |詳細  |
|---------|---------|
|請求/メータリング    |  リソースが顧客の Azure サブスクリプションでプロビジョニングされます。 従量課金制 (PAYGO) の仮想マシンが Microsoft 経由で顧客とトランザクション処理され、顧客の Azure サブスクリプション (PAYGO) により課金されます 
ライセンス持ち込みの場合、Microsoft が顧客サブスクリプションで生じるインフラストラクチャ コストを請求し、ユーザーが顧客に対して直接ソフトウェア ライセンス料金を処理します。        |
|Azure と互換性がある仮想ハード ディスク (VHD)    |   VM は、Windows または Linux 上に構築されている必要があります。<ul> <li>Linux VHD の作成の詳細については、[docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based) の「Azure と互換性のある VHD の作成 (Linux ベース)」セクションを参照してください。</li> <li>Windows VHD の作成の詳細については、[docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based) の「Azure と互換性のある VHD の作成 (Windows ベース)」セクションを参照してください。</li> </ul>      |



## <a name="next-steps"></a>次の手順
まだ行っていない場合は、 

- マーケットプレイスに[登録する](https://azuremarketplace.microsoft.com/sell)

登録済みかつ新しいオファーを作成しているまたは既存のオファーを操作している場合は、

- [Cloud パートナー ポータルにログイン](https://cloudpartner.azure.com)して、オファーを作成または完成させる
