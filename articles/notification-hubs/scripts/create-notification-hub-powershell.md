---
title: 'PowerShell スクリプト: Azure 通知ハブを作成する | Microsoft Docs'
description: この PowerShell スクリプトは、Azure 通知ハブを作成します。
services: data-factory
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 747d743a0573bd959b4d3c7100be8ae9451c5ed5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>PowerShell を使用して Azure 通知ハブを作成する

この PowerShell のサンプル スクリプトは、Azure 通知ハブのサンプルを作成します。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

このサンプル スクリプトを実行した後、次のコマンドを使用して、リソース グループとそれに関連付けられたすべてのリソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmNotificationHubsNamespace](/powershell/module//azurerm.notificationhubs/new-azurermnotificationhubsnamespace) | 通知ハブの名前空間を作成します。 |
| [New-AzureRmNotificationHub](/powershell/module//azurerm.notificationhubs/new-azurermnotificationhubsnamespace) | 通知ハブを作成します。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。
