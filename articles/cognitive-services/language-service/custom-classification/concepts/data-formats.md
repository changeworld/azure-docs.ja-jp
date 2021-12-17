---
title: カスタム テキスト分類データ形式
titleSuffix: Azure Cognitive Services
description: カスタムのエンティティ抽出によって受け入れられるデータ形式について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: f99dd2b0d540cad6fad605e264df4b6054240e35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092378"
---
# <a name="accepted-data-formats"></a>許容されるデータ形式

モデルでデータを学習のために使用する場合、データが特定の形式であることが想定されます。 Language Studio でデータにタグを付けると、この記事で説明されている JSON 形式に変換されます。 ファイルに手動でタグを付けることもできます。


## <a name="json-file-format"></a>JSON ファイル形式

タグ ファイルは下記の `json` 形式である必要があります。

```json
{
    "classifiers": [
        {
            "name": "Class1"
        },
        {
            "name": "Class2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "classifiers": [
                {
                    "classifierName": "Class2"
                },
                {
                    "classifierName": "Class1"
                }
            ]
        }
    ]
}
```

### <a name="data-description"></a>データの説明

* `classifiers`: データの分類子の配列。 各分類子は、データのタグ付けに用いるクラスの 1 つを表します。
* `documents`: タグ付きドキュメントの配列。 たとえば、次のように入力します。
  * `location`: タグを含む JSON ファイルのパス。 タグ ファイルは、ストレージ コンテナーのルートにある必要があります。
  * `language`: ドキュメントの言語。 [サポートされているカルチャ ロケール](../language-support.md)を使用してください。
  * `classifiers`: ドキュメントに割り当てられた分類子オブジェクトの配列。 1 つの分類プロジェクトで作業している場合、分類子は 1 つのみである必要があります。

## <a name="next-steps"></a>次のステップ

データのタグ付けに関する詳細については、[操作方法に関する記事](../how-to/tag-data.md)を参照してください。 データのタグ付けが完了したら、[モデルをトレーニング](../how-to/train-model.md)できます。  
