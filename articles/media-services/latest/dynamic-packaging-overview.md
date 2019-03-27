---
title: Azure Media Services ダイナミック パッケージの概要 | Microsoft Docs
description: このトピックでは、Media Services でのダイナミック パッケージの概要について説明します。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d1d07402bca5f01cf63d0b039c085e46bb0f0d62
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447924"
---
# <a name="dynamic-packaging"></a>動的パッケージ

Microsoft Azure Media Services を使用して、多数のメディア ソース ファイルの形式、メディア ストリーミング形式、およびコンテンツ保護形式をさまざまなクライアント テクノロジ (iOS、XBOX など) に配信できます。 これらのクライアントは異なるプロトコルを認識します。たとえば、iOS では HTTP ライブ ストリーミング (HLS) 形式が必要で、Xbox ではスムーズ ストリーミングが必要です。 HLS、MPEG DASH、またはスムーズ ストリーミングを認識するクライアントに提供する、一連のアダプティブ ビットレート (マルチビット レート) MP4 (ISO Base Media 14496-12) ファイルまたは一連のアダプティブ ビットレート スムーズ ストリーミング ファイルがある場合、Media Services の動的パッケージを活用する必要があります。

動的パッケージを使用する場合、必要な操作は、一連のアダプティブ ビットレート MP4 ファイルを含むアセットの作成のみです。 そうすれば、マニフェストまたはフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。

次の図は、従来のエンコードおよび静的パッケージングのワークフローを示しています。

![静的なエンコード](./media/dynamic-packaging-overview/media-services-static-packaging.png)

次の図は、動的パッケージのワークフローを示しています。

![動的なエンコード](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="dynamic-packaging-workflow"></a>動的パッケージのワークフロー

1. 入力ファイル (中間ファイル) をアセットにアップロードします。 たとえば、H.264、MP4、または WMV などです (サポートされている形式の一覧については、「 [Media Encoder Standard の形式とコーデック](media-encoder-standard-formats.md)」を参照してください)。
2. Mezzanine ファイルを H.264 MP4 アダプティブ ビットレート セットにエンコードします。
3. アダプティブ ビットレート MP4 セットが含まれる資産を発行します。
4. コンテンツにアクセスしてストリーミングするストリーミング URL を構築します。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>ダイナミック パッケージによってサポートされているオーディオ コーデック

ダイナミック パッケージでは、[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC、HE-AAC v1、HE-AAC v2)、[Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3、E-AC3)、または [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express、DTS LBR、DTS HD、DTS HD Lossless) でエンコードされたオーディオを含む MP4 ファイルがサポートされています。

> [!NOTE]
> ダイナミック パッケージでは、[Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) オーディオ (古いコーデック) を含むファイルはサポートされていません。

## <a name="next-steps"></a>次の手順

[ビデオのアップロード、エンコード、およびストリーミング](stream-files-tutorial-with-api.md)
