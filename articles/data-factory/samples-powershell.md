---
title: "Azure Data Factory の Azure PowerShell サンプル | Microsoft Docs"
description: "Azure PowerShell サンプル - データ ファクトリの作成と管理で役立つスクリプト。"
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: spelluru
ms.openlocfilehash: f223cd8efbff77b02598293afd3b30a662d3abf3
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Azure Data Factory の Azure PowerShell サンプル

次の表には、Azure Data Factory の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のサンプル](v1/data-factory-samples.md)に関する記事をご覧ください。

| |  |
|---|---|
|**データをコピーする**||
|[Azure Blob Storage で BLOB をフォルダーから別のフォルダーにコピーする](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Azure Blob Storage 内のフォルダーから同じ Blob Storage 内の別のフォルダーにデータをコピーします。 |
|[Copy data from on-premises SQL Server to Azure Blob Storage (オンプレミスの SQL Server から Azure Blob Storage にデータをコピーする)](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、オンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーします。 |
|[一括コピー](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| このサンプルの PowerShell スクリプトは、Azure SQL Database の複数のテーブルから Azure SQL Data Warehouse にデータをコピーします。 |
|[増分コピー](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| このサンプルの PowerShell スクリプトは、最初にソース データ ストアからシンク データ ストアにデータをすべてコピーした後で、新しいレコードまたは更新されたレコードだけをソースからシンクに読み込みます。 |
|**データを変換する**||
|[Spark クラスターを使用してデータを変換する](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Spark クラスターでプログラムを実行することでデータを変換します。 |
|**SSIS パッケージを Azure にリフトしてシフトする**||
|[Azure-SSIS 統合ランタイムを作成する](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Azure で SQL Server Integration Services (SSIS) パッケージを実行する Azure-SSIS 統合ランタイムをプロビジョニングします。 |



