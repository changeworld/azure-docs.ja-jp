---
title: Azure VM から Update Management、Change Tracking、および Inventory ソリューションをオンボードする
description: Azure 仮想マシンを Azure Automation の一部である Update Management、Change Tracking、および Inventory ソリューションでオンボードする方法について説明します。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 039e2d3c70493868ca2f79e89fc82d8970ec6865
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032402"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Azure 仮想マシンから Update Management、Change Tracking、および Inventory ソリューションをオンボードする

Azure Automation には、オペレーティング システムのセキュリティ更新プログラム、変更の追跡、および使用中のコンピューターにインストールされている内容のインベントリを管理するソリューションが用意されています。 マシンをオンボードする方法は複数あります。 ソリューションのオンボードは、仮想マシンから、[Automation アカウントから](automation-onboard-solutions-from-automation-account.md)、[複数のマシンを参照することから](automation-onboard-solutions-from-browse.md)、または [Runbook](automation-onboard-solutions.md) を使用して行うことができます。 この記事では、Azure 仮想マシンからこれらのソリューションをオンボードする方法について説明します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="enable-the-solutions"></a>ソリューションの有効化

既存の仮想マシンに移動します。 **［操作］** の下の **［更新プログラムの管理］**、**［インベントリ］** または **［変更の追跡］** を選択します。

ソリューションを VM に対してのみ有効にするには、**［Enable for this VM］\(この VM で有効にする\)** が選択されていることを確認します。 複数のマシンをソリューションにオンボードするには、**[Enable for VMs in this subscription]\(このサブスクリプションの VM を有効にする\)** を選択し、**[Click to select machines to enable]\(クリックして有効にするマシンを選択\)** を選択します。 一度に複数のマシンをオンボードする方法については、「[Update Management、Change Tracking、および Inventory ソリューションのオンボード](automation-onboard-solutions-from-automation-account.md)」を参照してください。

Log Analytics ワークスペースと Automation アカウントを選択し、**[有効にする]** を選択して、ソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

![Update Management ソリューションのオンボード](media/automation-onboard-solutions-from-vm/onboard-solution.png)

他のソリューションに移動し、**[有効化]** を選択します。 Log Analytics と Automation アカウントのドロップダウン リストは、前に有効にしたソリューションと同じワークスペースと Automation アカウントを使用するため、選択不可になっています。

> [!NOTE]
> **変更の追跡**と**インベントリ**は、同じソリューションを使用します。 これらのソリューションの片方を有効にすると、他方も有効になります。

## <a name="scope-configuration"></a>スコープ構成

各ソリューションは、ワークスペース内のスコープ構成を使用して、ソリューションの対象となるコンピューターを決定します。 スコープ構成は、ソリューションのスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件のグループです。 スコープ構成にアクセスするには、Automation アカウントで、**[関連リソース]** の下の **[ワークスペース]** を選択します。 ワークスペースで、**[ワークスペースのデータ ソース]** の下の **[スコープ構成]** を選択します。

選択したワークスペースに Update Management または Change Tracking ソリューションがまだない場合は、次のスコープ構成が作成されます。

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

選択したワークスペースにソリューションが既にある場合、ソリューションの再デプロイは行われず、スコープ構成の追加も行われません。

いずれかの構成の省略記号 (**...**) を選択し、**[編集]** を選択します。 **[スコープ構成の編集]** ウィンドウで、**[コンピューター グループの選択]** を選択します。 **[コンピューター グループ]** ウィンドウに、スコープ構成を作成するために使用された、保存された検索条件が表示されます。

## <a name="saved-searches"></a>保存した検索条件

コンピューターが Update Management Change Tracking、または Inventory ソリューションに追加されると、ワークスペースの 2 つの保存された検索条件のいずれかにそのコンピューターが追加されます。 保存された検索条件は、これらのソリューションの対象となるコンピューターを含むクエリです。

ワークスペースに移動します。 **[全般]** の下の **[保存された検索]** を選択します。 次の表は、これらのソリューションで使用される 2 つの保存された検索条件を示しています。

|Name     |Category  |エイリアス  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新プログラム        | Updates__MicrosoftDefaultComputerGroup         |

いずれかの保存された検索条件を選択して、グループの設定に使用されるクエリを表示します。 次の図は、クエリとその結果を示しています。

![保存した検索条件](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>ワークスペースのリンクの解除

以下のソリューションは、Log Analytics ワークスペースに依存しています。

* [更新管理](automation-update-management.md)
* [変更の追跡](automation-change-tracking.md)
* [勤務時間外に VM を起動/停止する](automation-solution-vm-management.md)

Automation アカウントを Log Analytics と統合する必要がなくなった場合は、Azure Portal から直接、アカウントのリンクを解除できます。  作業を進める前に、上記で説明したソリューションを削除する必要があります。そうしないと、このプロセスを続行できません。 インポート済みのソリューションに関する記事を確認して、削除に必要な手順を理解してください。

これらのソリューションを削除したら、以下の手順を行うと、Automation アカウントのリンクを解除できます。

> [!NOTE]
> Azure SQL 監視ソリューションの以前のバージョンを含む一部のソリューションでは、Automation アセットを作成している可能性があり、ワークスペースのリンクを解除する前にその削除が必要な場合があります。

1. Azure portal から Automation アカウントを開き、[Automation アカウント] ページで、左側にある **[関連リソース]** セクションで **[リンクされたワークスペース]** を選択します。

1. [ワークスペースのリンクを解除] ページ **[ワークスペースのリンクを解除]** をクリックします。

   ![[ワークスペースのリンクを解除] ページ](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   続行するかどうかを確認するプロンプトが表示されます。

1. Azure Automation によってアカウントと Log Analytics ワークスペースとのリンクが解除されている間、メニューの **[通知]** で進行状況を追跡できます。

更新の管理ソリューションを使用していた場合は、ソリューションの削除後に不要になる以下の項目を削除することもできます。

* スケジュールの更新 - 各スケジュールには、作成した更新のデプロイに一致する名前が付いています。

* ソリューションに作成されたハイブリッド worker グループ - 各グループの名前は machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8 のようになります。

勤務時間外の VM の開始/停止ソリューションを使用していた場合は、ソリューションの削除後に不要になる以下の項目を削除することもできます。

* VM の開始/停止の Runbook スケジュール
* VM の開始/停止の Runbook
* variables

## <a name="next-steps"></a>次の手順

ソリューションを使用する方法を学習するためのチュートリアルに進みます。

* [チュートリアル - VM の更新プログラムの管理](automation-tutorial-update-management.md)
* [チュートリアル - VM 上のソフトウェアの特定](automation-tutorial-installed-software.md)
* [チュートリアル - VM に対する変更のトラブルシューティング](automation-tutorial-troubleshoot-changes.md)
