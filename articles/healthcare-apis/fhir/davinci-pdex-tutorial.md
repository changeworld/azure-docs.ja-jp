---
title: チュートリアル-Da ヴィンチ PDex-FHIR 用 Azure API
description: このチュートリアルでは、Da ヴィンチ支払人データ交換実装ガイドのテストに合格するように、FHIR 用の Azure API を設定する手順について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/07/2021
ms.openlocfilehash: 454b049a90bcf6e1d1793606a8759222698e5697
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751111"
---
# <a name="da-vinci-pdex"></a>Da ヴィンチ PDex

このチュートリアルでは、 [Da ヴィンチ支払人データ交換実装ガイド](http://hl7.org/fhir/us/davinci-pdex/toc.html)(PDEX IG) の[Touchstone](https://touchstone.aegis.net/touchstone/)テストに合格するように、Fhir 用の Azure API を設定する手順について説明します。

> [!NOTE]
> これらのすべてのテストに対して、JSON テストに対して実行します。 Azure API for FHIR は、JSON と XML の両方をサポートしていますが、JSON または XML にアクセスするための個別のエンドポイントがありません。 このため、すべての XML テストが失敗します。 XML で機能ステートメントを表示する場合は、単に \_ format パラメーター: \` GET {fhirurl}/metadata を渡してください \_ 。format = xml\`

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

ここで注目するテストの最初のセットは、PDex IG 機能ステートメントに対して Azure API for FHIR をテストすることです。 これには、次の3つのテストが含まれます。

* 最初のテストでは、IG の要件に対して基本的な機能ステートメントを検証し、更新を行わずに成功します。

* 2番目のテストでは、すべてのプロファイルが米国コア用に追加されていることを検証します。 このテストは更新なしで成功しますが、多数の警告が含まれます。 これらの警告を削除するには、 [US コアプロファイルを読み込む](validation-against-profiles.md)必要があります。 すべてのプロファイルの作成手順を説明する [サンプル HTTP ファイル](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) を作成しました。 また、HL7 サイトから直接 [プロファイル](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) を取得することもできます。この場合、最新バージョンが使用されます。

* 3番目のテストでは、 [$patient すべての操作](patient-everything.md) がサポートされていることを検証します。 現時点では、このテストは失敗します。 この操作は、Azure API for FHIR の2021年6月中旬に利用可能になり、Cosmos DB のオープンソースの FHIR サーバーで使用できるようになりました。 ただし、機能ステートメントには不足しているため、このテストはバグ [1989](https://github.com/microsoft/fhir-server/issues/1989)の修正プログラムをリリースするまで失敗します。 

 
:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-failed.png" alt-text="Da ヴィンチ PDex の実行に失敗しました。":::

## <a name="touchstone-member-match-test"></a>Touchstone $member-一致テスト

[支払人データ交換] セクションの [2 番目のテスト](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) では、 [$member 一致操作](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)の存在をテストします。 $Member 一致操作の詳細については、「 [$member の一致操作の概要](tutorial-member-match.md)」を参照してください。

このテストでは、テストを成功させるためにいくつかのサンプルデータを読み込む必要があります。 [ここ](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http)には、テストに必要な、リンクされている患者と範囲を含む rest ファイルがあります。 このデータが読み込まれると、このテストを成功させることができます。 データが読み込まれていない場合は、完全一致が見つからないことが原因で422応答が返されます。

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-script-passed.png" alt-text="Da ヴィンチ PDex テストスクリプトが成功しました。":::

## <a name="touchstone-patient-by-reference"></a>Touchstone による患者の参照

次に確認するテストは、 [参照テストによる患者](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) です。 この一連のテストでは、さまざまな検索条件に基づいて患者を見つけることができるかどうかが検証されます。 患者を参照によってテストする最良の方法は、独自のデータに対してテストすることですが、使用するために読み込むことのできる [サンプルリソースファイル](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) もアップロードしています。

:::image type="content" source="media/cms-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Da ヴィンチ PDex の実行に成功しました。":::

## <a name="touchstone-patienteverything-test"></a>Touchstone の患者/$everything テスト

最後に説明するテストは、患者のすべてをテストすることです。 このテストでは、患者を読み込んだ後、その患者の ID を使用して、患者に関連するすべてのデータをプルするために $everything 操作を使用できることをテストします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Touchstone で支払人 Exchange テストを渡す方法について説明します。 次に、すべての Azure API for FHIR 機能について学習できます。

>[!div class="nextstepaction"]
>[サポートされる機能](fhir-features-supported.md)  