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
ms.date: 05/24/2021
ms.author: pafarley
ms.openlocfilehash: c2cdf1dc396a04adb106ee1b3777eb11e7c36279
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045486"
---
# <a name="whats-new-in-computer-vision"></a>Computer Vision の新機能

サービス内の新機能について説明します。 以下の項目には、リリース ノート、ビデオ、ブログの投稿、およびその他の種類の情報が含まれる可能性があります。 このページをブックマークして、常にサービスの最新情報を確認してください。

## <a name="september-2021"></a>2021 年 9 月

### <a name="ocr-read-api-public-preview-supports-122-languages"></a>OCR (Read) API パブリック プレビューが 122 の言語に対応
Computer Vision の [OCR (Read) API](overview-ocr.md) は、[対応する言語](language-support.md)が最新のプレビューで 122 に拡張されます。
* 49 の新しい言語 (ロシア語、ブルガリア語、その他のキリル語系やラテン語系など) での印刷テキストに対する OCR サポート。
* 6 つの新しい言語 (英語、簡体中国語、フランス語、ドイツ語、イタリア語、ポルトガル語、スペイン語など) での手書きテキストに対する OCR サポート。
* ID ドキュメント内の機械読み取り可能ゾーン (MRZ) テキストおよびデジタル PDF を処理する機能の強化。
* 一般的なパフォーマンスと AI の品質の向上

新しいプレビュー機能を使用する方法については、[OCR ハウツー ガイド](Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional)を参照してください。

> [!div class="nextstepaction"]
> [Read API の概要](./quickstarts-sdk/client-library.md)


## <a name="august-2021"></a>2021 年 8 月

### <a name="image-tagging-language-expansion"></a>画像のタグ付け言語の拡張

画像のタグ付け機能の[最新バージョン (v3.2)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) では、50 言語のタグがサポートされるようになりました。 詳細については、[言語サポート](language-support.md)に関するページを参照してください。

## <a name="may-2021"></a>2021 年 5 月

### <a name="spatial-analysis-container-update"></a>空間分析コンテナーの更新

新しい機能セットが含まれる[空間分析コンテナー](spatial-analysis-container.md)の新しいバージョンがリリースされています。 この Docker コンテナーを使用すると、リアルタイム ストリーミング ビデオを分析して、物理環境における人物とその動きとの間の空間的な関係を把握できます。 

* [空間分析操作](spatial-analysis-operations.md)を構成して、人が向いている方向を検出できるようになりました。 
    * 向き分類子は、`enable_orientation` パラメーターを構成することで、`personcrossingline` および `personcrossingpolygon` 操作に対して有効にすることができます。 既定ではオフに設定されています。

* [空間分析操作](spatial-analysis-operations.md)では、歩いたり走ったりしている人の速度を検出する構成も提供されるようになりました
     * 速度は、`enable_speed` 分類子をオンにすることによって、`personcrossingline` および `personcrossingpolygon` 操作について検出できます。既定ではオフになっています。 出力は、`speed`、`avgSpeed`、`minSpeed` の各出力に反映されます。


## <a name="april-2021"></a>2021 年 4 月

### <a name="computer-vision-v32-ga"></a>Computer Vision v3.2 GA

Computer Vision API v3.2 の一般提供が開始されました。次の点が更新されています。
* 改善された画像のタグ付けモデル: ビジュアル コンテンツが分析され、画像に表示されているオブジェクト、アクション、コンテンツに基づいて、関連するタグが生成されます。 このモデルは、[Tag Image API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) を介して利用できます。 詳細については、画像分析の[使用法ガイド](./vision-api-how-to-topics/howtocallvisionapi.md)および[概要](./overview-image-analysis.md)に関するページを参照してください。
* 更新されたコンテンツ モデレーション モデル: アダルト コンテンツの存在が検出され、成人向け、わいせつ、または不快なビジュアル コンテンツを含む画像をフィルター処理するためのフラグが提供されます。 このモデルは、[Analyze API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) を介して利用できます。 詳細については、画像分析の[使用法ガイド](./vision-api-how-to-topics/howtocallvisionapi.md)および[概要](./overview-image-analysis.md)に関するページを参照してください。
* [73 言語で利用可能な OCR (Read)](./language-support.md#optical-character-recognition-ocr)。これには、簡易字中国語と繁体字中国語、日本語、韓国語、ラテン語系の言語が含まれます。
* [OCR (Read)](./overview-ocr.md) は、オンプレミス デプロイ用の[ディストリビューションレス コンテナー](./computer-vision-how-to-install-containers.md?tabs=version-3-2)としても利用できます。

> [!div class="nextstepaction"]
> [Computer Vision v3.2 GA に関するページを参照してください](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>2021 年 3 月

### <a name="computer-vision-32-public-preview-update"></a>Computer Vision 3.2 パブリック プレビューの更新

Computer Vision API v3.2 パブリック プレビューが更新されました。 プレビュー リリースには、すべての Computer Vision 機能に加えて、更新された Read および Analyze API が含まれています。

> [!div class="nextstepaction"]
> [Computer Vision v3.2 パブリック プレビュー 3 を参照する](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>2021 年 2 月

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Read API v3.2 のパブリック プレビューが 73 言語の OCR に対応
クラウド サービスおよび Docker コンテナーとして利用できる Computer Vision Read API v3.2 パブリック プレビューには、こちらの更新が含まれます。
* [OCR が 73 言語に対応](./language-support.md#optical-character-recognition-ocr)。簡易字中国語と繁体字中国語、日本語、韓国語、ラテン語系の言語が含まれます。
* テキスト行出力に自然な読みの順序を使用 (ラテン語系の言語のみ)
* テキスト行に対する手書きスタイルの分類と信頼度スコア (ラテン語系の言語のみ)。
* 複数ページから成るドキュメントで選択ページのみのテキストを抽出。
* オンプレミス デプロイ用の[ディストリビューションレス コンテナー](./computer-vision-how-to-install-containers.md?tabs=version-3-2)として利用可能。

詳細については、[Read API の攻略ガイド](Vision-API-How-to-Topics/call-read-api.md)を参照してください。

> [!div class="nextstepaction"]
> [Read API v3.2 パブリック プレビューを使用する](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>2021 年 1 月

### <a name="spatial-analysis-container-update"></a>空間分析コンテナーの更新

新しい機能セットが含まれる[空間分析コンテナー](spatial-analysis-container.md)の新しいバージョンがリリースされています。 この Docker コンテナーを使用すると、リアルタイム ストリーミング ビデオを分析して、物理環境における人物とその動きとの間の空間的な関係を把握できます。 

* 人物がマスクなどの顔を保護するものを装着しているかどうかを検出するように[空間分析操作](spatial-analysis-operations.md)を構成できるようになりました。 
    * マスク分類子は、`ENABLE_FACE_MASK_CLASSIFIER` パラメーターを構成することで、`personcount`、`personcrossingline`、および `personcrossingpolygon` 操作に対して有効にすることができます。
    * ビデオ ストリームで検出された人物それぞれの信頼度スコアと共に、`face_mask` および `face_noMask` 属性がメタデータとして返されます
* *personcrossingpolygon* 操作が拡張され、人がゾーン内で費やした滞在時間を計算できるようになりました。 この操作のゾーンの構成で `type` パラメーターを `zonedwelltime` に設定できます。これにより、*personZoneDwellTimeEvent* 型の新しいイベントに、人がゾーン内で費やした時間 (ミリ秒単位) が設定された `durationMs` フィールドが含まれるようになります。
* **破壊的変更**:*personZoneEvent* イベントの名前が *personZoneEnterExitEvent* に変更されました。 人がゾーンに出入りするときに *personcrossingpolygon* イベントが発生し、交差したゾーンの番号付き側に方向情報を提供します。
* ビデオの URL を、すべての操作で "Private Parameter/obfuscated" として指定できます。 難読化はオプションになり、`KEY` および `IV` が環境変数として指定されている場合にのみ機能します。
* 既定では、すべての操作で較正が有効になっています。 無効にするには、`do_calibration: false` を設定します。
* `enable_recalibration` パラメーターによる自動再較正 (既定では無効) のサポートを追加しました。詳細については、「[空間分析操作](./spatial-analysis-operations.md)」を参照してください
* カメラ較正のパラメーターを `DETECTOR_NODE_CONFIG` にします。 詳細については、「[空間分析操作](./spatial-analysis-operations.md)」を参照してください。


## <a name="october-2020"></a>2020 年 10 月

### <a name="computer-vision-api-v31-ga"></a>Computer Vision API v3.1 GA

一般提供中の Computer Vision API は、v3.1 にアップグレードされました。

## <a name="september-2020"></a>2020 年 9 月

### <a name="spatial-analysis-container-preview"></a>空間分析コンテナー プレビュー

[空間分析コンテナー](spatial-analysis-container.md)は現在プレビュー段階です。 Computer Vision の空間分析機能を使用すると、リアルタイム ストリーミング ビデオを分析して、物理環境における人物とその動きとの間の空間的な関係を把握できます。 空間分析は、オンプレミスで使用できる Docker コンテナーです。 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v3.1 パブリック プレビューが新たに日本語の OCR に対応
Computer Vision Read API v3.1 パブリック プレビューで、こちらの機能が追加されました。
* 日本語の OCR
* それぞれのテキスト行について、その体裁が手書きであるか印刷スタイルであるかが信頼度スコア (ラテン語系の言語のみ) と共に示されます。
* 複数ページから成るドキュメントから、選択されたページまたはページ範囲のテキストのみを抽出します。

* この Read API のプレビュー バージョンでは、英語、オランダ語、フランス語、ドイツ語、イタリア語、日本語、ポルトガル語、簡体字中国語、およびスペイン語がサポートされています。

詳細については、[Read API の攻略ガイド](Vision-API-How-to-Topics/call-read-api.md)を参照してください。

> [!div class="nextstepaction"]
> [Read API v3.1 パブリック プレビュー 2 の詳細を確認する](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>2020 年 7 月

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Read API v3.1 のパブリック プレビューが簡体字中国語の OCR に対応
Computer Vision Read API v3.1 パブリック プレビューで、簡体字中国語のサポートが追加されました。

* この Read API のプレビュー バージョンでは、英語、オランダ語、フランス語、ドイツ語、イタリア語、ポルトガル語、簡体字中国語、およびスペイン語がサポートされています。

詳細については、[Read API の攻略ガイド](Vision-API-How-to-Topics/call-read-api.md)を参照してください。

> [!div class="nextstepaction"]
> [Read API v3.1 パブリック プレビュー 1 の詳細を確認する](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>2020 年 5 月
Computer Vision API v3.0 が一般提供になり、Read API が更新されています。

* 英語、オランダ語、フランス語、ドイツ語、イタリア語、ポルトガル語、およびスペイン語のサポート
* 精度の向上
* 抽出された各単語の信頼度スコア
* 新しい出力形式

詳細については、[OCR の概要](overview-ocr.md)に関するページを参照してください。

## <a name="march-2020"></a>2020 年 3 月

* TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

## <a name="january-2020"></a>2020 年 1 月

### <a name="read-api-30-public-preview"></a>Read API 3.0 パブリック プレビュー

Read API のバージョン3.0 を使用すると、印刷されたテキストまたは手書きテキストを画像から抽出できます。 以前のバージョンと比較して、3.0 では以下のものが提供されます。
* 精度の向上
* 新しい出力形式
* 抽出された各単語の信頼度スコア
* 言語パラメーターでのスペイン語と英語のサポート

3\.0 API の使用を開始するには、[テキストの抽出に関するクイックスタート](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3)に従ってください。

## <a name="cognitive-service-updates"></a>Cognitive Services の更新プログラム

[Cognitive Services に対する Azure 更新プログラムのお知らせ](https://azure.microsoft.com/updates/?product=cognitive-services)
