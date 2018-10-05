---
title: Azure Automation で Azure モジュールを更新する
description: この記事では、Azure Automation で既定で提供される一般的な Azure PowerShell モジュールを更新する方法について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbb57753117f3c60010fe910616b8d0af5178360
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434825"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation の Azure PowerShell モジュールを更新する方法

各 Automation アカウントでは、最も一般的な Azure PowerShell モジュールが既定で提供されます。 Azure チームは Azure モジュールを定期的に更新しているため、Automation アカウントには、新しいバージョンがポータルから使用可能になったらそのアカウントで各モジュールを更新するための方法が用意されています。

モジュールは製品グループによって定期的に更新されるため、含まれているコマンドレットと共に変更が発生する可能性があります。変更の種類 (パラメーター名を変更する、コマンドレット全体を非推奨にするなど) によっては、Runbook に悪影響が及ぶことがあります。 Runbook やそれにより自動化されるプロセスに影響を与えないようにするため、先に進む前にテストと検証を行うことをお勧めします。 その目的のための専用の Automation アカウントがない場合は、アカウントの作成を検討してください。アカウントを作成すると、PowerShell モジュールの更新などの反復的な変更に加えて、Runbook の開発中の多数のさまざまなシナリオや変更についてテストを行うことができます。 結果が検証され、必要な変更をすべて適用したら、変更を必要としていたすべての Runbook の移行の調整に進み、運用環境で説明に従って次の更新を実行します。

> [!NOTE]
> 新しい Automation アカウントには、最新のモジュールが含まれていない可能性があります。

## <a name="updating-azure-modules"></a>Azure モジュールの更新

1. Automation アカウントの [モジュール] ページに、**Update Azure Modules (Azure モジュールの更新)** オプションが追加されました。 このオプションは常に有効です。<br><br> ![[モジュール] ページの [Update Azure Modules] (Azure モジュールの更新) オプション](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Azure モジュールを更新する前に、既存のスクリプトが予期したとおり確実に動作するように、テスト用の Automation アカウントでモジュールを更新することをお勧めします。
  >
  > **[Update Azure Modules] (Azure モジュールの更新)** ボタンは、パブリック クラウドでのみ使用できます。 [独立リージョン](https://azure.microsoft.com/global-infrastructure/)では使用できません。 詳細については、[モジュールを更新する別の方法](#alternative-ways-to-update-your-modules)についてのセクションを参照してください。


2. **[Azure モジュールの更新]** をクリックすると、続行するかどうかを訪ねる確認通知が表示されます。<br><br> ![Azure モジュールの更新通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. **[はい]** をクリックすると、モジュールの更新プロセスが開始されます。 更新プロセスの所要時間は約 15 分から 20 分であり、以下のモジュールが更新されます。

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    モジュールが既に最新の状態である場合、このプロセスは数秒で完了します。 更新プロセスが完了すると、通知が表示されます。<br><br> ![Azure モジュールの更新の更新状態](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    .NET Core の AzureRm モジュール (AzureRm.*.Core) は、Azure Automation ではサポートされておらず、インポートすることはできません。

> [!NOTE]
> Azure Automation は、スケジュール済みの新しいジョブの実行時に Automation アカウントの最新のモジュールを使用します。  

Runbook でこれらの Azure PowerShell モジュールのコマンドレットを使用する場合は、最新のモジュールになっていることを確認するために、この更新プロセスを 1 か月に 1 回程度実行する必要があります。 Azure Automation は、モジュールを更新するとき、AzureRunAsConnection 接続を使って認証を行います。サービス プリンシパルが有効期限切れの場合、またはサブスクリプション レベルに存在しない場合、モジュールの更新は失敗します。

## <a name="alternative-ways-to-update-your-modules"></a>モジュールを更新する別の方法

前述のように、**[Update Azure Modules] (Azure モジュールの更新)** ボタンはソブリン クラウドでは使用できず、グローバル Azure クラウドでのみ使用できます。 これは、PowerShell ギャラリーにある Azure PowerShell モジュールの最新バージョンは、これらのクラウドに現在デプロイされている Resource Manager サービスでは機能しないという事実が原因です。

ただし、[Update AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook を Automation アカウントにインポートし、それを実行することで、モジュールの更新を行えます。

`AzureRmEnvironment` パラメーターを使用して、適切な環境を Runbook に渡します。  指定できる値は、**AzureCloud**、**AzureChinaCloud**、**AzureGermanCloud**、および **AzureUSGovernmentCloud** です。 このパラメーターに値を渡さない場合、Runbook は既定で、Azure パブリック クラウドの **AzureCloud** を使用します。

PowerShell ギャラリーで提供されている最新のものではなく、Azure PowerShell モジュールの特定のバージョンを使用する場合は、これらのバージョンを、**Update-AzureModule** Runbook のオプションの `ModuleVersionOverrides` パラメーターに渡します。 例については、[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook を参照してください。 `ModuleVersionOverrides` パラメーターで指定されていない Azure PowerShell モジュールは、PowerShell ギャラリーにあるモジュールの最新バージョンで更新されます。 `ModuleVersionOverrides` パラメーターに何も渡さなかった場合、PowerShell ギャラリーにあるモジュールの最新バージョンで、すべてのモジュールが更新されます。これが **[Update Azure Modules] (Azure モジュールの更新)** ボタンの動作です。

## <a name="next-steps"></a>次の手順

* 統合モジュールの詳細、およびカスタム モジュールを作成して Automation をさらに別のシステム、サービス、またはソリューションと統合する方法については、[統合モジュール](automation-integration-modules.md)に関するページを参照してください。

* ソース管理の統合を検討してください。そのためには、[GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) または [Azure DevOps](automation-scenario-source-control-integration-with-vsts.md) を使用して、Automation Runbook と構成ポートフォリオのリリースを一元的に管理および制御します。  
