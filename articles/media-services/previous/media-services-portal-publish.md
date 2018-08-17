---
title: Azure Portal でのコンテンツの発行 | Microsoft Docs
description: このチュートリアルでは、Azure Portal を使用してコンテンツを発行する手順について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: d93bfc548134f730f4fad49a37593c861d6b6cbb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785771"
---
# <a name="publish-content-in-the-azure-portal"></a>Azure Portal でコンテンツを発行する
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>概要
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 
> 

コンテンツのストリーミングまたはダウンロードに使用できる URL をユーザーに提供するには、まず、ロケーターを作成してアセットを発行する必要があります。 資産ファイルには、ロケーターを通じてアクセスできます。 Azure Media Services では、次の 2 種類のロケーターがサポートされています。  

* **ストリーミング (OnDemandOrigin) ロケーター**。 ストリーミング ロケーターは、アダプティブ ストリーミングに使用されます。 アダプティブ ストリーミングの例としては、Apple HTTP Live Streaming (HLS)、Microsoft Smooth Streaming、Dynamic Adaptive Streaming over HTTP (DASH。MPEG-DASH とも呼ばれます) などがあります。 ストリーミング ロケーターを作成するには、アセットに .ism ファイルが含まれている必要があります。 たとえば、「 http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest 」のように入力します。
* **プログレッシブ (Shared Access Signature) ロケーター**。 プログレッシブ ロケーターは、プログレッシブ ダウンロードを通じてビデオを配信する場合に使用されます。

HLS ストリーミング URL を作成するには、次のように、*(format=m3u8-aapl)* を URL に追加します。

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Smooth Streaming アセットを再生するためのストリーミング URL を作成するには、次の URL 形式を使用します。

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

MPEG-DASH ストリーミング URL を作成するには、*(format=mpd-time-csf)* を URL に追加します。

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

共有アクセス署名 URL の形式は、次のとおりです。

    {blob container name}/{asset name}/{file name}/{shared access signature}

詳細については、[コンテンツ配信の概要](media-services-deliver-content-overview.md)に関する記事を参照してください。

> [!NOTE]
> 2015 年 3 月より前に Azure Portal で作成されたロケーターには、2 年間の有効期限があります。  
> 
> 

ロケーターの有効期限を更新するには、[REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) または [.NET API](http://go.microsoft.com/fwlink/?LinkID=533259) を使用することができます。 

> [!NOTE]
> Shared Access Signature ロケーターの有効期限を更新すると、URL が変更されます。

### <a name="to-use-the-portal-to-publish-an-asset"></a>ポータルを使用してアセットを発行するには
1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** > **[アセット]** を参照してください。 発行するアセットを選択します。
3. **[発行]** を選択します。
4. ロケーターの種類を選択します。
5. **[追加]** を選択します。
   
    ![ビデオを発行する](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL が **[発行された URL]** の一覧に追加されます。

## <a name="play-content-in-the-portal"></a>ポータル内でコンテンツを再生する
Azure Portal のコンテンツ プレーヤーでビデオをテストできます。

ビデオを選択し、**[再生]** を選択します。

![Azure Portal でビデオを再生する](./media/media-services-portal-vod-get-started/media-services-play.png)

いくつかの考慮事項が適用されます。

* ビデオが発行されたことを確認します。
* Azure Portal のメディア プレーヤーは、既定のストリーミング エンドポイントから再生を行います。 既定以外のストリーミング エンドポイントから再生する場合は、URL を選択してコピーし、別のプレーヤーに貼り付けます。 たとえば、[Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) でビデオをテストすることができます。
* ストリーミング元となるストリーミング エンドポイントが実行されている必要があります。  

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次の手順
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

