---
title: オファーの種類別の公開ガイド - Microsoft 商業マーケットプレース
description: この記事では、Microsoft 商業マーケットプレースで利用できるオファーの種類について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/06/2020
ms.openlocfilehash: ff31e29b8c2a36c9aaa3d72eab86340ed405b1ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772653"
---
# <a name="publishing-guide-by-offer-type"></a>プランの種類別の公開ガイド

この記事では、商用マーケットプレースで利用できるオファーの種類について説明します。 商用マーケットプレースで提示されたオファーの構造 (メタデータ、成果物、およびその他のコンテンツを含む) は、*オファーの種類*によって定義されます。

[公開オプションを決定](determine-your-listing-type.md)した後、パートナー センターでオファーの作成を開始する前に、オファーの種類を選択する必要があります。 オファーの種類は、公開するソリューション、アプリ、またはサービスの種類および Microsoft の製品とサービスとの連携方法に対応します。

1 つのオファーの種類をさまざまな方法で構成して、異なる公開オプション、リスト オプション、プロビジョニング、または価格を使用することができます。 オファーの種類の公開オプションと構成は、オファーの適格性と技術面の要件に合わせて指定されます。

オファーの作成前に、オンライン ストアおよびオファーの種類の適格性の要件と、公開に関する技術面の要件を確認してください。

## <a name="list-of-offer-types"></a>オファーの種類の一覧

次の表は、パートナー センターにおける商用マーケットプレースのオファーの種類を示しています。

| **オファリング タイプ**    | **説明**  |
| :------------------- | :-------------------|
| [Azure アプリケーション:**マネージド アプリケーション**](marketplace-managed-apps.md) | 次の条件が必須であるときは、オファーの種類として Azure アプリケーション:マネージド アプリケーションを使用します。 <br> <ul> <li>VM または完全な IaaS ベースのソリューションを使用して、サブスクリプション ベースのソリューションを顧客向けにデプロイする。 </li> <li>貴社または貴社の顧客の要件として、ソリューションをパートナーが管理しなければならない。 </li> <ul> |
| [Azure アプリケーション:**ソリューション テンプレート**](marketplace-solution-templates.md) | 対象となるソリューションに、単独の VM を超える追加のデプロイと構成の自動化が必要となる場合は、オファーの種類として Azure アプリケーション:ソリューション テンプレートを使用します。 ソリューション テンプレートでは、VM を含むがそれに限定されない、さまざまな種類の Azure リソースを使用できます。  |
  | [**Azure Container**](marketplace-containers.md) | 対象となるソリューションが、Kubernetes ベースの Azure コンテナー サービスとしてプロビジョニングされる Docker コンテナー イメージであるときは、オファーの種類として Azure コンテナーを使用します。 |
| [**Azure 仮想マシン**](marketplace-virtual-machines.md) | 顧客に関連付けられているサブスクリプションに仮想アプライアンスをデプロイするときは、オファーの種類として仮想マシンを使用します。 |
| [**コンサルティング サービス**](consulting-services.md) | コンサルティング サービスは、Azure、Dynamics 365、または Power Suite サービスの使用をサポートし、拡張するために、お客様とサービスを結ぶうえで役立ちます。|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | Dynamics 365 Business Central、Dynamics 365 Customer Engagement、Power Apps、および Finance and Operations アプリを構築または拡張する AppSource のオファーを公開できます。|
| [**IoT Edge モジュール**](iot-edge-module.md) | Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Microsoft サービス、サードパーティー サービス、独自ソリューション固有のコードのいずれかを含めることができます。 |
| [**管理されたサービス**](partner-center-portal/create-new-managed-service-offer.md) | 管理サービス オファーを作成し、[Azure Lighthouse](/azure/lighthouse/overview) を使用して顧客が委任したサブスクリプションまたはリソース グループを管理できます。|
| [**Power BI アプリ**<br/>**Microsoft 365**](appsource-offer-publishing-guide.md) | Power BI と Microsoft 365 上にビルドされる、または拡張する AppSource のオファーを公開できます。|
| [**サービスとしてのソフトウェア**](plan-saas-offer.md) | サービスとしてのソフトウェア (SaaS) ベースの技術的ソリューションをサブスクリプションとして顧客が購入できるようにするには、オファーの種類として SaaS を使用します。 |


リスト オプションとオファーの種類別のシングル サインオンの要件については、「[**Azure AD 要件**](enable-appsource-marketplace-using-azure-ad.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- オファーの種類に対応する記事に示されている適格性の要件を確認し、オファーの選択と構成を確定する。
- オンライン ショップごとの公開パターン (ソリューションをオファーの種類や構成にマップする方法など) を確認します。
