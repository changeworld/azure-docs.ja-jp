---
title: Media Services によるクラウド内のエンコード - Azure | Microsoft Docs
description: このトピックでは、Azure Media Services を使用する場合のエンコード処理について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7c16fc61a8654fa6b7236b9c5252ed4874787d50
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141749"
---
# <a name="encoding-with-media-services"></a>Media Services でのエンコード

Azure Media Services を使用すると、高品質なデジタル メディア ファイルをさまざまなブラウザーやデバイスで再生できる形式にエンコードできます。 たとえば、Apple の HLS または MPEG DASH 形式のコンテンツをストリーム配信することが必要な場合があります。 このトピックでは、Media Services v3 でコンテンツをエンコードする方法について説明します。

Media Services v3 でエンコードするには、変換とジョブを作成する必要があります。 変換では、エンコード設定と出力のレシピを定義します。ジョブはレシピのインスタンスです。 詳細については、「[Transform と Job](transform-concept.md)」を参照してください。

Media Services でエンコードする場合、プリセットを使用してエンコーダーに入力メディア ファイルの処理方法を指示します。 たとえば、エンコードされたコンテンツに必要なビデオ解像度やオーディオ チャンネルの数を指定できます。 

業界のベスト プラクティスに基づいて推奨される組み込みのプリセットのいずれかを使用して、すぐに始めることができます。また、特定のシナリオやデバイスの要件を対象とするカスタム プリセットを作成することもできます。 詳細については、[カスタム変換を使用するエンコード](customize-encoder-presets-how-to.md)に関するページを参照してください。 

## <a name="built-in-presets"></a>組み込みのプリセット

現在、Media Services は次の組み込みのエンコード プリセットをサポートしています。  

|**プリセット名**|**シナリオ**|**詳細**|
|---|---|---|
|**BuiltInStandardEncoderPreset**|ストリーミング|Standard Encoder で入力ビデオをエンコードする組み込みのプリセットの設定に使用されます。 <br/>現在サポートされているプリセットは次のとおりです。<br/>**EncoderNamedPreset.AdaptiveStreaming** (推奨)。 詳細については、[ビットレート ラダーの自動生成](autogen-bitrate-ladder.md)に関するページを参照してください。<br/>**EncoderNamedPreset.AACGoodQualityAudio** - 192kbps でエンコードされたステレオ オーディオのみを含む単一の MP4 ファイルを生成します。<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** - GOP がアラインメントされた、ビットレートが 6,000 kbps から 400 kbps、オーディオが AAC ステレオである 8 つの MP4 ファイルを生成します。 解像度の上限は 1080p で下限は 360p です。<br/>**EncoderNamedPreset.H264MultipleBitrate720p** - GOP がアラインメントされた、ビットレートが 3,400 kbps から 400 kbps、オーディオが AAC ステレオである 6 つの MP4 ファイルを生成します。 解像度の上限は 720p で下限は 360p です。<br/>**EncoderNamedPreset.H264MultipleBitrateSD** - GOP がアラインメントされた、ビットレートが 1,600 kbps から 400 kbps、オーディオが AAC ステレオである 5 つの MP4 ファイルを生成します。 解像度の上限は 480p で下限は 360p です。<br/><br/>詳細については、[ファイルのアップロード、エンコード、ストリーミング](stream-files-tutorial-with-api.md)に関するページを参照してください。|
|**StandardEncoderPreset**|ストリーミング|Standard Encoder を使用して入力ビデオをエンコードするときに使用する設定を記述します。 <br/>変換プリセットをカスタマイズする場合は、このプリセットを使用します。 詳細については、[変換プリセットをカスタマイズする方法](customize-encoder-presets-how-to.md)に関するページを参照してください。|

## <a name="custom-presets"></a>カスタム プリセット

Media Services では、特定のエンコーディング ニーズと要件を満たすようにプリセットのすべての値をカスタマイズできます。 変換プリセットをカスタマイズする場合は、**StandardEncoderPreset** プリセットを使用します。 詳細な説明と例については、[エンコーダー プリセットのカスタマイズ方法](customize-encoder-presets-how-to.md)に関するページを参照してください。

## <a name="scaling-encoding-in-v3"></a>v3 でのエンコードのスケーリング

現在は、Azure portal または Media Services v2 API を使用して RU を設定する必要があります ([メディア処理のスケーリング](../previous/media-services-scale-media-processing-overview.md)に関するページの説明に従います)。 

## <a name="next-steps"></a>次の手順

### <a name="tutorials"></a>チュートリアル

以下のチュートリアルでは、Media Services でコンテンツをエンコードする方法について説明します。

* [Media Services を使用してアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)

### <a name="code-samples"></a>コード サンプル

以下のコード サンプルには、Media Services でのエンコード方法を示すコードが含まれています。

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [Azure CLI](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK

サポートされている以下の Media Services v3 SDK のいずれかを使用してコンテンツをエンコードできます。

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

