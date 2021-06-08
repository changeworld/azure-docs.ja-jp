---
title: チュートリアル-Medicare and 扶助 Services (CMS) の中心-Azure API for FHIR
description: この概要では、Medicare and 扶助 Services (CMS) の相互運用性と患者アクセスルールの中心に関連する一連のチュートリアルを紹介します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 05/27/2021
ms.openlocfilehash: 885fa84c19b5f0e3c5b4aa32d4aa3a230033b2f6
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592869"
---
# <a name="centers-for-medicare-and-medicaid-services-cms-interoperability-and-patient-access-rule-introduction"></a>Medicare and 扶助 Services (CMS) の相互運用性と患者のアクセスルールの概要に関するセンター

この一連のチュートリアルでは、Medicare and 扶助 Services (CMS) の相互運用性と患者のアクセス規則のセンターの概要と、この規則に記載されている技術要件について説明します。 このルールで参照されるさまざまな実装ガイドについて説明します。 また、これらの実装ガイドをサポートするために、FHIR 用の Azure API を構成する方法についても詳しく説明します。


## <a name="rule-overview"></a>ルールの概要

CMS は、2020年5月1日に、 [相互運用性と患者のアクセスルール](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index) をリリースしました。 このルールでは、患者の医療 (患者、プロバイダ、および保険会社) に関係するすべての当事者間のデータフローを、患者が必要に応じて患者にアクセスできるようにする必要があります。 相互運用性によって、医療業界が数十年にわたり、データが孤立しています。これにより、悪影響を及ぼす可能性のある負の正常性の結果が発生します。 CMS は、このルールを適用するために、Medicare アドバンテージ (MA)、扶助、子供の医療保険プログラム (チップ)、および連邦促進された機能 (QHP) の発行者を規制する機関を使用します。 

2020年8月、CMS は組織が要求を満たす方法を詳細に説明しています。 データを安全に交換し、標準化された方法でデータを交換できるようにするには、データ交換に必要な基本標準として FHIR バージョンリリース 4 (R4) を確認します。 

相互運用性と患者のアクセスの方法には、主に3つの部分があります。

* **患者アクセス API (2021 年7月 1** 日以降) – (前述のように) CMS 規制に準拠している企業は、患者が信頼性情報に簡単にアクセスして情報を表示できるようにする、セキュリティで保護された標準ベースの api を実装して維持する必要があります。  

* **プロバイダーディレクトリ API (2021 年7月 1** 日以降) –標準ベースの API を使用してプロバイダーのディレクトリ情報を公開するには、この部分で CMS 規制の対象となる必要があります。 この情報を利用できるようにすることで、サードパーティのアプリケーション開発者は、患者が特定のケアニーズに対してプロバイダーを検索するのに役立つサービスを作成できるようになります。また、nuance の他のプロバイダーを検索することもできます。  

* **支払人から支払人へのデータ交換 (2022 年1月1日以降)** –その他の保険会社との患者の要求で、特定の患者の臨床データを交換するために CMS 規制の保険会社が必要です。 どのような標準にも従う必要はありませんが、FHIR を適用してこのデータを交換することをお勧めします。 

## <a name="key-fhir-concepts"></a>主要な FHIR の概念

前述のように、この要求を満たすには FHIR R4 が必要です。 また、ルールに関するガイダンスを提供するいくつかの実装ガイドが用意されています。 [実装ガイド](https://www.hl7.org/fhir/implementationguide.html) は、基本の FHIR 仕様に追加のコンテキストを提供します。 これには、追加の検索パラメーター、プロファイル、拡張機能、操作、値セット、およびコードシステムの定義が含まれます。

Azure API for FHIR には、さまざまな実装ガイド用にデータベースを構成する際に役立つ次の機能が用意されています。

* [RESTful の相互作用のサポート](fhir-features-supported.md)
* [プロファイルの保存と検証](validation-against-profiles.md)
* [カスタム検索パラメーターの定義とインデックス作成](how-to-do-custom-search.md)
* [データの変換](convert-data.md)

## <a name="patient-access-api-implementation-guides"></a>患者アクセス API の実装ガイド

患者アクセス API は、4つの FHIR 実装ガイドに準拠していることを示しています。

* [Blue button®向けの ig](http://hl7.org/fhir/us/carin-bb/STU1/index.html): 保険会社は、患者の要求を作成し、ブルーボタン実装ガイドの carin IG (C4BB ig) に従って使用可能なデータを検出するために必要です。 C4BB IG は、FHIR API を使用して、保険会社がコンシューマーに表示できる一連のリソースを提供します。また、相互運用性と患者アクセス API の要求データに必要な詳細情報が含まれています。 この実装ガイドでは、ExplanationOfBenefit (EOB) リソースをメインリソースとして使用し、参照されている他のリソースをプルします。
* [HL7 FHIR Da ヴィンチ PDex ig](http://hl7.org/fhir/us/davinci-pdex/STU1/index.html): 支払人データ交換実装ガイド (pdex ig) は、医療機関が患者アクセス API の要件を満たすために、関連するすべての患者医療データを確実に提供することに重点を置いています。 これは、R4 リソースの US コアプロファイルを使用し、(少なくとも) 検出、プロバイダー、組織、場所、サービスの日付、診断、手順、および観察を含みます。 このデータは FHIR 形式でも使用できますが、要求データ、HL7 V2 メッセージ、および CDA ドキュメントの形式で他のシステムから取得される場合もあります。
* [HL7 Us コア ig](https://www.hl7.org/fhir/us/core/toc.html): HL7 Us コア実装ガイド (US コア ig) は、前述の pdex の表現のバックボーンです。 PDex IG では、米国のコアではなく一部のリソースが制限されていますが、多くのリソースは US コア IG の標準に従うだけです。

* [HL7 FHIR Da ヴィンチ-PDex US 薬品処方 IG](http://hl7.org/fhir/us/Davinci-drug-formulary/index.html): パート D Medicare アドバンテージプランでは、患者 API を介して処方情報を利用できるようにする必要があります。 これを行うには、PDex US 薬品処方実装ガイド (USDF IG) を使用します。 USDF IG は、health insurer の薬品処方情報の FHIR インターフェイスを定義します。これは、正常性 insurer が支払うことに同意したブランド名と汎用処方箋薬品の一覧です。 主なユースケースは、患者が、指定された別の薬品が使用可能かどうかを把握し、薬品のコストを比較できるようにするためです。

## <a name="provider-directory-api-implementation-guide"></a>プロバイダーディレクトリ API 実装ガイド

Provider Directory API は、1つの実装ガイドへの準拠について説明します。

* [HL7 Da ヴィンチ PDex Plan NETWORK IG](http://build.fhir.org/ig/HL7/davinci-pdex-plan-net/): この実装ガイドでは、正常性 insurer の保険プラン、関連付けられているネットワーク、およびこれらのネットワークに参加している組織とプロバイダーに対する FHIR インターフェイスを定義します。

## <a name="touchstone"></a>Touchstone

さまざまな実装ガイドへの準拠をテストするために、 [Touchstone](https://touchstone.aegis.net/touchstone/) は優れたリソースです。 今後のチュートリアルでは、さまざまな Touchstone テストを成功させるために、FHIR 用の Azure API を構成することに重点を置いて説明します。 Touchstone サイトには、起動して実行するのに役立つ多くの優れたドキュメントが用意されています。

## <a name="next-steps"></a>次のステップ

これで、相互運用性と患者のアクセスルール、実装ガイド、および使用可能なテストツール (Touchstone) についての基本的な知識が得られました。次は、青いボタン用の CARIN 用の Azure API for FHIR の設定について説明します。 

>[!div class="nextstepaction"]
>[青色のボタンの実装ガイド](carin-implementation-guide-blue-button-tutorial.md)  