---
title: Azure VMware Solution の VMware syslog を構成する
description: Azure VMware Solution プライベート クラウドの VMware syslog を収集するための診断設定の構成方法について説明します。
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 48422f6c0769953cee20c373ade8b8056ff0fee7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892774"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Azure VMware Solution の VMware syslog を構成する

リソースのプラットフォーム ログとメトリックを自分が選んだ宛先にストリーム配信するためのエクスポートは、診断設定を使用して構成します。 異なるログとメトリックを個別の宛先に送信するために、最大 5 つの診断設定を作成できます。 

この記事では、Azure VMware Solution プライベート クラウドの VMware syslog を収集するための診断設定の構成方法について説明します。 その syslog をストレージ アカウントに格納して、vCenter のログを確認したり、診断目的で分析したりします。 

## <a name="prerequisites"></a>前提条件

vCenter と NSX-T Manager のインターフェイスへのアクセスが可能な Azure VMware Solution プライベート クラウドが必要となります。 

## <a name="configure-diagnostic-settings"></a>診断設定を構成する

1. Azure VMware Solution プライベート クラウドから **[診断設定]** を選択し、 **[Add diagnostic settings]\(診断設定の追加\)** を選択します。
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="VMware syslog の構成画面を示すスクリーンショット。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. **vmwaresyslog** と **AllMetrics** を選択し、提示されたオプションのいずれかを選択します。

   >[!IMPORTANT]
   >**[Send to log analytics workspace]\(Log Analytics ワークスペースに送信する\)** オプションは現在利用できません。
 
   ### <a name="archive-to-storage-account"></a>ストレージ アカウントへのアーカイブ

    1. **[診断設定]** で、ログの格納先とするストレージ アカウントを選択し、 **[保存]** を選択します。

       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="syslog の格納に関して選択するオプションを示すスクリーンショット。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

    1. 自分の **ストレージ アカウント** に移動し、**Insight logs vmwarelog** が作成されていることを確認し、それを選択します。 
 
       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="作成されて選択できる状態になった Insight logs vmwarelog オプションを示すスクリーンショット。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


    1. **Insight logs vmwarelog** の中から json ファイルを見つけてダウンロードし、ログを確認します。

       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="json ファイルのドリルダウン パスを示すスクリーンショット。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

   ### <a name="stream-to-microsoft-azure-event-hubs"></a>Microsoft Azure Event Hubs へのストリーム

    1. **[診断設定]** の [宛先] 詳細で **[イベント ハブへのストリーム]** を選択します。 
    1. **[イベント ハブの名前空間]** ドロップダウン メニューからログの送信先を選択し、 **[保存]** を選択します。
    
       :::image type="content" source="media/diagnostic-settings/stream-event-hub.png" alt-text="ログの送信先となるドリルダウン パスのスクリーンショット。" lightbox="media/diagnostic-settings/stream-event-hub.png"::: 




