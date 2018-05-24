---
title: Azure Automation の Hybrid Runbook Worker での Runbook の実行
description: この記事では、Hybrid Runbook Worker ロールを持つローカル データセンターまたはクラウド プロバイダーのコンピューターでの Runbook の実行について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a4cf32ea7b77db3fc78a404063b8a4d69ecebf58
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195711"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook の実行

Azure Automation で実行される Runbook と、Hybrid Runbook Worker で実行される Runbook の構造に違いはありません。 Hybrid Runbook Worker をターゲットとする Runbook は通常ローカル コンピューター自体のリソースまたはデプロイされているローカル環境内のリソースを管理しますが、Azure Automation の Runbook は通常 Azure クラウド内のリソースを管理するため、ほとんどの場合、それぞれで使用する Runbook は大きく異なります。

Hybrid Runbook Worker で実行する Runbook を作成するときは、ハイブリッド worker をホストしているマシンで Runbook を編集し、テストする必要があります。 ホスト マシンには、ローカル リソースの管理とアクセスのために必要となる、すべての PowerShell モジュールとネットワーク アクセスがあります。 ハイブリッド worker マシンで Runbook を編集し、テストすると、ハイブリッド worker で実行するために使用できる Azure Automation 環境に Runbook をアップロードできるようになります。 Windows のローカル システム アカウント、または Linux の特殊なユーザー アカウント **nxautomation** でジョブを実行すると、細かな差異が生じる可能性があります。Hybrid Runbook Worker 用の Runbook を作成するときは、このことを考慮する必要があります。

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook の開始

[Azure Automation での Runbook の開始](automation-starting-a-runbook.md) 」では、Runbook を開始するためのさまざまな方法を説明しています。 Hybrid Runbook Worker は、Hybrid Runbook Worker グループの名前を指定できる **RunOn** オプションを追加します。 グループが指定されている場合は、Runbook が取得され、そのグループ内のいずれかの worker によって実行されます。 このオプションが指定されていない場合は、Azure Automation で通常どおり実行されます。

Azure Portal で Runbook を開始する際に、**Azure** または**ハイブリッド worker** を選択できる **[実行場所を選択して実行]** オプションが表示されます。 **Hybrid Worker**を選択した場合は、ドロップダウン リストからグループを選択できます。

**RunOn** パラメーターを使用します。 次のコマンドを使用し、Windows PowerShell を使用して MyHybridGroup という名前の Hybrid Runbook Worker グループで Test-Runbook という名前の Runbook を開始できます。

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> The **RunOn** parameter was added to the **Start-AzureAutomationRunbook** cmdlet in version 0.9.1 of Microsoft Azure PowerShell. 以前のバージョンがインストールされている場合は、 [最新バージョンをダウンロード](https://azure.microsoft.com/downloads/) する必要があります。 PowerShell から Runbook を開始するワークステーションでは、このバージョンをインストールするだけです。 ワーカー コンピューターから Runbook を開始する場合を除き、そのコンピューターにインストールする必要はありません。

## <a name="runbook-permissions"></a>Runbook のアクセス許可

Hybrid Runbook Worker で実行されている Runbook は Azure 外部のリソースにアクセスするため、Azure のリソースへの認証に Runbook で通常使用される方法と同じものを使用することはできません。 Runbook にローカル リソースに対して独自の認証機能を用意するか、すべての Runbook にユーザー コンテキストを提供する RunAs アカウントを指定することができます。

### <a name="runbook-authentication"></a>Runbook の認証

既定で、Runbook はオンプレミス コンピューターの Windows のローカル システム アカウントおよび Linux の特殊なユーザー アカウント **nxautomation** のコンテキストで実行されるため、アクセスするリソースを独自に認証する必要があります。

資格情報を指定できるコマンドレットで Runbook の[資格情報](automation-credentials.md)資産と[証明書](automation-certificates.md)資産を使用することで、さまざまなリソースへの認証が可能になります。 次の例は、コンピューターを再起動する Runbook の一部を示しています。 資格情報資産から資格情報を取得し、変数資産からはコンピューター名を取得して、Restart-Computer コマンドレットでこれらの値を使用します。

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

また、[PSCredential 共通パラメーター](/powershell/module/psworkflow/about/about_workflowcommonparameters)で指定された資格情報で別のコンピューター上でコード ブロックを実行できるようにする [InlineScript](automation-powershell-workflow.md#inlinescript) を利用することもできます。

### <a name="runas-account"></a>RunAs アカウント

既定で、Hybrid Runbook Worker は Windows のローカル システムおよび Linux の特殊なユーザー アカウント **nxautomation** を使用して Runbook を実行します。 Runbook でローカル リソースへの独自の認証機能を用意するのではなく、ハイブリッド worker グループの **RunAs** アカウントを指定することができます。 ローカル リソースに対するアクセス権を持つ [資格情報資産](automation-credentials.md) を指定すると、グループ内の Hybrid Runbook Worker で Runbook を実行するときに、その資格情報ですべての Runbook が実行されます。

資格情報のユーザー名は、次の形式にする必要があります。

* ドメイン\ユーザー名
* username@domain
* ユーザー名 (オンプレミス コンピューターのローカルのアカウントの場合)

ハイブリッド worker グループの RunAs アカウントを指定するには、以下の手順を実行します。

1. ローカル リソースに対するアクセス権を持つ [資格情報資産](automation-credentials.md) を作成します。
2. Azure ポータルで Automation アカウントを開きます。
3. **[ハイブリッド Worker グループ]** タイルを選択し、グループを選択します。
4. **[すべての設定]**、**[ハイブリッド Worker グループの設定]** の順に選択します。
5. **[実行者]** を **[既定]** から **[カスタム]** に変更します。
6. 資格情報を選択し、 **[保存]** をクリックします。

### <a name="automation-run-as-account"></a>Automation 実行アカウント

Azure にリソースをデプロイするために自動化されたビルド プロセスの一部として、オンプレミス システムにアクセスして、デプロイ シーケンス内のタスクまたは一連のステップをサポートする必要が生じる場合があります。 実行アカウントを使用した Azure の認証をサポートするには、実行アカウントの証明書をインストールする必要があります。

次の PowerShell Runbook、*Export-RunAsCertificateToHybridWorker* は、Azure Automation アカウントから実行証明書をエクスポートし、同じアカウントに接続されているハイブリッド worker のローカル マシン証明書ストアにそれをダウンロードおよびインポートします。 このステップが完了すると、worker が実行アカウントを使用して Azure の認証に成功できることが確認されます。

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

*Export-RunAsCertificateToHybridWorker* Runbook を、`.ps1` 拡張子を付けてコンピューターに保存します。 これを Automation アカウントにインポートし、変数 `$Password` の値を自分のパスワードで変更して、Runbook を編集します。 実行アカウントを使用して Runbook の実行および認証を行うハイブリッド worker グループを対象に、Runbook を発行し、実行します。 ジョブ ストリームによって、ローカル マシン ストアに証明書をインポートしようとする試行が報告されます。これに続いて、サブスクリプションに定義されている Automation アカウントの数に応じて複数の行で、認証が正常に完了したかどうかが報告されます。

## <a name="job-behavior"></a>ジョブの動作

Hybrid Runbook Worker で実行されるジョブの処理は、Azure サンドボックスで実行される場合の処理と少し異なります。 主な違いの 1 つは、Hybrid Runbook Worker ではジョブ期間に制限がないことです。 実行時間の長い Runbook がある場合は、ハイブリッド worker をホストしているマシンが再起動された場合などに、再起動に対する回復性があることを確認します。 ハイブリッド worker のホスト マシンが再起動された場合、実行中の Runbook ジョブは最初から再起動されるか、PowerShell ワークフロー Runbook の最後のチェックポイントから再起動されます。 Runbook ジョブが 3 回以上再起動された場合、そのジョブは中断されます。

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook のトラブルシューティング

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。 また、ハイブリッド worker は、**Application and Services Logs\Microsoft-SMA\Operational** にある Windows イベント ログにエラーとイベントを記録します。 Worker で実行される Runbook に関連するイベントは、**Application and Services Logs\Microsoft-Automation\Operational** に書き込まれます。 **Microsoft SMA** ログには、Worker にプッシュされる Runbook ジョブと Runbook の処理に関連するイベントが他にも多く含まれています。 **Microsoft Automation** イベント ログには、Runbook の実行のトラブルシューティングに役立つ詳細情報があるイベントは多くありませんが、Runbook ジョブの結果が含まれています。

[Runbook の出力とメッセージ](automation-runbook-output-and-messages.md) は Hybrid Worker から Azure Automation に送られます。 他の Runbook と同じ方法で、Verbose および Progress ストリームも有効にできます。

Runbook が正常に完了せず、ジョブの概要で状態が**中断**になっている場合は、トラブルシューティングの記事「[Hybrid Runbook Worker: Runbook ジョブが中断状態で終了する](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended)」を参照してください。

## <a name="next-steps"></a>次の手順

* Runbook を開始するために使用できるさまざまな方法の詳細については、「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」を参照して下さい。
* テキスト エディターを使用して、Azure Automation で PowerShell Runbook と PowerShell ワークフロー Runbook を操作するためのさまざまな手順については、[Azure Automation での Runbook の編集](automation-edit-textual-runbook.md)を参照してください。
