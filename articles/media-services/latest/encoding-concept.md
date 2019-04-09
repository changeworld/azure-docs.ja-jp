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
ms.date: 02/27/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: de2c60d4449762c4a8fcc3e2f486130f3df37c7c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243621"
---
# <a name="encoding-with-media-services"></a>Media Services でのエンコード

Azure Media Services を使用して、高品質なデジタル メディア ファイルをアダプティブ ビットレート MP4 ファイルにエンコードして、さまざまなブラウザーやデバイスで再生できるようにします。 Media Services のエンコード ジョブが成功すると、アダプティブ ビットレート MP4 とストリーミング構成ファイルのセットを含む出力アセットが作成されます。 構成ファイルには、.ism、.ismc、.mpi、および修正すべきではないその他のファイルが含まれます。 エンコード ジョブが終わったら、[ダイナミック パッケージ](dynamic-packaging-overview.md)を利用して、ストリーミングを開始できます。

出力アセット内の動画をクライアントが再生できるようにするには、**ストリーミング ロケーター**を作成し、ストリーミング URL をビルドする必要があります。 次に、マニフェストに指定された形式に基づいて、クライアントによって選択されたプロトコルでストリームがクライアントに送信されます。

次の図は、ダイナミック パッケージのワークフローを使用したオンデマンド ストリーミングを示しています。

![ダイナミック パッケージ](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

このトピックでは、Media Services v3 でコンテンツをエンコードする方法について説明します。

## <a name="transforms-and-jobs"></a>変換およびジョブ

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

## <a name="scaling-encoding-in-v3"></a>v3 でのエンコードのスケール

メディア処理のスケール設定を行うには、[CLI を使用したスケーリング](media-reserved-units-cli-how-to.md)に関するトピックをご覧ください。

## <a name="next-steps"></a>次の手順

* [組み込みのプリセットを使用して HTTPS の URL をエンコードする](job-input-from-http-how-to.md)
* [組み込みのプリセットを使用してローカル ファイルをエンコードする](job-input-from-local-file-how-to.md)
* [ご自分の特定のシナリオまたはデバイス要件に対応するカスタム プリセットを構築する](customize-encoder-presets-how-to.md)
* [Media Services を使用してアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
