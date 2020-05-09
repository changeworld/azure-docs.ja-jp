---
title: 'Azure アプリケーション: マネージド アプリケーション プランの発行ガイド | Azure Marketplace'
description: この記事では、Azure Marketplace でマネージド アプリケーションを発行するための要件を説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084874"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure アプリケーション:マネージド アプリケーション プランの発行の要件

この記事では、Azure Marketplace で Azure アプリケーションのプランを発行するための 1 つの方法である、マネージド アプリケーション プラン タイプの要件について説明します。 マネージド アプリケーションは、Azure Marketplace を通じてデプロイおよび課金されるトランザクション プランです。 ユーザーに表示される行動喚起は、"今すぐ入手する" です。

次の条件が必須であるときは、マネージド アプリケーション プラン タイプを使用します。

- VM または完全な IaaS ベースのソリューションを使用して、サブスクリプションベースのソリューションを顧客向けにデプロイする。
- 貴社または貴社の顧客の要件として、ソリューションをパートナーが管理しなければならない。

>[!NOTE]
>ここでのパートナーとしては、たとえば SI やマネージド サービス プロバイダー (MSP) が該当します。  

## <a name="managed-application-offer"></a>マネージド アプリケーションのオファー

|必要条件 |詳細  |
|---------|---------|
|顧客の Azure サブスクリプションにデプロイされている | マネージド アプリが顧客のサブスクリプションにデプロイされていて、サード パーティによって管理できる必要があります。 |
|請求/メータリング    |  リソースが顧客の Azure サブスクリプションでプロビジョニングされます。 従量課金制 (PAYGO) の仮想マシンが Microsoft 経由で顧客とトランザクション処理され、顧客の Azure サブスクリプション (PAYGO) により課金されます。 <br> ライセンス持ち込みの場合、Microsoft によって顧客サブスクリプションで発生するインフラストラクチャ コストが請求され、ユーザーが顧客に対して直接ソフトウェア ライセンス料金を処理します。        |
|Azure と互換性がある仮想ハード ディスク (VHD)    |   VM は、Windows または Linux 上に構築されている必要があります。<ul> <ul> <li>Linux VHD の作成の詳細については、「[Azure で動作保証済みの Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)」を参照してください。</li> <li>Windows VHD の作成の詳細については、「[Azure アプリケーション プランを作成する](./partner-center-portal/create-new-azure-apps-offer.md)」を参照してください。</li> </ul> |

>[!NOTE]
> 管理対象アプリは、Marketplace から展開できる必要があります。 顧客の通信に懸念がある場合、リード共有を有効にした後、興味を持つ顧客に連絡するようにしてください。  

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。 Microsoft CSP パートナー チャネルを通じたプランのマーケティングの詳細については、「[クラウド ソリューション プロバイダー](./cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- まだ行っていない場合は、Azure Marketplace について[学習](https://azuremarketplace.microsoft.com/sell)してください。
- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)して、プランを作成するか完成させます。
- 詳細については、「[Azure アプリケーション プランを作成する](./partner-center-portal/create-new-azure-apps-offer.md)」を参照してください。
