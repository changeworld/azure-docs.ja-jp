---
title: Media Encoder Standard を使用してオーバーレイを作成する方法
description: Media Encoder Standard を使用してオーバーレイを作成する方法について説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6d68f35cd7eff745a3d17009b65b1ed73a190173
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490252"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Media Encoder Standard を使用してオーバーレイを作成する方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard を使用すると、イメージ、オーディオ ファイル、その他のビデオを別のビデオにオーバーレイできます。 入力では、ファイルを 1 つだけ指定する必要があります。 JPG、PNG、GIF、または BMP 形式のイメージ ファイル、オーディオファイル (WAV、MP3、WMA、M4A ファイルなど)、またはビデオ ファイルを指定できます。


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

Transforms についてまだよく理解していない場合は、次のアクティビティを完了することをお勧めします。

* 「[Media Services を使用したビデオとオーディオのエンコード](encode-concept.md)」を読む
* 「[カスタム変換を使用してエンコードする方法 - .NET](transform-custom-presets-how-to.md)」を読む この記事の手順に従って、Transform を操作するために必要な .NET を設定してから、ここに戻り、オーバーレイのプリセット サンプルを試してみてください。
* [Transforms のリファレンス ドキュメント](/rest/api/media/transforms)を参照してください。

Transforms について理解したら、オーバーレイのサンプルをダウンロードします。

## <a name="overlays-preset-sample"></a>オーバーレイのプリセット サンプル

オーバーレイの使用を開始するには、[media-services-overlay サンプル](https://github.com/Azure-Samples/media-services-overlays)をダウンロードしてください。

## <a name="next-steps"></a>次の手順

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
