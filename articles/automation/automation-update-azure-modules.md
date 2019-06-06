---
title: Azure Automation で Azure モジュールを更新する
description: この記事では、Azure Automation で既定で提供される一般的な Azure PowerShell モジュールを更新する方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a2f55891ddd383ea15da499495909b56ffb0e06d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786143"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation の Azure PowerShell モジュールを更新する方法

Automation アカウントの Azure モジュールを更新するには、オープン ソースとして利用可能な [Azure モジュールの更新 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) を使用する必要があります。 **Update-AutomationAzureModulesForAccount** Runbook を使用した Azure モジュールの更新を始めるには、Runbook を GitHub の [Update Azure modules runbook repository (Azure モジュールの更新 Runbook リポジトリ)](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) からダウンロードします。 その後、それを Automation アカウントにインポートするか、スクリプトとして実行することができます。 これを行う方法の説明は、[Update Azure modules runbook repository (Azure モジュールの更新 Runbook リポジトリ)](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) にあります。

各 Automation アカウントでは、最も一般的な AzureRM PowerShell モジュールが既定で提供されます。 Azure チームは Azure モジュールを定期的に更新します。そのため、最新の状態に保つためには、[Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Runbook を使用して Automation アカウントのモジュールを更新する必要があります。

モジュールは製品グループによって定期的に更新されるため、含まれているコマンドレットについて変更が発生することがあります。 そのため、変更の種類 (パラメーター名の変更、コマンドレット全体の非推奨化など) によっては、Runbook に悪影響が及ぶ可能性があります。

Runbook やそれにより自動化されるプロセスに影響を与えないようにするため、先に進む前にテストと検証を行ってください。 その目的のための専用の Automation アカウントがない場合は、アカウントの作成を検討してください。アカウントを作成すると、Runbook の開発中の多数のさまざまなシナリオについてテストを行うことができます。 このテストで、PowerShell モジュールの更新など、反復的な変更を行ってください。

スクリプトをローカルで開発する場合、確実に同じ結果が得られることをテストするとき、Automation アカウントに置いているものと同じバージョンのモジュールをローカルに用意することをお勧めします。 結果を検証し、必要な変更を適用した後、変更を運用環境に移行してください。

> [!NOTE]
> 新しい Automation アカウントには、最新のモジュールが含まれていない可能性があります。

## <a name="considerations"></a>考慮事項

このプロセスを使用して Azure モジュールを更新するときの考慮事項を次に示します。

* この Runbook は現在、**Azure** と **AzureRm** モジュールの更新のみをサポートしています。 [Azure PowerShell Az モジュール](/powershell/azure/new-azureps-module-az)は Automation アカウントでサポートされますが、この Runbook では更新できません。 Automation アカウントで `Az` モジュールを使用するときに考慮しなければならない重要な要素があります。詳しくは、[Automation アカウントでの Az モジュールの使用](az-modules.md)に関するページをご覧ください。

* Az モジュールが含まれている Automation アカウントでは、この Runbook を開始しないでください。

* この Runbook を開始する前に、お使いの Automation アカウントに [Azure 実行アカウント資格情報](manage-runas-account.md)が作成されていることを確認すします。

* このコードは、Runbook ではなく通常の PowerShell スクリプトとして使用できます。そのためには、最初に [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) コマンドを使用して Azure にサインインし、スクリプトに `-Login $false` を渡します。

* ソブリン クラウドでこの Runbook を使用するには、`AzureRmEnvironment` パラメーターを使用して、適切な環境を Runbook に渡します。  指定できる値は、**AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud**、および **AzureUSGovernment** です。 これらの値は `Get-AzureRmEnvironment | select Name` を使用して取得できます。 このパラメーターに値を渡さない場合、Runbook は既定で、Azure パブリック クラウドの **AzureCloud** を使用します

* PowerShell ギャラリーで提供されている最新のものではなく、Azure PowerShell モジュールの特定のバージョンを使用する場合は、それらのバージョンを、**Update-AutomationAzureModulesForAccount** Runbook の省略可能な `ModuleVersionOverrides` パラメーターに渡します。 例については、[Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook をご覧ください。 `ModuleVersionOverrides` パラメーターで指定されていない Azure PowerShell モジュールは、PowerShell ギャラリーにあるモジュールの最新バージョンで更新されます。 `ModuleVersionOverrides` パラメーターに何も渡さなかった場合は、すべてのモジュールが、PowerShell ギャラリーにある最新のモジュール バージョンで更新されます。 この動作は、 **[Azure モジュールの更新]** ボタンをクリックするのと同じことです。

## <a name="known-issues"></a>既知の問題

0 から始まる数値名を持つリソース グループにある、Automation Account の AzureRM モジュールの更新については、既知の問題があります。 Automation Account で Azure モジュールを更新するには、それが英数字名を持つリソース グループになければなりません。 0 から始まる数値名を持つリソース グループは、現時点で AzureRM モジュールを更新できません。

## <a name="next-steps"></a>次の手順

詳しくは、オープン ソースの [Azure モジュールの更新 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) をご覧ください。
