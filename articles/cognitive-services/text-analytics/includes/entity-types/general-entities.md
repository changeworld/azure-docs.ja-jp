---
title: 名前付き一般エンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: 693a81cfb15407541311d7ab053bb2ab6a267b29
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799969"
---
## <a name="general-entity-types"></a>一般エンティティの種類:

### <a name="person"></a>Person
テキストで認識された名前と他の人。
言語:
* パブリック プレビュー: `English`

| サブタイプ名 | 説明             |
|--------------|-------------------------|
| 該当なし          | 認識された名前 (例、`Bill Gates`、`Marie Curie`) |

### <a name="location"></a>Location

自然および人口のランドマーク、構造、および地理的機能。

言語:


* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| 該当なし          | 場所 (例、`Atlantic Ocean`、`library`、`Eiffel Tower`、`Statue of Liberty`) |

### <a name="organization"></a>Organization  

認識された組織、企業、機関、およびその他の集団。 たとえば、企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関などです。 国籍と宗教は、このエンティティ型には含まれていません。 言語: 

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| 該当なし          | 組織 (例、`Microsoft`、`NASA`、`National Oceanic and Atmospheric Administration`) |

### <a name="phone-number"></a>電話番号

電話番号です。 

言語:


* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                  |
|----------|----------------------------------------------|
| 該当なし         | 電話番号 (例、`+1 123-123-123`)。 |

### <a name="email"></a>Email

電子メール アドレス。 

言語:


* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                  |
|----------|----------------------------------------------|
| 該当なし         | メール アドレス (例: `support@contoso.com`) |

### <a name="url"></a>URL

インターネット URL です。

言語:


* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                           |
|----------|-------------------------------------------------------|
| 該当なし         | Web サイトへの URL (例、`https://www.bing.com`)。 |

###  <a name="number"></a>Number

数値と数量です。 

言語:


* パブリック プレビュー: `English`

| サブタイプ名    | 例                     |
|-------------|------------------------------|
| 該当なし         | `6`、`six`                   |
| 割合  | `50%`、`fifty percent`       |
| Ordinal     | `2nd`、`second`              |
| Currency    | `$10.99`、`€30.00`           |
| Dimension   | `10 miles`、`40 cm`          |
| 気温 | `32 degrees`、`10°C`         |
