---
title: Computer Vision API とは - Computer Vision
titlesuffix: Azure Cognitive Services
description: Computer Vision サービスを使用すると、開発者は、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4c952c847aa56b385065a878b664b3d1d98763f4
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584022"
---
# <a name="what-is-computer-vision"></a>Computer Vision とは

クラウドベースの Computer Vision サービスを使用すると、開発者は、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。 Computer Vision では、JPEG や PNG などの一般的なイメージを操作します。 イメージを分析するには、イメージをアップロードするか、またはイメージの URL を指定します。 Computer Vision のアルゴリズムでは、ユーザーが関心を寄せた視覚的特徴に応じて、さまざまな方法でイメージのコンテンツを分析できます。 たとえば、Computer Vision では、イメージが成人向けや人種差別的なコンテンツを含んでいるかどうかを判断したり、イメージ内の人物の顔をすべて検出したりできます。

[クライアント ライブラリ](quickstarts-sdk/csharp-analyze-sdk.md)を使用してサービスを呼び出すか、または [REST API](vision-api-how-to-topics/howtocallvisionapi.md) を直接呼び出すことで、お使いのアプリケーションで次の目的に Computer Vision を使用できます。

- [イメージを分析して分析情報を得る](#analyzing-images-for-insight)
- [イメージからテキストを抽出する](#extracting-text-from-images)
- [イメージ内のコンテンツを調整する](#moderating-content-in-images)

## <a name="analyzing-images-for-insight"></a>イメージを分析して分析情報を得る

Computer Vision を使用してイメージを分析し、イメージの視覚的特徴や性質に関する分析情報を検出して提示できます。 イメージのコンテンツをアップロードしてローカル イメージを分析するか、またはイメージの URL を指定してリモート イメージを分析できます。

イメージを分析するとき、Computer Vision では次のアクションを実行できます。

| Action | 説明 |
| ------ | ----------- |
|**[視覚的特徴のタグ付け](concept-tagging-images.md)**|数千個の認識可能なオブジェクト、生物、風景、および動作のセットから、画像内の視覚的な特徴を識別してタグ付けします。 タグが不明確な場合や、常識的でない場合は、API 応答により、既知の設定のコンテキストでタグの意味を明確にするための "ヒント" が示されます。 タグ付けの対象は、前景の人物などの主題に限らず、背景 (屋内または屋外)、家具、道具、植物、動物、アクセサリ、ガジェットなども含まれます。|
|**[オブジェクトの検出](concept-object-detection.md)**| オブジェクトの検出はタグ付けに似ていますが、API で返されるのは、各タグが適用された境界ボックスの座標です。 たとえば、犬、猫、および人物が画像に含まれている場合、検出操作では、これらのオブジェクトと画像内での各オブジェクトの座標の一覧が表示されます。 この機能を使用して、画像内のオブジェクト間のリレーションシップをさらに処理できます。 画像内に同じタグの複数のインスタンスが存在する場合はそれも知ることができます。|
|**[イメージの分類](concept-categorizing-images.md)**|親/子で引き継がれる階層を備えた[カテゴリの分類](Category-Taxonomy.md)を使用して、イメージ全体を識別してタグ付けします。 カテゴリは単独で、または新しいタグ付けモデルと共に使用できます。<br/>現時点では、イメージのタグ付けと分類でサポートされている言語は、英語のみです。|
|**[イメージの説明](concept-describing-images.md)**|完全な文を使用して、人間が判読できる言語でイメージ全体の説明を生成します。 Computer Vision のアルゴリズムでは、イメージ内の識別されるオブジェクトに基づいて、さまざまな説明が生成されます。 説明はそれぞれ評価され、信頼度スコアが生成されます。 その後、信頼度スコアが最も高いものから最も低いものの順に並べられたリストが返されます。|
|**[顔の検出](concept-detecting-faces.md)** |イメージ内の人物の顔を検出して、検出された各顔に関する情報を提示します。 Computer Vision は検出された各顔の座標、四角い枠、性別、および年齢を返します。<br/>Computer Vision は、[[Face]](/azure/cognitive-services/face/) で見つけられる機能のサブセットを提供しており、顔の識別やポーズの検出など、より詳細な分析に Face サービスを使用できます。|
|**[イメージの種類の検出](concept-detecting-image-types.md)**|イメージが線による描画かクリップ アートのようになっているかなど、イメージの性質を検出します。|
|**[ドメイン固有のコンテンツの検出](concept-detecting-domain-content.md)**|ドメイン モデルを使用して、有名人やランドマークなど、イメージ内のドメイン固有のコンテンツを検出して識別します。 たとえば、イメージに人物が含まれている場合、Computer Vision では、サービスに含まれている有名人用のドメイン モデルを使用して、イメージ内で検出された人物が既知の有名人と一致するかどうかを判定できます。|
|**[配色の検出](concept-detecting-color-schemes.md)**|イメージ内にある色の使用状況を分析します。 Computer Vision では、イメージが白黒かカラーかを特定すると共に、カラーのイメージの場合、主要な色やアクセントになる色を識別できます。|
|**[サムネイルの生成](concept-generating-thumbnails.md)**|イメージのコンテンツを分析して、そのイメージの適切なサムネイルを生成します。 Computer Vision では、最初に高品質のサムネイルを生成した後、画像内のオブジェクトを分析して "*関心領域*" を特定します。 Computer Vision では、その後、関心領域の要件に合わせて、画像がトリミングされます。 ユーザーのニーズに応じて、元のイメージの縦横比とは異なる縦横比を使用して、生成されたサムネイルを表示することができます。|
|**[関心領域を取得する](concept-generating-thumbnails.md#area-of-interest)**|画像の内容が分析され、"*関心領域*" の座標が返されます。 これはサムネイルを生成するために使用される機能と同じですが、画像をクロップする代わりに、Computer Vision では領域の境界ボックスの座標が返されるため、呼び出し元のアプリケーションで必要に応じて元の画像を変更できます。|

## <a name="extracting-text-from-images"></a>イメージからのテキストの抽出

Computer Vision を使用し、イメージからコンピューターが読み取り可能な文字のストリームへ、[OCR を使用してテキストを抽出](concept-extracting-text-ocr.md)します。 必要に応じて、OCR では、認識されたテキストの回転を、イメージの水平軸を中心として度単位で修正し、各語句のフレーム座標を提供します。 OCR は 25 の言語をサポートし、抽出されたテキストの言語を自動的に検出します。

また、イメージから[印刷および手書きのテキストを認識](concept-recognizing-text.md)することもできます。 Computer Vision では、レシート、ポスター、名刺、手紙、およびホワイトボードなど、異なるサーフェスや背景を持つさまざまなオブジェクトのイメージから、印刷および手書き両方のテキストを検出して、抽出できます。 現時点では、印刷および手書きのテキストの認識はプレビュー段階であり、サポートされている言語は英語のみです。  

## <a name="moderating-content-in-images"></a>イメージ内のコンテンツを調整する

Computer Vision を使用して、イメージ内の[成人向けや人種差別的コンテンツを検出](concept-detecting-adult-content.md)できます。イメージに成人向けまたは人種差別的なコンテンツが含まれている可能性を評価し、その両方に対して信頼度スコアを生成します。 成人向けできわどいコンテンツを検出するためのフィルターは、設定に合わせて、スライディング スケールで設定することができます。

## <a name="using-containers"></a>コンテナーを使用する

印刷されたテキストと手書きのテキストをローカルに認識するには、標準化された Docker コンテナーをデータの近くにインストールして、[Computer Vision コンテナーを使用](computer-vision-how-to-install-containers.md)します。

## <a name="image-requirements"></a>イメージの要件

Computer Vision では、次の要件に合ったイメージを分析できます。

- イメージが、JPEG、PNG、GIF、または BMP で提示されている
- イメージのファイル サイズが 4 メガバイト (MB) 未満である
- イメージのディメンションが 50 x 50 ピクセルよりも大きい値である  
  OCR 用に、画像の寸法が 50 x 50 から 4,200 x 4,200 ピクセルの間である

## <a name="next-steps"></a>次の手順

以下のクイック スタートのいずれかに従って、実際に Computer Vision を使ってみましょう。

- [イメージを分析する](quickstarts-sdk/csharp-analyze-sdk.md)
- [手書きのテキストを抽出する](quickstarts-sdk/csharp-hand-text-sdk.md)
- [サムネイルを生成する](quickstarts-sdk/csharp-thumb-sdk.md)
