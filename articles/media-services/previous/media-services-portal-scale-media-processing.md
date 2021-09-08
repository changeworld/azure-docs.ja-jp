---
title: Azure Portal を使用したメディア処理のスケール設定 | Microsoft Docs
description: このチュートリアルでは、Azure Portal を使用したメディア処理のスケール設定の手順について説明します。
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1040ffa8bc3af3734a5c37961676b0914f72e991
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835873"
---
# <a name="change-the-reserved-unit-type"></a>予約ユニットの種類の変更

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概要

既定では、メディア占有ユニットを使用する必要がなくなり、Azure Media Services でサポートされなくなりました。 互換性のために、現在の Azure portal には、MRU を管理およびスケーリングするためのオプションがあります。 ただし既定では、エンコードのコンカレンシーやパフォーマンスを制御するために設定した MRU 構成は使用されません。

> [!IMPORTANT]
> メディア処理のスケール設定の詳細については、 [概要](media-services-scale-media-processing-overview.md) に関するトピックを必ず確認してください。

## <a name="scale-media-processing"></a>メディア処理のスケール設定
>[!NOTE]
>MRU を選択しても、Azure Media Services V3 のコンカレンシーやパフォーマンスに影響はありません。 

予約ユニットの種類と予約ユニットの数を変更するには、以下の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで **[メディア占有ユニット]** をクリックします。
   
    選択した予約ユニットの種類の予約ユニット数を変更するには、画面上部にある **[Media Served Units]\(メディア予約ユニット\)** スライダーを使用します。
   
    **[占有ユニットの種類]** を変更するには、 **[Speed of reserved processing units]\(占有処理ユニットの速度\)** バーをクリックします。 その後、次の必要な価格レベルを選択します。S1、S2、S3。
   
3. [保存] ボタンを押して、変更を保存します。
   
    [保存] をクリックすると、新しい予約ユニットが割り当てられます。

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
