---
title: 診断ログの設定 - Azure Event Hub | Microsoft Docs
description: Azure のイベント ハブのアクティビティ ログおよび診断ログを設定する方法について説明します。
ms.topic: article
ms.date: 06/13/2021
ms.openlocfilehash: ecdd6d9bda9f468bf5ad1510971437838fb75ff8
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417201"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Azure イベント ハブの診断ログを設定する

Azure Event Hubs の 2 種類のログを表示できます。

* **[アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)** : これらのログには、ジョブで実行された操作に関する情報が含まれます。 このログは常に有効になっています。 アクティビティ ログ エントリを確認するには、Azure portal のイベント ハブ名前空間の左側のウィンドウで **[アクティビティ ログ]** を選択します。 次に例を示します。"名前空間の作成または更新"、"イベント ハブの作成または更新"。

    ![Event Hubs 名前空間のアクティビティ ログ](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[診断ログ](../azure-monitor/essentials/platform-logs-overview.md)** : 診断ログでは、API を使用して、または言語 SDK 上の管理クライアントを使用して、名前空間に対して実行された操作とアクションに関する詳細な情報が提供されます。 
    
    次のセクションでは、Event Hubs 名前空間の診断ログを有効にする方法について説明します。

## <a name="enable-diagnostic-logs"></a>Traffic Manager で診断ログを有効にする
既定では、診断ログは無効になっています。 診断ログを有効にするには、次の手順を実行します。

1.  [Azure portal](https://portal.azure.com) で Event Hubs 名前空間に移動します。 
2. 左側のウィンドウで **[監視]** の下にある **[診断設定]** を選択してから、 **[+ 診断設定の追加]** を選択します。 

    ![[診断設定] ページ - 診断設定の追加](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. **[Category details] (カテゴリの詳細)** セクションで、有効にする **診断ログの種類** を選択します。 これらのカテゴリの詳細については、この記事の後半で説明します。 
5. **[Destination details] (送信先の詳細)** セクションで、アーカイブ ターゲット (送信先) を設定します (例: ストレージ アカウント、イベント ハブ、Log Analytics ワークスペース)。

    ![診断設定の追加ページ](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  ツール バーの **[保存]** を選択して診断設定を保存します。

    新しい設定は、10 分ほどで有効になります。 その後、構成されたアーカイブ ターゲットのログが **[診断ログ]** ウィンドウに表示されます。

    診断の構成の詳細については、[Azure 診断ログの概要](../azure-monitor/essentials/platform-logs-overview.md)に関するページを参照してください。

## <a name="diagnostic-logs-categories"></a>診断ログのカテゴリ
[!INCLUDE [event-hubs-diagnostic-log-schema](./includes/event-hubs-diagnostic-log-schema.md)]



## <a name="next-steps"></a>次のステップ
- [Event Hubs の概要](./event-hubs-about.md)
- [Event Hubs サンプル](sdks.md)
- Event Hubs の使用
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
