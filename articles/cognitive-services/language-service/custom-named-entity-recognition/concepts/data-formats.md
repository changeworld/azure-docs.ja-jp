---
title: カスタムの NER データ形式
titleSuffix: Azure Cognitive Services
description: カスタムの NER によって受け入れられるデータ形式について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: de37751b3d814110b5d37f1b7c84d41278b2bea9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092659"
---
# <a name="data-formats-accepted-by-custom-ner"></a>カスタムの NER によって受け入れられるデータ形式

モデルでデータを学習のために使う場合、データが特定の形式であると想定されます。 Language Studio でデータにタグを付けると、この記事で説明されている JSON 形式に変換されます。 ファイルに手動でタグを付けることもできます。


## <a name="json-file-format"></a>JSON ファイル形式

エンティティにタグを付けると、タグは次の JSON 形式で保存されます。 タグ ファイルをアップロードする場合は、同じ形式に従う必要があります。

```json
{
    //List of entity names. Their index within this array is used as an ID. 
    "entityNames": [
        "entity_name1",
        "entity_name2"
    ],
    "documents": "path_to_document", //Relative file path to get the text.
    "culture": "en-US", //Standard culture strings supported by CultureInfo.
    "entities": [
        {
            "regionStart": 0,
            "regionLength": 69,
            "labels": [
                {
                    "entity": 0, // Index of the entity in the "entityNames" array. Positions are relative to the original text (not bounding box)
                    "start": 4,
                    "length": 10
                },
                {
                    "entity": 1,
                    "start": 18,
                    "length": 11
                }
            ]
        }
    ]    
}
```

次の一覧では、上記のサンプルのさまざまな JSON プロパティについて説明します。

* `entityNames`: エンティティ名の配列。 配列内のエンティティのインデックスは、その ID として使用されます。
* `documents`: タグ付きドキュメントの配列。
  * `location`: JSON ファイルを基準にしたドキュメントのパス。 たとえば、タグファイルと同じレベルにあるドキュメントは `file.txt` で、1 つのディレクトリ レベル内のドキュメントは `dir1/file.txt` です。
  * `culture`: ドキュメントのカルチャおよび言語。 <!-- See [language support](../language-support.md) for more information. -->
  * `entities`: エンティティ認識タグを指定します。
    * `regionStart`: テキストの先頭の包括的な文字位置。
    * `regionLength`: UTF16 文字を基準とした場合の境界ボックスの長さ。 トレーニングではこの領域のデータのみが考慮されるため、タグが付けられたファイルの場合は `regionStart` を 0 に設定し、`regionLength` をファイル内の最後の文字の最後のインデックスに設定します。 この領域は、タグのないファイルの部分として領域を定義することで、トレーニングに負のサンプルを導入する場合にも設定できます。

    * `labels`: 境界ボックス内で発生したすべてのタグ。
      * `entity`: `entityNames` 配列内のエンティティのインデックス。
      * `start`: ドキュメント テキスト内のタグの先頭の包括的な文字位置。 これは、境界ボックスを基準としたものではありません。
      * `length`: UTF16 文字を基準とした場合のタグの長さ。

## <a name="next-steps"></a>次のステップ

データのタグ付けに関する詳細については、[操作方法に関する記事](../how-to/tag-data.md)を参照してください。 データのタグ付けが完了したら、[モデルをトレーニング](../how-to/train-model.md)できます。  
