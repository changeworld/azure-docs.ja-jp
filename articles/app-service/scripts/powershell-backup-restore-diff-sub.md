---
title: Azure PowerShell サンプル スクリプト - アプリのバックアップを別のサブスクリプションに復元する | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Web アプリを他のサブスクリプション内のバックアップから復元する
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 9e27585d44214a2826ca2c4ac705eb6bb803344a
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53586641"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>PowerShell を使用して Web アプリを別のサブスクリプションのバックアップから復元する

このサンプル スクリプトでは、既存の Web アプリから以前に完了したバックアップを取得し、別のサブスクリプション内の Web アプリに復元します。 

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzureRmAccount` を実行して、Azure との接続を作成します。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

Web アプリが必要なくなったら、次のコマンドを使用して、リソース グループ、Web アプリ、およびすべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) | 認証済みアカウントを追加して、Azure Resource Manager コマンドレットの要求に使用します。  |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Web アプリのバックアップの一覧を取得します。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Web アプリを作成します。 |
| [Restore-AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | 以前に完了したバックアップから Web アプリを復元します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。
