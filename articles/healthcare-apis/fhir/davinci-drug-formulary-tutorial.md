---
title: チュートリアル - DaVinci の薬の数式 - Azure API for FHIR
description: このチュートリアルでは、DaVinci Formulary 実装Azure API for FHIRに対して Touchstone テストに合格する方法を設定する方法について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 06/01/2021
ms.openlocfilehash: feeccd8f194397e9c99f19920d09b8bb2056ff08
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592956"
---
# <a name="davinci-drug-formulary"></a>DaVinci の薬の数式

このチュートリアルでは[、DaVinci Payer Data Exchange US の](http://hl7.org/fhir/us/Davinci-drug-formulary/)ドラッグ数式実装ガイド の[Touchstone](https://touchstone.aegis.net/touchstone/)テストに合格するために Azure API for FHIR を設定する方法について説明します。

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

最初に注目するテストは [、DaVinci の Formulary](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)機能ステートメント にAzure API for FHIRをテストします。 更新なしでこのテストを実行すると、検索パラメーターが見つからないとプロファイルが見つからないので、テストは失敗します。

### <a name="define-search-parameters"></a>検索パラメーターを定義する

Da Vinci Formulary IG の一部として、FormularyDrug[](how-to-do-custom-search.md)リソースの 3 つの新しい検索パラメーターを定義する必要があります。 これら 3 つはすべて、機能ステートメントでテストされます。

* [DrugTier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [Plan](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [DrugName](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

Da Vinci Formulary IG に必要な残りの検索パラメーターは基本仕様によって定義され、Azure API for FHIR で既に使用できます。それ以上の更新はありません。

### <a name="store-profiles"></a>プロファイルを保存する

検索パラメーターを定義する以外に、このテストに合格するために必要なその他の更新は、必要なプロファイル を読み [込むのみです](validation-against-profiles.md)。 Da Vinci Formulary IG の一部として使用される 2 つのプロファイルがあります。

* [数式のドラッグ](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [数式カバレッジ プラン](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>サンプルの rest ファイル

これらの検索パラメーターとプロファイルの作成を支援するために、上記のすべての手順を 1 つのファイルに含む、オープン ソース サイトに [Da Vinci Formulary](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) サンプル HTTP ファイルがあります。 必要なすべてのプロファイルと検索パラメーターをアップロードしたら、Touchstone で機能ステートメント テストを実行できます。 次のコマンドが正常に実行されます。

:::image type="content" source="media/cms-tutorials/davinci-test-script-execution.png" alt-text="DaVinci テスト スクリプトの実行。":::

## <a name="touchstone-query-test"></a>Touchstone クエリ テスト

2 番目のテストは、 [クエリ機能です](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)。 このテストでは、さまざまなパラメーターを使用して特定の リソースと `CoveragePlan` `Drug` リソースを検索できる検証を行います。 最適なパスは、データベースに既に存在するリソースに対してテストすることですが、リソースの作成とテストに使用できる IG の例から取得されたサンプル リソースで使用できる [DaVinciFormulary_Sample_Resources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http) HTTP ファイルもあります。

:::image type="content" source="media/cms-tutorials/davinci-test-execution-results.png" alt-text="DaVinci のテスト実行結果。":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Touchstone で Da Vinci Payer Data Exchange US Formulary を渡す方法について説明しました。 次に、Touchstone で Da Vinci PDex 実装ガイドをテストする方法について説明します。

>[!div class="nextstepaction"]
>[Da Vinci PDex](davinci-pdex-tutorial.md)