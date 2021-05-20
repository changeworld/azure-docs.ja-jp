---
title: 正規表現エンティティ型 - LUIS
description: 生の発話テキストには正規表現をお勧めします。 大文字小文字とカルチャのバリアントは無視されます。  正規表現の照合は、スペルチェックによる変更後に、トークン レベルではなく文字レベルで適用されます。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: e53d21cc4e28f6c388b4e4f980a3352021fc2bcb
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803406"
---
# <a name="regular-expression-entity"></a>正規表現エンティティ

正規表現エンティティは、指定された正規表現パターンに基づいてエンティティを抽出します。

生の発話テキストには正規表現をお勧めします。 大文字小文字とカルチャのバリアントは無視されます。  正規表現の照合は、トークン レベルでのスペルチェックによる変更後に適用されます。 角かっこが多用されているなど、正規表現が複雑すぎる場合は、このモデルに式を追加できません。 [.NET Regex](/dotnet/standard/base-types/regular-expressions) ライブラリの全体ではなく一部が使用されます。

**エンティティは、次のような場合に最適です。**

* データが一貫して書式設定され、バリエーションにも一貫性がある。
* 正規表現では、2 レベルを超える入れ子は必要ありません。

![正規表現エンティティ](./media/luis-concept-entities/regex-entity.png)

## <a name="example-json"></a>JSON の例

正規表現エンティティの定義として `kb[0-9]{6}` を使用したときの、次のクエリに対して返される正規表現エンティティを含む発話の例は、以下の JSON 応答のようになります。

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 予測エンドポイントの応答](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 予測エンドポイントの応答](#tab/V3)


これは、クエリ文字列で `verbose=false` が設定されている場合の JSON です。

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

これは、クエリ文字列で `verbose=true` が設定されている場合の JSON です。

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>次のステップ

エンティティの詳細を確認します。

* [概念](luis-concept-entity-types.md)
* [作成方法](luis-how-to-add-entities.md)
