---
title: SDK イベントをサブスクライブする
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用して、SDK イベントにサブスクライブします。
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 25f5575340ac35fa79ad7f8a1dc8e611a1820802
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699650"
---
# <a name="subscribe-to-sdk-events"></a>SDK イベントをサブスクライブする

Azure Communication Services SDK は動的であり、多数のプロパティが含まれています。 これらの変更が発生した場合、開発者は、変更が発生した時点と、変更内容が何であるかを知る必要があります。 ご覧ください。

::: zone pivot="platform-web"
[!INCLUDE [Events JavaScript](./includes/events/events-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Events Android](./includes/events/events-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Events iOS](./includes/events/events-ios.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ
- [通話のクイックスタートを試す](../../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [ビデオ通話のクイックスタートを試す](../../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- [プッシュ通知を有効にする方法について](./push-notifications.md)