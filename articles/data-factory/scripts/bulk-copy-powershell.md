---
title: "PowerShell スクリプト: - Azure Data Factory を使ってデータを一括コピーする | Microsoft Docs"
description: "この PowerShell スクリプトでは、Azure Data Factory を使い、ソース データ ストアからコピー先データ ストアにデータを一括コピーする方法について説明します。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: f8a66f3daab9b57723f67a60fa195e53333f3b8d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell スクリプト - Azure Data Factory を使って複数のテーブルを一括コピーする

このサンプルの PowerShell スクリプトは、Azure SQL Database の複数のテーブルから Azure SQL Data Warehouse にデータをコピーします。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

このサンプルを実行するための前提条件については、[チュートリアルの一括コピー](../tutorial-bulk-copy.md#prerequisites)に関する説明を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

> [!IMPORTANT]
> このスクリプトは、Data Factory エンティティ (リンクされたサービス、データセット、およびパイプライン) を定義する JSON ファイルをハード ドライブ上の c:\ フォルダー内に作成します。

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

このサンプル スクリプトを実行した後、次のコマンドを使用して、リソース グループとそれに関連付けられたすべてのリソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
リソース グループからデータ ファクトリを削除するには、次のコマンドを実行します。 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | データ ファクトリを作成します。 |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | データ ファクトリ内にリンクされたサービスを作成します。 リンクされたサービスは、データ ストアまたは計算をデータ ファクトリにリンクします。 |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | データ ファクトリ内にデータセットを作成します。 データセットは、パイプライン内のアクティビティの入出力を表します。 | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | データ ファクトリ内にパイプラインを作成します。 パイプラインには、特定の操作を実行する 1 つ以上のアクティビティが含まれています。 このパイプラインでは、コピー アクティビティが Azure Blob Storage 内のある場所から別の場所にデータをコピーします。 |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | パイプラインの実行を作成します。 つまり、パイプラインを実行します。 |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | パイプライン内のアクティビティの実行 (アクティビティ実行) に関する詳細情報を取得します。 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Data Factory のその他の PowerShell サンプル スクリプトについては、[Azure Data Factory の PowerShell スクリプト](../samples-powershell.md)に関する記事を参照してください。