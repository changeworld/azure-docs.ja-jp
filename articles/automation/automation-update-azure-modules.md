---
title: Azure Automation の Azure PowerShell モジュールを更新する
description: この記事では、Azure Automation で既定で提供される一般的な Azure PowerShell モジュールを更新する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769662"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Azure Automation の Azure PowerShell モジュールを更新する

Automation アカウントの Azure モジュールを更新するには、オープン ソースとして利用可能な [Azure モジュールの更新 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) を使用する必要があります。 **Update-AutomationAzureModulesForAccount** Runbook を使用した Azure モジュールの更新を始めるには、Runbook を GitHub の [Update Azure modules runbook repository (Azure モジュールの更新 Runbook リポジトリ)](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) からダウンロードします。 その後、それを Automation アカウントにインポートするか、スクリプトとして実行することができます。 Automation アカウントに Runbook をインポートする方法については、[Runbook のインポート](manage-runbooks.md#importing-a-runbook)に関する記事をご覧ください。

各 Automation アカウントでは、最も一般的な PowerShell モジュールが既定で提供されます。 Azure チームは Azure モジュールを定期的に更新しています。 そのため、Automation アカウントのモジュールを最新の状態に保つには、[Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Runbook を使用する必要があります。

モジュールは製品グループによって定期的に更新されるため、含まれているコマンドレットについて変更が発生することがあります。 こうした変更 (パラメーター名の変更、コマンドレット全体の非推奨化など) により、Runbook に悪影響が及ぶ可能性があります。

Runbook やそれにより自動化されるプロセスに影響を与えないようにするため、先に進む前にテストと検証を行ってください。 その目的のための専用の Automation アカウントがない場合は、アカウントの作成を検討してください。アカウントを作成すると、Runbook の開発中の多数のさまざまなシナリオについてテストを行うことができます。 このテストで、PowerShell モジュールの更新など、反復的な変更を行ってください。

スクリプトをローカルで開発する場合、テスト時に確実に同じ結果が得られるようにするために、Automation アカウントに置いているものと同じバージョンのモジュールをローカルに用意することをお勧めします。 結果を検証し、必要な変更を適用した後、変更を運用環境に移行してください。

> [!NOTE]
> 新しい Automation アカウントには、最新のモジュールが含まれていない可能性があります。

> [!NOTE]
> グローバル モジュールを削除することはできません。これらは、Automation で提供されるそのまま利用できるモジュールです。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 

## <a name="considerations"></a>考慮事項

この記事を使用して Azure モジュールを更新するときの考慮事項を次に示します。

* この記事で説明されている Runbook は、Azure、AzureRM、および Az モジュールの更新を既定でサポートしています。 この Runbook による Az.Automation モジュールの更新の詳細については、[Azure モジュールの更新 Runbook の README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) を確認してください。 Automation アカウントで Az モジュールを使用するときに考慮しなければならないその他の重要な要素があります。 詳細については、「[Azure Automation でモジュールを管理する](shared-resources/modules.md)」を参照してください。

* この Runbook を開始する前に、お使いの Automation アカウントに [Azure 実行アカウント資格情報](manage-runas-account.md)が作成されていることを確認すします。

* このコードは、Runbook ではなく通常の PowerShell スクリプトとして使用できます。そのためには、最初に [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) コマンドレットを使用して Azure にサインインし、スクリプトに `-Login $false` を渡します。

* ソブリン クラウドでこの Runbook を使用するには、`AzEnvironment` パラメーターを使用して、適切な環境を Runbook に渡します。  指定できる値は、AzureCloud (Azure パブリック クラウド)、AzureChinaCloud、AzureGermanCloud、および AzureUSGovernment です。 これらの値は `Get-AzEnvironment | select Name` を使用して取得できます。 このコマンドレットに値を渡さないと、Runbook は既定で AzureCloud を使用します。

* PowerShell ギャラリーで提供されている最新のモジュールではなく、Azure PowerShell モジュールの特定のバージョンを使用する場合は、それらのバージョンを、**Update-AutomationAzureModulesForAccount** Runbook の省略可能な `ModuleVersionOverrides` パラメーターに渡します。 例については、[Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook をご覧ください。 `ModuleVersionOverrides` パラメーターで指定されていない Azure PowerShell モジュールは、PowerShell ギャラリーにあるモジュールの最新バージョンで更新されます。 `ModuleVersionOverrides` パラメーターに何も渡さなかった場合は、すべてのモジュールが、PowerShell ギャラリーにある最新のモジュール バージョンで更新されます。 この動作は、 **[Azure モジュールの更新]** ボタンをクリックするのと同じことです。

## <a name="next-steps"></a>次のステップ

詳しくは、オープン ソースの [Azure モジュールの更新 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) をご覧ください。
