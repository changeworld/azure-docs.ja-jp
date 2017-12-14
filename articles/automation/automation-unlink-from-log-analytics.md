---
title: "Log Analytics から Azure Automation アカウントのリンクを解除する | Microsoft Docs"
description: "この記事では、OMS ワークスペースから Azure Automation アカウントのリンクを解除する方法の概要を説明します。"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to-article
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 7deac9800471065d2bd5b921143b4ed08ee408da
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Log Analytics ワークスペースから Automation アカウントのリンクを解除する方法

Azure Automation は Log Analytics と統合され、Automation アカウント全体の Runbook ジョブの監視をサポートします。また、Log Analytics に依存する以下のソリューションのインポートが可能になります。

* [更新管理](../operations-management-suite/oms-solution-update-management.md)
* [変更の追跡](../log-analytics/log-analytics-change-tracking.md)
* [勤務時間外に VM を起動/停止する](automation-solution-vm-management.md)
 
Automation アカウントを Log Analytics と統合する必要がなくなった場合は、Azure Portal から直接、アカウントのリンクを解除できます。  作業を進める前に、上記で説明したソリューションを削除する必要があります。そうしないと、このプロセスを続行できません。  インポート済みのソリューションに関するトピックを確認して、削除に必要な手順を理解してください。  

これらのソリューションを削除したら、以下の手順を実行して、Automation アカウントのリンクを解除できます。

## <a name="unlink-workspace"></a>ワークスペースのリンクの解除

1. Azure Portal から Automation アカウントを開き、[Automation アカウント] ページで、左側にある **[関連リソース]** セクションで **[ワークスペースのリンクを解除]** を選択します。<br><br> ![[Unlink workspace] (ワークスペースのリンクの解除) オプション](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)<br><br>  
2. [ワークスペースのリンクを解除] ページ **[ワークスペースのリンクを解除]** をクリックします。<br><br> ![ワークスペースのリンクを解除](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png)。<br><br>  続行するかどうかを確認するプロンプトが表示されます。<br><br>
3. Azure Automation によってアカウントと Log Analytics ワークスペースとのリンクが解除されている間、メニューの **[通知]** で進行状況を追跡できます。

更新の管理ソリューションを使用していた場合は、ソリューションの削除後に不要になる以下の項目を削除することもできます。

* 更新スケジュール。  各スケジュールには、作成した更新のデプロイメントに一致する名前が付いています。

* ソリューション用に作成したハイブリッド worker グループ。  各グループの名前は machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8 のようになります。

勤務時間外の VM の開始/停止ソリューションを使用していた場合は、ソリューションの削除後に不要になる以下の項目を削除することもできます。

* VM の開始/停止の Runbook スケジュール 
* VM の開始/停止の Runbook
* 変数   

## <a name="next-steps"></a>次のステップ

Automation アカウントを再構成して OMS Log Analytics と統合する場合は、「[Automation から Log Analytics (OMS) へのジョブの状態とジョブ ストリームの転送](automation-manage-send-joblogs-log-analytics.md)」を参照してください。 