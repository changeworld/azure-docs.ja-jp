---
title: チュートリアル-$member の一致操作
description: Da ヴィンチ Health レコード Exchange (hrex) の一部として定義されている $member 一致操作について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/06/2021
ms.openlocfilehash: 2771458c6c9f23c9e87691e1a74d3e29986ad482
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778267"
---
# <a name="member-match-operation-in-fhir-service"></a>FHIR サービスの $member 一致操作

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

[$member match](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)は、Da ヴィンチ Health レコード Exchange (hrex) の一部として定義される操作です。 このガイドでは、$member 一致とその使用方法について説明します。

## <a name="overview-of-member-match"></a>$member の概要

$Member 一致操作は、支払人から支払人へのデータ交換を支援するために作成されました。これにより、新しい支払人が患者の以前の支払人からの患者の一意の識別子を取得できるようになります。 $Member 一致操作では、要求の本文で次の3つの情報を渡す必要があります。

* 患者の人口統計
* 古いカバレッジ情報
* 新しいカバレッジ情報 (実装に基づく必要はありません)

データが渡されると、Azure の医療 Api (FHIR サービスと呼ばれます) の FHIR サービスは、渡された古いカバレッジ情報と共に渡された人口統計と正確に一致する患者を見つけることができるかどうかを検証します。 結果が見つかった場合、応答は、元の患者データと古い支払人から追加された新しい識別子、および古いカバレッジ情報を含むバンドルになります。

> [!NOTE]
> この仕様では、新しいカバレッジ情報を渡して戻すことが記述されています。 結果を小さくするために、そのデータを除外することにしました。

## <a name="example-of-member-match"></a>$member の一致の例

$Member 一致を使用するには、次の呼び出しを使用します。

`POST {{fhirurl}}/Patient/$member-match`

患者、古いカバレッジ、および新しいカバレッジを含むパラメーターリソースを本文に含める必要があります。 JSON 表現を表示するには、「 [$member の要求例](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)」を参照してください。

単一の一致が見つかった場合は、200応答を受け取り、別の識別子が追加されます。

:::image type="content" source="media/centers-medicare-services-tutorials/two-hundred-response.png" alt-text="200 100 応答コード。":::

$Member 一致で一意の一致が見つからない場合は、エラーコードを含む422応答が返されます。

## <a name="next-steps"></a>次の手順

このガイドでは、$member 一致操作について学習しました。 次に、Touchstone で Exchange IG の Da ヴィンチ支払人データをテストする方法について学習します。これには、$member 一致操作が必要です。

>[!div class="nextstepaction"]
>[Davinci.txt PDex](davinci-pdex-tutorial.md)
