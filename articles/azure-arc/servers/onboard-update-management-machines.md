---
title: Azure Automation Update Management からマシンを接続する
description: この記事では、Automation Update Management によって管理される Azure Arc にハイブリッド マシンを接続する方法について説明します。
ms.date: 09/14/2021
ms.topic: conceptual
ms.openlocfilehash: fc5de9146ea07e490e69760397a661f4bbb99eec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699272"
---
# <a name="connect-hybrid-machines-to-azure-from-automation-update-management"></a>Automation Update Management から Azure にハイブリッド マシンを接続する

1 つ以上の Windows または Linux 仮想マシンに対して、またはオンプレミスまたは Azure Automation Update Management で管理されている他のクラウド環境でホストされている物理サーバーに対して、Azure Arc 対応サーバーを有効にできます。 このオンボーディング プロセスにより、[Connected Machine エージェント](agent-overview.md)のダウンロードおよびインストールが自動化されます。 Azure Arc 対応サーバーにマシンを接続するには、特権 ID ではなく、Azure Active Directory [サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)を使用すると、マシンを[対話的に接続](onboard-portal.md)できます。 このサービス プリンシパルは、これらのマシンのオンボーディング プロセスの一部として自動的に作成されます。

開始する前に、必ず[前提条件](agent-overview.md#prerequisites)を確認し、ご利用のサブスクリプションおよびリソースが要件を満たしていることを確認してください。 サポートされているリージョン、および関連するその他の考慮事項については、 [サポート対象の Azure リージョン](overview.md#supported-regions) に関する記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="how-it-works"></a>しくみ

オンボード プロセスが開始されると、Active Directory [サービス プリンシパル](../../active-directory/fundamentals/service-accounts-principal.md)がテナントに作成されます。 

ターゲット マシンに Connected Machine エージェントをインストールして構成するために、**Add-AzureConnectedMachines** という名前のマスター Runbook が Azure サンドボックスで実行されます。 マシン上で検出されたオペレーティング システムに基づいて、マスター Runbook は、**Add-AzureConnectedMachineWindows** または **Add-AzureConnectedMachineLinux** という子 Runbook を呼び出します。この子 Runbook は、システム [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) ロールの下、直接マシン上で実行されます。 Runbook ジョブ出力がジョブ履歴に書き込まれ、[ステータスの概要](../../automation/automation-runbook-execution.md#job-statuses)を表示したり、[Azure portal](../../automation/manage-runbooks.md#view-statuses-in-the-azure-portal) で、または [Azure PowerShell](../../automation/manage-runbooks.md#retrieve-job-statuses-using-powershell) を使用して特定の Runbook ジョブの詳細をドリルダウンしたりできます。 Azure Automation で Runbook を実行すると、その Automation アカウントのアクティビティ ログに詳細が書き込まれます。 ログの使用の詳細については、「[アクティビティ ログから詳細を取得する](../../automation/manage-runbooks.md#retrieve-details-from-activity-log)」を参照してください。

最後の手順では、サービス プリンシパルを使用して Azure にリソースとしてマシンを登録する `azcmagent` コマンドを使用して Azure Arc への接続を確立します。

## <a name="prerequisites"></a>前提条件

この方法では、Automation アカウントで Runbook ジョブを作成できるように、[Automation ジョブ オペレーター](../../automation/automation-role-based-access-control.md#automation-job-operator) ロール以上のメンバーであることが必要です。 

Azure Policy で [Runbook の実行を管理](../../automation/enforce-job-execution-hybrid-worker.md)し、Hybrid Runbook Worker グループに対する Runbook の実行のターゲット設定を強制できるようにしている場合は、このポリシーを無効にする必要があります。 そうしないと、マシンを Arc 対応サーバーにオンボードする Runbook ジョブは失敗します。 

## <a name="add-machines-from-the-azure-portal"></a>Azure portal からマシンを追加する

Arc 対応サーバーを使用してハイブリッド マシンを構成するには、次の手順を実行します。 プロセスが正常に完了するには、サーバーまたはマシンの電源をオンにして、オンラインにしている必要があります。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

1. **[サーバー - Azure Arc]** ページに移動し、左上の **[追加]** を選択します。

1. **[メソッドを選択してください]** ページで、 **[Update Management から管理対象サーバーを追加する (プレビュー)]** タイルを選択し、 **[サーバーの追加]** を選択します。

1. **[基本]** ページで、次を構成します。

    1. **[リソース グループ]** ドロップダウンの一覧で、マシンを管理するリソース グループを選択します。
    1. **[リージョン]** ドロップダウンの一覧で、サーバーのメタデータを格納する Azure リージョンを選択します。
    1. マシンがプロキシ サーバー経由でインターネットに接続して通信を行っている場合は、プロキシ サーバーの IP アドレス、またはマシンでプロキシ サーバーとの通信に使用されている名前とポート番号を指定します。 `http://<proxyURL>:<proxyport>` の形式で値を入力します。
    1. **[次へ: マシン]** を選択します。

1. **[マシン]** ページで、Update Management 機能が有効になっており、Azure Arc 対応サーバーにオンボードするマシンが含まれるドロップダウン リストから **[サブスクリプション]** と **[Automation アカウント]** を選択します。

   Automation アカウントを指定すると、その Automation アカウントの、Update Management で管理される Azure 以外のマシンが下のリストに返されます。 Windows と Linux の両方のマシンが一覧表示され、それぞれに対して **[追加]** を選択します。

   **[選択項目の確認]** を選択することによって選択内容を確認でき、マシンを削除する場合は、 **[アクション]** 列の下から **[削除]** を選択します。 

   選択内容を確認したら、 **[次へ: タグ]** を選択します。

1. **[タグ]** ページで、標準をサポートする **[名前]** / **[値]** のペアを 1 つ以上指定します。 **[次へ: 確認 + 追加]** を選択します。

1. **[確認 _ 追加]** ページで、概要情報を確認し、 **[マシンの追加]** を選択します。 引き続き変更が必要な場合は、 **[前へ]** を選択します。

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc との接続を検証する

エージェントをインストールし、それを Azure Arc 対応サーバーに接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されたことを確認します。 自分のマシンは [Azure portal](https://aka.ms/hybridmachineportal) に表示されます。

![成功したサーバー接続](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>次のステップ

- トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

- [計画と展開ガイド](plan-at-scale-deployment.md)を参照して、任意の規模で Azure Arc 対応サーバーをデプロイし、一元的な管理と監視を実装する計画を立ててください。

- [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM 分析情報](../../azure-monitor/vm/vminsights-enable-policy.md)を使用した監視の有効化などの方法です。