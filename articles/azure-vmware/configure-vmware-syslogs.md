---
title: Azure VMware Solution の VMware syslog を構成する
description: Azure VMware Solution プライベート クラウドの VMware syslog を収集するための診断設定の構成方法について説明します。
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 983a62eb5b094c94048e7580fd53558df002d816
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699294"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Azure VMware Solution の VMware syslog を構成する

リソースのプラットフォーム ログとメトリックを自分が選んだ宛先にストリーム配信するためのエクスポートは、診断設定を使用して構成します。 異なるログとメトリックを個別の宛先に送信するために、最大 5 つの診断設定を作成できます。 

このトピックでは、Azure VMware Solution プライベート クラウドの VMware syslog を収集するための診断設定の構成方法について説明します。 その syslog をストレージ アカウントに格納して、vCenter のログを確認したり、診断目的で分析したりします。 

## <a name="prerequisites"></a>前提条件

vCenter と NSX-T Manager のインターフェイスへのアクセスが可能な Azure VMware Solution プライベート クラウドが必要となります。 

## <a name="configure-diagnostic-settings"></a>診断設定を構成する

1. Azure VMware Solution プライベート クラウドから **[診断設定]** を選択し、 **[Add diagnostic settings]\(診断設定の追加\)** を選択します。
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="VMware syslog の構成画面を示すスクリーンショット。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. **[vmwaresyslog]** 、 **[Allmetrics]** 、 **[Archive to storage account]\(ストレージ アカウントへのアーカイブ\)** の各オプションを選択します。

   >[!IMPORTANT]
   >**[Send to log analytics workspace]\(Log Analytics ワークスペースに送信する\)** オプションは現在正しく機能しません。
 
1. ログの格納先とするストレージ アカウントを選択し、 **[保存]** を選択します。

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="syslog の格納に関して選択するオプションを示すスクリーンショット。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

1. ストレージとアカウントに移動し、**Insight logs vmwarelog** が作成されていることを確認し、それを選択します。 
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="作成されて選択できる状態になった Insight logs vmwarelog オプションを示すスクリーンショット。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


1. json ファイルの保存先を参照してファイルをダウンロードし、ログを確認します。

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="json ファイルのドリルダウン パスを示すスクリーンショット。" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

