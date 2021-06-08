---
title: チュートリアル - DaVinci PDex - Azure API for FHIR
description: このチュートリアルでは、Da Vinci Payer Data Exchange 実装ガイドAzure API for FHIRテストに合格する方法を設定する方法について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/02/2021
ms.openlocfilehash: 3f9aa795a08aa027fd0cc9758e9479fa0ec81e09
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592962"
---
# <a name="davinci-pdex"></a>DaVinci PDex

このチュートリアルでは[、Da Vinci Payer Data Exchange](http://hl7.org/fhir/us/davinci-pdex/toc.html)実装ガイド (PDex IG) の[Touchstone](https://touchstone.aegis.net/touchstone/)テストに合格するために Azure API for FHIR を設定する方法について説明します。

> [!NOTE]
> これらのテストはすべて、JSON テストに対して実行します。 このAzure API for FHIRは JSON と XML の両方をサポートしますが、JSON または XML にアクセスする個別のエンドポイントを持つ必要があります。 この理由から、すべての XML テストは失敗します。 XML で機能ステートメントを表示する場合は \_ \` 、GET {fhirurl}/metadata という形式パラメーターを渡します。 \_format=xml\`

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

最初に注目するテストのセットは、PDex IG 機能ステートメントAzure API for FHIRテストです。 これには、次の 3 つのテストが含まれます。

* 最初のテストでは、基本的な機能ステートメントを IG 要件に対して検証し、更新なしで合格します。

* 2 番目のテストでは、US Core 用に追加されたすべてのプロファイルが検証されます。 このテストは更新なしで合格しますが、一連の警告が含まれます。 これらの警告を削除するには、US Core プロファイル [を読み込む必要があります](validation-against-profiles.md)。 すべてのプロファイルを作成 [するサンプル HTTP](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) ファイルを作成しました。 また、HL7 [サイトから](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) プロファイルを直接取得することもできます。プロファイルのバージョンは最新バージョンです。

* 3 番目のテストでは、$patient [すべての操作が](patient-everything.md) サポートされている検証が行います。 現在、このテストは失敗します。 この操作は、Azure API for FHIR の 2021 年 6 月半ばに使用できる予定であり、現在は Cosmos DB のオープン ソースの FHIR サーバーで使用できます。 ただし、機能ステートメントにはないので、バグ [1989](https://github.com/microsoft/fhir-server/issues/1989)の修正プログラムをリリースするまで、このテストは失敗します。 

 
:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-failed.png" alt-text="DaVinci PDex の実行に失敗しました。":::

## <a name="touchstone-member-match-test"></a>Touchstone $member一致テスト

[Payer](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) Data Exchange セクションの 2 番目のテストでは、一致する一致操作[$member存在をテストします](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)。 一致操作の詳細については$member一致操作の概要 [$memberを参照してください](tutorial-member-match.md)。

このテストでは、テストに合格するためにいくつかのサンプル データを読み込む必要があります。 ここには、 [患者とカバレッジ](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) がリンクされた、テストに必要な Rest ファイルがあります。 このデータが読み込まれたら、このテストに成功できます。 データが読み込まれない場合は、完全一致が見つからなから、422 応答が返されます。

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-passed.png" alt-text="DaVinci PDex テスト スクリプトに合格しました。":::

## <a name="touchstone-patient-by-reference"></a>参照によるタッチトン患者

次に確認するテストは、参照 [テストによる患者](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) です。 この一連のテストでは、さまざまな検索条件に基づいて患者を検索できると検証されます。 参照によって患者をテストする最善の方法は、独自のデータに対してテストすることですが、使用するために[](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http)読み込み可能なサンプル リソース ファイルもアップロードしました。

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-execution-passed.png" alt-text="DaVinci PDex の実行が渡された。":::

## <a name="touchstone-patienteverything-test"></a>Touchstone 患者/$everything テスト

最後に行うテストは、patient-everything のテストです。 このテストでは、患者を読み込む必要があります。その後、その患者の ID を使用して、$everything 操作を使用して患者に関連するデータをプルできるテストを行います。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Touchstone で Payer Exchange テストに合格する方法について説明しました。 次に、すべての機能についてAzure API for FHIRできます。

>[!div class="nextstepaction"]
>[サポートされる機能](fhir-features-supported.md)  