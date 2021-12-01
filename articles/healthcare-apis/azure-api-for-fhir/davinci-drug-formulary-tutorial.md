---
title: Azure API for FHIR の Da ヴィンチ薬品処方のチュートリアル
description: このチュートリアルでは、Touchstone テストを Davinci.txt 薬品処方実装ガイドに渡すために、FHIR 用の Azure API を設定する手順について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: 86efbb3c0d0dccdc99ef9364d9c579cb5c7655bb
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2021
ms.locfileid: "133398368"
---
# <a name="tutorial-for-da-vinci-drug-formulary-for-azure-api-for-fhir"></a>Azure API for FHIR 用の Da ヴィンチ薬品処方のチュートリアル

このチュートリアルでは、「 [Touchstone](https://touchstone.aegis.net/touchstone/) test for the [Da ヴィンチ支払人 Data」 Exchange US 薬品処方実装ガイド](http://hl7.org/fhir/us/Davinci-drug-formulary/)に合格するように、fhir 用の Azure API を設定する手順について説明します。

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

最初に焦点を当てるテストは、 [Da ヴィンチ薬品処方機能ステートメント](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)に対して Azure API for FHIR をテストすることです。 このテストを更新せずに実行すると、検索パラメーターがなく、プロファイルが見つからないことが原因でテストが失敗します。

### <a name="define-search-parameters"></a>検索パラメーターの定義

Da ヴィンチ薬品処方 IG の一部として、FormularyDrug リソースに対して3つの [新しい検索パラメーター](how-to-do-custom-search.md) を定義する必要があります。 これらの3つはすべて、機能ステートメントでテストされています。

* [DrugTier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [DrugPlan](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [DrugName](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

Da ヴィンチ薬品処方 IG に必要なその他の検索パラメーターは、基本仕様によって定義されており、これ以上更新を行わずに、Azure API for FHIR で既に利用できます。

### <a name="store-profiles"></a>ストアプロファイル

検索パラメーターを定義する以外に、このテストを成功させるために必要な唯一の更新は、 [必要なプロファイル](validation-against-profiles.md)を読み込むことです。 Da ヴィンチ薬品処方 IG の一部として使用されるプロファイルは2つあります。

* [処方薬品](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [処方カバレッジプラン](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>サンプルの rest ファイル

これらの検索パラメーターとプロファイルの作成を支援するために、前述の1つのファイルに記載されているすべての手順を含む、 [Da ヴィンチ処方](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) sample HTTP ファイルをオープンソースサイトに用意しています。 必要なすべてのプロファイルと検索パラメーターをアップロードしたら、Touchstone で機能ステートメントテストを実行できます。 正常に実行された場合は、次のようになります。

:::image type="content" source="media/cms-tutorials/davinci-test-script-execution.png" alt-text="Da ヴィンチテストスクリプトの実行。":::

## <a name="touchstone-query-test"></a>Touchstone クエリテスト

2番目のテストは [クエリ機能](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)です。 このテストでは、さまざまなパラメーターを使用して、特定のカバレッジ計画と薬品のリソースを検索できるかどうかを検証します。 最適なパスは、データベースに既に存在するリソースに対してテストすることですが、リソースの作成とテストに使用できる IG の例から抜粋したサンプルリソースでも、 [Da VinciFormulary_Sample_Resources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http) HTTP ファイルを利用できます。

:::image type="content" source="media/cms-tutorials/davinci-test-execution-results.png" alt-text="Da ヴィンチテストの実行結果。":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Touchstone で Da ヴィンチ支払人データ Exchange US 薬品処方に渡す方法について説明しました。 次に、Touchstone で Da ヴィンチ PDex 実装ガイドをテストする方法を学習します。

>[!div class="nextstepaction"]
>[Da Vinci PDex](davinci-pdex-tutorial.md)