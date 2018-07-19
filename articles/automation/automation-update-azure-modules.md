---
title: Azure Automation で Azure モジュールを更新する
description: この記事では、Azure Automation で既定で提供される一般的な Azure PowerShell モジュールを更新する方法について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 90aa19d690b1b4ab28c3a65a287a10aaf6a03ac6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929034"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation の Azure PowerShell モジュールを更新する方法

各 Automation アカウントでは、最も一般的な Azure PowerShell モジュールが既定で提供されます。 Azure チームは Azure モジュールを定期的に更新しているため、Automation アカウントには、新しいバージョンがポータルから使用可能になったらそのアカウントで各モジュールを更新するための方法が用意されています。

モジュールは製品グループによって定期的に更新されるため、含まれているコマンドレットと共に変更が発生する可能性があります。変更の種類 (パラメーター名を変更する、コマンドレット全体を非推奨にするなど) によっては、Runbook に悪影響が及ぶことがあります。 Runbook やそれにより自動化されるプロセスに影響を与えないようにするため、先に進む前にテストと検証を行うことをお勧めします。 その目的のための専用の Automation アカウントがない場合は、アカウントの作成を検討してください。アカウントを作成すると、PowerShell モジュールの更新などの反復的な変更に加えて、Runbook の開発中の多数のさまざまなシナリオや変更についてテストを行うことができます。 結果が検証され、必要な変更をすべて適用したら、変更を必要としていたすべての Runbook の移行の調整に進み、運用環境で説明に従って次の更新を実行します。

> [!NOTE]
> 新しい Automation アカウントには、最新のモジュールが含まれていない可能性があります。

## <a name="updating-azure-modules"></a>Azure モジュールの更新

1. Automation アカウントの [モジュール] ページに、**Update Azure Modules (Azure モジュールの更新)** オプションが追加されました。 このオプションは常に有効です。<br><br> ![[モジュール] ページの [Update Azure Modules] (Azure モジュールの更新) オプション](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

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

> [!NOTE]
> Azure Automation は、スケジュール済みの新しいジョブの実行時に Automation アカウントの最新のモジュールを使用します。    

Runbook でこれらの Azure PowerShell モジュールのコマンドレットを使用する場合は、最新のモジュールになっていることを確認するために、この更新プロセスを 1 か月に 1 回程度実行する必要があります。 Azure Automation は、モジュールを更新するとき、AzureRunAsConnection 接続を使って認証を行います。サービス プリンシパルが有効期限切れの場合、またはサブスクリプション レベルに存在しない場合、モジュールの更新は失敗します。

## <a name="next-steps"></a>次の手順

* 統合モジュールの詳細、およびカスタム モジュールを作成して Automation をさらに別のシステム、サービス、またはソリューションと統合する方法については、[統合モジュール](automation-integration-modules.md)に関するページを参照してください。

* ソース管理の統合を検討してください。そのためには、[GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) または [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) を使用して、Automation Runbook と構成ポートフォリオのリリースを一元的に管理および制御します。  
