---
title: Microsoft Cognitive Services の Computer Vision API | Microsoft Docs
description: Computer Vision API の高度なアルゴリズムを使用すれば、Microsoft Cognitive Services でイメージを処理して情報を返すのに役立ちます。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 86e0441c600162e479c678d3cb1dbeaad423ddb5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377221"
---
# <a name="what-is-computer-vision-api-version-10"></a>Computer Vision API バージョン 1.0 とは

> [!IMPORTANT]
> 新しいバージョンの Computer Vision API が利用できるようになりました。詳細については、以下を参照してください。
>- [概要](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Computer Vision API バージョン 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

クラウドベースの Computer Vision API を使用すると、開発者はイメージを処理し、情報を返すための高度なアルゴリズムにアクセスできます。 イメージをアップロードするか、イメージ URL を指定することで、Microsoft Computer Vision のアルゴリズムでは、入力とユーザーの選択に基づいてさまざまな方法でビジュアル コンテンツを分析できます。 Computer Vision API では、ユーザーはイメージを分析し、以下のことを行うことができます。
* [コンテンツに基づいて、イメージにタグ付けする。](#Tagging)
* [イメージを分類する。](#Categorizing)
* [イメージの種類と品質を識別する。](#Identifying)
* [人間の顔を検出し、その座標を返す。](#Faces)
* [ドメイン固有のコンテンツを認識する。](#Domain-Specific)
* [コンテンツの説明を生成する。](#Descriptions)
* [光学式文字認識を使用して、イメージで検出された、印刷テキストを識別する。](#OCR)
* [手書きのテキストを認識する。](#RecognizeText)
* [配色を区別する。](#Color)
* [成人向けコンテンツにフラグを設定する。](#Adult)
* [サムネイルとして使用する写真をトリミングする。](#Thumbnails)

## <a name="requirements"></a>必要条件
* サポートされている入力方法: アプリケーション/オクテット ストリームまたはイメージ URL の形式の RAW イメージ バイナリ。
* サポートされているイメージ形式: JPEG、PNG、GIF、BMP。
* イメージ ファイル サイズ: 4 MB 未満。
* イメージの寸法: 50 x 50 ピクセルより大きい。

## <a name="tagging-images"></a>イメージへのタグ付け
Computer Vision API では、2000 個を超える認識可能なオブジェクト、生物、風景、動作に基づいて、タグが返されます。 タグが不明瞭な場合や、常識的でない場合は、API 応答により、既知の設定のコンテキストでタグの意味を明確にするための "ヒント" が示されます。 タグは分類として整理されず、継承階層は存在しません。 一連のコンテンツ タグでは、完全な文章で書式設定された人間が判読できる言語として表示されるイメージの "説明" の基礎が形成されます。 現時点では、イメージの説明でサポートされている言語は英語のみであることに注意してください。

イメージのアップロード後、またはイメージ URL の指定後に、Computer Vision API のアルゴリズムにより、イメージで識別されたオブジェクト、生物、動作に基づいて、タグが出力されます。 タグ付けの対象は、前景の人物などの主題に限らず、背景 (屋内または屋外)、家具、道具、植物、動物、アクセサリ、ガジェットなども含まれます。

### <a name="example"></a>例
![House_Yard](./Images/house_yard.jpg) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>イメージの分類
タグ付けや説明に加え、Computer Vision API では、前のバージョンで定義した分類ベースのカテゴリが返されます。 これらのカテゴリは親/子の遺伝階層を持つ分類として整理されます。 すべてのカテゴリは英語です。 これらを単独で、または新しいモデルと共に使用することができます。

### <a name="the-86-category-concept"></a>86 のカテゴリ概念
以下の図に示されている 86 の概念のリストに基づき、イメージで検出された視覚特性を大きく分け、その中でさらに細かく分けることができます。 テキスト形式の完全な分類については、[カテゴリの分類](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)に関するページを参照してください。

![カテゴリの分析](./Images/analyze_categories.jpg)

イメージ                                                  | Response
------------------------------------------------------ | ----------------
![屋上にいる女性](./Images/woman_roof.jpg)                 | people
![家族写真](./Images/family_photo.jpg)             | people_crowd
![かわいい犬](./Images/cute_dog.jpg)                     | animal_dog
![屋外の山](./Images/mountain_vista.jpg)       | outdoor_mountain
![Vision Analyze パン](./Images/bread.jpg)       | food_bread

## <a name="identifying-image-types"></a>イメージの種類の識別
イメージを分類するにはいくつかの方法があります。 Computer Vision API では、ブール型のフラグを設定して、イメージが白黒であるかカラーであるかを示すことができます。 また、イメージが線画であるかどうかを示すフラグを設定することもできます。 イメージがクリップ アートであるかどうかや、0 から 3 のスケールなどでその品質を示すこともできます。

### <a name="clip-art-type"></a>クリップアート タイプ
イメージがクリップ アートであるかどうかを検出します。  

値 | 意味
----- | --------------
0     | クリップ アートではない
1     | あいまい
2     | 通常のクリップ アート
3     | 良好なクリップアート

イメージ|Response
----|----
![Vision Analyze チーズのクリップ アート](./Images/cheese_clipart.jpg)|3 good-clip-art
![Vision Analyze 家の庭](./Images/house_yard.jpg)|0 Non-clip-art

### <a name="line-drawing-type"></a>線画タイプ
イメージが線画であるかどうかを検出します。

イメージ|Response
----|----
![Vision Analyze ライオンの線画](./Images/lion_drawing.jpg)|True
![Vision Analyze 花](./Images/flower.jpg)|False

### <a name="faces"></a>顔
写真内の人間の顔を検出し、顔の座標と、顔、性別、年齢の四角形を生成します。 これらの視覚特性は、顔に対して生成されるメタデータのサブセットです。 顔に対して生成されるより広範なメタデータの場合 (顔識別や姿勢検出など)、Face API を使用します。  

イメージ|Response
----|----
![Vision Analyze 屋上にいる女性の顔](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![Vision Analyze 母親と娘の顔](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![Vision Analyze 家族写真の顔](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>ドメイン固有のコンテンツ

タグ付けやトップレベルの分類に加え、Computer Vision API では特殊な (つまり、ドメイン固有の) 情報もサポートされます。 特殊な情報は、スタンドアロン方式として、または高度な分類を使用して実装することができます。 これは、ドメイン固有のモデルを追加することにより、86 のカテゴリ分類をさらに絞り込む手段として機能します。

現時点では、サポートされている唯一の特殊な情報は、有名人認識とランドマーク認識です。 これらはドメイン固有のものであり、人物と人物グループのカテゴリ、世界中のランドマークを絞り込んだものです。

ドメイン固有のモデルを使用する場合、次の 2 つのオプションがあります。

### <a name="option-one---scoped-analysis"></a>オプション 1 - 範囲指定された分析
HTTP POST 呼び出しを行うことで、選択したモデルのみを分析します。 このオプションでは、使用するモデルがわかっている場合、モデルの名前を指定し、そのモデルに関連する情報のみを取得します。 たとえば、このオプションを使用して、有名人識別のみを検索することができます。 応答には、信頼度スコアと共に、潜在的に一致する有名人のリストが含まれます。

### <a name="option-two---enhanced-analysis"></a>オプション 2 - 高度な分析
86 のカテゴリ分類からのカテゴリに関連する詳細情報を取得するための分析です。 このオプションは、1 つ以上のドメイン固有のモデルからの詳細に加え、ユーザーが汎用的なイメージ分析を行うアプリケーションで使用できます。 このメソッドが呼び出された場合、最初に 86 のカテゴリ分類の分類子が呼び出されます。 いずれかのカテゴリが既知のモデルや一致するモデルのカテゴリと一致した場合、分類子呼び出しの 2 番目のパスが後に続きます。 たとえば、'details=all' または "details" に 'celebrities' が含まれている場合、86 のカテゴリ分類子が呼び出された後、メソッドで有名人の分類子が呼び出されます。 結果には、'people_' で始まるタグが含まれます。

## <a name="generating-descriptions"></a>説明の生成 
Computer Vision API のアルゴリズムでは、イメージのコンテンツを分析します。 この分析では、完全な文章で人間が判読できる言語として表示される "説明" の基礎が形成されます。 説明には、イメージで検出された内容の概要が示されます。 Computer Vision API のアルゴリズムでは、イメージで識別されるオブジェクトに基づき、さまざまな説明が生成されます。 説明はそれぞれ評価され、信頼度スコアが生成されます。 その後、信頼度スコアが最も高いものから最も低いものの順に並べられたリストが返されます。 このテクノロジを使用してイメージ キャプションを生成するボットの例については、[こちら](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption)を参照してください。  

### <a name="example-description-generation"></a>説明の生成例
![白黒のビル](./Images/bw_buildings.jpg) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>配色の認識
Computer Vision アルゴリズムでは、イメージから色を抽出します。 色は、前景、後景、全体の異なる 3 つのコンテキストで分析されます。 12 色のドミナント アクセント カラーにグループ分けされています。 そのアクセント カラーは、黒、青、茶色、灰色、緑、オレンジ、ピンク、紫、赤、青緑、白、黄色です。 イメージの色に応じて、シンプルな白黒またはアクセント カラーが 16 進数のカラー コードで返される場合があります。

イメージ                                                       | 前景 |バックグラウンド| 色
----------------------------------------------------------- | --------- | ------- | ------
![屋外の山](./Images/mountain_vista.jpg)            | 黒     | 黒   | 白
![Vision Analyze 花](./Images/flower.jpg)               | 黒     | 白   | 白、黒、緑
![Vision Analyze 鉄道の駅](./Images/train_station.jpg) | 黒     | 黒   | 黒

### <a name="accent-color"></a>アクセント カラー
ドミナント カラーと彩度の組み合わせによって、最もユーザーの目を引く色を表すように設計された、イメージから抽出された色。

イメージ                                                       | Response
----------------------------------------------------------- | ----
![屋外の山](./Images/mountain_vista.jpg)            | #BC6F0F
![Vision Analyze 花](./Images/flower.jpg)               | #CAA501
![Vision Analyze 鉄道の駅](./Images/train_station.jpg) | #484B83


### <a name="black--white"></a>白黒
イメージが白黒であるかどうかを示すブール型のフラグ。

イメージ                                                      | Response
---------------------------------------------------------- | ----
![Vision Analyze ビル](./Images/bw_buildings.jpg)      | True
![Vision Analyze 家の庭](./Images/house_yard.jpg)      | False

## <a name="flagging-adult-content"></a>成人向けコンテンツへのフラグ設定
さまざまな視覚カテゴリの中には、成人向けできわどいグループがあり、成人向けマテリアルを検出し、性的なコンテンツを含むイメージの表示を制限することができます。 成人向けできわどいコンテンツを検出するためのフィルターは、ユーザーの設定に合わせて、スライディング スケールで設定することができます。

## <a name="optical-character-recognition-ocr"></a>光学式文字認識 (OCR)
OCR テクノロジでは、イメージのテキスト コンテンツが検出され、識別されたテキストはコンピューターが読み取り可能な文字ストリームに抽出されます。 結果は、検索の際に、また、医療記録、セキュリティ、銀行取引などの他の多くの目的に使用できます。 言語は自動的に検出されます。 OCR を利用すれば、ユーザーはテキストを書き写すのではなく、撮影できるため、使い勝手がよく、時間を短縮できます。

OCR では 25 言語がサポートされています。 これらの言語は、アラビア語、簡体字中国語、繁体字中国語、チェコ語、デンマーク語、オランダ語、英語、フィンランド語、フランス語、ドイツ語、ギリシャ語、ハンガリー語、イタリア語、日本語、韓国語、ノルウェー語、ポーランド語、ポルトガル語、ルーマニア語、ロシア語、セルビア語 (キリルおよびラテン)、スロバキア語、スペイン語、スウェーデン語、トルコ語です。

必要に応じて、OCR では、認識されたテキストの回転が、イメージの水平軸を中心として度単位で修正されます。 OCR では、以下の図に示すように、各単語のフレーム座標が提供されます。

![OCR の概要](./Images/vision-overview-ocr.png) OCR の要件:
- 入力イメージのサイズは、40 x 40 から 3200 x 3200 ピクセルまでにする必要があります。
- イメージは、10 メガピクセルよりも大きくすることはできません。

入力イメージは、90 度の倍数に、40 度までの小角度を加えた度数で回転させることができます。

テキスト認識の精度は、イメージの品質によって異なります。 次のような状況では、読み取りが不正確になる可能性があります。
- ぼやけたイメージ。
- 手書きまたは筆記体のテキスト。
- アーティスティックなフォント スタイル。
- 小さなテキスト サイズ。
- 複雑な背景、影、テキスト上のグレア、射影ひずみ。
- 単語の先頭の大文字のサイズが大きすぎるか欠落している
- 下付き、上付き、または取り消し線テキスト。

制限事項: テキストが主な写真では、部分的に認識された単語により誤検知が発生する場合があります。 一部の写真、特にテキストのない写真では、イメージの種類によって、精度が大きく異なる場合があります。

## <a name="recognize-handwritten-text"></a>手書きのテキストを認識する
このテクノロジでは、メモ、手紙、レポート、ホワイトボード、用紙などから手書きのテキストを検出し、抽出することができます。白色の紙や黄色い付箋紙、ホワイトボードなど、さまざまな表面や下地に対応可能です。

手書きのテキスト認識を使用すれば、テキストを書き写すのではなく、撮影できるため、時間と手間が抑えられ、生産性を向上させることができます。 メモはデジタル化することができます。 このデジタル化により、迅速かつ簡単な検索が可能になります。 また、書類が散乱する事態も防ぐことができます。

入力の要件:
- サポートされているイメージ形式: JPEG、PNG、BMP。
- イメージ ファイル サイズは 4 MB 未満である必要があります。
- イメージの寸法は最小で 40 x 40、最大で 3200 x 3200 である必要があります。

注: このテクノロジは現在、プレビュー段階であり、英語のテキストでのみ使用できます。

## <a name="generating-thumbnails"></a>サムネイルの生成
サムネイルは、フルサイズのイメージを縮小したものです。 携帯電話、タブレット、PC などのさまざまなデバイスで、さまざまなユーザー エクスペリエンス (UX) のレイアウトとサムネイル サイズが必要になります。 この Computer Vision API 機能では、スマート トリミングを使用して問題を解決することができます。

イメージのアップロード後に、高品質なサムネイルが生成され、Computer Vision API アルゴリズムにより、イメージ内のオブジェクトが分析されます。 その後、"関心領域" (ROI) の要件に合わせて、イメージがトリミングされます。 下の図に示すように、特別なフレームワーク内に出力が表示されます。 ユーザーのニーズに合わせて、元のイメージの縦横比とは異なる縦横比を使用して、生成されたサムネイルを表示することができます。

サムネイルのアルゴリズムは次のように動作します。

1. イメージから不要な要素を削除し、メイン オブジェクトである、"関心領域" (ROI) を認識します。
2. 識別された関心領域に基づいて、イメージをトリミングします。
3. ターゲットのサムネイルの寸法に合わせて、縦横比を変更します。

![サムネイル](./Images/thumbnail-demo.png)
