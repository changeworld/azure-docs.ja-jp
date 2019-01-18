---
title: Azure Automation 共有リソースのエラーをトラブルシューティングする
description: Azure Automation 共有リソースのエラーをトラブルシューティングする方法を説明します
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848461"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>共有リソースのエラーをトラブルシューティングする

この記事では、Azure Automation の共有リソースの使用時に発生する可能性がある問題の解決策について説明します。

## <a name="modules"></a>モジュール

### <a name="module-stuck-importing"></a>シナリオ:モジュールでインポートが停止する

#### <a name="issue"></a>問題

Azure Automation でモジュールのインポートまたは更新を行うと、モジュールが**インポート**状態で停止します。

#### <a name="cause"></a>原因

PowerShell モジュールのインポートは、複雑な複数手順のプロセスです。 このプロセスでは、モジュールが正しくインポートされない可能性があります。 この問題が発生すると、インポートしているモジュールが一時的な状態で停止することがあります。 このプロセスの詳細については、[PowerShell モジュールのインポート]( /powershell/developer/module/importing-a-powershell-module#the-importing-process)に関する記事を参照してください。

#### <a name="resolution"></a>解決策

この問題を解決するには、[Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) コマンドレットを使用して、**インポート**状態で停止しているモジュールを削除する必要があります。 その後、モジュールのインポートを再試行できます。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="run-as-accounts"></a>実行アカウント

### <a name="unable-create-update"></a>シナリオ:実行アカウントを作成または更新できない

#### <a name="issue"></a>問題

実行アカウントを作成または更新しようとすると、次のエラー メッセージのようなエラーが表示されます。

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

実行アカウントを作成または更新するために必要なアクセス許可がないか、リソースがリソース グループ レベルでロックされています。

#### <a name="resolution"></a>解決策

実行アカウントを作成または更新するには、実行アカウントで使用するさまざまなリソースに対する適切なアクセス許可が必要です。 実行アカウントの作成または更新に必要なアクセス許可については、[実行アカウントのアクセス許可](../manage-runas-account.md#permissions)に関する記事を参照してください。

問題の原因がロックの場合は、ロックを解除しても問題ないことを確認し、ロックされているリソースに移動し、ロックを右クリックして **[削除]** を選択してロックを解除します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。