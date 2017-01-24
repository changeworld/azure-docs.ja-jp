---
title: "Operations Management Suite (OMS) での管理ソリューションの作成 | Microsoft Docs"
description: "管理ソリューションは、お客様の OMS ワークスペースに追加できるパッケージの管理シナリオを提供することで、 Operations Management Suite (OMS) の機能を拡張します。  この記事では、管理ソリューションを作成してお使いの環境で使用したり顧客に提供したりする方法について、詳しく説明します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: ab33a7610b8e7bbf64e9f1bfde3753f95956a82f


---
# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Operations Management Suite (OMS) での管理ソリューションの作成 (プレビュー)
> [!NOTE]
> 本記事は、現在プレビュー段階である OMS の管理ソリューションの作成手順に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。  
>
>

管理ソリューションは、お客様の OMS ワークスペースに追加できるパッケージの管理シナリオを提供することで、 Operations Management Suite (OMS) の機能を拡張します。  この記事では、お使いの環境で使用したりコミュニティを通じて顧客に提供したりできる独自の管理ソリューションを作成する方法について、詳しく説明します。

## <a name="planning-your-management-solution"></a>管理ソリューションの設計
OMS の管理ソリューションには、特定の管理シナリオをサポートする複数のリソースが含まれます。  ソリューションを設計する際は、実現したいシナリオに焦点を当て、そのために必要なすべてのリソースに集中する必要があります。  各ソリューションは自己完結している必要があります。つまり、1 つまたは複数のリソースが他のソリューションでも定義されているとしても、必要なリソースをすべて定義する必要があります。  管理ソリューションのインストール時には、既に存在している場合を除き、各リソースが作成されます。ソリューションが削除されたときのリソースに対する動作を定義することができます。  

たとえば、管理ソリューションには、[スケジュール](../automation/automation-schedules.md)を使って Log Analytics リポジトリにデータを収集する [Azure Automation Runbook](../automation/automation-intro.md) と、収集したデータのさまざまな視覚エフェクトを提供する[ビュー](../log-analytics/log-analytics-view-designer.md)を含めることができます。  同じスケジュールを、別のソリューションに使用することもできます。  管理ソリューションの作成者としては、3 つすべてのリソースを定義して、ソリューション削除時には Runbook とビューは自動的に削除されるように指定することもできます。    スケジュールも定義しますが、他のソリューションで使われている可能性を考慮して、ソリューション削除時にでもそのまま残すように指定することもあるでしょう。

## <a name="management-solution-files"></a>管理ソリューション ファイル
管理ソリューションは[リソース管理テンプレート](../azure-resource-manager/resource-manager-template-walkthrough.md)として実装されています。  管理ソリューションの作成を理解するには、[テンプレートを作成する](../azure-resource-manager/resource-group-authoring-templates.md)方法を参照してください。  この記事では、ソリューションに使用するテンプレートと、典型的なソリューション リソース定義の方法について、詳細を説明します。

管理ソリューション ファイルの基本的な構造は、次のような [Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md#template-format)と同じです。  以下の各セクションでは、最上位レベルの要素と、ソリューションにおけるその内容について説明します。  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>パラメーター
[パラメーター](../azure-resource-manager/resource-group-authoring-templates.md#parameters)は、管理ソリューションをインストールするときに、ユーザーから必要とする値です。  すべてのソリューションが持つ標準のパラメーターがありますが、特定のソリューションに必要な追加のパラメーターを加えることができます。  ソリューションのインストール時にユーザーがパラメーター値を設定する方法は、特定のパラメーターおよびソリューションのインストール方法によって異なります。

ユーザーが [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) または [Azure クイック スタート テンプレート](operations-management-suite-solutions.md#finding-and-installing-management-solutions) から管理ソリューションをインストールすると、[OMS ワークスペースと Automation アカウント](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account)を選択するよう求めるメッセージが表示されます。  これらは、各標準パラメーターの値の設定に使用されます。  ユーザーは、標準パラメーターに値を直接入力することは求められませんが、追加のパラメーターには値を入力することが求められます。

ユーザーが[別の方法で](operations-management-suite-solutions.md#finding-and-installing-management-solutions)ソリューションをインストールすると、標準パラメーターと追加のパラメーターのすべてに値を入力する必要があります。

サンプル パラメーターを次に示します。

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

次の表で、パラメーターの属性について説明します。

| Attribute | Description |
|:--- |:--- |
| type |パラメーターのデータ型。 ユーザーに表示される入力コントロールは、データ型によって異なります。<br><br>bool - ドロップダウン ボックス<br>string - テキスト ボックス<br>int - テキスト ボックス<br>securestring - パスワード フィールド<br> |
| カテゴリ |パラメーターの任意のカテゴリ。  同じカテゴリのパラメーターはグループ化されます。 |
| control |文字列パラメーターの追加の機能。<br><br>datetime - Datetime コントロールが表示されます。<br>guid - GUID 値が自動的に生成され、パラメーターは表示されません。 |
| description |パラメーターの説明です (省略可能)。  パラメーターの横の情報バルーンに表示されます。 |

### <a name="standard-parameters"></a>標準パラメーター
次の表に、すべての管理ソリューションの標準パラメーターを一覧表示します。  Azure Marketplace や クイック スタート テンプレートからソリューションをインストールすると、ユーザーに設定が求められることはなく、これらの値が設定されます。  ソリューションを別の方法でインストールした場合、ユーザーは値を入力する必要があります。

> [!NOTE]
> Azure Marketplace と クイック スタート テンプレートのユーザー インターフェイスには、表に記載したパラメーター名が必要です。  別のパラメーター名を使用すると、ユーザーに表のパラメーター名を設定するよう求めるメッセージが表示されます。パラメーター名は自動的に設定されません。
>
>

| パラメーター | 型 | 説明 |
|:--- |:--- |:--- |
| accountName |string |Azure automation アカウント名。 |
| pricingTier |string |Log Analytics ワークスペースと Azure Automation アカウントの両方の価格レベル。 |
| regionId |string |Azure Automation アカウントのリージョン。 |
| solutionName |string |ソリューションの名前。 |
| workspaceName |string |Log Analytics ワークスペース名。 |
| workspaceRegionId |string |Log Analytics ワークスペースのリージョン。 |

### <a name="sample"></a>サンプル
以下に、ソリューションのパラメーター エンティティのサンプルを示します。  これは、すべての標準パラメーターと、同じカテゴリ内の 2 つの追加のパラメーターを含みます。

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


ソリューション内のその他の要素のパラメーター値は、 **parameters('parameter name')**の構文を使用して参照します。  たとえば、ワークスペース名にアクセスするには、**parameters('workspaceName')** を使用します。

## <a name="variables"></a>変数
**Variables** 要素には、その他の管理ソリューションで使用する値が含まれています。  これらの値は、ソリューションをインストールするユーザーには公開されません。  これは、作成者に 1 つの場所を提供することを意図しており、それによって、ソリューション全体にわたって何度も使用する値を管理できるようにします。 **resources** 要素でハードコーディングするのとは異なり、ソリューション固有の値を変数で記述する必要があります。  これによってコードが読みやすくなるとともに、後のバージョンでこれらの値を簡単に変更することができます。

次の例は、ソリューションで使用される一般的なパラメーターを持つ**variables**要素です。

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

構文**variables('variable name')**ソリューションの変数値を参照します。  たとえば、SolutionName 変数にアクセスするには、**variables('solutionName')**を使います。

## <a name="resources"></a>リソース
**resources** 要素は、管理ソリューションに含まれる異なるリソースを定義します。  この部分が、テンプレートの大半を占め、最も複雑な部分です。  リソースは、次の構造で定義します。  

    "resources": [
        {
            "name": "<name-of-the-resource>",            
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",        
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>依存関係
**dependsOn** 要素は、他のリソースの[依存関係](../azure-resource-manager/resource-group-define-dependencies.md)を指定します。  ソリューションをインストールすると、すべての依存関係が作成されるまでリソースは作成されません。  たとえば、[ジョブ リソース](operations-management-suite-solutions-resources-automation.md#automation-jobs)を使用してソリューションをインストールすると、ソリューションが [Runbook を開始する](operations-management-suite-solutions-resources-automation.md#runbooks)ことがあります。  ジョブ リソースは、ジョブが作成される前に Runbook が作成されたことを確認するために Runbook リソースに依存します。

### <a name="oms-workspace-and-automation-account"></a>OMS ワークスペースと Automation アカウント
管理ソリューションでは、ビューを格納するために [OMS ワークスペース](../log-analytics/log-analytics-manage-access.md)が、Runbook と関連リソースを格納するために [Automation アカウント](../automation/automation-security-overview.md#automation-account-overview)が必要です。  これらはソリューションのリソースが作成される前に使用できるようにする必要があり、ソリューションそのもので定義すべきではありません。  ユーザーはソリューションのデプロイ時に[ワークスペースとアカウントを指定](operations-management-suite-solutions.md#oms-workspace-and-automation-account)しますが、作成者としては、次の点を考慮する必要があります。

## <a name="solution-resource"></a>ソリューションのリソース
各ソリューションは、ソリューション自体を定義する**resources**要素の中にリソース エントリが必要です。  これは**Microsoft.OperationsManagement/solutions** の 1 つを有します。  以下に、ソリューション リソースの例を示します。  異なる要素については、以降のセクションで説明します。

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>ソリューション名
ソリューション名は、Azure サブスクリプション内で一意である必要があります。 使用する推奨値は以下のとおりです。  名前が一意であるように、これはベース名に **SolutionName** という変数を使用し、**workspaceName** パラメーターを使用しています。

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

これは次のような名前になります。

    My Solution Name [MyWorkspace]


### <a name="dependencies"></a>依存関係
ソリューション リソースは、ソリューションが作成される前に存在している必要があるため、ソリューションの別のリソースごとに[依存関係](../azure-resource-manager/resource-group-define-dependencies.md)を持つ必要があります。  このために、**dependsOn** 要素にある各リソースにエントリを追加します。

### <a name="properties"></a>プロパティ
このソリューション リソースには、次の表のプロパティがあります。  これには、ソリューションに含まれ参照されるリソースが含まれます。ソリューションをインストールした後に、どのようにリソースを管理するかを定義しています。  ソリューション内の各リソースは、**referencedResources** または **containedResources** プロパティのいずれかに表示される必要があります。

| プロパティ | 説明 |
|:--- |:--- |
| workspaceResourceId |*<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Workspace Name\>* 形式での、OMS ワークスペースの ID。 |
| referencedResources |ソリューション削除時に削除すべきではないソリューション内のリソースの一覧。 |
| containedResources |ソリューション削除時に削除すべきではないソリューション内のリソースの一覧。 |

上記の例は、Runbook、スケジュール、およびビューを含むソリューションが対象です。  スケジュールと Runbook は **properties** 要素で*参照されている*ため、ソリューションが削除されても、削除されません。  ビューが *含まれている* ので、ソリューションが削除されたときでも、削除されません。

### <a name="plan"></a>プラン
ソリューション リソースの**プラン** エンティティには、次の表のプロパティがあります。

| プロパティ | Description |
|:--- |:--- |
| name |ソリューションの名前。 |
| version |作成者によって決定されるソリューションのバージョン。 |
| product |ソリューションを特定する一意の文字列。 |
| publisher |ソリューションの発行者。 |

## <a name="other-resources"></a>その他のリソース
管理ソリューションに共通するリソースについての詳細とサンプルは、次の記事をご覧ください。

* [ビューとダッシュボード](operations-management-suite-solutions-resources-views.md)
* [Automation リソース](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>管理ソリューションのテスト
管理ソリューションをデプロイする前に、[Test-AzureRmResourceGroupDeployment](../azure-resource-manager/resource-group-template-deploy.md#deploy) を使ってテストすることをお勧めします。  これにより、ソリューション ファイルを検証し、デプロイする前に問題を検出することができます。

## <a name="next-steps"></a>次のステップ
* [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)の詳細について
* Resource Manager テンプレートの様々なサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates) で検索できます。
* [管理ソリューションにビューを追加する方法](operations-management-suite-solutions-resources-views.md)の詳細を参照する。
* [管理ソリューションに Automation リソースを追加する方法](operations-management-suite-solutions-resources-automation.md)の詳細を参照する。



<!--HONumber=Jan17_HO1-->


