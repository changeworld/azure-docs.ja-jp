<properties
    pageTitle="Azure Resource Manager テンプレートを使用して Service Bus リソースを作成する | Microsoft Azure"
    description="Azure Resource Manager テンプレートを使用して Service Bus リソースの作成を自動化する"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm"/>

# Azure Resource Manager テンプレートを使用して Service Bus リソースを作成する

この記事では、Azure Resource Manager テンプレート、PowerShell、Service Bus リソース プロバイダーを使用して Service Bus と Event Hubs リソースを作成し、デプロイする方法について説明します。

Azure Resource Manager テンプレートを使用すると、ソリューションでデプロイするリソースを定義し、さまざまな環境用の値を入力できるパラメーターと変数を指定できます。テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。Azure Resource Manager テンプレートの作成の詳細と、テンプレート形式の説明については、「[Azure Resource Manager のテンプレートの作成](../resource-group-authoring-templates.md)」を参照してください。

>[AZURE.NOTE] この記事の例では、Azure Resource Manager を使用して Service Bus の名前空間とメッセージング エンティティ (キュー) を作成する方法について説明します。他のテンプレート例については、「[Azure クイックスタート テンプレート][]」ギャラリーで "Service Bus" を検索してください。

## Service Bus と Event Hubs Resource Manager テンプレート

これらの Service Bus と Event Hubs Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。次のリンクをクリックすると、それぞれの詳細情報と、GitHub のテンプレートを参照できます。

- [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
- [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
- [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
- [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
- [イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を作成する](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## PowerShell でデプロイする

次の手順は、PowerShell を使用して、Azure Resource Manager テンプレートをデプロイします。**標準**レベルの Service Bus 名前空間と、名前空間内にキューが作成されます。この例は、[キューを使用した Service Bus 名前空間の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue)テンプレートに基づいています。ワークフローの概要は次のとおりです。

1. PowerShell をインストールします。
2. テンプレートと (必要に応じて) パラメーター ファイルを作成します。
2. PowerShell で Azure アカウントにログインします。
3. リソース グループが存在しない場合は、新しいリソース グループを作成します。
4. デプロイをテストします。
5. 必要に応じて、デプロイ モードを設定します。
6. テンプレートをデプロイします。

Azure Resource Manager テンプレートのデプロイの詳細については、「[Azure リソース マネージャーのテンプレートを使用したリソースのデプロイ][]」を参照してください。

### PowerShell をインストールする

Azure PowerShell をインストールするには、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」の手順に従います。

### テンプレートの作成

GitHub から [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) テンプレートを複製またはコピーします。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### パラメーター ファイルを作成する (省略可能)

省略可能なパラメーター ファイルを使用するには、[201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) ファイルをコピーします。`serviceBusNamespaceName` の値を、このデプロイで作成する Service Bus 名前空間の名前で置き換えます。また、`serviceBusQueueName` の値を、作成するキューの名前で置き換えます。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

詳細については、「[パラメーター ファイル](../resource-group-template-deploy.md#parameter-file)」を参照してください。

### Azure にログインして Azure サブスクリプションを設定する

PowerShell プロンプトから、次のコマンドを実行します。

```
Login-AzureRmAccount
```

Azure アカウントにログオンするように求められます。ログオンしたら、次のコマンドを実行して、使用できるサブスクリプションを確認します。

```
Get-AzureRMSubscription
```

このコマンドを実行すると、使用できる Azure サブスクリプションの一覧が返されます。現在のセッションのサブスクリプションを選択するには、次のコマンドを実行します。`<YourSubscriptionId>` は、使用する Azure サブスクリプションの GUID に置き換えてください。

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### リソース グループを設定する

既存のリソース グループがない場合は、**New-AzureRmResourceGroup** コマンドで新しいリソース グループを作成します。使用するリソース グループの名前と場所を指定します。次に例を示します。

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

成功した場合、新しいリソース グループの概要が表示されます。

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### 展開をテスト

デプロイを検証するには、`Test-AzureRmResourceGroupDeployment` コマンドレットを実行します。デプロイをテストする場合、デプロイの実行時と同様に、必要なパラメーターを正確に指定します。

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### デプロイを作成する

新しいデプロイを作成するには、`New-AzureRmResourceGroupDeployment` コマンドを実行し、プロンプトに従って必要なパラメーターを指定します。パラメーターには、デプロイの名前、リソース グループの名前、テンプレート ファイルのパスまたは URL が含まれます。**Mode** パラメーターを指定しない場合、**Incremental** の既定値が使用されます。詳細については、「[増分デプロイと完全デプロイ](../resource-group-template-deploy.md#incremental-and-complete-deployments)」を参照してください。

次のコマンドを実行すると、PowerShell ウィンドウに 3 つのパラメーターを指定するように求められます。

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

代わりにパラメーター ファイルを使用するには、次のコマンドを使用します。

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

また、デプロイ コマンドレットを実行するときに、インライン パラメーターを使用することもできます。コマンドは次のとおりです。

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

[完全](../resource-group-template-deploy.md#incremental-and-complete-deployments)デプロイを実行するには、**Mode** パラメーターを **Complete** に設定します。

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### デプロイを検証する

リソースが正常にデプロイされると、デプロイの概要が PowerShell ウィンドウに表示されます。

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## 次のステップ

Azure Resource Manager テンプレートをデプロイする基本のワークフローとコマンドが表示されました。詳細については、次のリンクを参照してください。

- [Azure リソース マネージャーの概要][]
- [Azure リソース マネージャーのテンプレートを使用したリソースのデプロイ][]
- [テンプレートの作成](../resource-group-authoring-templates.md)


[Azure リソース マネージャーの概要]: ../resource-group-overview.md
[Azure リソース マネージャーのテンプレートを使用したリソースのデプロイ]: ../resource-group-template-deploy.md
[Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/?term=service+bus

<!---HONumber=AcomDC_0928_2016-->