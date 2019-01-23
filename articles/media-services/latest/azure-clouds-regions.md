---
title: Azure Media Services v3 を使用できるクラウドとリージョン | Microsoft Docs
description: この記事では、Azure Media Services v3 を使用できる Azure クラウドとリージョンについて説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/11/2019
ms.author: juliako
ms.openlocfilehash: 8eb49010d89c3039f46e5c84cd305b7d0b5ca025
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306976"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Azure Media Services v3 が存在するクラウドとリージョン

Azure Media Services v3 は、Azure Resource Manager マニフェストを通して、グローバル Azure、Azure Government、Azure Germany、Azure China 21Vianet で使用できます。 ただし、すべての Azure クラウドで Media Services のすべての機能を使用できるわけではありません。 このドキュメントでは、Media Services v3 の主要コンポーネントの使用可能性を示します。

## <a name="feature-availability-in-azure-clouds"></a>Azure クラウドで使用できる機能

| 機能|グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | 使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| [StandardEncoderPreset](encoding-concept.md) | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| [LiveEvents](live-streaming-overview.md) | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| [StreamingEndpoints](streaming-endpoint-concept.md) | 使用可能 | 使用可能 | 使用可能 | 使用可能 |

## <a name="regions"></a>リージョン 

**location** パラメーターを指定する必要がある場合は、**location** 値としてリージョンのコード名を指定する必要があります。 お使いのアカウントが存在し、呼び出しがルーティングされるリージョンのコード名を取得するには、次の行を [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) で実行できます。

```bash
az account list-locations
```

上記の行を実行すると、すべての Azure リージョンの一覧が取得されます。 探している *displayName* がある Azure リージョンに移動し、その *name* 値を **location** パラメーターで使用します。

たとえば、Azure リージョンの米国西部 2 の場合 (下に示されています)、"westus2" を **location** パラメーターで使用します。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>次の手順

[Media Services v3 の概要](media-services-overview.md)
