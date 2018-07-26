---
title: Azure Marketplace 用の仮想マシンのオファー発行ガイド
description: この記事では、Marketplace からデプロイされる仮想マシンとソフトウェアの無料試用版を発行するための要件について説明します。
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
ms.openlocfilehash: 5508b5943e116545297d91e85621d2a11a635299
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058074"
---
# <a name="virtual-machine-offer-publishing-guide"></a>仮想マシンのオファー発行ガイド

仮想マシンのイメージは、Marketplace でソリューションを発行するための 1 つの手段です。 このガイドでは、このオファーの要件について説明します。 

これらは、Marketplace を通じてデプロイされ課金されるトランザクションのオファーです。 ユーザーに表示される行動喚起は、"今すぐ入手する" です。

## <a name="free-trial"></a>無料試用版 

ライセンス持ち込み (BYOL) 課金モデルを使用するときに、期限付きのソフトウェア ライセンスにアクセスすることで、オファーをテストするユーザー向けに手配できます。 このオファーをデプロイする要件は以下のとおりです。 

|必要条件  |詳細  |
|---------|---------|
|無料試用期間および試用エクスペリエンス     |   顧客は、限られた期間、アプリを無料で試用することができます。 オファーに関して、顧客にはライセンス料金やサブスクリプション料金の支払いを一切要求しません。 基本となる Microsoft のファーストパーティ製品またはサービスの対価を顧客には一切要求しません。 すべての試用版オプションは、公開元の Azure サブスクリプションにデプロイされます。 コストの最適化と管理は、公開元によってのみ決められます。 無料試用版または対話型デモを選ぶことができます。 何を選んでも、無料試用版では、追加コストなしでオファーを試すための所定の期間を顧客に提供する必要があります。|
|簡単に構成できてすぐに使用できるソリューション    |  アプリは、短時間で簡単に構成およびセットアップできる必要があります。       |
|可用性/アップタイム    |    SaaS アプリまたはプラットフォームのアップタイムは 99.9% 以上である必要があります。     |
|Azure Active Directory     |    オファーでは、同意が有効な Azure Active Directory (Azure AD) フェデレーション シングル サインオン (SSO) (Azure AD フェデレーション SSO) を許可する必要があります。     |

## <a name="test-drive"></a>体験版

IaaS (サービスとしてのインフラストラクチャ) アプリまたは SaaS アプリとして仮想マシンをデプロイします。 体験版の発行オプションの利点は、案内役となるガイド ツアーをパートナーがホストしており、仮想マシンまたはソリューション全体のプロビジョニングが自動化されていることです。 体験版では、追加費用なしで顧客に評価版が提供されます。 試用版を体験する顧客は、既存の Azure 顧客である必要はありません。 

使用を開始するには amp-testdrive@microsoft.com にメールでお知らせください。 

|必要条件  |詳細 |
|---------|---------|
| Marketplace アプリを持っている   |    IaaS または SaaS を介した 1 台以上の仮想マシン。      |

## <a name="interactive-demo"></a>対話型デモ

対話型デモを使って、ソリューションのガイド付きエクスペリエンスを顧客に提供します。 対話型デモの発行オプションの利点は、複雑なソリューションの複雑なプロビジョニングなしで試用エクスペリエンスを提供できることです。 

## <a name="virtual-machine-offer"></a>仮想マシンのオファー

顧客に関連付けられているサブスクリプションに仮想アプライアンスをデプロイするときは、オファーの種類として仮想マシンを使用します。 VM は、従量課金制またはライセンス持ち込み (BYOL) のライセンス モデルを使用して完全に販売可能です。 商用トランザクションは Microsoft がホストし、公開元に代わって顧客に請求します。 公開元にとっては、顧客と Microsoft との間で推奨される支払い関係 (Enterprise Agreement など) を利用できる利点があります。

>[!NOTE]
>現在、Enterprise Agreement に関連付けられている年額コミットメントは VM の Azure 利用には使えますが、公開元のソフトウェア ライセンス料金には使えません。  

>[!NOTE]
>イメージと価格をプライベート オファーとして発行することで､VM の検出とデプロイを特定の顧客に限定することができます。 プライベート オファーとすることで､公開元が近しい顧客に排他的なオファーを作成し､カスタマイズされたソフトウェアと条件を提供できるようになります。 条件をカスタマイズすることによって､公開元は特別な価格と条件によるフィールド主導の取引､さらには限定的なソフトウェア リリースの利用などのさまざまなシナリオにスポットライトを当てることができます。 プライベート オファーにより､公開元はそうした詳細から成る新しい SKU を作成し､限られた顧客に具体的な価格や製品を提供することができます。  
*   プライベート オファーの詳細については、[azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace) の「Private Offers on Azure Marketplace (Azure Marketplace のプライベート オファー)」を参照してください。  

| 要件 | 詳細 |  
|:--- |:--- | 
| 請求/メータリング | 対象の VM は、BYOL または従量課金制の月額請求をサポートしている必要があります。 |  
| Azure と互換性がある仮想ハード ディスク (VHD) | VM は、Windows または Linux 上に構築されている必要があります。<ul> <li>Linux VHD の作成の詳細については、[docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based) の「Azure と互換性のある VHD の作成 (Linux ベース)」セクションを参照してください。</li> <li>Windows VHD の作成の詳細については、[docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based) の「Azure と互換性のある VHD の作成 (Windows ベース)」セクションを参照してください。</li> </ul> |  

## <a name="next-steps"></a>次の手順

まだ行っていない場合は、 

- マーケットプレイスに[登録する](https://azuremarketplace.microsoft.com/sell)

登録済みかつ新しいオファーを作成しているまたは既存のオファーを操作している場合は、

- [Cloud パートナー ポータルにログイン](https://cloudpartner.azure.com)して、オファーを作成または完成させる
