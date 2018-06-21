---
title: Azure Media Services によるクラウド内のエンコード | Microsoft Docs
description: このトピックでは、Azure Media Services を使用する場合のエンコード処理について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2018
ms.author: juliako
ms.openlocfilehash: e1c7536c59b110ae3dd753ff5f4b01195f8dadca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660090"
---
# <a name="encoding-with-azure-media-services"></a>Azure Media Services によるエンコード

Azure Media Services を使用すると、高品質なデジタル メディア ファイルをさまざまなブラウザーやデバイスで再生できる形式にエンコードできます。 たとえば、Apple の HLS または MPEG DASH 形式のコンテンツをストリーム配信することが必要な場合があります。 Media Services では、ビデオやオーディオのコンテンツを分析することもできます。 このトピックでは、Media Services v3 でコンテンツをエンコードする方法について説明します。

Media Services v3 でエンコードするには、変換とジョブを作成する必要があります。 変換では、エンコード設定と出力のレシピを定義します。ジョブはレシピのインスタンスです。 詳細については、「[Transform と Job](transform-concept.md)」を参照してください。

Azure Media Services でエンコードする場合、プリセットを使用してエンコーダーに入力メディア ファイルの処理方法を指示します。 たとえば、エンコードされたコンテンツに必要なビデオ解像度やオーディオ チャンネルの数を指定できます。 

業界のベスト プラクティスに基づいて推奨される組み込みのプリセットのいずれかを使用して、すぐに始めることができます。また、特定のシナリオやデバイスの要件を対象とするカスタム プリセットを作成することもできます。 

エンコーダーの詳細については、[OpenAPI の仕様](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview)を参照してください。 

## <a name="built-in-presets"></a>組み込みのプリセット

現在、Media Services は次の組み込みのエンコード プリセットをサポートしています。  

|**プリセット名**|**シナリオ**|**詳細**|
|---|---|---|
|**AudioAnalyzerPreset**|オーディオの分析|このプリセットは、音声の文字起こしなど、事前定義された一連の AI ベースの分析操作を適用します。 現在、プリセットは単一のオーディオ トラックでのコンテンツ処理をサポートしています。<br/>BCP-47 形式の 'language tag-region' (たとえば、'en-US') を使用して、入力のオーディオ ペイロードの言語を指定できます。 サポートされている言語は、'en-US'、'en-GB'、'es-ES'、'es-MX'、'fr-FR'、'it-IT'、'ja-JP'、'pt-BR'、'zh-CN' です。|
|**VideoAnalyzerPreset**|オーディオとビデオの分析|オーディオとビデオの両方から分析情報 (リッチ メタデータ) を抽出し、JSON 形式のファイルを出力します。 ビデオ ファイルを処理するときにオーディオの分析情報のみを抽出するかどうかを指定できます。 詳細については、[ビデオの分析](analyze-videos-tutorial-with-api.md)に関するページを参照してください。|
|**BuiltInStandardEncoderPreset**|ストリーミング|Standard Encoder で入力ビデオをエンコードする組み込みのプリセットの設定に使用されます。 <br/>現在サポートされているプリセットは次のとおりです。<br/>**EncoderNamedPreset.AdaptiveStreaming** (推奨)。 詳細については、[ビットレート ラダーの自動生成](autogen-bitrate-ladder.md)に関するページを参照してください。<br/>**EncoderNamedPreset.AACGoodQualityAudio** - 192kbps でエンコードされたステレオ オーディオのみを含む単一の MP4 ファイルを生成します。<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** - GOP がアラインメントされた、ビットレートが 6,000 kbps から 400 kbps、オーディオが AAC ステレオである 8 つの MP4 ファイルを生成します。 解像度の上限は 1080p で下限は 360p です。<br/>**EncoderNamedPreset.H264MultipleBitrate720p** - GOP がアラインメントされた、ビットレートが 3,400 kbps から 400 kbps、オーディオが AAC ステレオである 6 つの MP4 ファイルを生成します。 解像度の上限は 720p で下限は 360p です。<br/>**EncoderNamedPreset.H264MultipleBitrateSD** - GOP がアラインメントされた、ビットレートが 1,600 kbps から 400 kbps、オーディオが AAC ステレオである 5 つの MP4 ファイルを生成します。 解像度の上限は 480p で下限は 360p です。<br/><br/>詳細については、[ファイルのアップロード、エンコード、ストリーミング](stream-files-tutorial-with-api.md)に関するページを参照してください。|
|**StandardEncoderPreset**|ストリーミング|Standard Encoder を使用して入力ビデオをエンコードするときに使用する設定を記述します。 <br/>変換プリセットをカスタマイズする場合は、このプリセットを使用します。 詳細については、[変換プリセットをカスタマイズする方法](customize-encoder-presets-how-to.md)に関するページを参照してください。|

## <a name="custom-presets"></a>カスタム プリセット

Media Services では、特定のエンコーディング ニーズと要件を満たすようにプリセットのすべての値をカスタマイズできます。 変換プリセットをカスタマイズする場合は、**StandardEncoderPreset** プリセットを使用します。 詳細な説明と例については、[エンコーダー プリセットのカスタマイズ方法](customize-encoder-presets-how-to.md)に関するページを参照してください。

## <a name="scaling-encoding-in-v3"></a>v3 でのエンコードのスケーリング

現在は、Azure portal または AMS v2 API を使用して RU を設定する必要があります ([メディア処理のスケール設定](../previous/media-services-scale-media-processing-overview.md)に関するページを参照してください)。 

## <a name="next-steps"></a>次の手順

### <a name="tutorials"></a>チュートリアル

以下のチュートリアルでは、Media Services でコンテンツをエンコードする方法について説明します。

* [Azure Media Services を使用してアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
* [Azure Media Services を使用してビデオを分析する](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>コード サンプル

以下のコード サンプルには、Media Services でのエンコード方法を示すコードが含まれています。

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [CLI 2.0](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK

サポートされている以下の Media Services v3 SDK のいずれかを使用してコンテンツをエンコードできます。

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

