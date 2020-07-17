---
title: Azure Marketplace 用の仮想マシンのオファー発行ガイド
description: この記事では、Marketplace からデプロイされる仮想マシンとソフトウェアの無料試用版を発行するための要件について説明します。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687498"
---
# <a name="virtual-machine-offer-publishing-guide"></a>仮想マシンのオファー発行ガイド

仮想マシンのイメージは、Marketplace でソリューションを発行するための 1 つの手段です。 このガイドでは、このオファーの要件について説明します。 

これらは、Marketplace を通じてデプロイされ課金されるトランザクションのオファーです。 ユーザーに表示される行動喚起は、"今すぐ入手する" です。

## <a name="free-trial"></a>無料試用版 

ライセンス持ち込み (BYOL) 課金モデルを使用するときに、期限付きのソフトウェア ライセンスにアクセスすることで、オファーをテストするユーザー向けに手配できます。 

## <a name="test-drive"></a>体験版

IaaS (サービスとしてのインフラストラクチャ) アプリまたは SaaS (サービスとしてのソフトウェア) アプリを介して、1 つ以上の仮想マシンをデプロイします。 体験版の発行オプションの利点は、案内役となるガイド ツアーをパートナーがホストしており、仮想マシンまたはソリューション全体のプロビジョニングが自動化されていることです。 体験版では、追加費用なしで顧客に評価版が提供されます。 試用版を体験する顧客は、既存の Azure 顧客である必要はありません。 

始めるには、[amp-testdrive](mailto:amp-testdrive@microsoft.com) でお問い合わせください。 

|必要条件  |詳細 |
|---------|---------|
| Marketplace アプリを持っている   |    IaaS または SaaS を介した 1 台以上の仮想マシン。      |

## <a name="interactive-demo"></a>対話型デモ

対話型デモを使って、ソリューションのガイド付きエクスペリエンスを顧客に提供します。 対話型デモの発行オプションの利点は、複雑なソリューションの複雑なプロビジョニングなしで試用エクスペリエンスを提供できることです。 

## <a name="virtual-machine-offer"></a>仮想マシンのオファー

顧客に関連付けられているサブスクリプションに仮想アプライアンスをデプロイするときは、オファーの種類として仮想マシンを使用します。 VM は、従量課金制またはライセンス持ち込み (BYOL) のライセンス モデルを使用して完全に販売可能です。 商用トランザクションは Microsoft がホストし、公開元に代わって顧客に請求します。 公開元にとっては、顧客と Microsoft との間で推奨される支払い関係 (Enterprise Agreement など) を利用できる利点があります。

> [!NOTE]
> 現在、Enterprise Agreement に関連付けられている年額コミットメントは VM の Azure 利用には使えますが、公開元のソフトウェア ライセンス料金には使えません。  
> 
> [!NOTE]
> イメージと価格をプライベート オファーとして発行することで､VM の検出とデプロイを特定の顧客に限定することができます。 プライベート オファーとすることで､公開元が近しい顧客に排他的なオファーを作成し､カスタマイズされたソフトウェアと条件を提供できるようになります。 条件をカスタマイズすることによって､公開元は特別な価格と条件によるフィールド主導の取引､さらには限定的なソフトウェア リリースの利用などのさまざまなシナリオにスポットライトを当てることができます。 プライベート オファーにより､公開元はそうした詳細から成る新しい SKU を作成し､限られた顧客に具体的な価格や製品を提供することができます。  
> *   プライベート オファーの詳細については、[azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace) の「Private Offers on Azure Marketplace (Azure Marketplace のプライベート オファー)」を参照してください。  

| 要件 | 詳細 |  
|:--- |:--- | 
| 請求/メータリング | 対象の VM は、BYOL または従量課金制の月額請求をサポートしている必要があります。 |  
| Azure と互換性がある仮想ハード ディスク (VHD) | VM は、Windows または Linux 上に構築されている必要があります。 <ul> <li>Linux VHD の作成の詳細については、「[Azure で動作保証済みの Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)」を参照してください。</li> <li>Windows VHD の作成の詳細については、「[Azure と互換性のある VHD の作成](./partner-center-portal/azure-vm-create-offer.md)」を参照してください。</li> </ul> |  

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](./cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、 

- マーケットプレースについて[学習](https://azuremarketplace.microsoft.com/sell)します。

登録済みかつ新しいオファーを作成しているまたは既存のオファーを操作している場合は、

- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)し、オファーを作成するか完成させます。
- 詳細については、「[仮想マシン オファーを作成する](./partner-center-portal/azure-vm-create-offer.md)」を参照してください。
