---
title: オファーの種類別の公開ガイド - Microsoft 商業マーケットプレース
description: この記事では、Microsoft 商業マーケットプレースで利用できるオファーの種類について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: 565ad4267bcdab095a85ab537c10728981eadb0d
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484155"
---
# <a name="publishing-guide-by-offer-type"></a>プランの種類別の公開ガイド

この記事では、商用マーケットプレースで利用できるオファーの種類について説明します。 商用マーケットプレースで提示されたオファーの構造 (メタデータ、成果物、およびその他のコンテンツを含む) は、*オファーの種類*によって定義されます。

[公開オプションを決定](determine-your-listing-type.md)した後、オファーの作成を開始する前に、オファーの種類を選択する必要があります。 オファーの種類は、公開するソリューション、アプリ、またはサービスの種類および Microsoft の製品とサービスとの連携方法に対応します。

1 つのオファーの種類をさまざまな方法で構成して、異なる公開オプション、リスト オプション、プロビジョニング、または価格を使用することができます。 オファーの種類の公開オプションと構成は、オファーの適格性と技術面の要件に合わせて指定されます。

オファーの作成前に、オンライン ストアおよびオファーの種類の適格性の要件と、公開に関する技術面の要件を確認してください。

## <a name="list-of-offer-types"></a>オファーの種類の一覧

次の表は、Azure Marketplace のオファーの種類の一覧です。

| **オファリング タイプ**    | **説明**  |
| :------------------- | :-------------------|
| [**Virtual Machines**](./marketplace-virtual-machines.md) | 顧客に関連付けられているサブスクリプションに仮想アプライアンスをデプロイするときは、オファーの種類として仮想マシンを使用します。 |
| [**ソリューション テンプレート**](./marketplace-solution-templates.md) | デプロイと構成に関して、単一 VM 以外の追加のオートメーションが必要となるソリューションの場合、オファーの種類としてソリューション テンプレート (Azure アプリケーションとも呼ばれる) を使用します。 ソリューション テンプレートでは、VM を含むがそれに限定されない、さまざまな種類の Azure リソースを使用できます。  |
| [**マネージド アプリケーション**](./marketplace-managed-apps.md) | 次の条件が必須であるときは、オファーの種類として Azure アプリ (マネージド アプリ) を使用します。 <br> <ul> <li> VM または完全な IaaS ベースのソリューションを使用して、サブスクリプション ベースのソリューションを顧客向けにデプロイする。 </li> <li>貴社または貴社の顧客の要件として、ソリューションをパートナーが管理しなければならない。 </li> <ul> |
| [**SaaS**](plan-saas-offer.md) | サービスとしてのソフトウェア (SaaS) ベースの技術的ソリューションをサブスクリプションとして顧客が購入できるようにするには、オファーの種類として SaaS を使用します。 |
| [**コンテナー オファー**](./marketplace-containers.md) | 対象となるソリューションが、Kubernetes ベースの Azure コンテナー サービスとしてプロビジョニングされる Docker コンテナー イメージであるときは、オファーの種類としてコンテナーを使用します。 |
| [**Azure IoT Edge モジュール**](./iot-edge-module.md) | Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Microsoft サービス、サードパーティー サービス、独自ソリューション固有のコードのいずれかを含めることができます。 |
| [**コンサルティング サービス**](./consulting-services.md) | コンサルティング サービスは、Azure、Dynamics 365、または Power Suite サービスの使用をサポートし、拡張するために、お客様とサービスを結ぶうえで役立ちます。|
| [**Microsoft 365、Dynamics 365、Power BI**](./appsource-offer-publishing-guide.md) | Dynamics 365、Microsoft 365、Power BI、および Power Apps 上でビルドされる、またはそれらを拡張する AppSource オファーを公開できます。|
| [**統合ソリューション**](./integrated-solutions-for-publishers.md) | テクノロジとサービスを単一のオファーとして組み合わせる、業界に合わせた統合ソリューションを公開できます。|

リスト オプションとオファーの種類別のシングル サインオンの要件については、「[**Azure AD 要件**](./enable-appsource-marketplace-using-azure-ad.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- オファーの種類に対応する記事に示されている適格性の要件を確認し、オファーの選択と構成を確定する。
- オンライン ショップごとの公開パターン (ソリューションをオファーの種類や構成にマップする方法など) を確認します。
