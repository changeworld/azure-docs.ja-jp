---
title: Azure Automation の FAQ | Microsoft Docs
description: Azure Automation についてよく寄せられる質問の回答です。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921758"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation についてよく寄せられる質問

この Microsoft FAQ は、Azure Automation についてよく寄せられる質問の一覧です。 その機能について追加のご質問がある場合は、ディスカッション フォーラムにアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="update-management-solution"></a>Update Management ソリューション

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>予期しない OS レベルのアップグレードを防止することはできますか?

Red Hat Enterprise Linux など、Linux バリエーションの中には、パッケージを使用して OS レベルのアップグレードが発生することがあります。 これにより、OS バージョン番号の変更を伴う Update Management が実行される可能性があります。 Update Management がパッケージを更新するときに使用する方法は、管理者がローカルで Linux マシンに使用するものと同じであるため、この動作は意図的なものです。

Update Management のデプロイによる OS バージョンの更新を回避するには、**除外**機能を使用します。

Red Hat Enterprise Linux で、除外するパッケージ名は redhat-release-server.x86_64 です

### <a name="why-arent-criticalsecurity-updates-applied"></a>重要な更新プログラムまたはセキュリティ更新プログラムが適用されないのはなぜですか?

更新プログラムを Linux マシンにデプロイする場合、更新プログラムの分類を選択できます。 このオプションにより、更新プログラムはフィルター処理され、指定した条件を満たすマシンに適用される更新プログラムに絞られます。 このフィルターは、更新プログラムが展開されるときに、マシンに対してローカルに適用されます。

Update Management は更新プログラムの強化をクラウドで実行するため、Update Management では、一部の更新プログラムに、セキュリティへの影響ありとしてフラグが設定されることがありますが、ローカル マシンにはその情報がありません。 このため、重大な更新プログラムを Linux マシンに適用する場合、そのマシンにセキュリティへの影響ありとマークされていない更新プログラムが存在する可能性があり、その結果、これらの更新プログラムは適用されません。 ただし、Update Management には関連更新プログラムに関する追加情報があるため、そのマシンは引き続き、非対応として報告されることがあります。

更新プログラムの分類ごとの更新プログラムのデプロイは、RTM バージョンの CentOS では動作しません。 CentOS に更新プログラムを正しくデプロイするには、更新プログラムが確実に適用されるように、すべての分類を選択します。 SUSE では、分類として *[他の更新プログラム]* "**のみ**" を選択すると、zypper (パッケージ マネージャー) に関連するセキュリティ更新プログラムまたはその依存関係がまず必要になる場合は、いくつかのセキュリティ更新プログラムもインストールされることがあります。 この動作は、zypper の制限です。 場合によっては、更新プログラムのデプロイを再実行する必要があります。 確認するには、更新プログラムのログを調べてください。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure テナントの境界を越えて更新プログラムをデプロイすることはできますか?

Update Management にレポートする別の Azure テナントに、修正プログラムを適用する必要があるマシンが存在する場合は、次の対処法を使用して、それらをスケジュールを設定する必要があります。 スケジュールは、[New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) コマンドレットと `-ForUpdate` スイッチを使用して作成できます。[New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) コマンドレットを使用する際、`-NonAzureComputer` パラメーターに他のテナントのマシンを渡すことができます。 以下の例は、その方法を示しています。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>次のステップ

こちらでご質問の回答が見つからない場合は、次のフォーラムで他の質問と回答を参照できます。

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Update Management ソリューションに関する一般的なフィードバックについては、[フィードバック フォーラム](https://feedback.azure.com/forums/905242-update-management)にアクセスしてください。