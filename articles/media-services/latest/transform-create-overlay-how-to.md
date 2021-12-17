---
title: 画像オーバーレイを作成する方法
description: 画像オーバーレイを作成する方法を説明します
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 532e26e486e758b3fd8079f6f61f3e00e82dbdca
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662306"
---
# <a name="how-to-create-an-image-overlay"></a>画像オーバーレイを作成する方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services を使用すると、画像、オーディオ ファイル、または別の動画を、動画の上にオーバーレイできます。 入力では、画像ファイルを 1 つだけ指定する必要があります。 JPG、PNG、GIF、または BMP 形式の画像ファイル、オーディオ ファイル (WAV、MP3、WMA、M4A ファイルなど)、またはサポートされるファイル形式の動画ファイルを指定できます。


## <a name="prerequisites"></a>前提条件

* サンプルの *appsettings.json* ファイルを構成するために必要なアカウント情報を収集します。 これを行う方法がわからない場合は、「[クイックスタート:Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-register-app.md)」を参照してください。 *appsettings.json* ファイルには、次の値が必要です。

```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
```

Transform の作成についてまだよく理解していない場合は、次のアクティビティを完了することをお勧めします。

* 「[Media Services を使用したビデオとオーディオのエンコード](encode-concept.md)」を読む
* 「[カスタム変換を使用してエンコードする方法 - .NET](transform-custom-presets-how-to.md)」を読む この記事の手順に従って、Transform を操作するために必要な .NET を設定してから、ここに戻り、オーバーレイのプリセット サンプルを試してみてください。
* [Transforms のリファレンス ドキュメント](/rest/api/media/transforms)を参照してください。

Transforms について理解したら、オーバーレイのサンプルをダウンロードします。

## <a name="overlays-preset-sample"></a>オーバーレイのプリセット サンプル

Media Services の .NET サンプル リポジトリをクローンします。

```bash
    git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

ソリューション フォルダーに移動し、Visual Studio Code または Visual Studio 2019 を起動します。

VideoEncoding フォルダーには、いくつかのエンコード サンプルが用意されています。 [VideoEncoding/Encoding_OverlayImage](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_OverlayImage) ソリューション フォルダーのプロジェクトを開き、オーバーレイの使用方法の学習を始めます。

サンプル プロジェクトには、2 つのメディア ファイルが含まれています。 動画ファイルと、動画の上にオーバーレイするロゴ画像です。
* ignite.mp4
* cloud.png

このサンプルでは、まず、CreateCustomTransform メソッドで動画の上に画像をオーバーレイできるカスタム Transform を作成します。  *[StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)* の *[Filters](/rest/api/media/transforms/create-or-update#filters)* プロパティを使用して、動画のオーバーレイの設定が含まれる新しい Filters コレクションを割り当てます。

[VideoOverlay](/rest/api/media/transforms/create-or-update#videooverlay) には、ジョブに送信されるジョブ入力ファイルの一覧からマップし、オーバーレイ画像または動画として使用するための適切な入力ソース ファイルを検索するために必要な、*InputLabel* というプロパティが含まれています。  ジョブを送信するときは、この同じラベル名を使用して、Transform での設定に一致させます。 このサンプルでは、文字列定数 *OverlayLabel* で示されているように、ラベル名として "logo" を使用しています。

次のコード スニペットは、オーバーレイを使用するために Transform の形式を設定する方法を示したものです。

```csharp
new TransformOutput
                {
                    Preset = new StandardEncoderPreset
                    {
                        Filters = new Filters
                        {
                            Overlays = new List<Overlay>
                            {
                                new VideoOverlay
                                {
                                    InputLabel = OverlayLabel,   // same as the one used in the JobInput to identify which asset is the overlay image
                                    Position = new Rectangle( "1200","670") // left, top position of the overlay in absolute pixel position relative to the source videos resolution. 
    
                                }
                            }
                        },
                        Codecs = new List<Codec>
                        {
                            new AacAudio
                            {
                            },
                            new H264Video
                            {
                                KeyFrameInterval = TimeSpan.FromSeconds(2),
                                Layers = new List<H264Layer>
                                {
                                    new H264Layer
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 1000000, // 1Mbps
                                        Width = "1280",
                                        Height = "720"
                                    },
                                    new H264Layer   // Adding a second layer to see that the image also is scaled and positioned the same way on this layer. 
                                    {
                                        Profile = H264VideoProfile.Baseline,
                                        Bitrate = 600000, // 600 kbps
                                        Width = "480",
                                        Height = "270"
                                    }
                                }
                            }
                        },
                        Formats = new List<Format>
                        {
                            new Mp4Format
                            {
                                FilenamePattern = "{Basename}_{Bitrate}{Extension}",
                            }
                        }
                    }
                }
```

Transform にジョブを送信するときは、最初に 2 つの入力資産を作成する必要があります。

* 資産 1 - このサンプルで作成される最初の資産は、ローカル動画ファイル "ignite.mp4" です。 この動画を合成の背景として使用し、ロゴ画像をその上にオーバーレイします。 
* 資産 2 - このサンプルの 2 番目の資産 (overlayImageAsset 変数に格納されています) には、ロゴに使用される .png ファイルが含まれています。 この画像が、エンコードの間に動画上に配置されます。

*SubmitJobAsync* メソッドでジョブを作成するときは、最初に List<> オブジェクトを使用して JobInput 配列を作成します。  この List には、2 つのソース資産への参照が含まれます。

上の Transform で定義されている Filter でオーバーレイとして使用する入力資産を識別して一致させるため、もう一度 "logo" というラベル名を使用して一致を処理します。 ラベル名が、.png 画像の JobInputAsset に追加されます。 これにより、オーバーレイ操作を行うときに使用する資産が Transform に指示されます。 この同じ Transform を、オーバーレイするさまざまなロゴやグラフィックが含まれる、Media Services に格納されているさまざまな資産で再利用できます。その場合は、ジョブに渡される資産名を変更するだけであり、Transform に対しては同じラベル名 "logo" を使用して一致させることができます。

``` csharp
    // Add both the Video and the Overlay image assets here as inputs to the job.
    List<JobInput> jobInputs = new List<JobInput>() {
        new JobInputAsset(assetName: inputAssetName),
        new JobInputAsset(assetName: overlayAssetName, label: OverlayLabel)
    };
```

Visual Studio Code の [実行とデバッグ] ウィンドウでプロジェクトを選択して、サンプルを実行します。 サンプルにより、エンコード操作の進行状況が出力され、最後に、プロジェクト ルートの /Output フォルダーにコンテンツがダウンロードされます。このフォルダーは、完全な Visual Studio の場合は、/bin/Output フォルダーになることがあります。 

サンプルでは、ストリーミング用のコンテンツも発行され、すべての互換プレーヤーで使用できる完全な HLS、DASH、Smooth Streaming のマニフェスト ファイルの URL も出力されます。  また、マニフェストの URL を [Azure Media Player デモ](http://ampdemo.azureedge.net/)にコピーし、/manifest で終わる URL を [URL] ボックスに貼り付けて、 *[Update Player]\(プレーヤーの更新\)* をクリックすることもできます。

## <a name="api-references"></a>API リファレンス

* [VideoOverlay オブジェクト](/rest/api/media/transforms/create-or-update#videooverlay)
* [フィルター](/rest/api/media/transforms/create-or-update#filters)
* [StandardEncoderPreset](/rest/api/media/transforms/create-or-update#standardencoderpreset)


[!INCLUDE [reference dotnet sdk references](./includes/reference-dotnet-sdk-references.md)]

## <a name="next-steps"></a>次の手順

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
