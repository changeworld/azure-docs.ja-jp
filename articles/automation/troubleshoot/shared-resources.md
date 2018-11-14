---
title: Azure Automation 共有リソースのエラーをトラブルシューティングする
description: Azure Automation 共有リソースのエラーをトラブルシューティングする方法を説明します
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263417"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>共有リソースのエラーをトラブルシューティングする

この記事では、Azure Automation の共有リソースの使用時に発生する可能性がある問題の解決策について説明します。

## <a name="modules"></a>モジュール

### <a name="module-stuck-importing"></a>シナリオ: モジュールでインポートが停止する

#### <a name="issue"></a>問題

Azure Automation でモジュールのインポートまたは更新を行うと、モジュールが**インポート**状態で停止します。

#### <a name="error"></a>Error

PowerShell モジュールのインポートは、複雑な複数手順のプロセスです。 このプロセスでは、モジュールが正しくインポートされない可能性があります。 このような場合は、インポートしているモジュールが一時的な状態で停止することがあります。 このプロセスの詳細については、[PowerShell モジュールのインポート]( /powershell/developer/module/importing-a-powershell-module#the-importing-process)に関する記事を参照してください。

#### <a name="resolution"></a>解決策

この問題を解決するには、[Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) コマンドレットを使用して、**インポート**状態で停止しているモジュールを削除する必要があります。 その後、モジュールのインポートを再試行できます。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。