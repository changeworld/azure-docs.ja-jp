---
title: "Azure PowerShell のサンプル スクリプト - Web アプリのスケジュールされたバックアップの作成 | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - Web アプリのスケジュールされたバックアップの作成"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: cca2434a7af381eaa8ee7bc803a102a7781c7639
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-scheduled-backup-for-a-web-app"></a>Web アプリのスケジュールされたバックアップの作成

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、これに対する 1 回限りのバックアップを作成します。 

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Login-AzureRmAccount` を実行して、Azure との接続を作成します。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | ストレージ アカウントを作成します。 |
| [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) | Azure ストレージ コンテナーを作成します。 |
| [New-AzureStorageContainerSASToken](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service プランを作成します。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Web アプリを作成します。 |
| [Edit-AzureRmWebAppBackupConfiguration](/powershell/module/azurerm.websites/edit-azurermwebappbackupconfiguration) | Web アプリのバックアップ構成を編集します。 |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Web アプリのバックアップの一覧を取得します。 |
| [Get-AzureRmWebAppBackupConfiguration](/powershell/module/azurerm.websites/get-azurermwebappbackupconfiguration) | Web アプリのバックアップ構成を取得します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../app-service-powershell-samples.md)のページにあります。
