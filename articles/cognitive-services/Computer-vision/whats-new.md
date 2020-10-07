---
title: Computer Vision の新機能
titleSuffix: Azure Cognitive Services
description: この記事には、Computer Vision に関するニュースが含まれています。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: f007ddf2625bf9e8b9d4365902ac1511ea95fa81
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "90904102"
---
# <a name="whats-new-in-computer-vision"></a>Computer Vision の新機能

サービス内の新機能について説明します。 以下の項目には、リリース ノート、ビデオ、ブログの投稿、およびその他の種類の情報が含まれる可能性があります。 このページをブックマークして、常にサービスの最新情報を確認してください。

## <a name="september-2020"></a>2020 年 9 月

### <a name="spatial-analysis-container-preview"></a>空間分析コンテナー プレビュー

[空間分析コンテナー](spatial-analysis-container.md)は現在プレビュー段階です。 Computer Vision の空間分析機能を使用すると、リアルタイム ストリーミング ビデオを分析して、物理環境における人物とその動きとの間の空間的な関係を把握できます。 空間分析は、オンプレミスで使用できる Docker コンテナーです。 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v3.1 パブリック プレビューが新たに日本語の OCR に対応
Computer Vision の Read API v3.1 パブリック プレビューで次の機能が追加されました。
* 日本語の OCR
* それぞれのテキスト行について、その体裁が手書きであるか印刷スタイルであるかが信頼度スコア (ラテン語系の言語のみ) と共に示されます。
* 複数ページから成るドキュメントから、選択されたページまたはページ範囲のテキストのみを抽出します。

* この Read API のプレビュー バージョンでは、英語、オランダ語、フランス語、ドイツ語、イタリア語、日本語、ポルトガル語、簡体字中国語、およびスペイン語がサポートされています。

詳細については、[Read API の概要](concept-recognizing-text.md)に関する記事を参照してください。

> [!div class="nextstepaction"]
> [Read API v3.1 パブリック プレビュー 2 の詳細を確認する](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>2020 年 7 月

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Read API v3.1 のパブリック プレビューが簡体字中国語の OCR に対応
Computer Vision の Read API v3.1 パブリック プレビューでは、簡体字中国語のサポートが追加されています。

* この Read API のプレビュー バージョンでは、英語、オランダ語、フランス語、ドイツ語、イタリア語、ポルトガル語、簡体字中国語、およびスペイン語がサポートされています。

詳細については、[Read API の概要](concept-recognizing-text.md)に関する記事を参照してください。

> [!div class="nextstepaction"]
> [Read API v3.1 パブリック プレビュー 1 の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>2020 年 5 月
Computer Vision API v3.0 が一般提供になり、[Read API](concept-recognizing-text.md) が更新されています。

* 英語、オランダ語、フランス語、ドイツ語、イタリア語、ポルトガル語、およびスペイン語のサポート
* 精度の向上
* 抽出された各単語の信頼度スコア
* 新しい出力形式

## <a name="march-2020"></a>2020 年 3 月

* TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

## <a name="january-2020"></a>2020 年 1 月

### <a name="read-api-30-public-preview"></a>Read API 3.0 パブリック プレビュー

Read API のバージョン3.0 では、印刷されたテキストまたは手書きテキストを画像から抽出するオプションが追加されました。 以前のバージョンと比較して、3.0 では以下のものが提供されます。
* 精度の向上
* 新しい出力形式
* 抽出された各単語の信頼度スコア
* 言語パラメーターの追加によるスペイン語および英語のサポート

3\.0 API の使用を開始するには、[テキストの抽出に関するクイックスタート](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text?tabs=version-3)に従ってください。

## <a name="cognitive-service-updates"></a>Cognitive Services の更新プログラム

[Cognitive Services に対する Azure 更新プログラムのお知らせ](https://azure.microsoft.com/updates/?product=cognitive-services)
