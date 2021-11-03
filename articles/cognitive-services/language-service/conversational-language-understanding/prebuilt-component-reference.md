---
title: サポートされている事前構築済みエンティティ コンポーネント
titleSuffix: Azure Cognitive Services
description: Conversational Language Understanding で自動的に検出できるエンティティについて説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: fc7188b647e6e299390179c68bcf9c9742a018f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092206"
---
# <a name="supported-prebuilt-entity-components"></a>サポートされている事前構築済みエンティティ コンポーネント

Conversational Language Understanding を使用すると、事前構築済みのコンポーネントをエンティティに追加できます。 事前構築済みのコンポーネントでは、発話から共通の型を自動的に予測できます。 詳細については、[エンティティのコンポーネント](concepts/entity-components.md)に関する記事を参照してください。

## <a name="reference"></a>関連項目

Conversational Language Understanding では、次の事前構築済みコンポーネントを使用できます。

| Type | 説明 | サポートされている言語 |
| --- | --- | --- |
| Quantity.Age | 人物または物の年齢。 例: "30 歳"、"9 か月" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| Quantity.Number | 数値またはテキスト形式の基数。 例: "三十"、"23"、"14.5"、"2 個半" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| Quantity.Percentage | 記号 % または単語 "パーセント" を使用した割合。 例: "10%"、"5.6 パーセント" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| Quantity.Ordinal | 数値またはテキスト形式の序数。 例: "最初"、"2 番目"、"最後"、"10 番目" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| Quantity.Dimension | 長さ、距離、量、面積、速度などの特殊なディメンション。 例: "2 マイル"、"650 平方キロメートル"、"35 km/h" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| Quantity.Temperature | 摂氏または華氏の温度。 例: "32F"、"摂氏 34 度"、"2 ℃" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| Quantity.Currency | 通貨を含む金額。 例: "1000.00 米ドル"、"£20.00"、"$67.5B" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| Datetime | 日付と時刻。 例: "1976 年 6 月 23 日"、"午前 7 時"、"午後 6:49"、"明日の午後 7 時"、"来週" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| Email | メール アドレス。 For example: "user@contoso.com"、"user_name@contoso.com"、"user.name@contoso.com" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| 電話番号 | 米国の電話番号。 例: "123-456-7890"、"+1 123 456 7890"、"(123)456-7890" | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |
| URL | Web サイトの URL とリンク。 | 英語、中国語、フランス語、イタリア語、ドイツ語、ポルトガル語 (ブラジル)、スペイン語 |

## <a name="prebuilt-components-in-multilingual-projects"></a>多言語プロジェクトの事前構築済みコンポーネント

多言語会話プロジェクトでは、任意の事前構築済みコンポーネントを有効にできます。 コンポーネントは、事前構築済みコンポーネントでクエリの言語がサポートされている場合にのみ予測されます。 言語は要求で指定されるか、指定されていない場合は既定でアプリケーションのプライマリ言語に設定されます。

## <a name="next-steps"></a>次のステップ

[エンティティ コンポーネント](concepts/entity-components.md) 
