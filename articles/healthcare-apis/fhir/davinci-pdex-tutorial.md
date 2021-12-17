---
title: チュートリアル-Da ヴィンチ PDex-Azure の医療 Api (プレビュー)
description: このチュートリアルでは、Da ヴィンチ支払人データ Exchange 実装ガイドのテストに合格するように fhir サービスを設定する手順について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.author: cavoeg
author: caitlinv39
ms.date: 11/12/2021
ms.openlocfilehash: 12f7cbac225cfe4e85d2df21c363d4c08fb0b4c6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494457"
---
# <a name="da-vinci-pdex"></a>Da Vinci PDex

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、Azure の医療 api (fhir サービスと呼ばれる) の fhir サービスを設定して、 [Da ヴィンチ支払人データ Exchange 実装ガイド](http://hl7.org/fhir/us/davinci-pdex/toc.html)(pdex IG) の[Touchstone](https://touchstone.aegis.net/touchstone/)テストに合格する手順を説明します。

> [!NOTE]
> FHIR サービスは、JSON のみをサポートしています。 Microsoft オープンソースの FHIR サービスは、JSON と XML の両方をサポートしています。また、オープンソースでは、_format パラメーターを使用して XML 機能ステートメントを表示できます。 `GET {fhirurl}/metadata?_format=xml`

## <a name="touchstone-capability-statement"></a>Touchstone 機能ステートメント

ここで注目するテストの最初のセットは、PDex IG の機能ステートメントに対して FHIR サービスをテストすることです。 これには、次の3つのテストが含まれます。

* 最初のテストでは、IG の要件に対して基本的な機能ステートメントを検証し、更新を行わずに成功します。

* 2番目のテストでは、すべてのプロファイルが米国コア用に追加されていることを検証します。 このテストは更新なしで成功しますが、多数の警告が含まれます。 これらの警告を削除するには、 [US コアプロファイルを読み込む](validation-against-profiles.md)必要があります。 すべてのプロファイルの作成手順を説明する [サンプル HTTP ファイル](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) を作成しました。 また、HL7 サイトから直接 [プロファイル](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) を取得することもできます。この場合、最新バージョンが使用されます。

* 3番目のテストでは、 [$patient すべての操作](patient-everything.md) がサポートされていることを検証します。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-failed.png" alt-text="Da ヴィンチ PDex の実行に失敗しました。":::

## <a name="touchstone-member-match-test"></a>Touchstone $member-一致テスト

[支払人データ Exchange] セクションの[2 番目のテスト](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS)では、 [$member 一致操作](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)が存在するかどうかをテストします。 $Member 一致操作の詳細については、「 [$member の一致操作の概要](tutorial-member-match.md)」を参照してください。

このテストでは、テストを成功させるためにいくつかのサンプルデータを読み込む必要があります。 [ここ](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http)には、テストに必要な、リンクされている患者と範囲を含む rest ファイルがあります。 このデータが読み込まれると、このテストを成功させることができます。 データが読み込まれていない場合は、完全一致が見つからないことが原因で422応答が返されます。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-passed.png" alt-text="Da ヴィンチ PDex テストスクリプトが成功しました。":::

## <a name="touchstone-patient-by-reference"></a>Touchstone による患者の参照

次に確認するテストは、 [参照テストによる患者](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) です。 この一連のテストでは、さまざまな検索条件に基づいて患者を見つけることができるかどうかが検証されます。 患者を参照によってテストする最良の方法は、独自のデータに対してテストすることですが、使用するために読み込むことのできる [サンプルリソースファイル](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) もアップロードしています。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Da ヴィンチ PDex の実行に成功しました。":::

## <a name="touchstone-patienteverything-test"></a>Touchstone の患者/$everything テスト

最後に説明するテストは、患者のすべてをテストすることです。 このテストでは、患者を読み込んだ後、その患者の ID を使用して、患者に関連するすべてのデータをプルするために $everything 操作を使用できることをテストします。

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-patient-everything.png" alt-text="touchstone の患者/$everything テストに合格しました。":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Touchstone で支払人 Exchange テストに渡す方法について説明します。 次に、Da ヴィンチ PDEX 支払人 Network (Plan-Net) 実装ガイドをテストする方法を学習します。

>[!div class="nextstepaction"]
>[Da Vinci Plan Net](davinci-plan-net.md)  
