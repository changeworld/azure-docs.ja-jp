---
title: チュートリアル-Blue Button&#174; の実装ガイド-Azure API for FHIR
description: このチュートリアルでは、ブルーボタン (C4BB IG) 用の CARIN 実装ガイドの Touchstone テストに合格するように、FHIR 用の Azure API を設定する手順について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 05/27/2021
ms.openlocfilehash: 25be80678389b59e907caf60529a8e4048a959e0
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562783"
---
# <a name="carin-implementation-guide-for-blue-button174"></a>青色のボタンの実装ガイド&#174;

このチュートリアルでは、青色のボタン (C4BB IG)[用の Carin 実装ガイド](https://build.fhir.org/ig/HL7/carin-bb/index.html)の[Touchstone](https://touchstone.aegis.net/touchstone/)テストに合格するように、Fhir 用の Azure API を設定する手順について説明します。

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

最初に焦点を当てるテストは、 [C4BB IG の機能ステートメント](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)に対して Azure API for FHIR をテストすることです。 更新を行わずに Azure API for FHIR に対してこのテストを実行すると、検索パラメーターがなく、プロファイルが見つからないことが原因でテストが失敗します。 


### <a name="define-search-parameters"></a>検索パラメーターの定義

C4BB IG の一部として、リソースに対して3つの [新しい検索パラメーター](how-to-do-custom-search.md) を定義する必要があり `ExplanationOfBenefit` ます。 これらのうちの2つは、機能ステートメント (型とサービス日付) でテストされ、検索に必要なもの `_include` (insurer) です。  

* [type](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-type.json)
* [サービス-日付](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-service-date.json)
* [insurer](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-insurer.json)

> [!NOTE]
> これらの検索パラメーターの未加工の JSON では、という名前がに設定されて `ExplanationOfBenefit_<SearchParameter Name>` います。 Touchstone テストでは、これらの名前が **type**、 **service-date**、および **insurer** であることを想定しています。  
 
C4BB IG に必要な残りの検索パラメーターは、基本仕様によって定義されており、追加の更新を行わずに、Azure API for FHIR で既に使用できます。
 
### <a name="store-profiles"></a>ストアプロファイル

検索パラメーターを定義する以外に、このテストを成功させるために必要なもう1つの更新は、 [必要なプロファイル](validation-against-profiles.md)を読み込むことです。 C4BB IG 内には、8つのプロファイルが定義されています。 

* [C4BB カバレッジ](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Coverage.html) 

* [C4BB ExplanationOfBenefit 入院機関](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Inpatient-Institutional.html) 

* [C4BB ExplanationOfBenefit Outpatient 機関](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Outpatient-Institutional.html) 

* [C4BB ExplanationOfBenefit 薬剤](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Pharmacy.html) 

* [C4BB ExplanationOfBenefit Professional NonClinician](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Professional-NonClinician.html) 

* [C4BB 組織](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Organization.html) 

* [C4BB の患者](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Patient.html) 

* [C4BB の専門家](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Practitioner.html) 

### <a name="sample-rest-file"></a>サンプルの rest ファイル

これらの検索パラメーターとプロファイルの作成を支援するために、 [サンプルの http ファイル](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB.http) を使用します。このファイルには、上記で説明したすべての手順が1つのファイルに含まれています。 必要なすべてのプロファイルと検索パラメーターをアップロードしたら、Touchstone で機能ステートメントテストを実行できます。

:::image type="content" source="media/cms-tutorials/capability-test-script-execution-results.png" alt-text="機能テストスクリプトの実行結果。":::

## <a name="touchstone-read-test"></a>Touchstone read テスト

Capabilities ステートメントをテストした後、C4BB IG に対して Azure API for FHIR の [読み取り機能](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/01-Read&activeOnly=false&contentEntry=TEST_SCRIPTS) をテストします。 このテストは、最初のテストで読み込まれた8つのプロファイルに対する準拠をテストしています。 プロファイルに準拠したリソースを読み込む必要があります。 最善の方法は、データベースに既に存在するリソースに対してテストすることですが、リソースの作成とテストに使用できる IG の例から抜粋したサンプルリソースでも [http ファイル](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB_Sample_Resources.http) を利用できます。

:::image type="content" source="media/cms-tutorials/test-execution-results-touchstone.png" alt-text="Touchstone テストの実行結果を読み取ります。":::

## <a name="touchstone-eob-query-test"></a>Touchstone EOB クエリテスト

次に確認するテストは、 [EOB クエリテスト](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/02-EOBQuery&activeOnly=false&contentEntry=TEST_SCRIPTS)です。 読み取りテストを既に完了している場合は、必要なすべてのデータが読み込まれています。 このテストでは、さまざまなパラメーターを使用して、特定の患者や特典リソースの説明を検索できるかどうかを検証します。

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-eob-query-test.png" alt-text="Touchstone EOB クエリの実行結果。":::

## <a name="touchstone-error-handling-test"></a>Touchstone エラー処理テスト

最後のテストでは、 [エラー処理](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/99-ErrorHandling&activeOnly=false&contentEntry=TEST_SCRIPTS)をテストします。 必要な手順は、データベースから ExplanationOfBenefit リソースを削除し、テストで delete ExplanationOfBenfit リソースの ID を使用することだけです。

:::image type="content" source="media/cms-tutorials/test-execution-touchstone-error-handling.png" alt-text="Touchstone EOB エラー処理の結果。":::


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Touchstone でブルーボタンテスト用に CARIN IG を渡す方法について説明します。 次に、Da ヴィンチ処方テストのテスト方法を確認します。

>[!div class="nextstepaction"]
>[Davinci.txt 薬品処方](davinci-drug-formulary-tutorial.md)       
 