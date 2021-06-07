---
title: チュートリアル-$member の一致操作-FHIR 用 Azure API
description: このチュートリアルでは、Da ヴィンチ Health レコード交換 (HRex) の一部として定義されている $member 一致操作について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/01/2021
ms.openlocfilehash: a6d24d0ebf91e2ae6992f6b101b8be0d4a0d4201
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111562732"
---
# <a name="member-match-operation"></a>$member の一致操作

[$member-match](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) は、Da ヴィンチ Health レコード交換 (hrex) の一部として定義される操作です。 このガイドでは、$member 一致とその使用方法について説明します。

## <a name="overview-of-member-match"></a>$member の概要

$Member 一致操作は、支払人から支払人へのデータ交換を支援するために作成されました。これにより、新しい支払人が患者の以前の支払人からの患者の一意の識別子を取得できるようになります。 $Member 一致操作では、要求の本文で次の3つの情報を渡す必要があります。

* 患者の人口統計

* 古いカバレッジ情報

* 新しいカバレッジ情報 (実装に基づく必要はありません)

データが渡されると、Azure API for FHIR は、渡された古いカバレッジ情報と共に渡された人口統計と正確に一致する患者を見つけることができるかどうかを検証します。 結果が見つかった場合、応答は、元の患者データと古い支払人から追加された新しい識別子、および古いカバレッジ情報を含むバンドルになります。

> [!NOTE]
> この仕様では、新しいカバレッジ情報を渡して戻すことが記述されています。 結果を小さくするために、そのデータを除外することにしました。

## <a name="example-of-member-match"></a>$member の一致の例

$Member 一致を使用するには、次の呼び出しを使用します。

`POST {{fhirurl}}/Patient/$member-match`

患者、古いカバレッジ、および新しいカバレッジを含むパラメーターリソースを本文に含める必要があります。 JSON 表現を表示するには、「 [$member の要求例](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)」を参照してください。

単一の一致が見つかった場合は、200応答を受け取り、別の識別子が追加されます。

:::image type="content" source="media/cms-tutorials/two-hundred-response.png" alt-text="200 100 応答コード。":::

$Member 一致で一意の一致が見つからない場合は、エラーコードを含む422応答が返されます。

## <a name="next-steps"></a>次のステップ

このガイドでは、$member 一致操作について学習しました。 次に、Touchstone での Da ヴィンチ支払人データ交換 IG のテストについて説明します。これには、$member 一致操作が必要です。

>[!div class="nextstepaction"]
>[Davinci.txt PDex](davinci-pdex-tutorial.md)