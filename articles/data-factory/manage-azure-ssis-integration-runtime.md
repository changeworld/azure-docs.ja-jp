---
title: "Azure-SSIS 統合ランタイムの再構成 | Microsoft Docs"
description: "プロビジョニングが済んだ Azure-SSIS 統合ランタイムを Azure Data Factory で再構成する方法を説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: spelluru
ms.openlocfilehash: c1743a0d06f911122ed0aba586aec837f81c578c
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2018
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを管理する
[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関する記事には、Azure Data Factory を使用して Azure-SSIS 統合ランタイム (IR) を作成する方法が示されています。 この記事では、既存の Azure-SSIS 統合ランタイムの再構成に関する情報を提供します。  

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

Azure-SSIS 統合ランタイムのインスタンスをプロビジョニングして開始した後、`Stop` - `Set` - `Start` PowerShell コマンドレットを順番に実行して、Azure-SSIS 統合ランタイムを再構成できます。 たとえば、次の PowerShell スクリプトは、Azure-SSIS 統合ランタイムのインスタンスに割り当てられているノードの数を 5 に変更します。

## <a name="reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR の再構成

1. 最初に、[Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) コマンドレットを使用して Azure-SSIS 統合ランタイムを停止します。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. 次に、[Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) コマンドレットを使用して Azure-SSIS IR を再構成します。 次のサンプル コマンドは、Azure-SSIS 統合ランタイムを 5 つのノードにスケールアウトします。

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. 続いて、[Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) コマンドレットを使用して Azure-SSIS 統合ランタイムを開始します。 このコマンドにより、すべてのノードが SSIS パッケージの実行用に割り当てられます。   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="delete-an-azure-ssis-ir"></a>Azure-SSIS IR の削除
1. 最初に、データ ファクトリにあるすべての既存 Azure SSIS IR をすべて一覧表示します。

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. 次に、データ ファクトリにあるすべての既存 Azure SSIS IR を停止します。

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. 次に、データ ファクトリにある既存の Azure SSIS IR を 1 つずつすべて削除します。

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. 最後に、データ ファクトリを削除します。

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. 新しいリソース グループを作成していた場合は、そのリソース グループを削除します。

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>次の手順
Azure-SSIS 統合ランタイムについて詳しくは、以下のトピックをご覧ください。 

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md):  この記事では、チュートリアルを基に、Azure SQL マネージ インスタンス (プライベート プレビュー) の使い方と、IR を VNet に参加させる方法が説明されています。 
- [Azure-SSIS IR を VNet に参加させる](join-azure-ssis-integration-runtime-virtual-network.md):  この記事では、Azure 仮想ネットワーク (VNet) への Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が VNet に参加できるように Azure Portal を使って VNet を構成する手順も説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
 
