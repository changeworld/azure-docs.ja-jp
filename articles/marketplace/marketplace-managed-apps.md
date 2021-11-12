---
title: 'Azure アプリケーション: マネージド アプリケーション プランの発行ガイド - Azure Marketplace'
description: この記事では、Azure Marketplace でマネージド アプリケーションを発行するための要件を説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 11/11/2021
ms.openlocfilehash: 3a0ebed6767666373095b9ec33d2f20228c9d373
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289663"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Azure マネージド アプリケーションの発行ガイド

Azure "*マネージド アプリケーション*" プランは、Azure Marketplace で Azure アプリケーションを発行する 1 つの方法です。 マネージド アプリケーションは、Azure Marketplace を通じてデプロイおよび課金されるトランザクション プランです。 ユーザーに表示される掲載オプションは、 *[今すぐ入手する]* です。

この記事では、マネージド アプリケーション プラン タイプの要件について説明します。

次の条件の下でマネージド アプリケーション プラン タイプを使用します。

- 仮想マシン (VM) と IaaS (サービスとしてのインフラストラクチャ) ベース ソリューション全体のいずれかを使用し、顧客のサブスクリプションベース ソリューションをデプロイしている。
- 自分の場合も、顧客の場合も、ソリューションはパートナーが管理しなければならない。

>[!NOTE]
>たとえば、システム インテグレーターやマネージド サービス プロバイダー (MSP) がパートナーになります。  

## <a name="managed-application-offer-requirements"></a>マネージド アプリケーション プランの要件

|必要条件 |詳細  |
|---------|---------|
|Azure サブスクリプション | マネージド アプリケーションを顧客のサブスクリプションにデプロイする必要があるが、アプリケーションはサード パーティが管理できます。 |
|請求/メータリング    |  リソースは、顧客の Azure サブスクリプションで提供されます。 従量課金制の支払モデルを使用する Azure リソースは Microsoft 経由で顧客が処理し、顧客の Azure サブスクリプション経由で請求されます。 <br><br> ライセンス持ち込みの Azure リソースの場合、顧客サブスクリプションで発生するインフラストラクチャ コストを Microsoft が請求しますが、ユーザーが顧客に対して直接ソフトウェア ライセンス料金を処理します。        |
|Azure マネージド アプリケーション パッケージ    |   アプリケーションを顧客のサブスクリプションにデプロイするために使用される、構成済みの Azure Resource Manager テンプレートと UI 定義の作成。<br><br>マネージド アプリケーションの作成の詳細については、[マネージド アプリケーションの概要](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)に関するページを参照してください。|

---

> [!NOTE]
> 管理対象アプリケーションは、Azure Marketplace から展開できる必要があります。 顧客の通信に懸念がある場合、リード共有を有効にした後、興味を持つ顧客に連絡してください。  

> [!Note]
> クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。 Microsoft CSP パートナー チャネルを通じたプランのマーケティングの詳細については、「[クラウド ソリューション プロバイダー](./cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、[Azure Marketplace を使用してクラウド ビジネスを拡大する](https://azuremarketplace.microsoft.com/sell)方法について学習します。

パートナー センターで登録して作業を開始するには、次のようにします。

- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)し、オファーを作成するか完成させます。
- 詳細については、「[Azure アプリケーション プランを作成する](azure-app-offer-setup.md)」を参照してください。
