---
title: Conversational Language Understanding の評価メトリック
titleSuffix: Azure Cognitive Services
description: Conversational Language Understanding の評価メトリックについて説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: b81273e3955ab866648a1d70f01991272ecba787
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091143"
---
# <a name="evaluation-metrics-for-conversational-language-understanding-models"></a>Conversational Language Understanding モデルの評価メトリック

会話言語理解のモデル評価には、次の評価メトリックが使用されます。


|指標 |説明  |計算  |
|---------|---------|---------|
|Precision     |  すべての試された認識に対して成功した認識の比率。 これは、モデルのエンティティ認識が本当に適切な認識である回数を示します。       | `Precision = #True_Positive / (#True_Positive + #False_Positive)`        |
|Recall     | 実際に存在するエンティティの数に対して成功した認識の比率。        | `Recall = #True_Positive / (#True_Positive + #False_Negatives)`        |
|F1 スコア    |  精度とリコールの組み合わせ。       |  `F1 Score = 2 * Precision * Recall / (Precision + Recall)`       |

## <a name="model-level-and-entity-level-evaluation-metrics"></a>モデル レベルとエンティティ レベルの評価メトリック

精度、リコール、F1 スコアは、エンティティについては個別に計算され (エンティティ レベルの評価)、モデルについてはまとめて計算されます (モデル レベルの評価)。

精度、リコール、評価の定義は、エンティティ レベルとモデル レベルのどちらの評価でも同じです。 ただし、"*真陽性*"、"*擬陽性*"、"*擬陰性*" の数は異なる場合があります。 たとえば、次のようなテキストがあるとします。

### <a name="example"></a>例

*The first party of this contract is John Smith, resident of 5678 Main Rd., City of Frederick, state of Nebraska. And the second party is Forrest Ray, resident of 123-345 Integer Rd., City of Corona, state of New Mexico. There is also Fannie Thomas resident of 7890 River Road, city of Colorado Springs, State of Colorado.*

このテキストからエンティティを抽出するモデルにより、次のように予測されることがあります。

| Entity | 予測結果 | 実際の種類 |
|--|--|--|
| John Smith | Person | Person |
| Frederick | Person | City |
| Forrest | City | Person |
| Fannie Thomas | Person | Person |
| Colorado Springs | City | City |

### <a name="entity-level-evaluation-for-the-person-entity"></a>"*人*" エンティティのエンティティ レベルの評価 

このモデルでは、"*人*" エンティティについて次のエンティティ レベルの評価が行われます。

| Key | Count | 説明 |
|--|--|--|
| 真陽性 | 2 | *John Smith* と *Fannie Thomas* は、"*人*" として正しく予測されました。 |
| 偽陽性 | 1 | *Frederick* は、"*都市*" でなければならないのに、誤って "*人*" として予測されました。 |
| 偽陰性 | 1 | *Forrest* は、"*人*" でなければならないのに、誤って "*都市*" として予測されました。 |

* **精度**: `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **リコール**: `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **F1 スコア**: `2 * Precision * Recall / (Precision + Recall)` = `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="entity-level-evaluation-for-the-city-entity"></a>"*都市*" エンティティのエンティティ レベルの評価

このモデルでは、"*都市*" エンティティについて次のエンティティ レベルの評価が行われます。

| Key | Count | 説明 |
|--|--|--|
| 真陽性 | 1 | *Colorado Springs* は、"*都市*" として正しく予測されました。 |
| 偽陽性 | 1 | *Forrest* は、"*人*" でなければならないのに、誤って "*都市*" として予測されました。 |
| 偽陰性 | 1 | *Frederick* は、"*都市*" でなければならないのに、誤って "*人*" として予測されました。 |

* **精度** = `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **リコール** = `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **F1 スコア** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="model-level-evaluation-for-the-collective-model"></a>集合モデルのモデル レベルの評価

このモデルでは、モデル全体が次のように評価されます。

| Key | Count | 説明 |
|--|--|--|
| 真陽性 | 3 | *John Smith* と *Fannie Thomas* は、"*人*" として正しく予測されました。 *Colorado Springs* は、"*都市*" として正しく予測されました。 これは、すべてのエンティティの真陽性の合計です。 |
| 偽陽性 | 2 | *Forrest* は、"*人*" でなければならないのに、誤って "*都市*" として予測されました。 *Frederick* は、"*都市*" でなければならないのに、誤って "*人*" として予測されました。 これは、すべてのエンティティの擬陽性の合計です。 |
| 偽陰性 | 2 | *Forrest* は、"*人*" でなければならないのに、誤って "*都市*" として予測されました。 *Frederick* は、"*都市*" でなければならないのに、誤って "*人*" として予測されました。 これは、すべてのエンティティの擬陰性の合計です。 |

* **精度** = `#True_Positive / (#True_Positive + #False_Positive)` = `3 / (3 + 2) = 0.6`
* **リコール** = `#True_Positive / (#True_Positive + #False_Negatives)` = `3 / (3 + 2) = 0.6`
* **F1 スコア** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.6 * 0.6) / (0.6 + 0.6) = 0.6`

## <a name="interpreting-entity-level-evaluation-metrics"></a>エンティティ レベルの評価メトリックの解釈

それでは、特定のエンティティに対する精度またはリコールが高いとは、実際にはどのような意味なのでしょうか。

| Recall | Precision | 解釈 |
|--|--|--|
| 高 | 高 | このエンティティは、モデルによって適切に処理されます。 |
| 低 | 高 | このエンティティは、モデルで抽出されないことがありますが、抽出は高い信頼度で行われます。 |
| 高 | 低 | このエンティティは、モデルで確実に抽出されますが、別の種類として抽出されることがあるため、信頼度は低くなります。 |
| 低 | 低 | このエンティティの種類は、通常は抽出されないため、モデルで適切に処理されません。 抽出されたとしても、信頼度は高くありません。 |

## <a name="confusion-matrix"></a>混同行列

混同行列は、モデルのパフォーマンスの評価に使われる N x N 行列であり、N はエンティティの数です。
マトリックスを使用して、実際のタグとモデルによって予測されたタグを比較します。
これにより、モデルのパフォーマンスと、発生しているエラーの種類を、全体的に把握できます。

混同行列を使用すると、互いに近すぎて、誤りが発生しやすい (あいまいな) エンティティを識別できます。 この場合は、これらのエンティティの種類をまとめて結合することを検討します。 それができない場合は、モデルで両者を区別できるよう、両方のエンティティのタグ付けされた例を追加することを検討します。

次の図で強調されている対角線は、正しく予測されたエンティティであり、予測されたタグは実際のタグと同じです。

:::image type="content" source="../media/confusion-matrix-example.png" alt-text="混同行列の例のスクリーンショット" lightbox="../media/confusion-matrix-example.png":::

混同行列からエンティティ レベルとモデル レベルの評価メトリックを計算できます。

* 斜線の付いている値は、各エンティティの "*真陽性*" の値です。
* エンティティの行の値の合計は (斜線を除く)、モデルの "*擬陽性*" です。
* エンティティの列の値の合計は (斜線を除く)、モデルの "*擬陰性*" です。

同様に、

* モデルの "*真陽性*" は、すべてのエンティティの "*真陽性*" の合計です。
* モデルの "*擬陽性*" は、すべてのエンティティの "*擬陽性*" の合計です。
* モデルの "*擬陰性*" は、すべてのエンティティの "*擬陰性*" の合計です。

## <a name="next-steps"></a>次のステップ

[Language Studio でモデルをトレーニングする](../how-to/train-model.md)
