---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375336"
---
<a name="paths"></a>
## <a name="paths"></a>パス

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>要求された時系列データ ポイントの異常ポイントを検出します
```
POST /anomalydetection
```


#### <a name="parameters"></a>parameters

|type|Name|説明|スキーマ|
|---|---|---|---|
|**本文**|**body**  <br>*必須*|時系列データ ポイントと期間 (必要な場合)。|[request](#request)|


#### <a name="responses"></a>応答

|HTTP コード|説明|スキーマ|
|---|---|---|
|**200**|操作は成功しました。|< [応答](#response) > 配列|
|**400**|JSON 要求を解析できません。|コンテンツなし|
|**403**|指定された証明書はサーバーで受け付けられません。|コンテンツなし|
|**405**|メソッドは許可されていません。|コンテンツなし|
|**500**|内部サーバー エラー。|コンテンツなし|


#### <a name="consumes"></a>消費

* `application/json`


#### <a name="produces"></a>生成

* `application/json`


#### <a name="tags"></a>タグ

* anomalydetection



