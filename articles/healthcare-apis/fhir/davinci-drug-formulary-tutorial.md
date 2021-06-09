---
title: チュートリアル-Da ヴィンチ薬品処方-FHIR 用 Azure API
description: このチュートリアルでは、Touchstone テストを Davinci.txt 薬品処方実装ガイドに渡すために、Azure API for FHIR を設定する手順について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 06/07/2021
ms.openlocfilehash: fc66de13c02d6d04e8a156937de70775b9053da3
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751093"
---
# <a name="da-vinci-drug-formulary"></a>Da ヴィンチ薬品処方

このチュートリアルでは、「 [Da ヴィンチ支払人データ交換の米国薬品処方実装ガイド」](http://hl7.org/fhir/us/Davinci-drug-formulary/)に記載されている[Touchstone](https://touchstone.aegis.net/touchstone/)テストに合格するように、Fhir 用の Azure API を設定する手順について説明します。

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

まず、Azure API for FHIR を [Da ヴィンチ薬品処方機能ステートメント](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)に対してテストします。 このテストを更新せずに実行すると、検索パラメーターがなく、プロファイルが見つからないことが原因でテストが失敗します。

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

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Touchstone で Da ヴィンチ支払人データ交換の米国薬品処方を渡す方法について説明しました。 次に、Touchstone で Da ヴィンチ PDex 実装ガイドをテストする方法を学習します。

>[!div class="nextstepaction"]
>[Da ヴィンチ PDex](davinci-pdex-tutorial.md)