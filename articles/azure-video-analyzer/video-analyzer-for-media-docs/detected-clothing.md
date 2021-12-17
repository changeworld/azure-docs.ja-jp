---
title: ビデオで検出された人物の衣服
description: このトピックでは、ビデオで人物の衣服を検出する機能について概説します。
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: juliako
ms.openlocfilehash: 4bc0c0fd21a68d80bf8c8803f1a64e6e0ea097c7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495278"
---
# <a name="peoples-detected-clothing-preview"></a>検出された人物の衣服 (プレビュー)

Video Analyzer for Media では、ビデオに映っている人物の衣服が検出され、さらに、検出された衣服の種類とそれが出現するタイムスタンプ (開始、終了) などの情報が得られます。 API からは、検出の信頼度レベルが返されます。
 
この機能が役に立つ可能性がある 2 つの例を次に示します。
 
* ビデオ広告、ニュース、スポーツの試合など、コンテンツ作成者が生データを作成する際の効率を向上させます (たとえば、ビデオ アーカイブの中から赤いシャツを着ている人物を見つけるなど)。
* 事後分析 - 人物の動きを検出して追跡し、事故や犯罪の事後 (たとえば、爆発、銀行強盗、事件) をより適切に分析します。
 
新たに追加された衣服検出機能は、ファイルにインデックスを付けるときに **[詳細オプション]**  ->  **[Advanced video]\(詳細ビデオ\)** または **[Advanced video + audio]\(詳細ビデオ + オーディオ\)** プリセット ([動画 + オーディオのインデックス作成] の下) を選択して使用できます。 標準のインデックス作成には、この新しい詳細モデルは含まれません。
 
:::image type="content" source="./media/detected-clothing/index-video.png" alt-text="このスクリーンショットは、ビデオのインデックス作成オプションを表します":::  

[Video Analyzer for Media](https://www.videoindexer.ai/) (旧称 Video Indexer) Web サイトでビデオの **[分析情報]** を表示すると、 **[観察された人]** というトレース分析情報から、人物の検出された衣服を確認できます。 人物のサムネイルを選択すると、検出された衣服の情報が提供されます。

:::image type="content" source="./media/detected-clothing/observed-people.png" alt-text="観察された人物のスクリーンショット":::  
 
Video Analyzer for Media Web サイトのビデオのタイムラインで、検出された人物の衣服を確認したい場合は、 **[表示]**  ->  **[分析情報の表示]** に移動し、 **[すべて]** オプションを選択するか、または **[表示]**  ->  **[カスタム ビュー]** に移動し、 **[観察された人]** を選択します。 

:::image type="content" source="./media/detected-clothing/observed-person.png" alt-text="観察された人物のスクリーンショット":::  
 
特定の衣服を検索すると、それを着ている観察対象の人物をすべて取得することができます。この機能を有効にするには、Video Analyzer for Media Web サイトにあるビデオの **[タイムライン]** から、または **[分析情報]** の検索バーを使用します。

次の JSON 応答は、検出された衣服が関連付けられている観察対象の人物をトレースしたときに、Video Analyzer for Media から返されるデータを示しています。

```json
"observedPeople": [
    {
        "id": 1,
        "thumbnailId": "68bab0f2-f084-4c2b-859b-a951ed03c209",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:05.5055",
                "adjustedEnd": "0:00:09.9766333",
                "start": "0:00:05.5055",
                "end": "0:00:09.9766333"
            }
        ]
    },
    {
        "id": 2,
        "thumbnailId": "449bf52d-06bf-43ab-9f6b-e438cde4f217",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:07.2072",
                "adjustedEnd": "0:00:10.5105",
                "start": "0:00:07.2072",
                "end": "0:00:10.5105"
            }
        ]
    },
]
```

## <a name="limitations-and-assumptions"></a>制限事項と前提

衣服検出の制限事項に注意して、偽陰性 (検出漏れ) の影響を回避または軽減することが重要です。
 
* 検出器の結果を最適化するには (動くカメラや混合シーンでも結果が得られますが)、静止カメラのビデオ映像を使用します。
* 人物は、小さく映っている場合は検出されません (人物の最小の高さは 200 ピクセル)。
* 最大フレーム サイズは HD です
* 人物は、立っていなかったり歩いていたりすると検出されません。
* 低品質のビデオ (たとえば、暗い照明条件) は検出結果に影響を与える可能性があります。
* 推奨フレーム レートは 30 FPS 以上です。
* 推奨されるビデオ入力は、1 つのフレームに含まれる人物が最大 10 人です。 この機能では、1 つのフレーム内にそれより多くの人物がいても処理できますが、検出結果としては、フレーム内の検出の信頼度が高い人物が最大 10 人取得されます。
* 服装が似ている人物 (たとえば、ユニフォームを着ている人物、スポーツの試合の選手) は、ID 番号が同じ同一人物として検出される可能性があります。
* オクルージョン – オクルージョン (シーンと自分、または他の人物によるオクルージョン) がある場合、エラーが発生する可能性があります。
* ポーズ: ポーズ (背面/前面) が異なることが理由で、トラックが分割される可能性があります

## <a name="next-steps"></a>次のステップ 

[ビデオ内の観察対象人物を追跡する](observed-people-tracing.md)
