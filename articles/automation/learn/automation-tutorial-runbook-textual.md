---
title: チュートリアル - Azure Automation で PowerShell ワークフロー Runbook を作成する
description: このチュートリアルでは、PowerShell ワークフロー Runbook を作成、テスト、発行する方法を説明します。
services: automation
ms.subservice: process-automation
ms.date: 10/28/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c677d7690acc3ab05c5d8df2ab516d396be2eb4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465617"
---
# <a name="tutorial-create-a-powershell-workflow-runbook-in-automation"></a>チュートリアル: Automation で PowerShell ワークフロー Runbook を作成する

このチュートリアルでは、Azure Automation で [PowerShell Workflow Runbook](../automation-runbook-types.md#powershell-workflow-runbooks) を作成します。 PowerShell ワークフロー Runbook は、Windows PowerShell ワークフローに基づくテキスト Runbook です。 Azure portal のテキスト エディターを使用して、Runbook のコードを作成したり編集したりすることができます。

>[!NOTE]
>  この記事は PowerShell 5.1 に適用されます。PowerShell 7.1 (プレビュー) ではワークフローはサポートされていません。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * PowerShell Workflow Runbook を作成する
> * Runbook をテストして発行する
> * Runbook ジョブの状態を実行、追跡する
> * Azure リソースを管理するための認証を追加する
> * Azure 仮想マシンを起動するように Runbook パラメーターを更新する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* 少なくとも 1 つのユーザー割り当てマネージド ID を持つ Azure Automation アカウント。 詳細については、[マネージド ID の有効化](../quickstarts/enable-managed-identity.md)に関するページを参照してください。
* Az モジュール: `Az.Accounts` および `Az.Compute` が Automation アカウントにインポートされている。 詳細については、「[Az モジュールをインポートする](../shared-resources/modules.md#import-az-modules)」を参照してください。
* 2 台以上の [Azure 仮想マシン](../../virtual-machines/windows/quick-create-powershell.md)。 これらのマシンを停止して起動するため、運用環境の VM にしないでください。
* [Azure Az PowerShell モジュール](/powershell/azure/new-azureps-module-az)がマシンにインストールされている。 インストールまたはアップグレードするには、[Azure Az PowerShell モジュールをインストールする方法](/powershell/azure/install-az-ps)に関するページを参照してください。

## <a name="assign-permissions-to-managed-identities"></a>マネージド ID にアクセス許可を割り当てる

適切な[マネージド ID](../automation-security-overview.md#managed-identities) にアクセス許可を割り当てて、仮想マシンを停止できるようにします。 Runbook では、Automation アカウントのシステム割り当てマネージド ID またはユーザー割り当てマネージド ID のいずれかを使用できます。 以下に、各 ID にアクセス許可を割り当てる手順を示します。 以下の手順では、Azure portal を使用します。 PowerShell を使用する場合は、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

1. [Azure portal](https://portal.azure.com) にサインインし、お使いの Automation アカウントに移動します。

1. **[アカウント設定]** で、 **[ID (プレビュー)]** を選択します。

1. **[システム割り当て済み]** タブの **[アクセス許可]** で、 **[Azure ロールの割り当て]** を選択して、 **[Azure ロールの割り当て]** ページを開きます。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-role-assignments-portal.png" alt-text="portal で [Azure ロールの割り当て] を選択する。":::

1. **[+ ロールの割り当ての追加 (プレビュー)]** を選択して、 **[ロールの割り当ての追加 (プレビュー)]** ページを開きます。 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-add-role-assignment-portal.png" alt-text="portal でロールの割り当てを追加する。":::

1. 適切な値を選択します。

   |プロパティ |説明 |
   |---|---|
   |Scope| スコープは、ロールの割り当てが適用される一連のリソースです。 ドロップダウン リストから、 **[リソース グループ]** を選択します。|
   |サブスクリプション|このフィールドには、ご自分のサブスクリプションが自動的に設定されます。|
   |リソース グループ|ドロップダウン リストから、ID のアクセス許可を付与するリソース グループを選択します。|
   |ロール|ドロップダウン リストから、 **[DevTest Labs ユーザー]** を選択します。|

1. **[保存]** を選択し、 **[Azure ロールの割り当て]** ページを閉じて、 **[システム割り当て済み]** タブに戻ります。

1. **[ユーザー割り当て済み]** タブを選択します。

1. 一覧からお使いのユーザー割り当てマネージド ID を選択して、 **[マネージド ID]** ページを選択します。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/select-user-assigned-identity-portal.png" alt-text="portal でユーザー割り当てマネージド ID を選択する。":::

1. 後で使用できるように、**クライアント ID** をメモします。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/managed-identity-client-id-portal.png" alt-text="portal に表示されているマネージド ID のクライアント ID":::

1. 左側のメニューから、 **[Azure ロールの割り当て]** を選択し、 **[+ ロールの割り当ての追加 (プレビュー)]** を選択して、 **[ロールの割り当ての追加 (プレビュー)]** ページを開きます。 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/user-assigned-add-role-assignment-portal.png" alt-text="portal でユーザー割り当て ID のロールの割り当てを追加する。":::

1. 適切な値を選択します。

   |プロパティ |説明 |
   |---|---|
   |Scope| ドロップダウン リストから、 **[リソース グループ]** を選択します。|
   |サブスクリプション|このフィールドには、ご自分のサブスクリプションが自動的に設定されます。|
   |リソース グループ|ドロップダウン リストから、ID のアクセス許可を付与するリソース グループを選択します。|
   |ロール|ドロップダウン リストから、 **[DevTest Labs ユーザー]** を選択します。|

1. **[保存]** を選択し、 **[Azure ロールの割り当て]** ページを閉じて、 **[ユーザー割り当て済み]** タブに戻ります。

## <a name="create-new-runbook"></a>新しい Runbook を作成する

まず、単純な [PowerShell ワークフロー Runbook](../automation-runbook-types.md#powershell-workflow-runbooks) を作成します。 Windows PowerShell ワークフローの利点の 1 つは、一般的なスクリプトのように順番に実行するのでなく、一連のコマンドを並行して実行できることです。

>[!NOTE]
> このリリースでは、Azure portal での Runbook の作成エクスペリエンスが新しくなりました。 **[Runbook]** ブレード > **[Runbook の作成]** を選択すると、新しいページ **[Runbook の作成]** が開き、適用可能なオプションが表示されます。 

1. 開いている [Automation アカウント] ページの **[プロセス オートメーション]** で、 **[Runbook]** を選択します。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook.png" alt-text="portal から PowerShell ワークフロー Runbook を作成する":::

1. **[+ Runbook の作成]** を選択します。
    1. Runbook に名前を付けます。 例: test。
    1. **[Runbook の種類]** ドロップダウン メニューから、 **[PowerShell]** を選択します。
    1. **[ランタイム バージョン]** ドロップダウンから、 **[5.1]** を選択します。
    1. 該当する **[説明]** を入力します。
    1. **［作成］** を選択します
   
    :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook-options.png" alt-text="portal での PowerShell ワークフロー Runbook のオプション":::
   

## <a name="add-code-to-the-runbook"></a>Runbook にコードを追加する

Runbook に直接コードを入力するか、ライブラリ コントロールからコマンドレット、Runbook、資産を選択し、関連するパラメーターを使って Runbook に追加できます。 このチュートリアルでは、Runbook に直接コードを入力します。

Runbook はこの時点で空です (必要な `Workflow` キーワード、Runbook の名前、ワークフロー全体を囲む中かっこ以外)。

```powershell
Workflow MyFirstRunbook-Workflow
{
}
```

1. `Parallel` キーワードを使用すると、複数のコマンドが同時に実行されるスクリプト ブロックを作成できます。 中かっこの "*間*" に次のコードを入力します。

   ```powershell
   Parallel {
        Write-Output "Parallel"
        Get-Date
        Start-Sleep -s 3
        Get-Date
    }

   Write-Output " `r`n"
   Write-Output "Non-Parallel"
   Get-Date
   Start-Sleep -s 3
   Get-Date  
   ```

1. **[保存]** を選択して Runbook を保存します。

## <a name="test-the-runbook"></a>Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、テストして正常に動作することを確認します。 Runbook をテストすると、そのドラフト バージョンが実行され、その出力を対話形式で表示できます。

1. **[テスト ウィンドウ]** を選択して、 **[テスト]** ページを開きます。

1. **[開始]** を選択してテストを開始します。  [Runbook ジョブ](../automation-runbook-execution.md)が作成され、その状態がペインに表示されます。

   ジョブの最初の状態は "キュー済み" であり、クラウドで runbook worker が使用可能になるのをジョブが待機していることを示します。 worker がジョブを要求すると、状態は "開始中" になります。 最後に、Runbook が実際に実行を開始すると [実行中] になります。

1. Runbook ジョブが完了すると、 **[テスト]** ページにその出力が表示されます。 出力は、次の画像のようになります。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-parallel-output.png" alt-text="PowerShell ワークフロー Runbook の Parallel の出力":::

   出力結果を確認します。 `Start-Sleep` コマンドなど、`Parallel` ブロック内のコマンドはすべて同時に実行されました。 同じコマンドでも、`Parallel` ブロック外にあるものは、異なる日時スタンプで示されているように、順次実行されました。 

1. **[テスト]** ページを閉じて、キャンバスに戻ります。

## <a name="publish-and-start-the-runbook"></a>Runbook を発行して開始する

作成した Runbook は、まだドラフト モードです。 運用環境で実行できるようにするには、発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** を選択して Runbook を発行し、確認を要求されたら **[はい]** をクリックします。

1. 以上で、 **[状態]** フィールドに、 **[発行済み]** と表示されます。 Runbook の即時開始、将来の開始スケジュールの設定、または Runbook を HTTP 呼び出しで開始できるようにする [Webhook](../automation-webhooks.md) の作成を行うために使用できる上部のオプションが表示されています。 **[開始]** を選択し、確認を求められたら **[はい]** をクリックして、Runbook を開始します。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-overview.png" alt-text="PowerShell ワークフロー Runbook の [概要] ページ":::

1. 作成した Runbook ジョブの **ジョブ** ページが開きます。 この場合、ジョブの進行状況を確認できるように、このページを開いたままにしておきます。 **[状態]** フィールドは、Runbook のテスト時に確認した状態と一致します。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-page-overview.png" alt-text="Runbook の [ジョブ] ページのスクリーンショット。":::

1. Runbook の状態が **[完了]** になったら、 **[出力]** を選択します。 出力は、テスト出力と同じようになります。

1. **[ジョブ]** ページを閉じて、Runbook の **[概要]** ページに戻ります。

1. **[リソース]** で、 **[ジョブ]** を選択します。 このページには、お使いの Runbook によって作成されたすべてのジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。

1. このジョブを選択すると、Runbook を開始したときに表示されたものと同じ **[ジョブ]** ページが開きます。 Runbook に対して作成されたジョブの詳細を表示するには、このページを使用します。 **[ジョブ]** ページを閉じて、Runbook の **[概要]** ページに戻ります。

## <a name="add-authentication-to-manage-azure-resources"></a>Azure リソースを管理するための認証を追加する

Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。 Azure リソースを管理させることにします。 サブスクリプションの資格情報を使用して認証を行わないかぎり、これを行うことはできません。 Runbook では、VM に対する管理アクションの実行を Azure で認証するために、Automation アカウントのシステム割り当てマネージド ID が使用されます。 Runbook は、ユーザー割り当てマネージド ID を使用するように簡単に変更することができます。

1. **[概要]** 、 **[編集]** の順に選択して、テキスト エディターを開きます。

1. すべての既存のコードを次のコードに置き換えます。

   ```powershell
   workflow MyFirstRunbook-Workflow
   {
   $resourceGroup = "resourceGroupName"
    
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process
    
   # Connect to Azure with system-assigned managed identity
   $AzureContext = (Connect-AzAccount -Identity).context
    
   # set and store context
   $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
   }
   ```

   リソース グループを表す有効な値を使用して、`$resourceGroup` 変数を編集します。

1. Runbook をシステム割り当てマネージド ID で実行する場合は、コードをそのままにしておきます。 ユーザー割り当てマネージド ID を使用する場合は、次のようにします。
    1. 行 9 から `$AzureContext = (Connect-AzAccount -Identity).context` を削除します。
    1. それを `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` に置き換えた後、
    1. 前に取得したクライアント ID を入力します。

1. **[保存]** 、 **[テスト] ペイン** の順に選択します。

1. **[開始]** を選択してテストを開始します。 完了すると、次のような出力が表示され、アカウントの基本情報が表示されます。 このアクションにより、資格情報が有効であることを確認できます。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/runbook-auth-output.png" alt-text="資格情報を確認する基本情報。":::

1. **[テスト]** ページを閉じて、キャンバスに戻ります。

## <a name="add-code-to-start-a-virtual-machine"></a>仮想マシンを開始するコードを追加する

これで、Runbook で Azure サブスクリプションに対する認証が行われ、リソースを管理できるようになります。 仮想マシンを起動するコマンドを追加します。 Azure サブスクリプション内の任意の VM を選択し、ここではその名前を Runbook にハードコーディングします。 

1. 右中かっこの直前に、最後の行として次のコードを追加します。 `VMName` は、VM の実際の名前に置き換えてください。 

   ```powershell
   Start-AzVM -Name "VMName" -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Runbook をテストして、VM が起動したことを確認します。 キャンバスに戻ります。

## <a name="add-input-parameters-to-the-runbook"></a>入力パラメーターを Runbook に追加する

Runbook では現在、Runbook にハードコードされている VM を起動しています。 Runbook の開始時に VM を指定できるとより便利です。 その機能を備えるために、Runbook に入力パラメーターを追加します。

1. 行 3 `$resourceGroup = "resourceGroupName"` を次のコードに置き換えます。

    ```powershell
    Param(
        [string]$resourceGroup,
        [string]$VMName
    )
   ```

1. 前の `Start-AzVM` コマンドを次のコマンドに置き換えます。

   ```powershell
   Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Runbook をテストして、VM が起動したことを確認します。 キャンバスに戻ります。

## <a name="manage-multiple-vms-simultaneously"></a>複数の VM を同時に管理する

`ForEach -Parallel` の構文を使用することにより、コレクション内の各項目のコマンドを同時に処理できます。 Runbook で次のことが実行されるようにコードを修正します。
- 仮想マシン名のコレクションを受け入れる
- 仮想マシンを停止または起動するパラメーターを受け入れる
- すべての仮想マシンに対してアクションを並行して実行する

1. すべての既存のコードを次のコードに置き換えます。

    ```powershell
    workflow MyFirstRunbook-Workflow
    {
    Param(
        [string]$resourceGroup,
        [string[]]$VMs,
        [string]$action
    )
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
    
    # Start or stop VMs in parallel
    if($action -eq "Start")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Start-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
            }
        }
    elseif ($action -eq "Stop")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Stop-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
            }
        }
    else {
            Write-Output "`r`n Action not allowed. Please enter 'stop' or 'start'."
        }
    }
    ```

1. Runbook をシステム割り当てマネージド ID で実行する場合は、コードをそのままにしておきます。 ユーザー割り当てマネージド ID を使用する場合は、次のようにします。
    1. 行 13 から `$AzureContext = (Connect-AzAccount -Identity).context` を削除します。
    1. それを `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` に置き換えた後、
    1. 前に取得したクライアント ID を入力します。

1. **[保存]** 、 **[発行]** の順に選択して、確認を求められたら **[はい]** を選択します。

1. **[概要]** ページで **[開始]** を選択します。

1. パラメーターを設定して、 **[OK]** を選択します。

   |パラメーター |説明 |
   |---|---|
   |RESOURCEGROUP|VM のリソース グループの名前を入力します。|
   |VM|構文 `["VM1","VM2","VM3"]` を使用して、仮想マシンの名前を入力します。|
   |アクション|`stop` または `start` を入力します。|

1. 仮想マシンの一覧に移動し、ページが数秒ごとに最新の情報に更新されます。 各 VM に対するアクションが並行して実行されていることを確認します。 `-Parallel` キーワードがない場合、アクションは順番に実行されています。 VM は順番に起動しますが、各 VM が **実行** フェーズに到達する時間は、各 VM の特性に基づいて、わずかに異なる可能性があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この Runbook を引き続き使用しない場合は、次の手順を使用して削除します。

1. お使いの Automation アカウントに移動します。
1. **[プロセス オートメーション]** の **[Runbook]** を選択します。
1. Runbook を選択します。
1. Runbook の **[概要]** ページで、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、PowerShell ワークフロー Runbook を作成しました。 Python 3 Runbook については、次を参照してください。

> [!div class="nextstepaction"]
> [チュートリアル:Python 3 Runbook (プレビュー) を作成する](automation-tutorial-runbook-textual-python-3.md)