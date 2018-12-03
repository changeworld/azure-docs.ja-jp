---
title: Azure PowerShell のサンプル スクリプト - Web アプリをバックアップから復元する | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Web アプリをバックアップから復元する
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6e8d950db6facacd9ae84566c6e85a14657050fe
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291573"
---
# <a name="restore-a-web-app-from-a-backup"></a>Web アプリをバックアップから復元する

このサンプル スクリプトでは、既存の Web アプリから以前に完了したバックアップを取得し、コンテンツを上書きすることによってアプリを復元します。 

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzureRmAccount` を実行して、Azure との接続を作成します。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

Web アプリが必要なくなったら、次のコマンドを使用して、リソース グループ、Web アプリ、およびすべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Web アプリのバックアップの一覧を取得します。 |
| [Restore-AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | 以前に完了したバックアップから Web アプリを復元します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../app-service-powershell-samples.md)のページにあります。
