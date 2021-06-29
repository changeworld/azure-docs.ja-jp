---
title: チュートリアル-Da ヴィンチ Plan Net-Azure API for FHIR
description: このチュートリアルでは、Da ヴィンチ支払人データ交換実装ガイドの Touchstone テストに合格するように、FHIR 用の Azure API を設定する手順について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 06/25/2021
ms.openlocfilehash: b91fc4d01a9279d6f2ce58b15fc8b8c00790ea57
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113012943"
---
# <a name="da-vinci-plan-net"></a>Da ヴィンチ Plan Net

このチュートリアルでは、Da ヴィンチ PDEX 支払人 Network (Plan-Net) 実装ガイドの [Touchstone](https://touchstone.aegis.net/touchstone/) テストに合格するように、FHIR 用の Azure API を設定する手順について説明します。

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

最初に注目するテストは、 [Da ヴィンチ Plan-Net 機能ステートメント](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)に対して Azure API for FHIR をテストすることです。 このテストを更新せずに実行すると、検索パラメーターがなく、プロファイルが見つからないことが原因でテストが失敗します。

## <a name="define-search-parameters"></a>検索パラメーターの定義

Da ヴィンチ Plan-Net IG の一部として、医療サービス、保険プラン、専門家の役割、組織、組織の関連リソースに対して、6つの [新しい検索パラメーター](https://docs.microsoft.com/azure/healthcare-apis/fhir/how-to-do-custom-search) を定義する必要があります。 これらの6つはすべて、機能ステートメントでテストされています。

* [医療サービスの範囲](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [保険契約の範囲](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [保険プランのプランの種類](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [組織の範囲](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [組織の所属ネットワーク](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [実践ロールネットワーク](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> これらの検索パラメーターの未加工の JSON では、という名前がに設定されて `Plannet_sp_<Resource Name>_<SearchParameter Name>` います。 Touchstone テストでは、これらの名前が `SearchParameter Name` (カバレッジエリア、プランの種類、またはネットワーク) のみになることを想定しています。

Da ヴィンチ Plan-Net IG に必要な残りの検索パラメーターは、基本仕様によって定義されており、追加の更新を行わずに、Azure API for FHIR で既に使用できます。

## <a name="store-profiles"></a>ストアプロファイル

検索パラメーターを定義する以外に、このテストに合格するために [必要なプロファイルと拡張機能](https://docs.microsoft.com/azure/healthcare-apis/fhir/validation-against-profiles#storing-profiles) を読み込む必要があります。 Da ヴィンチ Plan-Net IG の一部として使用されるプロファイルは9つあります。

* [プラン-Net エンドポイント](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [プラン-Net 医療サービス](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [プラン-Net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [計画-ネットの場所](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [計画-Net ネットワーク](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [プラン-Net 組織](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [プラン-Net 組織の所属](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [プラン-Net の専門家](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [プラン-Net PractitionerRole](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>サンプルの rest ファイル

これらの検索パラメーターとプロファイルの作成を支援するために、1つのファイルで前述したすべての手順を含むサンプル http ファイルをオープンソースサイトに用意しています。 必要なすべてのプロファイルと検索パラメーターをアップロードしたら、Touchstone で機能ステートメントテストを実行できます。

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Da ヴィンチ plan net sample rest テスト実行スクリプトが成功しました":::

## <a name="touchstone-error-handling-test"></a>Touchstone エラー処理テスト

2番目のテストでは、 [エラー処理](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS)をテストします。 実行する必要がある手順は、データベースから HealthcareService リソースを削除し、テストで削除された HealthcareService リソースの ID を使用することだけです。 この手順では、オープンソースサイトのサンプル [DaVinci_PlanNet](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) HealthcareService ファイルに、post と delete の例が示されています。

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Da ヴィンチ plan net touchstone error テスト実行スクリプトが成功しました":::

## <a name="touchstone-query-test"></a>Touchstone クエリテスト

次のテストでは、 [クエリ機能のテスト](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)について説明します。 このテストは、最初のテストで読み込まれたプロファイルに対する準拠をテストしています。 プロファイルに準拠したリソースを読み込む必要があります。 最善の方法は、データベースに既に存在するリソースに対してテストすることですが、リソースの作成とテストに使用できる IG の例から抜粋したサンプルリソースを含む [DaVinci_PlanNet_Sample_Resources の http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) ファイルも用意されています。  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Da ヴィンチ plan net クエリのテストに失敗しました":::

> [!NOTE]
> 提供されるサンプルリソースを使用して、クエリテストの成功率を98% にする必要があります。

> * FHIR サーバーに対して、次のいずれかのテストが失敗する原因となっている GitHub の問題が発生しています: [リソースが基本条件と _include 条件の両方を満たしている場合は、複数回返されます。問題 #2037 ·microsoft/fhir-サーバー (github.com)](https://github.com/microsoft/fhir-server/issues/2037)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Da ヴィンチ PDEX 支払人 Network (Plan-Net) 実装ガイドの Touchstone テストに合格するように、FHIR 用の Azure API を設定する方法について説明します。 次に、すべての Azure API for FHIR 機能について学習できます。

>[!div class="nextstepaction"]
>[サポートされる機能](fhir-features-supported.md)