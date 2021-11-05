---
title: Azure Resource Manager テンプレートを使用して統合ランタイムを作成する
description: Azure 内で SSIS パッケージをデプロイして実行できるように、Azure Resource Manager テンプレートを使用して、Azure-SSIS 統合ランタイムを Azure Data Factory 内に作成する方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 322a2277169b53a9c49d496524789636a709af2a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852638"
---
# <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Azure Resource Manager テンプレートを使用して統合ランタイムを作成する

このセクションでは、Azure Resource Manager テンプレートを使用して、Azure-SSIS 統合ランタイムを作成します。 

## <a name="sample-azure-resource-manager-template"></a>サンプルの Azure Resource Manager テンプレート

Azure Resource Manager テンプレートを使用して、Azure-SSIS 統合ランタイムを作成する手順を次に示します。

1. 次の Azure Resource Manager テンプレートを使用して、JSON ファイルを作成します。 山かっこ (プレースホルダー) 内のすべての値をご自分の値に置き換えます。

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Azure Resource Manager テンプレートをデプロイするには、次の例に示すように、`New-AzResourceGroupDeployment` コマンドを実行します。 例では、`ADFTutorialResourceGroup` がリソース グループの名前です。 `ADFTutorialARM.json` はデータ ファクトリおよび Azure-SSIS IR の JSON 定義を含むファイルです。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    このコマンドは、データ ファクトリを作成し、その中に Azure-SSIS IR を作成しますが、IR の起動は行いません。

3. Azure-SSIS IR を起動するには、`Start-AzDataFactoryV2IntegrationRuntime` コマンドを実行します。

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> この処理は、カスタムのセットアップ時間があればそれを除くと、5 分以内で完了するはずです。 ただし、Azure-SSIS IR を仮想ネットワークに参加させるには 20 分から 30 分かかることがあります。
>
> SSISDB を使用する場合、Data Factory サービスは、SSISDB を準備するためにデータベース サーバーに接続します。 また、仮想ネットワーク (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS IR を仮想ネットワークに参加させます。
> 
> Azure-SSIS IR をプロビジョニングすると、Access の再頒布可能パッケージと Azure Feature Pack for SSIS もインストールされます。 これらのコンポーネントは、組み込みのコンポーネントで既にサポートされているデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 組み込みコンポーネントとプレインストール コンポーネントの詳細については、[Azure-SSIS IR の組み込みコンポーネントとプレインストール コンポーネント](./built-in-preinstalled-components-ssis-integration-runtime.md)に関するページを参照してください。 その他インストールできるコンポーネントの詳細については、[Azure-SSIS IR のカスタム設定](./how-to-configure-azure-ssis-ir-custom-setup.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して Azure-SSIS IR をプロビジョニングする方法を学習する](create-azure-ssis-integration-runtime-portal.md)。
- [Azure PowerShell を使用して Azure-SSIS IR をプロビジョニングする方法を学習する](create-azure-ssis-integration-runtime-powershell.md)。
- [Azure Data Factory 内にご自身の SSIS パッケージをデプロイして実行する](create-azure-ssis-integration-runtime-deploy-packages.md)。

このドキュメントでは、Azure SSIS IR に関する、次のような他のトピックも参照してください。

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR など、統合ランタイムに関する一般的な情報が説明されています。
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
