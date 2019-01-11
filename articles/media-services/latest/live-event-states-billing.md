---
title: Azure Media Services における LiveEvent の状態と課金 | Microsoft Docs
description: このトピックでは、Azure Media Services における LiveEvent の状態と課金について、その概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 588aeede123848900fac6fab663dd1f6c6c169b6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719423"
---
# <a name="liveevent-states-and-billing"></a>LiveEvent の状態と課金

Azure Media Services の LiveEvent では、その状態が "**実行中**" に遷移するとすぐに課金が開始されます。 LiveEvent の課金を停止するには、LiveEvent を "停止" する必要があります。

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) の **LiveEventEncodingType** を Standard に設定した場合、入力フィードがなくなり、実行中の **LiveOutput**s がなくなってから 12 時間後に、まだ**実行中**状態の LiveEvent があれば、それらは Media Services によって自動的に停止されます。 ただし、LiveEvent が**実行中**状態だった期間については課金されます。

## <a name="states"></a>States

LiveEvent は、次の状態のいずれかになります。

|状態|説明|
|---|---|
|**Stopped**| これは、LiveEvent 作成後の初期状態です (自動開始が true に設定された場合を除く)。この状態では、課金は行われません。 この状態で、LiveEvent のプロパティを更新できますが、ストリーミングは許可されていません。|
|**開始中**| LiveEvent が開始され、リソースが割り当てられています。 この状態では、課金は行われません。 この状態の間は、更新もストリーミングもできません。 エラーが発生した場合は、LiveEvent は Stopped 状態に戻ります。|
|**実行中**| LiveEvent のリソースが割り当てられ、取り込み URL とプレビュー URL も生成済みで、ライブ ストリームを受信することができます。 この時点で、課金がアクティブになります。 それ以上の課金を停止するには、LiveEvent リソースの Stop を明示的に呼び出す必要があります。|
|**停止中**| LiveEvent が停止され、リソースのプロビジョニングが解除されています。 この遷移状態では、課金は行われません。 この状態の間は、更新もストリーミングもできません。|
|**削除中**。| LiveEvent を削除しています。 この遷移状態では、課金は行われません。 この状態の間は、更新もストリーミングもできません。|

## <a name="next-steps"></a>次の手順

- [ライブ ストリーミングの概要](live-streaming-overview.md)
- [ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)
