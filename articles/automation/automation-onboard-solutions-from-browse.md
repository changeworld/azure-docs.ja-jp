---
title: Azure Automation の Update Management、Change Tracking、および Inventory ソリューションを複数の VM で使用する準備を行う方法について説明します
description: Azure Automation に含まれる Update Management、Change Tracking、および Inventory ソリューションで、Azure Virtual Machine の使用準備を行う方法について説明します
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0a624d850b8c3260acb24cb17566090e8ad0043e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233939"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Update Management、Change Tracking、および Inventory ソリューションを複数の VM で使用できるようにする

Azure Automation には、オペレーティング システムのセキュリティ更新プログラム、変更の追跡、およびご使用のコンピューターにインストールされている内容のインベントリを管理するソリューションが用意されています。 マシンの使用準備を行う方法は複数あります。[仮想マシンから](automation-onboard-solutions-from-vm.md)、お使いの [Automation アカウントから](automation-onboard-solutions-from-automation-account.md) (仮想マシンを参照する場合) または [Runbook](automation-onboard-solutions.md) によってソリューションの使用準備を行うことができます。 この記事では、Azure の仮想マシンを参照するときにこれらのソリューションの使用準備を行う方法について説明します。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure にサインインします ( https://portal.azure.com )。

## <a name="enable-solutions"></a>ソリューションの有効化

Azure Portal で**仮想マシン**に移動します。

チェックボックスを使用して、Change Tracking と Inventory または Update Management を使用準備する仮想マシンを選択します。 使用準備は、同時に最大 3 つの異なるリソース グループに対して行うことができます。

![VM の一覧](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> フィルター コントロールを使用して仮想マシンの一覧を変更し、一番上のチェックボックスをオンにし、一覧のすべての仮想マシンを選択します。

コマンド バーから **[サービス]** をクリックし、**[Change Tracking]**、**[Inventory]**、または **[Update Management]** のいずれかを選択します。

> [!NOTE]
> **[変更追跡]** と **[インベントリ]** は、同じソリューションを使用します。一方が有効であれば、もう一方も有効です。

次のイメージは Update Management です。 Change Tracking と Inventory のレイアウトおよび動作は同じです。

仮想マシンの一覧はフィルターされ、同じサブスクリプションおよび場所にある仮想マシンのみが表示されます。 お使いの仮想マシンが 4 つ以上のリソース グループにある場合、最初の 3 つのリソース グループが選択されます。

フィルター コントロールを使用して、異なるサブスクリプション、場所、およびリソース グループから仮想マシンを選択します。

![Update Management ソリューションの使用準備](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Log Analytics ワークスペースと Automation アカウントの選択を確認します。 既定では、新しいワークスペースおよび Automation アカウントが選択されます。 既存の Log Analytics ワークスペースおよび Automation アカウントがあり、これらを使用する場合、**[変更]** をクリックして **[構成]** ページから選択します。 完了したら、**[保存]** をクリックします。

![ワークスペースとアカウントを選択する](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

有効にしない仮想マシンの横のチェックボックスをオフにします。 有効にできない仮想マシンは既に選択解除されています。

**[有効にする]** をクリックしてソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

## <a name="troubleshooting"></a>トラブルシューティング

複数のマシンを使用準備する場合、**[有効にできません]** と表示されるマシンが存在する場合があります。 有効にできないマシンが存在する理由はさまざまです。 次の各セクションでは、使用準備しようとするときに VM が**有効にできない**状態になる考え得る理由を示します。

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM reports to a different workspace: '\<workspaceName\>' (VM のレポート先が異なるワークスペース <ワークスペース名> である)。  Change configuration to use it for enabling (これを使用するように構成を変更して有効化できるようにしてください)

**原因**: このエラーは、使用準備しようとしている VM のレポート先が別のワークスペースであることを示しています。

**ソリューション**: **[構成として使用する]** をクリックして、対象の Automation アカウントおよび Log Analytics ワークスペースを変更します。

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM reports to a workspace that is not available in this subscription (VM のレポート先がこのサブスクリプションで使用できないワークスペースである)

**原因**: 仮想マシンのレポート先ワークスペースが次のいずれかです。

* 別のサブスクリプションにある
* 存在しない
* アクセス許可がないリソース グループにある

**ソリューション**: VM のレポート先のワークスペースに関連付けられた Automation アカウントを探し、スコープ構成を変更して仮想マシンを使用準備します。

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM operating system version or distribution is not supported (VM のオペレーティング システムのバージョンまたはディストリビューションがサポートされていない)

**原因:** このソリューションは、すべての Linux ディストリビューションまたはすべてのバージョンの Windows に対してサポートされているわけではありません。

**ソリューション:** [サポート対象クライアントの一覧](automation-update-management.md#clients)を参照して解決してください。

### <a name="classic-vms-cannot-be-enabled"></a>Classic VMs cannot be enabled (クラシック VM を有効にできない)

**原因**: クラシック デプロイ モデルを使用する仮想マシンはサポートされていません。

**ソリューション**: 仮想マシンをリソース マネージャー デプロイ モデルに移行します。 この方法については、「[Migrate classic deployment model resources](../virtual-machines/windows/migration-classic-resource-manager-overview.md)」(クラシック デプロイ モデル リソースの移行) を参照してください。

### <a name="vm-is-stopped-deallocated"></a>VM is stopped. (deallocated) (VM が停止 (割り当て解除) している)

**原因**: 仮想マシンが**実行**状態ではありません。

**ソリューション**: VM がソリューションを使用できるようにするためには VM は実行中である必要があります。 **[VM の起動]** インライン リンクをクリックし、ページから移動せずに VM を起動します。

## <a name="next-steps"></a>次の手順

お使いの仮想マシンでソリューションを使用できるようになりました。Update Management の概要の記事にアクセスし、お使いのマシンに更新の評価を表示する方法を参照してください。

> [!div class="nextstepaction"]
> [Update Management - 更新の評価を表示する](./automation-update-management.md#viewing-update-assessments)

ソリューションおよびその使用方法についてのその他のチュートリアル

* [チュートリアル - VM の更新プログラムの管理](automation-tutorial-update-management.md)

* [チュートリアル - VM 上のソフトウェアの特定](automation-tutorial-installed-software.md)

* [チュートリアル - VM に対する変更のトラブルシューティング](automation-tutorial-troubleshoot-changes.md)