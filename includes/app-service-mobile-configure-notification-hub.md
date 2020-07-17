---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181678"
---
Azure App Service の Mobile Apps 機能は [Azure Notification Hubs] を使用してプッシュを送信するため、ここではモバイル アプリの通知ハブを構成します。

1. [Azure Portal] で、 **[App Services]** に移動し、アプリ バックエンドを選択します。 **[設定]** で **[プッシュ]** を選択します。
2. 通知ハブ リソースをアプリに追加するには、 **[接続]** を選択します。 ハブを作成することも、既存のハブに接続することもできます。

    ![ハブの構成](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

これで、通知ハブが Mobile Apps のバックエンド プロジェクトに接続されました。 後でこの通知ハブを構成して、デバイスにプッシュ通知を送信するプラットフォーム通知システム (PNS) に接続します。

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
