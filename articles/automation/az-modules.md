---
title: Azure Automation での Az モジュールのサポート
description: この記事では、Azure Automation での Az モジュールの使用について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: a8d6d25a2ba7f0040b13982f14f3d6081ac32f15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637997"
---
# <a name="az-module-support-in-azure-automation"></a>Azure Automation での Az モジュールのサポート

Azure Automation では、Runbook での [Azure PowerShell の Az モジュール](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)の使用がサポートされています。 ロールアップの Az モジュールが新規または既存の Automation アカウントに自動的にインポートされることはありません。 

## <a name="considerations"></a>考慮事項

Azure Automation で Az モジュールを使用する際には、考慮しなければならない点がいくつかあります。

* Runbook とモジュールは、Automation アカウントの上位レベルのソリューションで使用できます。 そのため、Runbook を編集したり、モジュールをアップグレードしたりすると、ご利用のソリューションで問題が発生する可能性があります。 新しい Az モジュールをインポートする前に、別の Automation アカウントですべての Runbook とソリューションを十分にテストする必要があります。 

* モジュールを変更すると、[起動/停止](automation-solution-vm-management.md)ソリューションに悪影響を及ぼす可能性があります。 

* Automation アカウントに Az モジュールをインポートしても、Runbook で使用される PowerShell セッションにモジュールが自動的にインポートされることはありません。 モジュールは、次の状況で PowerShell セッションにインポートされます。

    * Runbook がモジュールからコマンドレットを呼び出したとき
    * Runbook で `Import-Module` コマンドレットを使用してモジュールが明示的にインポートされたとき
    * モジュールに応じて、Runbook で別のモジュールがインポートされたとき

> [!NOTE]
> ソリューションが含まれる Automation アカウントでは、モジュールと Runbook の変更はお勧めしません。 このプロビジョニングは、Az モジュールに固有のものではありません。 Automation アカウントで何らかの変更を行うときは、その点を考慮する必要があります。

> [!IMPORTANT]
> Automation アカウントの Runbook では、Az モジュールと [AzureRM](https://www.powershellgallery.com/packages/AzureRM/6.13.1) モジュールのいずれかを PowerShell セッションにインポートし、両方はインポートしないようにします。 Runbook で AzureRM モジュールの前に Az モジュールをインポートしても、その Runbook は完了します。 ただし、[Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) コマンドレットを参照するエラーがジョブ ストリームに表示され、コマンドレットが正常に実行されない可能性があります。 また、Runbook で Az モジュールの前に AzureRM モジュールをインポートしても、その Runbook は完了します。 ただし、この場合は、Az と AzureRM の両方を同じセッションにインポートしたり、同じ Runbook で使用したりできないというエラーがジョブ ストリームで発生します。

## <a name="migrating-to-az-modules"></a>Az モジュールへの移行

テスト Automation アカウントで Az モジュールへの移行をテストすることをお勧めします。 アカウントを作成したら、このセクションの手順を使用して、モジュールを操作できます。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>AzureRM モジュールが使用されるすべての Runbook を停止してスケジュールを解除する

AzureRM モジュールが使用される既存の Runbook を実行しないようにするために、影響を受けるすべての Runbook を停止し、スケジュールを解除します。 この例のようなコードを実行して、存在するスケジュールと削除されるスケジュールを確認できます。

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

各スケジュールを個別に確認し、後で必要に応じて Runbook で再スケジュールできるようにすることが重要です。

### <a name="import-the-az-modules"></a>Az モジュールをインポートする

>[!NOTE]
>Runbook では必要な Az モジュールのみをインポートするようにします。 ロールアップの Az モジュールにはすべての Az モジュールが含まれているので、これはインポートしないようにしてください。 このガイダンスは、すべてのモジュールについて同じです。

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) モジュールは、他の Az モジュールに対する依存関係です。 そのため、他のすべてのモジュールをインポートする前に、自分の Runbook でこのモジュールを Automation アカウントにインポートする必要があります。

Azure portal でモジュールをインポートするには:

1. Automation アカウントから、 **[共有リソース]** の **[モジュール]** を選択します。 
2. **[Browse Gallery]\(ギャラリーの参照\)** をクリックして、[Browse Gallery]\(ギャラリーの参照\) ページを開きます。  
3. 検索バーにモジュール名を入力します (`Az.Accounts` など)。 
4. [PowerShell Module]\(PowerShell モジュール\) ページで、 **[インポート]** をクリックしてモジュールを Automation アカウントにインポートします。

![Automation アカウントからモジュールをインポートする](media/az-modules/import-module.png)

このインポート プロセスは、インポートするモジュールを [PowerShell ギャラリー](https://www.powershellgallery.com)で検索して行うこともできます。 モジュールを見つけたら、それを選択し、 **[Azure Automation]** タブを選択して **[Deploy to Azure Automation]\(Azure Automation にデプロイ\)** をクリックします。

![ギャラリーからモジュールを直接インポートする](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Runbook のテスト

Az モジュールを Automation アカウントにインポートしたら、それらのモジュールを使用するように Runbook を編集できます。 大半のコマンドレットの名前は、AzureRM モジュールの場合と同じです。ただし、AzureRM (または AzureRm) プレフィックスは Az に変更されています。 この名前付け規則に従わないモジュールの一覧については、[例外の一覧](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)を参照してください。

新しいコマンドレットを使用するという Runbook の変更をテストする 1 つの方法として、Runbook の先頭で `Enable-AzureRmAlias -Scope Process` を使用できます。 このコマンドを Runbook に追加することで、変更なしでスクリプトを実行できます。

## <a name="after-migration-details"></a>移行後の詳細

移行の完了後は、Automation アカウントで AzureRM モジュールを使用して Runbook を開始しないようにします。 また、このアカウントでは AzureRM モジュールのインポートと更新を行わないこともお勧めします。 アカウントが Az に移行されたと考えて、Az モジュールのみを操作しましょう。 

新しい Automation アカウントを作成すると、依然として既存の AzureRM モジュールがインストールされます。 引き続き、AzureRM コマンドレットを使用してチュートリアルの Runbook を更新できます。 ただし、これらの Runbook は実行しないでください。

## <a name="next-steps"></a>次のステップ

Az モジュールの使用について詳しくは、[Az モジュールの入門](/powershell/azure/get-started-azureps?view=azps-1.1.0)に関する記事をご覧ください。
