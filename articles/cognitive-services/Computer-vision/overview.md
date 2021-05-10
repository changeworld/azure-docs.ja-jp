---
title: Computer Vision とは
titleSuffix: Azure Cognitive Services
description: Computer Vision サービスを使用すると、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: computer vision, computer vision アプリケーション, computer vision サービス
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286136"
---
# <a name="what-is-computer-vision"></a>Computer Vision とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure の Computer Vision サービスを使用すると、関心のある視覚的特徴に基づいて、画像を処理して情報を返す高度なアルゴリズムにアクセスできます。 

| サービス|説明|
|---|---|
|[光学式文字認識 (OCR)](overview-ocr.md)|光学式文字認識 (OCR) サービスは、画像からテキストを抽出します。 新しい Read API を使用して、印刷されたテキストと手書きのテキストを写真やドキュメントから抽出することができます。 ディープラーニングベースのモデルを使用して、さまざまなサーフェスや背景のテキストを操作します。 たとえば、ビジネス ドキュメント、請求書、レシート、ポスター、名刺、レター、ホワイトボードが対象となります。 OCR API では、[複数の言語](./language-support.md)で印刷されたテキストの抽出がサポートされます。 使用を開始するには、[OCR クイックスタート](quickstarts-sdk/client-library.md)に従ってください。|
|[画像分析](overview-image-analysis.md)| 画像分析サービスは、物、顔、成人向けコンテンツ、自動生成されたテキストの説明など、さまざまな視覚的特徴を画像から抽出します。 使用を開始するには、[画像分析のクイックスタート](quickstarts-sdk/image-analysis-client-library.md)に従ってください。|
| [空間分析](intro-to-spatial-analysis-public-preview.md)| 空間分析サービスは、ビデオ フィードでの人々の存在と移動を分析し、他のシステムが応答できるイベントを生成します。 使用を開始するには、[空間分析コンテナー](spatial-analysis-container.md)をインストールしてください。|

## <a name="computer-vision-for-digital-asset-management"></a>デジタル資産管理用の Computer Vision

Computer Vision は、多くのデジタル資産管理 (DAM) シナリオに活用できます。 DAM は、リッチ メディア アセットの整理、保管、取得、およびデジタルの権利とアクセス許可の管理を行うビジネス プロセスです。 たとえば、会社では、表示されるロゴ、顔、オブジェクト、色などに基づいて、画像をグループ化し、識別することができます。 または、自動的に[画像のキャプションを生成](./Tutorials/storage-lab-tutorial.md)し、キーワードを添付して検索できるようにすることもできます。 Cognitive Services、Azure Cognitive Search、およびインテリジェント レポートを使用するオールインワンの DAM ソリューションについては、GitHub 上の「[ナレッジ マイニング ソリューション アクセラレータ ガイド](https://github.com/Azure-Samples/azure-search-knowledge-mining)」を参照してください。 その他の DAM の例については、[Computer Vision ソリューション テンプレート](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates)のリポジトリを参照してください。

## <a name="image-requirements"></a>イメージの要件

Computer Vision では、次の要件に合ったイメージを分析できます。

- イメージが、JPEG、PNG、GIF、または BMP で提示されている
- イメージのファイル サイズが 4 メガバイト (MB) 未満である
- イメージのディメンションが 50 x 50 ピクセルよりも大きい値である
  - Read API の場合、画像の寸法は、50 x 50 から 10,000 x 10,000 ピクセルの間である必要があります。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Computer Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

クイックスタートに従って、任意の開発言語でサービスを実装して実行します。

* [クイックスタート: 光学式文字認識 (OCR)](quickstarts-sdk/client-library.md)
* [クイックスタート: 画像分析](quickstarts-sdk/image-analysis-client-library.md)
* [クイックスタート: 空間分析コンテナー](spatial-analysis-container.md)
