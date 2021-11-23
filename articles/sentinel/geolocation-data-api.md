---
title: Microsoft Azure Sentinel において REST API を使用して位置情報データでエンティティをエンリッチする | Microsoft Docs
description: この記事では、Microsoft Azure Sentinel において REST API を使用して位置情報データでエンティティをエンリッチする方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: cce16ca2f0db3d4f44c4077e4760f599e565bae6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520600"
---
# <a name="enrich-entities-in-microsoft-sentinel-with-geolocation-data-via-rest-api-public-preview"></a>Microsoft Azure Sentinel において REST API を使用して位置情報データでエンティティをエンリッチする (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Azure Sentinel において REST API を使用して位置情報データでエンティティをエンリッチする方法について示します。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="common-uri-parameters"></a>共通 URI パラメーター

次に示すのは、位置情報 API に共通の URI パラメーターです。




| 名前 | / | 必須 | 型 | [説明] |
|-|-|-|-|-|
| **{subscriptionId}** | path | yes | GUID | Azure サブスクリプション ID |
| **{resourceGroupName}** | path | yes | string | サブスクリプション内のリソース グループの名前 |
| **{api-version}** | query | yes | string | この要求を行うために使用されるプロトコルのバージョン。 2021 年 4 月 30 日の時点で、位置情報 API のバージョンは *2019-01-01-preview* です。|
| **{ipAddress}** | query | yes | string | 位置情報を必要とする IP アドレス (IPv4 または IPv6 形式)。   |
|

## <a name="enrich-ip-address-with-geolocation-information"></a>位置情報で IP アドレスをエンリッチする

このコマンドを使用すると、指定した IP アドレスの位置情報データが取得されます。

### <a name="request-uri"></a>要求 URI

| Method | 要求 URI |
|-|-|
| **GET** | `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.SecurityInsights/enrichment/ip/geodata/?ipaddress={ipAddress}&api-version={api-version}` |
|

### <a name="responses"></a>応答

|status code  |説明  |
|---------|---------|
|**200**     |   成功      |
|**400**     |      IP アドレスが指定されていないか、形式が無効です    |
|**404**     | この IP アドレスの位置情報データが見つかりません         |
|**429**     |      要求が多すぎます。指定された期間内に再試行してください    |
|     |         |

### <a name="fields-returned-in-the-response"></a>応答で返されるフィールド

|フィールド名  |説明  |
|---------|---------|
|**ASN**     |  この IP アドレスに関連付けられている自律システム番号       |
|**carrier**     |  この IP アドレスの通信事業者の名前       |
|**city**     |   この IP アドレスが存在する市区町村      |
|**cityCf**     | "city" フィールドの値が正しい確信度の値 (0 - 100 のスケール)        |
|**continent**     | この IP アドレスが存在する大陸        |
|**country**     |この IP アドレスが存在する国        |
|**countryCf**     |   "country" フィールドの値が正しい確信度の値 (0 - 100 のスケール)      |
|**ipAddr**     |   IP アドレスのドット形式 10 進またはコロン区切りの文字列表現      |
|**ipRoutingType**     |   この IP アドレスの接続の種類の説明      |
|**latitude**     |     この IP アドレスの緯度    |
|**longitude**     |  この IP アドレスの経度       |
|**organization**     |  この IP アドレスの組織名       |
|**organizationType**     | この IP アドレスの組織の種類        |
|**リージョン**     |    この IP アドレスが存在する地理的リージョン     |
|**state**     |  この IP アドレスが存在する州や都道府県       |
|**stateCf**     | "state" フィールドの値が正しい確信度の値 (0 - 100 のスケール)        |
|**stateCode**     |   この IP アドレスが存在する州や都道府県の省略名      |
|     |         |


## <a name="throttling-limits-for-the-api"></a>API のスロットリング制限

この API には、1 ユーザーにつき 1 時間あたり 100 呼び出しの制限があります。

### <a name="sample-response"></a>応答のサンプル

```rest
"body":
{
    "asn": "12345",
    "carrier": "Microsoft",
    "city": "Redmond",
    "cityCf": 90,
    "continent": "north america",
    "country": "united states",
    "countryCf": 99
    "ipAddr": "1.2.3.4",
    "ipRoutingType": "fixed",
    "latitude": "40.2436",
    "longitude": "-100.8891",
    "organization": "Microsoft",
    "organizationType": "tech",
    "region": "western usa",
    "state": "washington",
    "stateCf": null
    "stateCode": "wa"
}
```

## <a name="next-steps"></a>次のステップ

Microsoft Azure Sentinel の詳細については、次の記事を参照してください。

- エンティティの詳細を確認します。

    - [Microsoft Azure Sentinel エンティティ型リファレンス](entities-reference.md)
    - [Microsoft Azure Sentinel でエンティティを使用してデータを分類および分析する](entities-in-azure-sentinel.md)
    - [データ フィールドを Microsoft Azure Sentinel のエンティティにマップする](map-data-fields-to-entities.md)

- [Microsoft Azure Sentinel API](/rest/api/securityinsights/) の他の使用方法を確認する
