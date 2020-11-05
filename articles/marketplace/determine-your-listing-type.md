---
title: 公開オプションを選定する - Microsoft 商業マーケットプレース
description: この記事では､Microsoft AppSource や Azure Marketplace にオファーを公開する際の適格性条件と要件について説明します｡
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: 0b4941d326359a51ff6a75385a74a8bcdabfa075
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128999"
---
# <a name="determine-your-publishing-option"></a>公開オプションを選定する

オファーに対して選択する公開オプションは、適格性の要件と商業マーケットプレースの GTM 特典の両方に直接関係しています。 さらに重要なこととして、公開オプションとオファーの種類の選択によって、ユーザーが商業マーケットプレースのオファーをどのように扱うかが定義されます。

オファーを構成するには、商業マーケットプレースの主要な概念 (公開オプション、オファーの種類と構成、および商業マーケットプレースのオンライン ストアでオファーを提示する方法と場所を管理するリスト オプション) について理解する必要があります。

この記事では、次のことについて説明します。

- ソリューションに適したオンライン ストアを選定する方法。
- 各オンライン ストアで使用可能な公開オプションとリスト オプション。
- 各公開オプションで使用可能なオファーの種類。

## <a name="commercial-marketplace-publishing-options"></a>商業マーケットプレースの公開オプション

次の表は、Microsoft AppSource と Azure Marketplace でのオファーの種類に対する公開オプションを示しています。

|   | **一覧 (問い合わせ)**  | **一覧 (試用版)**  | **Free** | **BYOL** | **トランザクション**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **仮想マシン** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure アプリ (マルチ VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **コンテナー イメージ** |  |  | Azure Marketplace | Azure Marketplace |   |
| **IoT Edge モジュール** |  |  | Azure Marketplace | Azure Marketplace |   |
| **マネージド サービス** |  |  |  | Azure Marketplace |   |
| **コンサルティング サービス** | 両方のオンライン ストア |  |  |  |   |
| **SaaS アプリ** | 両方のオンライン ストア | 両方のオンライン ストア | 両方のオンライン ストア |  | 両方のオンライン ストア* |
| **Microsoft 365 アプリ** | AppSource | AppSource |  |  | AppSource**  |
| **Dynamics 365 アドイン** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Microsoft AppSource での SaaS アプリのトランザクション オファーは、現在クレジット カードのみです。

&#42;&#42; Microsoft 365 のオファーは、無料でインストールすることができ、SaaS オファーを介してライセンス サービスとして収益化できます。 詳細については、「[Microsoft Commercial Marketplace を通して Microsoft 365 アドインを収益化する](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)」をご覧ください。

## <a name="choose-a-listing-option"></a>リスト オプションを選択する

リスト オプションによってお客様のエンゲージメントが差別化され、潜在顧客の共有や[商業マーケットプレース特典](./gtm-your-marketplace-benefits.md)へのアクセスも可能になります。 公開オプションに対応したリスト オプションを次に示します。

| **公開オプション**    | **説明**  |
| :------------------- | :-------------------|
| **一覧** | アプリケーションまたはサービスのシンプルな一覧表示です。これにより、商業マーケットプレース ユーザーは **[連絡する]** リスト オプションを通じてお客様に連絡を取るよう依頼できます。 |
| **試用版** | 商業マーケットプレースを使用して、ソリューションを見つけやすくし、ソリューションの試用版のプロビジョニングを自動化します。これにより、見込みユーザーは SaaS、IaaS、または Microsoft のアプリを購入前の一定の期間、無料で試用できます。 試用版公開オプションで使用されるリスト オプションは、 **無料試用版** または **体験版** です。 |
| **BYOL** | 商業マーケットプレースを使用して、ソリューションを見つけやすくし、ソリューションのプロビジョニングを自動化し、金融取引を個別に完了します。 BYOL オファーの種類は、オンプレミスからクラウドへの移行に最適です。 リスト オプションは、 **今すぐ入手する** です。
| **トランザクション** | トランザクション オファーは、商業マーケットプレースを通して販売されます。 課金と回収については Microsoft が担当します。 リスト オプションは、 **今すぐ入手する** です。|

> [!Note]
> トランザクション公開オプションを使用する場合は、オファーの種類を選択してオファーを作成する前に、価格、課金、請求、および支払いに関する考慮事項を理解することが重要です。 詳細については、「[商用マーケットプレース トランザクションの機能と考慮事項](./marketplace-commercial-transaction-capabilities-and-considerations.md)」の記事を確認してください。

## <a name="selecting-an-online-store"></a>オンライン ストアの選択

各オンライン ストアは、ビジネスおよび IT ソリューションに関するお客様のさまざまな要求を満たします。 オファーの種類、トランザクションの機能、およびカテゴリによって、オファーの公開先が決まります。 カテゴリとサブカテゴリは、公開するソリューションの種類に基づいて各オンライン ストアにマップされます。

**Microsoft AppSource** は、Dynamics 365、Microsoft 365、および Power Platform を対象に、業界ソリューションやコンサルティング サービスなどのビジネス ソリューションを提供します。

**Azure Marketplace** は、Azure に向けて/において構築された IT ソリューション、およびお客様の Azure の使用を促進するコンサルティング サービスを提供します。

ソリューションの種類に最も合ったカテゴリとサブカテゴリを選択してください。 たとえば、Web アプリケーション ファイアウォールは、"セキュリティ" のカテゴリで Azure Marketplace に公開する必要がある IT ソリューションです。 契約管理アプリは、"販売" のカテゴリで AppSource に公開する必要があるビジネス ソリューションです。 間違ったカテゴリやサブカテゴリを選択すると、オファーが間違ったオンライン ストアに公開される可能性があります。

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>両方のオンライン ストアに公開 (SaaS オファーのみ)

SaaS オファーは、Azure Marketplace または AppSource に公開できます。 SaaS オファーが IT ソリューション (Azure Marketplace) とビジネス ソリューション (AppSource) の " *両方* " である場合は、各オンライン ストアに適用されるカテゴリやサブカテゴリを選択します。 両方のオンライン ストアに公開されるオファーには、IT ソリューション " *および* " ビジネス ソリューションとしての価値提案が存在する必要があります。

> [!IMPORTANT]
> [従量制課金](partner-center-portal/saas-metered-billing.md)での SaaS オファーは、Azure Marketplace と Azure portal を通じて利用できます。 プライベート プランのみを含む SaaS オファーは、Azure portal から利用できます。

| 従量制課金 | パブリック プラン | プライベート プラン | 次で利用可能: |
|---|---|---|---|
| はい             | はい         | いいえ           | Azure Marketplace と Azure portal |
| はい             | はい         | はい          | Azure Marketplace と Azure portal* |
| はい             | いいえ          | はい          | Azure portal のみ |
| いいえ              | いいえ          | はい          | Azure portal のみ |

&#42; このオファーのプライベート プランは、Azure portal 経由でのみ利用可能です

たとえば、従量制課金とプライベート プランのみ (パブリックプランなし) のオファーを購入するのは、Azure portal のお客様のみになります。 詳細については、「[Microsoft 商業マーケットプレースでのプライベート オファー](private-offers.md)」を参照してください。

### <a name="categories"></a>Categories

カテゴリとサブカテゴリは、ソリューションの種類に基づいて各オンライン ストアにマップされます。 ソリューションに最も合ったカテゴリとサブカテゴリを選択します。 選択できるオプションは次のとおりです。

- 少なくとも 1 つ、最大 2 つのカテゴリ。 プライマリとセカンダリのカテゴリを選択できます。
- プライマリ カテゴリ、セカンダリ カテゴリ、またはその両方についてそれぞれ最大 2 つのサブカテゴリ。 サブカテゴリを選択しない場合でも、オファーは選択したカテゴリで検出可能になります。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>次のステップ

- 公開オプションを決定したら、[オファーの種類別の公開ガイド](./publisher-guide-by-offer-type.md)に関する記事を確認してください。