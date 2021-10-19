---
title: チュートリアル - センター for Medicare and Mediaid Services (CMS) の概要 - FHIR サービス
description: Center for Medicare and Mediaid Services (CMS) の相互運用性と患者アクセス規則に関連する一連のチュートリアルを紹介します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/05/2021
ms.openlocfilehash: 0f1c14976aae6b0335d968a2967e48b89b997f28
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778339"
---
# <a name="introduction-centers-for-medicare-and-medicaid-services-cms-interoperability-and-patient-access-rule"></a>概要: センター for Medicare and Mediaid Services (CMS) の相互運用性と患者アクセス規則

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この一連のチュートリアルでは、Center for Medicare and Mediaid Services (CMS) の相互運用性と患者アクセス規則の概要と、この規則で概説されている技術的要件について説明します。 この規則で参照されるさまざまな実装ガイドについて説明します。 また、これらの実装ガイドをサポートするために、Azure Healthcare API (ここでは FHIR サービスと呼ばれる) で FHIR サービスを構成する方法の詳細も説明します。


## <a name="rule-overview"></a>ルールの概要

CMS は、2020 年 5 月 1 日に相互運用性と患者アクセス規則をリリースしました。 [](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index) この規則では、患者が必要なときに医療情報にアクセスするために、患者の治療に関与しているすべての当事者 (患者、プロバイダー、支払者) 間の無料で安全なデータ フローが必要です。 相互運用性は何十年もの間、医療業界を悩らせてきたため、サイロ化されたデータが原因で、医療費が高く予測不可能なコストで、健康上の悪影響が生じる結果になります。 CMS は、連邦円滑化交換 (FFFEs) の医療アドバント (MA)、リサイダー、子医療保険プログラム (CHIP)、および適格医療計画 (QHP) 発行者を規制するために、その権限を使用してこの規則を適用しています。 

2020 年 8 月、CMS では、組織が義務を満たす方法について詳しく説明しました。 データを安全かつ標準化された方法で交換できるよう、CMS では、データ交換に必要な基礎標準として FHIR バージョン リリース 4 (R4) を特定しました。 

相互運用性と患者アクセスには、主に次の 3 つの部分があります。

* **Patient Access API (2021** 年 7 月 1 日に必要) – CMS 規制の支払者 (上記で定義) は、患者が要求に簡単にアクセスし、コストなどの情報を検出できる、セキュリティで保護された標準ベースの API を実装および維持するために必要です。また、選択したサードパーティのアプリケーションを通じて、定義された医療情報のサブセットも必要です。  

* **Provider Directory API (2021** 年 7 月 1 日に必要) – 標準ベースの API を介してプロバイダー ディレクトリ情報を一般公開するには、この規則のこの部分で CMS 規制の支払者が必要です。 この情報を利用できるサード パーティのアプリケーション開発者は、患者が特定の医療ニーズのプロバイダーを見つけ、医師が治療調整のための他のプロバイダーを見つけるのに役立つサービスを作成できます。  

* **Payer-to-Payer Data Exchange (2022** 年 1 月 1 日に必要) – CMS 規制の支払者は、患者の要求時に特定の患者の臨床データを他の支払者と交換する必要があります。 どのような種類の標準にも従う必要はありませんが、FHIR を適用してこのデータを交換してください。 

## <a name="key-fhir-concepts"></a>主要な FHIR の概念

前述のように、この要件を満たすには FHIR R4 が必要です。 さらに、ルールのガイダンスを提供する実装ガイドがいくつか開発されています。 [実装ガイドでは、](https://www.hl7.org/fhir/implementationguide.html) 基本 FHIR 仕様の上に追加のコンテキストが提供されます。 これには、追加の検索パラメーター、プロファイル、拡張機能、操作、値セット、コード システムの定義が含まれます。

FHIR サービスには、さまざまな実装ガイド用にデータベースを構成するのに役立つ次の機能があります。

* [RESTful 対話のサポート](fhir-features-supported.md)
* [プロファイルの格納と検証](validation-against-profiles.md)
* [カスタム検索パラメーターの定義とインデックス作成](how-to-do-custom-search.md)
* [データの変換](../data-transformation/convert-data.md)

## <a name="patient-access-api-implementation-guides"></a>Patient Access API 実装ガイド

Patient Access API では、次の 4 つの FHIR 実装ガイドへの準拠について説明します。

* [CARIN IG for Blue Button®:](http://hl7.org/fhir/us/carin-bb/STU1/index.html)患者の請求を行い、CARIN IG for Blue Button 実装ガイド (C4BB IG) に従ってデータを利用するには、支払者が必要です。 C4BB IG は、支払者が FHIR API を介してコンシューマーに表示できる一連のリソースを提供し、相互運用性と患者アクセス API の要求データに必要な詳細を含みます。 この実装ガイドでは、ExplanationOfBenefit (EOB) リソースをメイン リソースとして使用し、参照されている他のリソースをプルします。
* [HL7 FHIR Da Vinci PDex IG:](http://hl7.org/fhir/us/davinci-pdex/STU1/index.html)Payer Data Exchange Implementation Guide (PDex IG) は、Payer Data Exchange Implementation Guide (PDex IG) は、患者アクセス API の要件を満たすために、支払者が関連するすべての患者の臨床データを確実に提供する方法に重点を置きます。 これには、R4 リソースの US Core プロファイルが使用され、(少なくとも) 検出、プロバイダー、組織、場所、サービスの日付、診断、手順、観察が含まれます。 このデータは FHIR 形式で使用できる場合があります。要求データ、HL7 V2 メッセージ、および C-CDA ドキュメントの形式の他のシステムから取得される場合もあります。
* [HL7 US Core IG:](https://www.hl7.org/fhir/us/core/toc.html)HL7 US Core 実装ガイド (US Core IG) は、前述の PDex IG のバックボーンです。 PDex IG では、一部のリソースが US Core IG よりもさらに多く制限されているのに対し、多くのリソースは US Core IG の標準に従っているだけである。

* [HL7 FHIR Da Vinci - PDex US Formulary IG:](http://hl7.org/fhir/us/Davinci-drug-formulary/index.html)パート D Medicare Advantage プランでは、Patient API を介して数式情報を使用できる必要があります。 PDex US ドラッグ数式実装ガイド (USDF IG) を使用してこれを行います。 USDF IG は、医療保険者の薬の数式情報に対する FHIR インターフェイスを定義します。これは、医療保険者が支払いを同意するブランド名と汎用的な化学薬の一覧です。 この主な使用例は、患者に対して規定されている代替の薬が利用可能な場合に患者が理解し、ドラッグ コストを比較できるようです。

## <a name="provider-directory-api-implementation-guide"></a>プロバイダー ディレクトリ API 実装ガイド

Provider Directory API では、1 つの実装ガイドへの準拠について説明します。

* [HL7 Da Vinci PDex Plan Network IG:](http://build.fhir.org/ig/HL7/davinci-pdex-plan-net/)この実装ガイドでは、医療保険者の保険プラン、関連するネットワーク、およびこれらのネットワークに参加する組織とプロバイダーへの FHIR インターフェイスを定義します。

## <a name="touchstone"></a>試金石

さまざまな実装ガイドへの準拠をテストするために [、Touchstone](https://touchstone.aegis.net/touchstone/) は最適なリソースです。 今後のチュートリアルでは、さまざまな Touchstone テストに合格するように FHIR サービスが構成されていることを確認します。 Touchstone サイトには、立ち上げと実行に役立つ多くの素晴らしいドキュメントがあります。

## <a name="next-steps"></a>次の手順

相互運用性と患者アクセス規則、実装ガイド、および使用可能なテスト ツール (Touchstone) の基本的な理解が得られるので、CARIN IG for Blue Button の FHIR サービスの設定について説明します。 

>[!div class="nextstepaction"]
>[CARIN Blue Button 実装ガイド](carin-implementation-guide-blue-button-tutorial.md)  
