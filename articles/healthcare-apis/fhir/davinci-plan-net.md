---
title: チュートリアル - Da Vinci Plan Net - Azure Healthcare API
description: このチュートリアルでは、Da Vinci Payer Data Azure API for FHIR実装ガイドの Touchstone テストに合格するExchangeを設定する方法について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: e9a6da0564c5338d8a62eb34ef6d213cd8835219
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2021
ms.locfileid: "133358942"
---
# <a name="da-vinci-plan-net"></a>Da Vinci Plan Net

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、Azure Healthcare API (ここでは FHIR サービスと呼ばれる) で FHIR サービスを設定し、Da Vinci PDEX Payer Network (Plan-Net) 実装ガイドの [Touchstone](https://touchstone.aegis.net/touchstone/) テストに合格する方法について説明します。

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

最初に注目するテストは [、Da Vinci](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)の機能ステートメント に対して FHIR サービスPlan-Netです。 更新なしでこのテストを実行すると、検索パラメーターが見つからないとプロファイルが見つからないので、テストは失敗します。

## <a name="define-search-parameters"></a>検索パラメーターを定義する

Da Vinci Plan-Net IG の一部として、医療サービス、保険プラン、[](how-to-do-custom-search.md)実践者ロール、組織、組織の所属リソースに対して 6 つの新しい検索パラメーターを定義する必要があります。 これら 6 つはすべて、機能ステートメントでテストされます。

* [医療サービスの対象領域](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [保険プランの対象範囲](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [保険プランの種類](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [組織の対象範囲](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [組織の所属ネットワーク](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [実践者ロール ネットワーク](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> これらの検索パラメーターの未加工の JSON では、名前は に設定されます `Plannet_sp_<Resource Name>_<SearchParameter Name>` 。 Touchstone テストでは、これらの名前が (カバレッジ領域、プランの種類、またはネットワーク) のみである `SearchParameter Name` 必要があります。

Da Vinci Plan-Net IG に必要な残りの検索パラメーターは基本仕様によって定義され、追加の更新なしで FHIR サービスで既に使用できます。

## <a name="store-profiles"></a>プロファイルを保存する

検索パラメーターを定義する以外に、このテストに合格するために必要なプロファイルと [拡張機能を読](./validation-against-profiles.md#storing-profiles) み込む必要があります。 Da Vinci アプリケーション IG の一部として使用される 9 Plan-Netがあります。

* [Plan-Net エンドポイント](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [Plan-Net Healthcare Service](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [Plan-Net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [プランネットの場所](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [Plan-Net Network](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [Plan-Net Organization](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [Plan-Net OrganizationAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [Plan-Net の実践者](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [Plan-Net の実践者ロール](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>サンプルの rest ファイル

これらの検索パラメーターとプロファイルの作成を支援するために、上記のすべての手順を 1 つのファイルに含むサンプル http ファイルがオープンソース サイトに作成されています。 必要なすべてのプロファイルと検索パラメーターをアップロードしたら、Touchstone で機能ステートメント テストを実行できます。

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Da Vinci plan net sample rest test execution script passed":::

## <a name="touchstone-error-handling-test"></a>Touchstone エラー処理テスト

2 つ目のテストでは、エラー処理の [テストを行います](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS)。 実行する必要がある唯一の手順は、データベースから HealthcareService リソースを削除し、テストで削除された HealthcareService リソースの ID を使用します。 オープンソース サイト [DaVinci_PlanNet.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) ファイルのサンプルでは、この手順の投稿と削除を行う HealthcareService の例を示します。

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Da Vinci plan net touchstone error test execution script passed":::

## <a name="touchstone-query-test"></a>Touchstone クエリ テスト

次に説明するテストは、クエリ機能 [のテストです](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS)。 このテストは、最初のテストで読み込んだプロファイルに対する準拠をテストしています。 プロファイルに準拠するリソースを読み込む必要があります。 最適なパスは、データベースに既に存在するリソースに対してテストすることですが、サンプル リソースを含む [DaVinci_PlanNet_Sample_Resources.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) ファイルも用意されています。サンプル リソースは、リソースの作成とテストに使用できる IG の例から取得されます。  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Da Vinci プランの net クエリ テストに失敗しました":::

> [!NOTE]
> サンプル リソースが用意されている場合は、クエリ テストの成功率が 98% である必要があります。
> FHIR サーバーに対GitHub問題が発生し、これらのテストの 1 つが失敗しています。
リソースは、基本条件と条件の両方を満たしている場合に複数回_includeされます。 [#2037](https://github.com/microsoft/fhir-server/issues/2037)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Da Vinci PDEX Payer Network (Plan-Net) 実装ガイドの Touchstone テストに合格するために Azure API for FHIR を設定する方法について説明しました。 次に、すべての FHIR サービス機能について学習できます。

>[!div class="nextstepaction"]
>[サポートされる機能](fhir-features-supported.md)
