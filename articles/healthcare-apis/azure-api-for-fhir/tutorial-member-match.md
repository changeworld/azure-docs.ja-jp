---
title: チュートリアル - $member一致操作 - Azure API for FHIR
description: このチュートリアルでは、Da Vinci health Record $member (HRex) の一部として定義されている一致するExchangeについて説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/01/2021
ms.openlocfilehash: 592a5a473cf000713bc8a266e36516bc777a6e62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785877"
---
# <a name="member-match-operation-for-azure-api-for-fhir"></a>$memberの一致操作Azure API for FHIR

[$member一致](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html)は、Da Vinci Health Record (HRex) の一部として定義Exchange操作です。 このガイドでは、一致する$memberと、その使い方について確認します。

## <a name="overview-of-member-match"></a>一致$memberの概要

$member一致操作は、新しい支払者が患者の前の支払者から患者の一意の識別子を取得できるようにして、支払者から支払者へのデータ交換を支援するために作成されました。 一$member一致操作では、要求の本文に 3 つの情報を渡す必要があります。

* 患者の人口統計

* 古いカバレッジ情報

* 新しいカバレッジ情報 (実装に基づく必要はありません)

データが渡された後、Azure API for FHIR は、渡された人口統計と渡された古いカバレッジ情報と完全に一致する患者を見つけ出す可能性を検証します。 結果が見つかった場合、応答は、元の患者データと、古い支払者から追加された新しい識別子と、古いカバレッジ情報を含むバンドルになります。

> [!NOTE]
> 仕様では、新しいカバレッジ情報の受け渡しと戻しについて説明します。 結果を小さくするために、そのデータを省略することを決定しました。

## <a name="example-of-member-match"></a>一致$member例

一致$member使用するには、次の呼び出しを使用します。

`POST {{fhirurl}}/Patient/$member-match`

患者、古いカバレッジ、新しいカバレッジを含むパラメーター リソースを本文に含める必要があります。 JSON 表現を表示するには、「$member [一致する要求」を参照してください](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)。

1 つの一致が見つかった場合は、別の識別子が追加された 200 応答が返されます。

:::image type="content" source="media/cms-tutorials/two-hundred-response.png" alt-text="20000 応答コード。":::

一致$member一致が見つからなかった場合は、エラー コードを含む 422 応答が返されます。

## <a name="next-steps"></a>次の手順

このガイドでは、一致する$memberについて学習しました。 次に、Touchstone で Da Vinci Payer Data Exchange IG をテストする方法について学習できます。このテストでは、一致操作$member必要です。

>[!div class="nextstepaction"]
>[DaVinci PDex](../fhir/davinci-pdex-tutorial.md)