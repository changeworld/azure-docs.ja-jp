---
title: コンテンツに対応したエンコード プリセットの使用方法
description: この記事では、Microsoft Azure Media Services v3 でコンテンツに対応したエンコード プリセットを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b6537547db2bb3371b6d7b4cd36ebf9ffc48530c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429829"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>コンテンツに対応したエンコード プリセットの使用方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>はじめに
[コンテンツに対応したエンコード プリセット](encode-content-aware-how-to.md)を使用すると、複数のビット レートでエンコードされる必要があるビデオに対する[アダプティブ ビットレート ストリーミング](encode-autogen-bitrate-ladder.md)でのコンテンツ配信の準備が向上します。これには、広範な評価分析をせずに、エンコーダーが特定の解像度に最適なビットレート値を求めるカスタム ロジックが含まれています。 このプリセットにより、アダプティブ ストリーミングでの配信に最適なレイヤー数、適切なビット レート、解像度の設定が決定されます。 

コンテンツに対応したエンコードは、アダプティブ ストリーミングのプリセットと比較し、出力ファイルのビットレートは低いが、表示エクスペリエンスの品質は良い、複雑度が低および中程度のビデオにより効果的です。

## <a name="use-the-content-aware-encoding-preset"></a>コンテンツに対応したエンコード プリセットを使用する
次のようにこのプリセットを使用する変換を作成することができます。

> [!NOTE]
> ContentAwareEncodingExperimental ではなく、必ず **ContentAwareEncoding** プリセットを使用してください。 または、HEVC でエンコードする場合は **H265ContentAwareEncoding** を使用できます。

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

変換の出力を使用するチュートリアルについては、「[次のステップ](#next-steps)」セクションを参照してください。 (チュートリアルに示されているように) 出力アセットは、MPEG-DASH や HLS などのプロトコルで Media Services ストリーミング エンドポイントから配信することができます。

> [!NOTE]
> `ContentAwareEncoding` プリセットを使用したエンコード ジョブは、出力時間 (分) のみに基づいて課金されます。 AMS では 2 パス エンコードが使用され、どのプリセットを使用した場合でも、[価格ページ](https://azure.microsoft.com/pricing/details/media-services/#overview)に記載されている以外の追加料金は発生しません。
  
## <a name="next-steps"></a>次のステップ

* [チュートリアル:Media Services v3 を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - REST](stream-files-tutorial-with-rest.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - CLI](stream-files-cli-quickstart.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - .NET](stream-files-dotnet-quickstart.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - Node.js](stream-files-nodejs-quickstart.md)
