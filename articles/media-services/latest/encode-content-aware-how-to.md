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
ms.date: 09/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 09d3f199cc17cc1457413f673afe3c6be0646f58
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668623"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>コンテンツに対応したエンコード プリセットの使用方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>はじめに
[コンテンツに対応したエンコード プリセット](encode-content-aware-concept.md)を使用すると、複数のビット レートでエンコードされる必要があるビデオに対する[アダプティブ ビットレート ストリーミング](encode-autogen-bitrate-ladder.md)でのコンテンツ配信の準備が向上します。これには、広範な評価分析をせずに、エンコーダーで特定の解像度に最適なビットレート値のレンダリングを可能にするカスタム ロジックが含まれています。 このプリセットにより、アダプティブ ストリーミングでの配信に最適なレイヤー数、適切なビット レート、解像度の設定が決定されます。

コンテンツに対応したエンコードは、アダプティブ ストリーミングのプリセットと比較し、出力ファイルのビットレートは低いが、表示エクスペリエンスの品質は良い、複雑度が低および中程度のビデオにより効果的です。

### <a name="using-the-content-aware-encoding-presets"></a>コンテンツに対応したエンコード プリセットを使用する

コード内でコンテンツ対応プリセットを使用してエンコードするには、[BuiltInStandardEncoderPreset](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset) オブジェクトを使用し、[PresetName](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset.presetname) プロパティを次の組み込みのプリセット名の 1 つに設定します。

- **ContentAwareEncoding** - このプリセットは H.264 に対応しています。
- **H265ContentAwareEncoding** - このプリセットは HEVC (H.265) に対応しています。

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

## <a name="configure-output-settings"></a>出力設定の構成

さらに、開発者は、コンテンツ対応エンコード プリセットで、アダプティブ ビットレート ストリーミング ラダーのエンコードに最適な設定を決定する際に使用される出力の範囲を制御することもできます。

開発者は、**PresetConfigurations** クラスを使用することで、一連の制約とオプションをコンテンツ対応エンコード プリセットに渡して、エンコーダーによって生成される結果ファイルを制御できます。 プロパティは、エンコード ジョブのエクスペリエンスまたはコストを制御するために、すべてのエンコードを特定の最大解像度に制限する場合に特に役立ちます。  また、モバイル ネットワークや帯域幅の制約があるグローバル リージョンで視聴者がサポートできる最大および最小ビットレートを制御できる点も便利です。

PresetConfigurations クラスを使用すると、コンテンツ対応エンコード プリセットで次の設定を調整できます。 


| プロパティ                   | 説明                                                                                                                                                                         |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 複雑さ              | [速度]、[バランス]、または [品質]。 エンコーダーの設定を構成して、速度と品質のバランスを制御できます。 例: 圧縮効率を低くしてエンコードを高速化するには、[複雑さ] を [速度] に設定する |
| interleaveOutput          |    出力 MP4 ファイルのフォーマット方法を制御します。  個別に共有しやすくするために、オーディオとビデオの両方とインターリーブすることも、追加言語のオーディオ トラックを遅延バインディングしたり、後でオーディオを変更したりしやすくするために、別個のオーディオ ファイルとビデオ ファイルにすることもできます。                                                                                                                                                                                 |
| keyFrameIntervalInSeconds |        ファイルのエンコード時に各画像グループ (GOP) に使用されるキー フレームの距離を設定します。  一般的な最新のアダプティブ ストリーミング プロトコルでは、2 秒が使用されます。  配信するネットワーク条件によっては、より長い間隔を使用するほうがよい場合があります。 お使いの CDN プロバイダーに確認するか、独自のネットワークでいくつか実験してみてください。                                                                                                                                                                             |
| maxBitrateBps             |               アダプティブ ストリーミング ラダーの最大ビットレートを制限します。  この制約は、ストレージのコストとネットワーク配信のコストを制御するのに役立ちます。  また、これにより、配信されたビットレートをクライアントでサポートされている範囲内で維持できます。                                                                                                                                                                       |
| maxHeight                 |       アダプティブ ラダーでエンコードが許可される最大解像度。  これは、コストを制御したり、対象ユーザーのエクスペリエンスを制御したりする場合にも役立ちます。 すべてのエンコードを標準解像度に制限するか、必要に応じて最大 720P に制限できます。                                                                                                                                                                              |
| maxLayers                 |        このプロパティは、アダプティブ ストリーミング ラダー内のレイヤーの数を制御します。 また、これにより定義上、ストレージ コンテナーに MP4 として出力されるファイルの数も制御されます。  この値を下げると、生成される出力の表示が少なくなり、エンコード時間 (分) の合計が削減されるので、コストも削減されます。 これを maxHeight や maxBitrateBps と組み合わせて使用することで、エンコード中のコストを管理し、課金をより予測可能にすることができます。                                                                                                                                                                             |
| minBitrateBps             |                           これにより、エンコーダーで選択される最も低いビットレートが制御されます。  これは、低ビットレートと低解像度のレンディションの品質を最適化するのに使用します。                                                                                                                                                         |
| minHeight                 |     この設定は、アダプティブ ビットレート ラダーによって生成される最小解像度を制御します。 これは、エンコードするコンテンツの種類に対して、不鮮明すぎる可能性がある非常に低解像度のレンディションがクライアントによって選択されないようにするのに役立ちます。 これは、提供するエクスペリエンスの品質を高めるのに大いに役立ちます。                                                                                                                                                                                |
|

### <a name="example-code-for-configuring-content-aware-preset"></a>コンテンツ対応プリセットを構成するためのコード例

次のコード スニペットは、サンプル「[コンテンツ対応、H.246、および制約を使用したエンコード](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained)」からのものです。

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/Encoding_H264_ContentAware_Constrained/Program.cs#PresetConfigurations)]

## <a name="samples"></a>サンプル

次のサンプルでは、使用できるさまざまなコーデックと制約、プリセットをインスタンス化する方法、エンコード ジョブを送信および監視する方法を示します。

### <a name="net"></a>.NET

| サンプル | 説明|
|---------|------------------|
| [コンテンツ対応と H.246 を使用したエンコード](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | 制約のない H.264 コンテンツ対応エンコードの最も基本的な使用方法を示します |
| [コンテンツ対応、H.246、および制約を使用したエンコード](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | PresetConfigurations クラスを使用して、プリセットの出力動作を制限する方法を示します|
| [コンテンツ対応と HEVC (H.265) を使用したエンコード](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | コンテンツ対応エンコードを使用した、制約のない HEVC コーデックの基本的な使用方法を示します。  PresetConfigurations クラスは HEVC でもサポートされ、このサンプルに追加できます。|

> [!NOTE]
> `ContentAwareEncoding` プリセットを使用したエンコード ジョブは、出力時間 (分) のみに基づいて課金されます。 AMS では 2 パス エンコードが使用され、どのプリセットを使用した場合でも、[価格ページ](https://azure.microsoft.com/pricing/details/media-services/#overview)に記載されている以外の追加料金は発生しません。
  
## <a name="next-steps"></a>次のステップ

* [チュートリアル:Media Services v3 を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - REST](stream-files-tutorial-with-rest.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - CLI](stream-files-cli-quickstart.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - .NET](stream-files-dotnet-quickstart.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - Node.js](stream-files-nodejs-quickstart.md)
