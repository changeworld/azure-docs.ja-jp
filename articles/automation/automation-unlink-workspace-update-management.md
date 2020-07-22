---
title: Update Management の Automation アカウントからワークスペースのリンクを解除する
description: この記事では、Update Management の Automation アカウントから Log Analytics ワークスペースのリンクを解除する方法について説明します。
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835786"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Update Management の Automation アカウントからワークスペースのリンクを解除する

[Update Management](automation-update-management.md)の操作中、Log Analytics ワークスペースに Automation アカウントを統合しないようにすることもできます。 この記事では、アカウントからワークスペースのリンクを解除する方法について説明します。

1. https://portal.azure.com で Azure にサインインします。

2. VM の Update Management を削除します。 「[Update Management から VM を削除する](automation-remove-vms-from-update-management.md)」を参照してください。

3. Update Management に以前のバージョンの Azure SQL Monitoring が含まれている場合、機能のセットアップ時に、Automation の資産が作成されている可能性があります。それらの資産はご自身で削除する必要があります。 Update Management では、以下の項目は不要であり、削除することができます。

   * 更新スケジュール - それぞれに、作成した更新プログラムのデプロイと一致する名前が付いています。
   * Update Management に作成されたハイブリッド worker グループ - 各グループの名前は machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8 のようになります。

4. 自分の Automation アカウントを開いて、左側にある **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

5. [ワークスペースのリンクを解除] ページ **[ワークスペースのリンクを解除]** をクリックして、プロンプトに応答します。

   ![[ワークスペースのリンクを解除] ページ](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png)。

6. Azure Automation によって Log Analytics ワークスペースとのリンクが解除されている間、メニューの **[通知]** で進行状況を追跡できます。

または、Automation アカウントと Log Analytics ワークスペースのリンクをワークスペース内から解除することもできます。

1. 自分のワークスペースで、 **[関連リソース]** の **[Automation アカウント]** を選択します。 
2. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

## <a name="next-steps"></a>次のステップ

* この機能を使った作業については、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」を参照してください。
* この機能に関するエラーのトラブルシューティングについては、「[Update Management ソリューションに関する問題のトラブルシューティング](troubleshoot/update-management.md)」を参照してください。
* Windows Update エージェントに関するエラーのトラブルシューティングについては、「[Windows Update エージェントの問題をトラブルシューティングする](troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関するエラーのトラブルシューティングについては、「[Linux Update エージェントに関する問題のトラブルシューティング](troubleshoot/update-agent-issues-linux.md)」を参照してください。
