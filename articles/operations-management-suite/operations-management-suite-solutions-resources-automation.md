---
title: "OMS ソリューションの Automation リソース | Microsoft Docs"
description: "一般的な OMS のソリューションでは、Azure Automation の Runbook を追加して、監視データの収集や処理などのプロセスを自動化します。  この記事では、ソリューションに Runbook とその関連リソースを追加する方法について説明します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>OMS ソリューションの Automation リソース (プレビュー)
> [!NOTE]
> 本記事は、現在プレビュー段階である OMS の管理ソリューションの作成手順に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。   
> 
> 

一般的な [OMS の管理ソリューション](operations-management-suite-solutions.md)では、Azure Automation の Runbook を追加して、監視データの収集や処理などのプロセスを自動化します。  Automation アカウントには、Runbook だけでなく、ソリューションで用いる Runbook をサポートする変数やスケジュールなどのアセットも用意されています。  この記事では、ソリューションに Runbook とその関連リソースを追加する方法について説明します。

> [!NOTE]
> この記事のサンプルでは、管理ソリューションに必須であるかまたは一般的に用いられるパラメーターと変数を使用します。これらについては、「[Operations Management Suite (OMS) での管理ソリューションの作成](operations-management-suite-solutions-creating.md)」で説明しています。 
> 
> 

## <a name="prerequisites"></a>前提条件
この記事では、[管理ソリューションの作成方法](operations-management-suite-solutions-creating.md)およびソリューション ファイルの構造を理解していることを前提としています。

## <a name="samples"></a>サンプル
Automation リソース用の Resource Manager テンプレート サンプルは、[GitHub のクイックスタート テンプレート](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates) ページにあります。

## <a name="automation-account"></a>Automation アカウント
Azure Automation のリソースはすべて、[Automation アカウント](../automation/automation-security-overview.md#automation-account-overview) に含まれています。  [OMS ワークスペースと Automation アカウント](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account) に関するページの説明にあるように、Automation アカウントは管理ソリューションに含まれていませんが、ソリューションのインストール前に追加する必要があります。  このアカウントが含まれていない場合、ソリューションのインストールは失敗します。

各 Automation リソースの名前には、Automation アカウントの名前を含めます。  これは、次の Runbook リソースの例で示すように **accountName** パラメーターをソリューションで指定することにより行います。

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
[Azure Automation Runbook](../automation/automation-runbook-types.md) リソースのタイプは **Microsoft.Automation/automationAccounts/runbooks** であり、次の表のプロパティがあります。

| プロパティ | Description |
|:--- |:--- |
| runbookType |Runbook のタイプを指定します。 <br><br> Script - PowerShell スクリプト <br>PowerShell - PowerShell ワークフロー <br> GraphPowerShell - グラフィカル PowerShell スクリプト Runbook <br> GraphPowerShellWorkflow - グラフィカル PowerShell ワークフロー Runbook |
| logProgress |Runbook の[進捗状況レコード](../automation/automation-runbook-output-and-messages.md)を生成するかどうかを指定します。 |
| logVerbose |Runbook の[詳細レコード](../automation/automation-runbook-output-and-messages.md)を生成するかどうかを指定します。 |
| description |Runbook の説明です (省略可能)。 |
| publishContentLink |Runbook のコンテンツを指定します。 <br><br>uri - Runbook のコンテンツへの URI です。  PowerShell Runbook およびスクリプト Runbook の場合は .ps1 ファイル、Graph Runbook の場合はエクスポート済みのグラフィカル Runbook ファイルになります。  <br> version - 追跡対象の Runbook のバージョンです。 |

Runbook リソースの例を次に示します。  この例では、[PowerShell ギャラリー](https://www.powershellgallery.com)から Runbook を取得しています。

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Runbook の起動
管理ソリューションで runbook を起動する方法は 2 つあります。

* ソリューションのインストール時に Runbook を起動するには、下記の説明に従って[ジョブ リソース](#automation-jobs)を作成します。
* スケジュールに応じて Runbook を起動するには、下記の説明に従って[スケジュール リソース](#schedules)を作成します。 

## <a name="automation-jobs"></a>Automation ジョブ
管理ソリューションのインストール時に Runbook を起動するには、**ジョブ** リソースを作成します。  ジョブ リソースのタイプは **Microsoft.Automation/automationAccounts/jobs** であり、次の表のプロパティがあります。

| プロパティ | Description |
|:--- |:--- |
| runbook |Runbook の名前を指定する単一の **name** エンティティです。 |
| parameters |Runbook に必要な各パラメーターのエンティティです。 |

ジョブでは、Runbook 名と、Runbook に送信するパラメーター値を指定します。  Runbook はジョブよりも前に作成しなければならないため、ジョブは起動対象の Runbook に[依存](operations-management-suite-solutions-creating.md#resources)させる必要があります。  また、Runbook の現在のジョブよりも前に完了する必要がある他のジョブに対する依存関係も作成します。

ジョブ リソースの名前には GUID を含める必要があります。これは、通常はパラメーターで割り当てます。  GUID パラメーターの詳細は、「[Creating solutions in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md#parameters)」(Operations Management Suite (OMS) でのソリューションの作成) に記載されています。  

以下に、管理ソリューションのインストール時に Runbook を起動するジョブ リソースの例を示します。  この Runbook の起動前にもう一つの Runbook を完了する必要があるため、そちらの Runbook のジョブに対する依存関係を設定しています。  ジョブの詳細は、直接ではなく、パラメーターと変数を通じて指定しています。

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>証明書
[Azure Automation 証明書](../automation/automation-certificates.md)リソースのタイプは **Microsoft.Automation/automationAccounts/certificates** であり、次の表のプロパティがあります。

| プロパティ | Description |
|:--- |:--- |
| base64Value |証明書の Base 64 値です。 |
| thumbprint |証明書の拇印です。 |

証明書リソースの例を次に示します。

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>資格情報
[Azure Automation 資格情報](../automation/automation-credentials.md)リソースのタイプは **Microsoft.Automation/automationAccounts/credentials** であり、次の表のプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| userName |資格情報のユーザー名です。 |
| パスワード |資格情報のパスワードです。 |

資格情報リソースの例を次に示します。

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>スケジュール
[Azure Automation スケジュール](../automation/automation-schedules.md) リソースのタイプは **Microsoft.Automation/automationAccounts/schedules** であり、次の表のプロパティがあります。

| プロパティ | Description |
|:--- |:--- |
| description |スケジュールの説明です (省略可能)。 |
| startTime |スケジュールの開始時刻を DateTime オブジェクトとして指定します。 有効な DateTime に変更可能な文字列を指定することもできます。 |
| isEnabled |スケジュールを有効にするかどうかを指定します。 |
| interval |スケジュールの間隔の種類です。<br><br>day<br>hour |
| frequency |スケジュールの起動頻度を日数または時間数で指定します。 |

スケジュール リソースの例を次に示します。

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>変数
[Azure Automation 変数](../automation/automation-variables.md)リソースのタイプは **Microsoft.Automation/automationAccounts/variables** であり、次の表のプロパティがあります。

| プロパティ | Description |
|:--- |:--- |
| description |変数の説明です (省略可能)。 |
| isEncrypted |変数を暗号化するかどうかを指定します。 |
| type |変数のデータ型です。 |
| 値 |変数の値です。 |

変数リソースの例を次に示します。

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>モジュール
Runbook で使用する[グローバル モジュール](../automation/automation-integration-modules.md)は常に利用可能であるため、管理ソリューションで定義する必要はありません。  含める必要があるのは、Runbook で使用するその他すべてのモジュールのリソースであり、こうしたモジュール リソースが Runbook よりも前に作成されるように、Runbook にモジュール リソースに対する依存関係を設定する必要があります。

[統合モジュール](../automation/automation-integration-modules.md)のタイプは **Microsoft.Automation/automationAccounts/modules** であり、次の表のプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| contentLink |モジュールのコンテンツを指定します。 <br><br>uri - Runbook のコンテンツへの URI です。  PowerShell Runbook およびスクリプト Runbook の場合は .ps1 ファイル、Graph Runbook の場合はエクスポート済みのグラフィカル Runbook ファイルになります。  <br> version - 追跡対象の Runbook のバージョンです。 |

モジュール リソースの例を次に示します。

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>モジュールの更新
スケジュールを使用する Runbook が含まれる管理ソリューションを更新する場合、新バージョンのソリューションにこの Runbook で使用する新しいモジュールを含めても、Runbook では旧バージョンのモジュールが使用される場合があります。  ソリューションに次の各 Runbook を含め、その他の Runbook よりも先にこれらの Runbook を実行するジョブを作成する必要があります。  こうすることで、Runbook のロード前に、必要に応じてモジュールが更新されます。

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript): ソリューション内の Runbook で使用されるすべてのモジュールを最新バージョンに保ちます。  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript): スケジュール リソースにリンクされている Runbook で最新のモジュールが使用されるように、すべてのスケジュール リソースを再登録します。

以下に、モジュールの更新をサポートするソリューションの必須要素のサンプルを示します。

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>次のステップ
* [ビューをソリューションに追加](operations-management-suite-solutions-resources-views.md)して、収集したデータを視覚化します。




<!--HONumber=Nov16_HO3-->


