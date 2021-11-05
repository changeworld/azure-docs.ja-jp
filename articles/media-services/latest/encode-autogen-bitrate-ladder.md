---
title: Media Services の標準エンコーダーを使用してビデオをエンコードする
description: このトピックでは、Media Services の標準エンコーダーを使用して、入力解像度とビットレートに基づく自動生成されたビットレート ラダーを持つ入力ビデオをエンコードする方法を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 058d80b85a9a669612c64de6f0f705f6d605e419
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065021"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>自動生成されたビットレート ラダーでエンコードする

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>概要

このトピックでは、Media Services の標準エンコーダーを使用して、入力ビデオを、入力解像度とビットレートに基づいて自動生成されたビットレート ラダーにエンコードする方法を示します。 この組み込みの設定 (プリセット) は、入力解像度とビットレートを超えることはありません。 たとえば、入力が 3Mbps で 720p の場合、出力は最高でも 720p のままになり、3Mbps よりも低い速度で開始されます。

### <a name="encoding-for-streaming"></a>ストリーミングのエンコード

**Transform** で **AdaptiveStreaming** または **H265AdaptiveStreaming** プリセットを使用すると、HLS や DASH などのストリーミング プロトコルでの配信に適した出力が得られます。 この 2 つのプリセットの 1 つを使用すると、どのビットレートと解像度で何層のビデオ レイヤーを生成するかが、サービスによりインテリジェントに決定されます。 出力コンテンツには、AAC でエンコードされたオーディオと H.264 でエンコードされたビデオ (AdaptiveStreaming プリセットの場合) または H.265/HEVC (H265AdaptiveStreaming プリセットの場合) のいずれかの MP4 ファイルが含まれます。 出力 MP4 ファイルはインターリーブされません。

このプリセットの使用方法の例については、[ファイルのストリーミング](stream-files-dotnet-quickstart.md)に関する記事を参照してください。

## <a name="output"></a>出力

このセクションでは **、AdaptiveStreaming**(H.264) または **H265AdaptiveStreaming** (HEVC) プリセットを使用したエンコードの結果として、Media Services エンコーダーによって生成される出力ビデオ レイヤーの 3 つの例を示します。 どの場合でも、出力には、128 kbps でエンコードされたステレオ音声を含む音声のみの MP4 ファイルが含まれます。

### <a name="example-1"></a>例 1
高さが "1080" でフレーム レートが "29.970" のソースからは、6 層のビデオ レイヤーが生成されます。

|レイヤー|[高さ]|幅|ビットレート (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>例 2
高さが "720" でフレーム レートが "23.970" のソースからは、5 層のビデオ レイヤーが生成されます。

|レイヤー|[高さ]|幅|ビットレート (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>例 3
高さが "360" でフレーム レートが "29.970" のソースからは、3 層のビデオ レイヤーが生成されます。

|レイヤー|[高さ]|幅|ビットレート (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|


## <a name="content-aware-encoding-comparison"></a>コンテンツに対応したエンコードの比較

[コンテンツに対応したエンコード プリセット](./encode-content-aware-concept.md)は、ラダーで使用する出力ビットレートと解像度の適切なセットを決定する前にソース コンテンツを分析することにより、アダプティブ ストリーミング プリセットよりも優れたソリューションを提供します。
アダプティブ ビットレート ストリーミング プリセットによって提供されるより静的で固定されたラダーを使用する前に、まず[コンテンツに対応したエンコード プリセット](./encode-content-aware-concept.md)を試してみることをお勧めします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ファイルをストリーム配信する](stream-files-dotnet-quickstart.md)
> [コンテンツに対応したエンコード プリセットを使用する](./encode-content-aware-concept.md)
> [コンテンツに対応したエンコード プリセットの使用方法](./encode-content-aware-how-to.md)
