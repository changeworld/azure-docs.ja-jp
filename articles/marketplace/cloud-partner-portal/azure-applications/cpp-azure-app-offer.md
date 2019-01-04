---
title: Azure アプリケーション オファー | Microsoft Docs
description: Azure Marketplace で Azure アプリケーション オファーを発行するプロセスの概要です。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195945"
---
# <a name="azure-application-offer"></a>Azure アプリケーション プラン

このセクションでは、新しい Azure アプリケーション オファーを <a href="https://azuremarketplace.microsoft.com">Microsoft Azure Marketplace</a> に発行する方法について説明します。
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| 各 Azure アプリケーションには、アプリケーションで使用されるすべての技術資産が定義されている Azure Resource Manager テンプレートが 1 つ含まれています。このテンプレートには通常、1 つ以上の仮想マシンとその他のサポートする Azure ベースまたは Web ベースのサービスが含まれています。 | ![Azure アプリ アイコン](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>メリット

次に、Microsoft のマーケットプレースにアプリケーションを公開する利点をいくつか挙げます。

* Office 365 および Dynamics 365 にわたる 1 億の Azure Active Directory ユーザーにリーチ。

* 販売チームの拡張: 世界中のビジネス ユーザーへのリーチ、エンドユーザーを取り込む販売チャネルの獲得、潜在顧客の発掘に役立つ、業種を超えた新しい顧客との会話のきっかけを作ることができます。

* アクションにつながる分析情報を取得: AppSource でアプリがどのように実行され、どのくらいうまく機能しているか、および営業の手順をさらに改善する方法についての分析情報を共有します。

## <a name="types-of-azure-applications"></a>Azure アプリケーションの種類

Azure アプリケーションには、マネージド アプリケーションとソリューション テンプレートの 2 種類があります。 似てはいますが、注目すべき違いがいくつかあります。

### <a name="solution-template"></a>ソリューション テンプレート

ソリューション テンプレートは、Marketplace でソリューションを発行するための 1 つの手段です。 このオファーの種類は、単一の仮想マシン (VM) の範囲を超えてデプロイと構成のオートメーションを追加する必要があるソリューションで使用します。 ソリューション テンプレートを使用して複数の VM の提供を自動化することができます。 これには、複雑な IaaS ソリューションを提供するためのネットワーク リソースとストレージ リソースのプロビジョニングが含まれます。 ソリューション テンプレートの要件と課金モデルの概要については、[Azure アプリケーションのソリューション テンプレート](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)に関するページをご覧ください。

### <a name="managed-application"></a>マネージド アプリケーション

マネージド アプリケーションは、Marketplace のソリューション テンプレートと似ていますが、1 つ大きな違いがあります。 マネージド アプリケーションでは、アプリの発行元によって管理されているリソース グループが、リソースのデプロイ先となります。 リソース グループはユーザーのサブスクリプション内に存在しますが、そのリソース グループにアクセスできるのは、発行元のテナントの ID です。 発行元は、ソリューションの継続的なサポートにかかるコストを指定します。 フル マネージドのターンキー アプリケーションを簡単に構築して顧客に提供するには、Azure マネージド アプリケーションを使用します。

Marketplace だけでなく、サービス カタログでマネージド アプリケーションを提供することもできます。 サービス カタログは、組織内のユーザー向けの承認済みのソリューションの内部カタログです。 組織の基準を満たしながら、組織内のグループ用のソリューションを提供するには、カタログを使用します。 従業員は、カタログを使用して、IT 部門が推奨し、承認しているアプリケーションを簡単に見つけることができます。

マネージド アプリケーションの利点と種類について詳しくは、「[Azure マネージド アプリケーションの概要](https://docs.microsoft.com/azure/managed-applications/overview)」をご覧ください。

## <a name="publishing-overview"></a>公開の概要

次のビデオ「[Building Solution Templates, and Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)」 (Azure Marketplace 用にソリューション テンプレートとマネージド アプリケーションをビルドする) は、Azure Resource Manager を作成して Azure アプリケーション ソリューションを定義する方法と、その後でアプリ オファーを Azure Marketplace に公開する方法に関する概要です。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>発行プロセスのワークフロー

次の図は、Azure アプリケーション オファーを発行するプロセスの概要を示したものです。

![オファー発行のワークフロー](./media/new-offer-process.png)

## <a name="offer-components"></a>オファーのコンポーネント

このセクションはマネージド アプリケーション オファーの発行の要素についての概要であり、Azure Marketplace への発行元のためのガイドとして提供されています。 発行は、次の主要な部分に分かれています。 

* [前提条件](./cpp-prerequisites.md) - マネージド アプリケーション オファーを作成または発行する前の、技術要件およびビジネス要件の一覧を示します。 
* [オファーを作成する](./cpp-create-offer.md) - Cloud パートナー ポータルを使用してマネージド アプリケーション オファーのエントリを作成するために必要な手順を示します。 
* [オファーを発行する](./cpp-publish-offer.md) - Azure Marketplace に発行するためにオファーを送信する方法について説明します。

## <a name="steps-in-the-publishing-process"></a>発行プロセスの手順

Azure アプリケーション オファーを発行する手順の概要は次のとおりです。

1. オファーを作成する - オファーに関する詳細情報を指定します。 この情報には、オファーの説明、マーケティング資料、サポート情報、資産の仕様などが含まれます。

2. ビジネス資産と技術資産を作成する - 関連付けられたソリューションに対するビジネス資産 (法務文書とマーケティング資料) と技術資産を作成します。

3. SKU を作成する - オファーに関連付けられた SKU を作成します。 発行を計画しているイメージごとに固有の SKU が必要です。

4. オファーを認定して発行する - オファーと技術資産が完成したら、オファーを送信できます。 この送信によって発行プロセスが開始します。 このプロセスの間に、ソリューションがテストされ、検証され、認定されて、Azure Marketplace での "運用が開始" されます。

## <a name="next-steps"></a>次の手順

これらの手順を検討する前に、Microsoft Azure Marketplace へのマネージド アプリケーションの発行に関する[技術要件とビジネス要件](./cpp-prerequisites.md)を満たしている必要があります。