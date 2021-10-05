---
title: オーディオ効果検出
description: オーディオ効果検出は、Azure Video Analyzer for Media AI 機能の 1 つです。 さまざまな音響イベントを検出し、それらをさまざまな音響カテゴリ (銃声、叫び声、観客の反応など) に分類できます。
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: juliako
ms.openlocfilehash: 689abf9c61c0b1a462cfafe02c461906626e516b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632540"
---
#  <a name="audio-effects-detection-preview"></a>オーディオ効果検出 (プレビュー)

**オーディオ効果検出** は、Azure Video Analyzer for Media AI 機能の 1 つです。 さまざまな音響イベントを検出し、それらをさまざまな音響カテゴリ (銃声、叫び声、観客の反応など) に分類できます。
 
オーディオ イベント検出は多くの領域で使用できます。 次に 2 つの例を示します。

* オーディオ効果検出の使用は、**公安と司法** の領域です。 オーディオ効果検出では銃声、爆発音およびガラスの破砕音を検出して分類できます。 そのため、スマートシティ システムや、カメラやマイクを含む他のパブリック環境で実装できます。 暴力事件を迅速かつ正確に検出できます。 
* **メディアと娯楽** 領域では、大量のビデオ アーカイブを持つ企業は、音声以外の効果を使用してビデオの文字起こしを強化し、聞き取りにくいユーザーに対してより多くのコンテキストを提供することで、アクセシビリティ シナリオを簡単に改善できます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/audio-effects.jpg" alt-text="オーディオ効果の画像":::
<br/>*Video Analyzer for Media オーディオ効果検出の出力例*

## <a name="supported-audio-categories"></a>サポートされているオーディオ カテゴリ  

**オーディオ効果検出** では、8 つの異なるカテゴリを検出して分類できます。 次の表で、異なるカテゴリが異なる VI プリセットに分けられ、**標準** と **高度** に分けられていることがわかります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/media-services/)に関するページを参照してください。

|インデックス作成の種類 |標準的なインデックス作成| 高度なインデックス作成|
|---|---|---|
|**プリセット名** |**"オーディオのみ"** <br/>**"ビデオとオーディオ"** |**"高度なオーディオ"**<br/> **"高度なビデオとオーディオ"**|
|**分析情報ペインに表示**|| V|
|観客の反応 |V| V|
| 無音| V| V|
| 銃声 ||V |
| ガラスの破砕音 ||V|
| アラーム音|| V |
| サイレン音|| V |
| 笑い声|| V |
| 犬の鳴き声|| V|

## <a name="result-formats"></a>結果の形式

オーディオ効果は分析情報の JSON で取得されます。これには、カテゴリ ID、種類、名前、およびカテゴリごとのインスタンスのセットと、特定の期間と信頼スコアが含まれます。

`name` パラメーターは JSON のインデックスが作成された言語で表示されますが、種類は常に同じままです。

```json
audioEffects: [{
        id: 0,
        type: "Gunshot",
        name: "Gunshot",
        instances: [{
                confidence: 0.649,
                adjustedStart: "0:00:13.9",
                adjustedEnd: "0:00:14.7",
                start: "0:00:13.9",
                end: "0:00:14.7"
            }, {
                confidence: 0.7706,
                adjustedStart: "0:01:54.3",
                adjustedEnd: "0:01:55",
                start: "0:01:54.3",
                end: "0:01:55"
            }
        ]
    }, {
        id: 1,
        type: "CrowdReactions",
        name: "Crowd Reactions",
        instances: [{
                confidence: 0.6816,
                adjustedStart: "0:00:47.9",
                adjustedEnd: "0:00:52.5",
                start: "0:00:47.9",
                end: "0:00:52.5"
            },
            {
                confidence: 0.7314,
                adjustedStart: "0:04:57.67",
                adjustedEnd: "0:05:01.57",
                start: "0:04:57.67",
                end: "0:05:01.57"
            }
        ]
    }
],
```

## <a name="how-to-index-audio-effects"></a>オーディオ効果のインデックスを作成する方法

オーディオ効果の検出を含めるようにインデックス プロセスを設定するには、以下に示すように、ユーザーは [動画 + オーディオのインデックス作成] メニューの高度なプリセットのいずれかを選択する必要があります。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/index-audio-effect.png" alt-text="オーディオ効果のインデックスを作成する画像":::

## <a name="closed-caption"></a>クローズド キャプション

オーディオ効果は、クローズド キャプション ファイルで取得される際に、角かっこで囲まれて取得されます。その構造は次のとおりです。

|Type| 例|
|---|---|
|SRT |00:00:00,000  00:00:03,671<br/>[銃声]|
|VTT |00:00:00.000  00:00:03.671<br/>[銃声]|
|TTML|信頼度: 0.9047 <br/> `<p begin="00:00:00.000" end="00:00:03.671">[Gunshot]</p>`|
|TXT |[銃声]|
|CSV |0.9047,00:00:00.000,00:00:03.671, [銃声]|

クローズド キャプション ファイルのオーディオ効果は、次のロジックを使用して取得されます。

* `Silence` イベントの種類はクローズド キャプションに追加されません
* イベントを表示する最大期間 I 5 秒
* イベントを表示する最小タイマー期間は 700 ミリ秒です

## <a name="adding-audio-effects-in-closed-caption-files"></a>クローズド キャプション ファイルへのオーディオ効果の追加

オーディオ効果は、Azure Video Analyzer でサポートされているクローズド キャプション ファイルに追加することができます。その場合、`includeAudioEffects` パラメーターで true を選んで [ビデオ キャプションの取得 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Captions) を使用するか、 **[ダウンロード]**  ->  **[クローズド キャプション]**  ->  **[Include Audio Effects]\(オーディオ効果を含める\)** の順に選択して video.ai portal エクスペリエンスを使用します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/close-caption.jpg" alt-text="CC でのオーディオ効果":::

> [!NOTE]
> クローズド キャプション ファイルからの[トランスクリプトの更新](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)、またはクローズド キャプション ファイルからの[カスタム言語モデルの更新](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model)を使用する場合、それらのファイルに含まれるオーディオ効果は無視されます。

## <a name="limitations-and-assumptions"></a>制限事項と前提

* モデルは音声以外のセグメントでのみ動作します。
* モデルは現在、一度に 1 つのカテゴリに対して動作しています。 たとえば、現時点では、バックグラウンドの泣き声や音声、あるいは銃声と爆発音はサポートされていません。
* バックグラウンドで大音量の音楽が流れている場合、このモデルは現在サポートされていません。
* 最小セグメント長 – 2 秒。

## <a name="next-steps"></a>次のステップ

[概要](video-indexer-overview.md)を確認する
