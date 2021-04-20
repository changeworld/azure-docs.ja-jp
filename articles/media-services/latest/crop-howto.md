---
title: Media Services でビデオ ファイルをトリミングする方法 - .NET | Microsoft Docs
description: トリミングは、ビデオ フレーム内で四角形のウィンドウを選択して、そのウィンドウ内のピクセルだけをエンコードするプロセスです。 このトピックでは、.NET を使用して Media Services でビデオ ファイルをトリミングする方法について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572258"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Media Services でビデオ ファイルをトリミングする方法 - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services を使用して、入力ビデオをトリミングできます。 トリミングは、ビデオ フレーム内で四角形のウィンドウを選択して、そのウィンドウ内のピクセルだけをエンコードするプロセスです。 次の図は、そのプロセスを示しています。

## <a name="pre-processing-stage"></a>前処理段階

トリミングは前処理段階であるため、エンコード プリセットの *トリミング パラメーター* は元の *入力* ビデオに適用されます。 エンコードは後処理段階です。幅/高さの設定は、元のビデオではなく *"前処理された"* ビデオに適用されます。 プリセットを設計するときは、次の手順を実行します。

1. 元の入力ビデオに基づいてトリミング パラメーターを選択します
1. トリミングされたビデオに基づいてエンコード設定を選択します。

> [!WARNING]
> エンコード設定とトリミングされたビデオが対応しないと、意図したとおりに出力されません。

たとえば、解像度が 1920 x 1080 ピクセル (アスペクト比は 16:9) の入力ビデオがあるとします。このビデオの左右には黒いバー (ピラー ボックス) があるため、実際のビデオが含まれるのは、4:3 のウィンドウ、つまり 1440 x 1080 ピクセルの範囲内だけです。 黒いバーをトリミングして、1440 x 1080 の領域をエンコードできます。

## <a name="transform-code"></a>変換のコード

次のコード スニペットは、NET で変換を記述してビデオをトリミングする方法を示しています。  このコードは、使用するローカル ファイルがあることを前提としています。

- Left は、トリミングの左端です。
- Top は、トリミングの一番上の位置です。
- Width は、トリミングの最終的な幅です。
- Height は、トリミングの最終的な高さです。

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
