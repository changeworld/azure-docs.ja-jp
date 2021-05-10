---
title: 画像分析とは
titleSuffix: Azure Cognitive Services
description: 画像分析サービスは、事前トレーニング済みの AI モデルを使用して、画像からさまざまな視覚的特徴を抽出します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: computer vision, computer vision アプリケーション, computer vision サービス
ms.openlocfilehash: 0258eb7c57bc0734b5c0a67644cbaa4f62a34537
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766917"
---
# <a name="what-is-image-analysis"></a>画像分析とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Computer Vision Image Analysis サービスでは、画像からさまざまな視覚的特徴を抽出できます。 たとえば、成人向けコンテンツが画像に含まれているかどうかを判断したり、特定のブランドや物体、人の顔を検出したりすることができます。

画像分析は、クライアント ライブラリ SDK を通じて、または [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v2-ga/operations/5d986960601faab4bf452005) を直接呼び出すことで使用できます。 使用を開始するには、[クイックスタート](quickstarts-sdk/image-analysis-client-library.md)に従ってください。

このドキュメントには、次のような記事が記載されています。
* [クイックスタート](./quickstarts-sdk/image-analysis-client-library.md)は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。 
* [攻略ガイド](./Vision-API-How-to-Topics/HowToCallVisionAPI.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [概念の記事](concept-tagging-images.md)では、サービスの機能と特長について詳しく説明します。
* [チュートリアル](./tutorials/storage-lab-tutorial.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。

## <a name="image-analysis-features"></a>画像分析の機能

画像を分析し、その視覚的特徴や性質に関する分析情報を提示できます。 次の一覧のすべての機能は、[Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API に備わっています。 [クイックスタート](./quickstarts-sdk/image-analysis-client-library.md)に従って始めてください。


### <a name="tag-visual-features"></a>視覚的特徴のタグ付け

数千個の認識可能なオブジェクト、生物、風景、および動作のセットから、画像内の視覚的な特徴を識別してタグ付けします。 タグが不明確な場合や、常識的でない場合は、API 応答により、タグのコンテキストを明確にするためのヒントが示されます。 タグ付けの対象は、前景の人物などの被写体に限らず、背景 (屋内または屋外)、家具、道具、植物、動物、アクセサリ、ガジェットなども含まれます。 [視覚的特徴のタグ付け](concept-tagging-images.md)

### <a name="detect-objects"></a>物体を検出する

オブジェクトの検出はタグ付けに似ていますが、API で返されるのは、各タグが適用された境界ボックスの座標です。 たとえば犬や猫、人物が画像に含まれている場合、検出操作によって、それらのオブジェクトが、画像における対応する座標と共に一覧表示されます。 この機能を使用して、画像内のオブジェクト間のリレーションシップをさらに処理できます。 画像内に同じタグの複数のインスタンスが存在する場合はそれも知ることができます。 [物体を検出する](concept-object-detection.md)

### <a name="detect-brands"></a>ブランドを検出する

数千点ものグローバル ロゴのデータベースから、画像または動画に含まれる商業ブランドを識別します。 この機能は、たとえば、ソーシャル メディアで最も人気のあるブランドや、メディアのプロダクト プレイスメントの中で最も普及しているブランドを検出する目的で使用できます。 [ブランドを検出する](concept-brand-detection.md)

### <a name="categorize-an-image"></a>イメージの分類

親/子で引き継がれる階層を備えた[カテゴリの分類](Category-Taxonomy.md)を使用して、イメージ全体を識別してタグ付けします。 カテゴリは単独で、または新しいタグ付けモデルと共に使用できます。<br/>現時点では、イメージのタグ付けと分類でサポートされている言語は、英語のみです。 [イメージの分類](concept-categorizing-images.md)

### <a name="describe-an-image"></a>イメージの説明

完全な文を使用して、人間が判読できる言語でイメージ全体の説明を生成します。 Computer Vision のアルゴリズムでは、イメージ内の識別されるオブジェクトに基づいて、さまざまな説明が生成されます。 説明はそれぞれ評価され、信頼度スコアが生成されます。 その後、信頼度スコアが最も高いものから最も低いものの順に並べられたリストが返されます。 [イメージの説明](concept-describing-images.md)

### <a name="detect-faces"></a>顔を検出する

イメージ内の人物の顔を検出して、検出されたそれぞれの顔に関する情報を提示します。 Computer Vision は検出された各顔の座標、四角い枠、性別、および年齢を返します。<br/>Computer Vision では、[Face](../face/index.yml) サービス機能のサブセットが提供されます。 Face サービスは、顔識別や姿勢検出など、より詳細な分析に使用できます。 [顔を検出する](concept-detecting-faces.md)

### <a name="detect-image-types"></a>イメージの種類の検出

イメージが線による描画かクリップ アートのようになっているかなど、イメージの性質を検出します。 [イメージの種類の検出](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>ドメイン固有のコンテンツの検出

ドメイン モデルを使用して、有名人やランドマークなど、イメージ内のドメイン固有のコンテンツを検出して識別します。 たとえば、画像に人物が含まれている場合、Computer Vision では、有名人用のドメイン モデルを使用して、画像内で検出された人物が既知の有名人と一致するかどうかを判断できます。 [ドメイン固有のコンテンツの検出](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>配色の検出

イメージ内にある色の使用状況を分析します。 Computer Vision では、イメージが白黒かカラーかを特定すると共に、カラーのイメージの場合、主要な色やアクセントになる色を識別できます。 [配色の検出](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>サムネイルの生成

イメージのコンテンツを分析して、そのイメージの適切なサムネイルを生成します。 Computer Vision では、最初に高品質のサムネイルを生成した後、画像内のオブジェクトを分析して "*関心領域*" を特定します。 Computer Vision では、その後、関心領域の要件に合わせて、画像がトリミングされます。 ユーザーのニーズに応じて、元のイメージの縦横比とは異なる縦横比を使用して、生成されたサムネイルを表示することができます。 [サムネイルの生成](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>関心領域を取得する

画像の内容が分析され、"*関心領域*" の座標が返されます。 画像をクロップしたり、サムネイルを生成したりする代わりに、Computer Vision では領域の境界ボックスの座標が返されるため、呼び出し元のアプリケーションで必要に応じて元の画像を変更できます。 [関心領域を取得する](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>画像内のコンテンツを調整する

Computer Vision を使用すると、画像内の[成人向けコンテンツを検出](concept-detecting-adult-content.md)し、さまざまな分類の信頼度スコアを返すことができます。 コンテンツをフラグ設定するためのしきい値は、自分の都合に合わせて、スライディング スケールで設定することができます。

## <a name="image-requirements"></a>イメージの要件

画像分析は、次の要件を満たす画像で動作します。

- イメージが、JPEG、PNG、GIF、または BMP で提示されている
- イメージのファイル サイズが 4 メガバイト (MB) 未満である
- イメージのディメンションが 50 x 50 ピクセルよりも大きい値である

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Computer Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

使用する開発言語のクイックスタート ガイドに従って、画像分析の使用を開始します。

- [クイック スタート: Computer Vision REST API またはクライアント ライブラリ](./quickstarts-sdk/image-analysis-client-library.md)
