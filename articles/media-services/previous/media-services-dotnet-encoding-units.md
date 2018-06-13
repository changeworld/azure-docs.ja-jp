---
title: エンコード ユニットの追加によるメディア処理のスケール調整 - Azure | Microsoft Docs
description: .NET を使用して、エンコード ユニットを追加する方法を説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 913ee05a68ec6f87ef658abd88935c7360b30626
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782971"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>.NET SDK を使用してエンコードを拡張する方法
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> 最新バージョンの Java SDK を入手し、Java での開発を始めるには、「[Azure Media Services 用 Java クライアント SDK の概要](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)」を参照してください。 <br/>
> Media Services 用の最新の PHP SDK をダウンロードするには、[Packagist リポジトリ](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)でバージョン 0.5.7 の Microsoft/WindowAzure パッケージを検索してください。  

## <a name="overview"></a>概要
> [!IMPORTANT]
> メディア処理のスケール設定について詳しくは、[概要](media-services-scale-media-processing-overview.md)に関するトピックを必ず確認してください。
> 
> 

.NET SDK を使用して予約ユニットの種類とエンコード予約ユニットの数を変更するには、以下の手順に従います。

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>サポート チケットを開く

既定で、各 Media Services アカウントは最大 10 個の S2/S3 メディア予約ユニット (MRU) または 25 個の S1 MRU と、5 個のオンデマンド ストリーミング予約ユニットを設定できます。 サポート チケットを開くと、上限の拡大を要求できます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

