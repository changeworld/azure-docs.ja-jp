---
title: Azure Portal を使用したサービス正常性通知の表示
description: サービス正常性通知を使用すると、Microsoft Azure によって発行されるサービスの正常性に関するメッセージを表示できます。
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069350"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal を使用したサービス正常性通知の表示

サービス正常性通知は、Azure インフラストラクチャから発行されます。 お客様のサブスクリプションに属するリソースに関する情報が含まれています。 これらの通知はアクティビティ ログのイベントのサブクラスであるため、アクティビティ ログでも確認できます。 サービス正常性通知には、クラスに応じて、情報のみの通知とアクションが必要になる通知があります。

さまざまなクラスのサービス正常性通知の詳細については、[サービス正常性通知のプロパティ](../../service-health/service-health-notifications-properties.md)に関するページを参照してください。

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal でのサービス正常性通知の表示

1. [Azure Portal](https://portal.azure.com) で、 **[モニター]** を選択します。

    ![Azure Portal メニューのスクリーンショット。[モニター] が選択されている](./media/service-notifications/home-monitor.png)

    Azure Monitor は、すべての監視設定とデータが 1 つにまとめられた統合ビューです。 最初に開くのは **[アクティビティ ログ]** セクションです。

1. **[Alerts]** (アラート) を選択します。

    ![モニターのアクティビティ ログのスクリーンショット。[アラート] が選択されている](./media/service-notifications/service-health-summary.png)

1. **[+ アクティビティ ログ アラートの追加]** を選択し、今後サービスの通知を受け取るようにアラートを設定します。 詳細については、「[サービス通知のアクティビティ ログ アラートを作成する](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [サービス正常性通知が投稿されるたびにアラートを受け取る](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。  
* [アクティビティ ログのアラート](../../azure-monitor/platform/activity-log-alerts.md)について詳しく学習します。
