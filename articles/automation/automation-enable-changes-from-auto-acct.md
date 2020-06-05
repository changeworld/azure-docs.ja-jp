---
title: Automation アカウントから Azure Automation の変更履歴とインベントリを有効にする
description: この記事では、Automation アカウントで変更履歴とインベントリを有効にする方法について説明します。
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 28c61e637a37c158dcd80c0f02b748b4813945fb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826844"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Automation アカウントで変更履歴とインベントリを有効にする

この記事では、Automation アカウントを使用して、お客様の環境内の VM に対して[変更履歴とインベントリ](change-tracking.md)機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、変更履歴とインベントリを使用して既存の VM を有効にする必要があります。 

> [!NOTE]
> 変更履歴とインベントリを有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](automation-offering-get-started.md)。
* [仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

https://portal.azure.com で Azure にサインインします。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

1. Automation アカウントに移動し、 **[構成管理]** で、 **[インベントリ]** または **[変更の追跡]** を選択します。

2. Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** をクリックして、変更履歴とインベントリを有効にします。 セットアップが完了するまでに最大 15 分かかります。

    ![Change Tracking と Inventory の有効化](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>スコープ構成の確認

変更履歴とインベントリでは、ワークスペース内のスコープ構成を使用して、変更の対象となるコンピューターを決定します。 スコープ構成は、機能のスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件のグループです。 詳細については、[変更履歴とインベントリのスコープ構成の操作](automation-scope-configurations-change-tracking.md)に関するページを参照してください。

## <a name="enable-azure-vms"></a>Azure VM の有効化

1. Automation アカウントから、 **[構成管理]** の **[インベントリ]** または **[Change tracking]\(変更履歴\)** を選択します。

2. **[+ Azure VM の追加]** をクリックし、リストから 1 つ以上の VM を選択します。 有効にできない仮想マシンは灰色で表示され、選択できません。 Azure VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。 

3. **[Enable]\(有効にする\)** をクリックすると、選択された VM が、この機能の "保存した検索条件" コンピューター グループに追加されます。 詳細については、[変更履歴とインベントリのスコープ構成の操作](automation-scope-configurations-change-tracking.md)に関するページを参照してください。

    ![Azure VM の有効化](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Azure 以外の VM を有効にする

Azure に存在しないマシンは手動で追加する必要があります。 

1. Automation アカウントから、 **[構成管理]** の **[インベントリ]** または **[Change tracking]\(変更履歴\)** を選択します。

2. **[Add non-Azure machine]\(Azure 以外のマシンを追加する\)** をクリックします。 このアクションにより、新しいブラウザー ウィンドウが開きます。このウィンドウには、変更履歴とインベントリの操作に対するレポートをマシンが開始できるように、[Windows 用の Log Analytics エージェントをインストールして構成する方法](../azure-monitor/platform/log-analytics-agent.md)が示されます。 Operations Manager によって現在管理されているマシンを有効にする場合、新しいエージェントは必要ありません。ワークスペース情報は、既存のエージェントに入力されます。

## <a name="enable-machines-in-the-workspace"></a>ワークスペースでのマシンの有効化

手動でインストールしたマシンや、既にワークスペースへのレポートを行っているマシンは、Azure Automation に追加して変更履歴とインベントリを有効にする必要があります。 

1. Automation アカウントから、 **[構成管理]** の **[インベントリ]** または **[Change tracking]\(変更履歴\)** を選択します。

2. **[マシンの管理]** を選択します。 前に **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択している場合、 **[マシンの管理]** ボタンはグレー表示される可能性があります。

    ![保存した検索条件](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. 使用可能なすべてのマシンに対して変更履歴とインベントリを有効にするには、[マシンの管理] ページの **[使用可能なすべてのマシンで有効にします]** を選択します。 このアクションにより、マシンを個別に追加するコントロールが無効になります。 このタスクにより、ワークグループへのレポートを行うすべてマシンの名前が、"保存した検索クエリ" コンピューター グループに追加されます。 選択されている場合は、 **[マシンの管理]** ボタンが無効になります。

4. 使用可能なすべてのマシンと今後のマシンに対してこの機能を有効にするには、 **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択します。 このオプションを選択すると、保存した検索条件とスコープ構成がワークスペースから削除され、ワークスペースへのレポートを行うすべての Azure マシンと Azure 以外のマシンに対して機能が開放されます。 選択された場合は、残されたスコープ構成がないため、 **[マシンの管理]** ボタンが永続的に無効になります。

5. 必要であれば、最初に保存した検索条件を再度追加することで、スコープ構成を追加し直すことができます。 詳細については、[変更履歴とインベントリのスコープ構成の操作](automation-scope-configurations-change-tracking.md)に関するページを参照してください。

6. 1 つ以上のマシンに対して機能を有効にするには、 **[選択したマシンで有効にします]** を選択し、機能を有効にする各マシンの横にある **[追加]** をクリックします。 このタスクにより、選択したマシン名が、機能の "保存した検索クエリ" コンピューター グループに追加されます。

## <a name="next-steps"></a>次のステップ

* この機能の詳細については、「[Change Tracking と Inventory の管理](change-tracking-file-contents.md)」を参照してください。
* スコープ構成の詳細については、[変更履歴とインベントリのスコープ構成の操作](automation-scope-configurations-change-tracking.md)に関するページを参照してください。
* この機能を使用して環境にインストールされているソフトウェアを特定する方法については、「[VM にインストールされているソフトウェアを検出する](automation-tutorial-installed-software.md)」を参照してください。
* 機能を有効にするときに Automation アカウントを Log Analytics ワークスペースに統合しない場合は、「[Automation アカウントからワークスペースのリンクを解除する](automation-unlink-workspace-change-tracking.md)」を参照してください。
* VM への変更のデプロイが完了したら、「[変更履歴とインベントリから VM を削除する](automation-remove-vms-from-change-tracking.md)」の説明に従って削除できます。
* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](troubleshoot/change-tracking.md)」を参照してください。