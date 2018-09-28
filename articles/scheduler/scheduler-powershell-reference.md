---
title: PowerShell コマンドレット リファレンス - Azure Scheduler
description: Azure Scheduler の PowerShell コマンドレットについて
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: a06439150ac255e7b436082ecc88702bf0c1dec1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991098"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure Scheduler の PowerShell コマンドレット リファレンス

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、廃止予定の Azure Scheduler の後継です。 ジョブをスケジュールするには、[Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) を代わりにお使いください。 

Scheduler ジョブとジョブ コレクションを作成および管理するためのスクリプトを作成するために、PowerShell コマンドレットを使用できます。 この記事では、[Azure Scheduler 用の主要な PowerShell コマンドレット](/powershell/module/azurerm.scheduler)と、その参考記事へのリンクを列挙します。 お使いの Azure サブスクリプションの Azure PowerShell をインストールするには、[Azure PowerShell をインストールして構成する方法](/powershell/azure/overview)を参照してください。 [Azure Resource Manager コマンドレット](/powershell/azure/overview)の詳細については、「[Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

| コマンドレット | 説明 |
|--------|-------------|
| [Disable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |ジョブ コレクションを無効にします。 |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |ジョブ コレクションを有効にします。 |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Scheduler ジョブを取得します。 |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |ジョブ コレクションを取得します。 |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |ジョブ履歴を取得します。 |
| [New-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |HTTP ジョブを作成します。 |
| [New-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |ジョブ コレクションを作成します。 |
| [New-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Service Bus キュー ジョブを作成します。 |
| [New-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Service Bus トピック ジョブを作成します。 |
| [New-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Storage キュー ジョブを作成します。 |
| [Remove-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Scheduler ジョブを削除します。 |
| [Remove-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |ジョブ コレクションを削除します。 |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Scheduler HTTP ジョブを変更します。 |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |ジョブ コレクションを変更します。 |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Service Bus キュー ジョブを変更します。 |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Service Bus トピック ジョブを変更します。 |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Storage キュー ジョブを変更します。 |
||| 

詳細については、これらのコマンドレットのいずれかを実行してください。 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>関連項目

* [Azure Scheduler とは](scheduler-intro.md)
* [概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [最初のジョブの作成とスケジュール設定 - Azure ポータル](scheduler-get-started-portal.md)
* [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)
