---
title: エンコード ユニットの追加によるメディア処理のスケール調整 - Azure | Microsoft Docs
description: この記事では、Azure Media Services .NET を使用してエンコード ユニットを追加する方法について説明します。
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.reviewer: milangada
ms.custom: devx-track-csharp
ms.openlocfilehash: b7b7cce9fd33e14175e03486499ec93997617683
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835340"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>.NET SDK を使用してエンコードを拡張する方法

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## <a name="overview"></a>概要
> [!IMPORTANT]
> 既定では、メディア占有ユニットを使用する必要がなくなり、Azure Media Services でサポートされなくなりました。 メディア処理のスケール設定について詳しくは、[概要](media-services-scale-media-processing-overview.md)に関するトピックを必ず確認してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
