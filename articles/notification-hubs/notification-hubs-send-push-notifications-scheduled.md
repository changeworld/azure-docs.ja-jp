---
title: スケジュール設定された通知を送信する方法 | Microsoft Docs
description: このトピックでは、Azure Notification Hubs でスケジュール設定された通知を使用する方法について説明します。
services: notification-hubs
documentationcenter: .net
keywords: プッシュ通知,プッシュ通知,プッシュ通知のスケジュール設定
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776979"
---
# <a name="how-to-send-scheduled-notifications"></a>方法: スケジュール設定された通知の送信
## <a name="overview"></a>概要
未来のある時点で通知を送信することが必要なシナリオに対して、通知を送信するためのバックエンド コードの簡単なウェイクアップ方法がないような場合、 Standard レベルの通知ハブは、通知を最大 7 日先までスケジュールできる機能をサポートしています。

通知を送信する場合は、次の例に示すように Notification Hubs SDK の [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) クラスを使用します。

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

また、既にスケジュール設定されている通知を、その notificationId を使用して取り消すこともできます。

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

スケジュール設定された、送信可能な通知の数に制限はありません。

