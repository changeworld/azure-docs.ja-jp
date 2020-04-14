---
title: Azure Automation での最初の PowerShell Workflow Runbook
description: PowerShell Workflow を使用して簡単なテキスト形式の Runbook を作成、テスト、および発行する手順を説明するチュートリアルです。
keywords: PowerShell ワークフロー, PowerShell ワークフローの例, ワークフロー PowerShell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367263"
---
# <a name="my-first-powershell-workflow-runbook"></a>最初の PowerShell Workflow Runbook

> [!div class="op_single_selector"]
> * [グラフィカル](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell ワークフロー](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

このチュートリアルでは、Azure Automation で [PowerShell Workflow Runbook](automation-runbook-types.md#powershell-workflow-runbooks) を作成します。 簡単な Runbook を作成、テスト、発行しながら、Runbook ジョブの状態を追跡する方法を学びます。 その後、実際に Azure リソースを管理するように Runbook を変更し、Azure 仮想マシンを開始するようにします。 最後に Runbook のパラメーターを追加することによって、Runbook をいっそう堅牢にします。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* [Automation アカウント](automation-offering-get-started.md)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。

## <a name="step-1---create-new-runbook"></a>手順 1 - 新しい Runbook を作成する

最初に、`Hello World` というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。

   Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。 既に資産がいくつかあります。 これらの資産のほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。 また、サブスクリプションに関連付けられている資格情報資産も持っている必要があります。
 
1. **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
1. **[Runbook の作成]** を選択して、新しい Runbook を作成します。
1. Runbook に **MyFirstRunbook-Workflow**という名前を付けます。
1. ここでは、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) を作成します。 **[Runbook の種類]** で、 **[PowerShell ワークフロー]** を選びます。
1. **[作成]** をクリックして Runbook を作成し、テキスト エディターを開きます。

## <a name="step-2---add-code-to-the-runbook"></a>手順 2 - コードを Runbook に追加する

Runbook に直接コードを入力するか、ライブラリ コントロールからコマンドレット、Runbook、資産を選択し、関連するパラメーターを使って Runbook に追加できます。 このチュートリアルでは、Runbook に直接コードを入力します。

1. Runbook はこの時点で空です (必要な `Workflow` キーワード、Runbook の名前、ワークフロー全体を囲む中かっこ以外)。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. かっこの間に「`Write-Output "Hello World"`」と入力します。

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. **[保存]** をクリックして Runbook を保存します。

## <a name="step-3---test-the-runbook"></a>手順 3 - Runbook をテストする

Runbook を発行して運用環境で使用できるようにする前に、テストして正常に動作することを確認します。 Runbook をテストすると、そのドラフト バージョンが実行され、その出力を対話形式で表示できます。

1. **[テスト] ウィンドウ**を選択して、[テスト] ウィンドウを開きます。
1. **[開始]** をクリックしてテストを開始し、有効になっているオプションのみをテストします。
1. [Runbook ジョブ](automation-runbook-execution.md)が作成され、その状態がペインに表示されることに注意してください。

   ジョブの最初の状態は `Queued` であり、クラウドで runbook worker が使用可能になるのをジョブが待機していることを示しています。 worker がジョブを要求すると、状態は `Starting` になります。 最後に、Runbook の実行が実際に開始されると、状態は `Running` になります。

1. Runbook ジョブが完了すると、[テスト] ウィンドウにその出力が表示されます。 ここでは、`Hello World` が表示されます。

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. テスト ウィンドウを閉じてキャンバスに戻ります。

## <a name="step-4---publish-and-start-the-runbook"></a>手順 4 - Runbook を発行して開始する

作成した Runbook は、まだドラフト モードです。 運用環境で実行できるようにするには、発行する必要があります。 Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。
1. **Runbook** ページで左にスクロールして Runbook を表示し、 **[編集状態]** の値が **発行済み**に設定されていることを確認します。
1. 右にスクロールして戻り、 **MyFirstRunbook-Workflow**のウィンドウを表示します。

   上部のオプションを使用すると、Runbook の即時開始、将来の開始スケジュールの設定、または Runbook を HTTP 呼び出しで開始できるようにする [Webhook](automation-webhooks.md) の作成を行うことができます。

1. **[開始]** を選択し、確認を求められたら **[はい]** をクリックして、Runbook を開始します。

   ![Runbook の開始](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. 作成した Runbook ジョブのジョブ ウィンドウが開きます。 この場合、ジョブの進行状況を確認できるように、ウィンドウを開いたままにしておきます。

1. ジョブの状態は **[ジョブの概要]** に表示されることに注意してください。 この状態は、Runbook のテスト時に確認した状態と一致します。

   ![ジョブの概要](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Runbook の状態が `Completed` になったら、 **[出力]** をクリックします。 [出力] ページが開き、`Hello World` メッセージが表示されます。

   ![ジョブの概要](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. [出力] ページを閉じます。

1. **[すべてのログ]** をクリックして、Runbook ジョブのストリーム ウィンドウを開きます。 出力ストリームには `Hello World` のみが表示されます。 Runbook で詳細ストリームやエラー ストリームに書き込まれている場合は、[ストリーム] ペインで Runbook ジョブの他のストリームも表示できることに注意してください。

   ![ジョブの概要](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. [ストリーム] ウィンドウと [ジョブ] ウィンドウを閉じ、 **MyFirstRunbook** ページに戻ります。
1. **[リソース]** の下の **[ジョブ]** をクリックして、この Runbook の [ジョブ] ページを開きます。 このページには、お使いの Runbook によって作成されたすべてのジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。

   ![ジョブ](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. ジョブ名をクリックすると、Runbook を開始したときに表示されたのと同じジョブ ウィンドウが開きます。 このウィンドウを使用して、Runbook に対して作成されたジョブの詳細を表示します。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>手順 5 - Azure リソースを管理するための認証を追加する

Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。 Azure リソースを管理させることにします。 サブスクリプションの資格情報を使用して認証を行わないかぎり、これを行うことはできません。 認証では、`Connect-AzAccount` コマンドレットを使用します。

>[!NOTE]
>PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は `Connect-AzAccount` の別名です。 これらのコマンドレットを使用するか、Automation アカウントの[モジュール最新バージョンに更新](automation-update-azure-modules.md)することができます。 Automation アカウントを作成したばかりのときでも、モジュールを更新する必要がある場合があります。

1. **MyFirstRunbook-Workflow** ページに移動し、 **編集**をクリックして、テキスト エディターを開きます。
2. `Write-Output` 行を削除します。
3. 中かっこ内の空白行にカーソルを置きます。
4. Automation の実行アカウントを使用して認証を処理する次のコードを入力またはコピーして貼り付けます。

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. **テスト ウィンドウ**をクリックして、Runbook をテストできるようにします。
1. **[開始]** をクリックしてテストを開始します。 完了すると、次のような出力が表示され、アカウントの基本情報が表示されます。 このアクションにより、資格情報が有効であることを確認できます。

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>手順 6 - 仮想マシンを開始するコードを追加する

これで、Runbook で Azure サブスクリプションに対する認証が行われ、リソースを管理できるようになります。 仮想マシンを起動するコマンドを追加してみましょう。 Azure サブスクリプション内の任意の VM を選択し、ここではその名前を Runbook にハードコーディングします。 複数のサブスクリプションにわたってリソースを管理しようとしている場合は、`AzContext` パラメーターを [Get-AzContext](/powershell/module/az.accounts/get-azcontext) コマンドレットと共に使用する必要があります。

1. 次に示すように、[Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) コマンドレットの呼び出しを入力することにより、起動する VM の名前とリソース グループ名を指定します。 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Runbook を保存し、**テスト ウィンドウ**をクリックしてテストできるようにします。
1. **[開始]** をクリックしてテストを開始します。 完了したら、VM が開始されたことを確認します。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>手順 7 - Runbook に入力パラメーターを追加する

Runbook では現在、Runbook にハードコードされている VM を起動しています。 Runbook の開始時に VM を指定できるとより便利です。 その機能を提供するための入力パラメーターを Runbook に追加してみましょう。

1. `VMName` と `ResourceGroupName` パラメーターの変数を Runbook に追加し、次に示す変数を `Start-AzVM` コマンドレットと共に使用します。

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Runbook を保存してテスト ウィンドウを開きます。 テストで使用される 2 つの入力変数の値を指定できるようになりました。
3. テスト ウィンドウを閉じます。
4. **[発行]** をクリックして、新しいバージョンの Runbook を発行します。
5. 開始した VM を停止します。
6. **[開始]** をクリックして Runbook を開始します。 
7. 起動する VM の **VMNAME** と **RESOURCEGROUPNAME** の値を入力します。

   ![Runbook の開始](media/automation-first-runbook-textual/automation-pass-params.png)

8. Runbook が完了したら、VM が開始されていることを確認します。

## <a name="next-steps"></a>次のステップ

* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)に関するページを参照してください。
* グラフィカルな Runbook の使用を開始するには、「[初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md)」を参照してください。
* PowerShell Runbook の使用を開始するには、「[初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)」を参照してください。
* Runbook の種類とそれらの利点や制限事項の詳細については、「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。
* PowerShell スクリプトのサポート機能の詳細については、[Azure Automation での PowerShell スクリプトのネイティブ サポート](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)に関する記事を参照してください。
