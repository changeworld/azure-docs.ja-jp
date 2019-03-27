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
ms.date: 02/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 52e7fdf6de25300d4f78ee9822aca4ad83f646e9
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408427"
---
# <a name="encoding-with-media-services"></a>Media Services でのエンコード

Azure Media Services を使用すると、高品質なデジタル メディア ファイルをさまざまなブラウザーやデバイスで再生できる形式にエンコードできます。 たとえば、Apple の HLS または MPEG DASH 形式のコンテンツをストリーム配信することが必要な場合があります。 このトピックでは、Media Services v3 でコンテンツをエンコードする方法について説明します。

Media Services v3 でエンコードするには、[変換](https://docs.microsoft.com/rest/api/media/transforms)と[ジョブ](https://docs.microsoft.com/rest/api/media/jobs)を作成する必要があります。 変換では、エンコード設定と出力のレシピを定義します。ジョブはレシピのインスタンスです。 詳細については、「[Transform と Job](transforms-jobs-concept.md)」を参照してください。

Media Services でエンコードする場合、プリセットを使用してエンコーダーに入力メディア ファイルの処理方法を指示します。 たとえば、エンコードされたコンテンツに必要なビデオ解像度やオーディオ チャンネルの数を指定できます。 

業界のベスト プラクティスに基づいて推奨される組み込みのプリセットのいずれかを使用して、すぐに始めることができます。また、特定のシナリオやデバイスの要件を対象とするカスタム プリセットを作成することもできます。 詳細については、[カスタム変換を使用するエンコード](customize-encoder-presets-how-to.md)に関するページを参照してください。 

2019 年 1 月より、Media Encoder Standard でエンコードして MP4 ファイルを生成すると、新しい .mpi ファイルが生成され、出力アセットに追加されます。 この MPI ファイルの目的は、[ダイナミック パッケージ](dynamic-packaging-overview.md)とストリーミングのシナリオのパフォーマンスを向上させることです。

> [!NOTE]
> MPI ファイルを変更または削除したり、サービスにこのようなファイルが存在するかどうかに依存しないようにしてください。

## <a name="built-in-presets"></a>組み込みのプリセット

現在、Media Services は次の組み込みのエンコード プリセットをサポートしています。  

### <a name="builtinstandardencoderpreset-preset"></a>BuiltInStandardEncoderPreset プリセット

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) は、Standard Encoder で入力ビデオをエンコードする組み込みのプリセットの設定に使用されます。 

現在サポートされているプリセットは次のとおりです。

- **EncoderNamedPreset.AdaptiveStreaming** (推奨)。 詳細については、[ビットレート ラダーの自動生成](autogen-bitrate-ladder.md)に関するページを参照してください。
- **EncoderNamedPreset.AACGoodQualityAudio** - 192kbps でエンコードされたステレオ オーディオのみを含む単一の MP4 ファイルを生成します。
- **EncoderNamedPreset.H264MultipleBitrate1080p** - GOP がアラインメントされた、ビットレートが 6,000 kbps から 400 kbps、オーディオが AAC ステレオである 8 つの MP4 ファイルを生成します。 解像度の上限は 1080p で下限は 360p です。
- **EncoderNamedPreset.H264MultipleBitrate720p** - GOP がアラインメントされた、ビットレートが 3,400 kbps から 400 kbps、オーディオが AAC ステレオである 6 つの MP4 ファイルを生成します。 解像度の上限は 720p で下限は 360p です。
- **EncoderNamedPreset.H264MultipleBitrateSD** - GOP がアラインメントされた、ビットレートが 1,600 kbps から 400 kbps、オーディオが AAC ステレオである 5 つの MP4 ファイルを生成します。 解像度の上限は 480p で下限は 360p です。<br/><br/>詳細については、[ファイルのアップロード、エンコード、ストリーミング](stream-files-tutorial-with-api.md)に関するページを参照してください。

### <a name="standardencoderpreset-preset"></a>StandardEncoderPreset プリセット

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) には、Standard Encoder を使用して入力ビデオをエンコードするときに使用する設定を記述します。 変換プリセットをカスタマイズする場合は、このプリセットを使用します。 

#### <a name="custom-presets"></a>カスタム プリセット

Media Services では、特定のエンコーディング ニーズと要件を満たすようにプリセットのすべての値をカスタマイズできます。 変換プリセットをカスタマイズする場合は、**StandardEncoderPreset** プリセットを使用します。 詳細な説明と例については、[エンコーダー プリセットのカスタマイズ方法](customize-encoder-presets-how-to.md)に関するページを参照してください。

## <a name="scaling-encoding-in-v3"></a>v3 でのエンコードのスケーリング

現在は、Azure portal または Media Services v2 API を使用して RU を設定する必要があります ([メディア処理のスケーリング](../previous/media-services-scale-media-processing-overview.md)に関するページの説明に従います)。 

## <a name="next-steps"></a>次の手順

* [Transform と Job](transforms-jobs-concept.md)
* [Media Services を使用してアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
