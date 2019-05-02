---
title: Azure アプリケーションのマネージド アプリケーション オファーの発行ガイド
description: この記事では、Marketplace でマネージド アプリケーションを発行するための要件を説明します
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: cb7c0bb571dcb9ec763d0247042e93966bfd0b65
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937798"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure アプリケーション:マネージド アプリケーション オファーの発行ガイド

マネージド アプリケーションは、Marketplace でソリューションを発行するための 1 つの手段です。 このガイドでは、このオファーの要件について説明します。 

これらは、Marketplace を通じてデプロイされ課金されるトランザクションのオファーです。 ユーザーに表示される行動喚起は、"今すぐ入手する" です。

次の条件が必須であるときは、オファーの種類として Azure アプリ (マネージド アプリ) を使用します。
- VM または完全な IaaS ベースのソリューションを使用して、サブスクリプション ベースのソリューションを顧客向けにデプロイする。
- 貴社または貴社の顧客の要件として、ソリューションをパートナーが管理しなければならない。

>[!NOTE]
>ここでのパートナーとしては、たとえば SI やマネージド サービス プロバイダー (MSP) が該当します。  

## <a name="managed-application-offer"></a>マネージド アプリケーションのオファー

|必要条件 |詳細  |
|---------|---------|
|顧客の Azure サブスクリプションにデプロイされている | マネージド アプリが顧客のサブスクリプションにデプロイされていて、サード パーティによって管理できる必要があります。 | 
|請求/メータリング    |  リソースが顧客の Azure サブスクリプションでプロビジョニングされます。 従量課金制 (PAYGO) の仮想マシンが Microsoft 経由で顧客とトランザクション処理され、顧客の Azure サブスクリプション (PAYGO) により課金されます 
ライセンス持ち込みの場合、Microsoft が顧客サブスクリプションで生じるインフラストラクチャ コストを請求し、ユーザーが顧客に対して直接ソフトウェア ライセンス料金を処理します。        |
|Azure と互換性がある仮想ハード ディスク (VHD)    |   VM は、Windows または Linux 上に構築されている必要があります。<ul> <ul> <li>Linux VHD の作成の詳細については、「[Azure で動作保証済みの Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)」を参照してください。</li> <li>Windows VHD の作成の詳細については、「[Azure と互換性のある VHD の作成](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)」を参照してください。</li> </ul> |

>[!NOTE]
> 管理対象アプリは、Marketplace から展開できる必要があります。 顧客の通信に懸念がある場合、リード共有を有効にした後、興味を持つ顧客に連絡するようにしてください。  

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](./cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次の手順
まだ行っていない場合は、 

- マーケットプレースに[登録](https://azuremarketplace.microsoft.com/sell)します。

登録済みかつ新しいオファーを作成しているまたは既存のオファーを操作している場合は、

- [Cloud パートナー ポータルにログイン](https://cloudpartner.azure.com)して、オファーを作成または完成させます。
