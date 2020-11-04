---
title: PowerShell を使用してハイブリッド マシンを Azure に接続する
description: この記事では、PowerShell を使用している Azure Arc 対応サーバーを使用して、エージェントをインストールし、マシンを Azure に接続する方法について説明します。
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0755846ef02377edade98b69e478908a111ab247
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901534"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>PowerShell を使用してハイブリッド マシンを Azure に接続する

手動で一連の手順を実行することで、環境内の 1 つ以上の Windows マシンまたは Linux マシンに対して Azure Arc 対応サーバーを有効にすることができます。 または、PowerShell コマンドレット [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) を使用して、Connected Machine エージェントをダウンロードし、エージェントをインストールし、Azure Arc にマシンを登録することができます。このコマンドレットを使用すると、Windows エージェント Windows インストーラー パッケージが Microsoft ダウンロード センターから、Linux エージェント パッケージが Microsoft のパッケージ リポジトリからダウンロードされます。

この方法では、エージェントをインストールおよび構成するために、マシンに対する管理者権限が必要です。 Linux ではルート アカウントを使用し、Windows ではローカルの Administrators グループのメンバーである必要があります。 このプロセスは、[PowerShell リモート処理](/powershell/scripting/learn/ps101/08-powershell-remoting)を使用して、Windows サーバー上で対話形式でまたはリモートで実行できます。

開始する前に、必ず[前提条件](agent-overview.md#prerequisites)を確認し、ご利用のサブスクリプションおよびリソースが要件を満たしていることを確認してください。 サポートされているリージョン、および関連するその他の考慮事項については、[サポート対象の Azure リージョン](overview.md#supported-regions)に関する記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

- Azure PowerShell がインストールされたマシン。 手順については、 [Azure PowerShell のインストールおよび構成](/powershell/azure/)を参照してください。

Azure PowerShell を使用して、Arc 対応サーバーで管理されているハイブリッド サーバー上の VM 拡張機能を管理するには、事前に `Az.ConnectedMachine` モジュールをインストールする必要があります。 Arc 対応サーバーで次のコマンドを実行します。

```powershell
Install-Module -Name Az.ConnectedMachine
```

インストールが完了すると、次のメッセージが返されます。

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>エージェントをインストールして Azure に接続する

1. 昇格された特権で PowerShell コンソールを開きます。

2. コマンド `Connect-AzAccount` を実行して、Azure にサインインします。

3. Connected Machine エージェントをインストールするには、`Connect-AzConnectedMachine` を `-Name`、`-ResourceGroupName`、および `-Location` のパラメーターと共に使用します。 サインイン後に作成された Azure コンテキストの結果として、既定のサブスクリプションをオーバーライドするには、`-SubscriptionId` パラメーターを使用します。 次のいずれかのコマンドを実行します。

    * Azure と直接通信できる Connected Machine エージェントをターゲット マシンにインストールするには、次を実行します。

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
        ```
    
    * プロキシ サーバー経由で通信するターゲット コンピューターに Connected Machine エージェントをインストールするには、次を実行します。
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
        ```

セットアップの完了後にエージェントが起動しない場合は、詳細なエラー情報のログを確認します。 Windows の場合は *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log* に、Linux の場合は */var/opt/azcmagent/log/himds.log* にあります。

## <a name="install-and-connect-using-powershell-remoting"></a>PowerShell リモート処理を使用したインストールと接続

Azure Arc 対応サーバーで 1 つ以上の Windows サーバーを構成するには、次の手順を実行します。 リモート マシンで PowerShell リモート処理を有効にする必要があります。 `Enable-PSRemoting` コマンドレットを使用すると、PowerShell リモート処理を有効にできます。

1. 管理者として PowerShell コンソールを開きます。

2. コマンド `Connect-AzAccount` を実行して、Azure にサインインします。

3. Connected Machine エージェントをインストールするには、`Connect-AzConnectedMachine` を `-Name`、`-ResourceGroupName`、および `-Location` のパラメーターと共に使用します。 サインイン後に作成された Azure コンテキストの結果として、既定のサブスクリプションをオーバーライドするには、`-SubscriptionId` パラメーターを使用します。

    * Azure と直接通信できる Connected Machine エージェントをターゲット マシンにインストールするには、次のコマンドを実行します。
    
        ```azurepowershell
        $session = Connect-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
        ```
    
    * Connected Machine エージェントを複数のリモート マシンに同時にインストールするには、コンマで区切られたリモート マシン名の一覧を追加します。

        ```azurepowershell
        $session = Connect-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
        ```

    次の例は、1 台のマシンをターゲットにしたコマンドの結果です。
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc との接続を検証する

エージェントをインストールし、それを Azure Arc 対応サーバーに接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されたことを確認します。 自分のマシンは [Azure portal](https://portal.azure.com) に表示されます。

![成功したサーバー接続](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>次のステップ

* トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

* [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM での Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) を使用した監視の有効化などの方法です。

* [Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)の詳細を確認します。 Windows および Linux 用の Log Analytics エージェントは、オペレーティング システムやワークロードの監視データを収集し、Automation Runbook や Update Management などの機能を使用してそれを管理するか、または [Azure Security Center](../../security-center/security-center-introduction.md) などの他の Azure サービスを使用する場合に必要になります。