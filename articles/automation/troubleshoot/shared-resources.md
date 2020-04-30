---
title: Azure Automation での共有リソースのトラブルシューティング
description: Azure Automation 共有リソースに関する問題のトラブルシューティングと解決方法について説明します。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733569"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Azure Automation での共有リソースのトラブルシューティング

この記事では、Azure Automation の[共有リソース](../automation-intro.md#shared-resources)の使用時に発生する可能性がある問題の解決策について説明します。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="modules"></a>モジュール

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>シナリオ:インポート中にモジュールが停止する

#### <a name="issue"></a>問題

Azure Automation モジュールをインポートまたは更新しているときに、モジュールがインポート中の状態で停止します。

#### <a name="cause"></a>原因

PowerShell モジュールのインポートは複雑なマルチステップ プロセスであるため、モジュールが正しくインポートされず、一時的な状態で停止することがあります。 このインポート プロセスの詳細については、「[PowerShell モジュールのインポート](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)」を参照してください。

#### <a name="resolution"></a>解像度

この問題を解決するには、[Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) コマンドレットを使用して、インポート中の状態で停止しているモジュールを削除する必要があります。 その後、モジュールのインポートを再試行できます。

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>シナリオ:更新の試行後、インポート中に AzureRM モジュールが停止する

#### <a name="issue"></a>問題

AzureRM モジュールの更新を試行した後、アカウントに以下のメッセージのバナーが残ります。

```error
Azure modules are being updated
```

#### <a name="cause"></a>原因

0 から始まる数値名を持つリソース グループに含まれている Automation アカウントでの AzureRM モジュールの更新には、既知の問題があります。

#### <a name="resolution"></a>解像度

Automation アカウントで AzureRM モジュールを更新するには、英数字名を持つリソース グループにアカウントが含まれている必要があります。 0 から始まる数値名を持つリソース グループは、現時点で AzureRM モジュールを更新できません。

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>シナリオ:モジュールがインポートに失敗するか、インポート後、コマンドレットを実行できない

#### <a name="issue"></a>問題

モジュールがインポートに失敗するか、成功してもコマンドレットが抽出されません。

#### <a name="cause"></a>原因

モジュールが正常に Azure Automation にインポートできない一般的な理由には次が考えられます。

* その構造が、Automation で必要な構造と一致しません。
* Automation アカウントにデプロイされていない別のモジュールにモジュールが依存しています。
* モジュールのフォルダーにその依存関係がありません。
* モジュールのアップロードに [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) コマンドレットを使用していますが、完全なストレージ パスを指定していないか、パブリックにアクセスできる URL でモジュールを読み込んでいません。

#### <a name="resolution"></a>解像度

この問題を修正するには、次の解決策のいずれかを使用します。

* モジュールが次の形式に従っていることを確認します。ModuleName.zip -> ModuleName またはバージョン番号 -> (ModuleName.psm1、ModuleName.psd1)。
* **.psd1** ファイルを開き、モジュールに依存関係があるかどうかを確認します。 依存関係がある場合、それらのモジュールを Automation アカウントにアップロードします。
* 参照される **.dll** ファイルがモジュール フォルダーにあることを確認します。

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>シナリオ:モジュールの更新中に Update-AzureModule.ps1 が中断する

#### <a name="issue"></a>問題

[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook を使用して Azure モジュールを更新しているときに、モジュールの更新プロセスが中断されます。

#### <a name="cause"></a>原因

**Update-AzureModule.ps1** を使用している場合は、同時に更新されるモジュールの数を決定する既定の設定は 10 です。 更新プロセスは、同時に更新されるモジュールが多すぎると、エラーが発生しやすくなります。

#### <a name="resolution"></a>解像度

同じ Automation アカウントですべての AzureRM または Az モジュールが必要になることは、通常はありません。 必要な特定のモジュールのみをインポートすることをお勧めします。

> [!NOTE]
> `Az.Automation` または `AzureRM.Automation` モジュール全体をインポートしないようにしてください。これにより、含まれているすべてのモジュールがインポートされます。

更新プロセスが中断する場合は、`SimultaneousModuleImportJobCount` パラメーターを **Update-AzureModules.ps1** スクリプトに追加し、既定値の 10 より小さい値を指定してください。 このロジックを実装する場合、3 または 5 の値から始めることをお勧めします。 `SimultaneousModuleImportJobCount` は、Azure モジュールの更新に使用される **Update-AutomationAzureModulesForAccount** システム Runbook のパラメーターです。 この調整を行うと、更新プロセスの実行時間は長くなりますが、完了する可能性が高くなります。 次の例に、パラメーターと Runbook のそれを配置する場所を示します。

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>実行アカウント

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>シナリオ:実行アカウントを作成または更新できない

#### <a name="issue"></a>問題

実行アカウントを作成または更新しようとすると、次のエラー メッセージのようなエラーが表示されます。

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

実行アカウントを作成または更新するために必要なアクセス許可がないか、リソースがリソース グループ レベルでロックされています。

#### <a name="resolution"></a>解像度

実行アカウントを作成または更新するには、実行アカウントで使用するさまざまなリソースに対する適切な[アクセス許可](../manage-runas-account.md#permissions)が必要です。 

問題の原因がロックである場合は、ロックを削除しても構わないか確認します。 次に、Azure portal でロックされているリソースに移動し、ロックを右クリックして、 **[削除]** をクリックします。

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>シナリオ:Runbook の実行時に "DLL 'iplpapi.dll' の 'GetPerAdapterInfo' というエントリ ポイントが見つかりません" というエラーが表示される

#### <a name="issue"></a>問題

Runbook を実行すると、以下の例外を受け取ります。

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>原因

このエラーは、正しく構成されていない [[アカウントとして実行]](../manage-runas-account.md) によって発生する可能性があります。

#### <a name="resolution"></a>解像度

実行アカウントが正しく構成されていることを確認します。 次に、Azure で認証するための適切なコードが Runbook にあることを確認します。 次のサンプルは、[アカウントとして実行] を使用して Runbook で Azure を認証するコードのスニペットを示しています。

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>次のステップ

自分の問題が上記にない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。
* Azure サポート インシデントを送信する。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
