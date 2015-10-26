<properties 
	pageTitle="SQL Database を使用する Web アプリをプロビジョニングする" 
	description="SQL Database を含む Web アプリをデプロイするには、Azure リソース マネージャー テンプレートを使用します。" 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="tomfitz"/>

# Web アプリと SQL Database をプロビジョニングする

このトピックでは、Web アプリと SQL Database をデプロイする Azure リソース マネージャー テンプレートを作成する方法について説明します。デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../resource-group-authoring-templates.md)」を参照してください。

アプリのデプロイの詳細については、「[Deploy a complex application predictably in Azure (Azure で複雑なアプリケーションを予測どおりにデプロイする)](app-service-deploy-complex-application-predictably.md)」を参照してください。

完全なテンプレートについては、「[Web App With SQL Database template (SQL Database を使用する Web アプリ用のテンプレート)](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json)」を参照してください。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## デプロイ対象

このテンプレートでは、以下をデプロイします。

- Web アプリ
- SQL Database サーバー
- SQL Database
- AutoScale 設定
- アラート ルール
- App Insights

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/app-service-web-arm-with-sql-database-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-sql-database%2Fazuredeploy.json)

## 指定するパラメーター

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### serverName

作成する新しいデータベース サーバーの名前。

    "serverName": {
      "type": "string"
    }

### serverLocation

データベース サーバーの場所。最高のパフォーマンスを得るために、この場所は Web アプリの場所と同じする必要があります。

    "serverLocation": {
      "type": "string"
    }

### administratorLogin

データベース サーバー管理者で使用するアカウントの名前。

    "administratorLogin": {
      "type": "string"
    }

### administratorLoginPassword

データベース サーバー管理者で使用するパスワード。

    "administratorLoginPassword": {
      "type": "securestring"
    }

### databaseName

作成する新しいデータベースの名前。

    "databaseName": {
      "type": "string"
    }

### collation

文字の適切な使用を制御するために使用するデータベース照合。

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

### edition

作成するデータベースの種類。

    "edition": {
      "type": "string",
      "defaultValue": "Standard",
      "metadata": {
        "description": "The type of database to create. The available options are: Web, Business, Basic, Standard, and Premium."
      }
    }

### maxSizeBytes

データベースの最大サイズ (バイト単位)。

    "maxSizeBytes": {
      "type": "string",
      "defaultValue": "1073741824"
    }

### requestedServiceObjectiveName

エディションのパフォーマンス レベルに対応する名前。

    "requestedServiceObjectiveName": {
      "type": "string",
      "defaultValue": "S0",
      "metadata": {
        "description": "The name corresponding to the performance level for edition. The available options are: Shared, Basic, S0, S1, S2, S3, P1, P2, and P3."
      }
    }


## デプロイするリソース

### SQL Server と Database

新しい SQL Server とデータベースを作成します。サーバーの名前を **serverName** パラメーターに、場所を **serverLocation** パラメーターに指定します。新しいサーバーを作成するときは、データベース サーバー管理者のログイン名とパスワードを指定する必要があります。

    {
      "name": "[parameters('serverName')]",
      "type": "Microsoft.Sql/servers",
      "location": "[parameters('serverLocation')]",
      "apiVersion": "2014-04-01-preview",
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
      },
      "resources": [
        {
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2014-08-01",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "[parameters('edition')]",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "[parameters('maxSizeBytes')]",
            "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
          }
        },
        {
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "location": "[parameters('serverLocation')]",
          "name": "AllowAllWindowsAzureIps",
          "properties": {
            "endIpAddress": "0.0.0.0",
            "startIpAddress": "0.0.0.0"
          },
          "type": "firewallrules"
        }
      ]
    },


[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]


### Web アプリ

    {
      "apiVersion": "2015-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": ["[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "empty"
      },
      "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2014-11-01",
          "type": "config",
          "name": "connectionstrings",
          "dependsOn": [ "[concat('Microsoft.Web/Sites/', parameters('siteName'))]" ],
          "properties": {
              "DefaultConnection":{
              "value":"[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
              "type": 2 
            },
          }
        }
      ]
    }

### Autoscale

    {
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('hostingPlanName'), '-', resourceGroup().name)]",
      "type": "microsoft.insights/autoscalesettings",
      "location": "East US",
      "tags": {"[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"},
      "dependsOn": ["[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"],
      "properties": {
        "profiles": [
        {
          "name": "Default",
          "capacity": {
            "minimum": "1",
            "maximum": "2",
            "default": "1"
            },
            "rules": [
            {
              "metricTrigger": 
              {
                "metricName": "CpuPercentage",
                "metricResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 80.0
              },
              "scaleAction": 
              {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT10M"
              }
            },
            {
              "metricTrigger": 
              {
                "metricName": "CpuPercentage",
                "metricResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT1H",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 60.0
              },
              "scaleAction": 
              {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT1H"
              }
            }
            ]
          }
          ],
          "enabled": false,
          "name": "[concat(parameters('hostingPlanName'), '-', resourceGroup().name)]",
          "targetResourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        }
    }

### ステータス コード 403 と 500 用のアラート ルール、高い CPU 使用率、および HTTP キューの長さ 

    {
      "apiVersion": "2014-04-01",
      "name": "[concat('ServerErrors ', parameters('siteName'))]",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": ["[concat('Microsoft.Web/sites/', parameters('siteName'))]"],
      "tags": {"[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"},
      "properties": 
      {
        "name": "[concat('ServerErrors ', parameters('siteName'))]",
        "description": "[concat(parameters('siteName'), ' has some server errors, status code 5xx.')]",
        "isEnabled": false,
        "condition": 
        {
          "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
          "dataSource": 
          {
            "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
            "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]",
            "metricName": "Http5xx"
          },
          "operator": "GreaterThan",
          "threshold": 0.0,
          "windowSize": "PT5M"
        },
        "action": 
        {
          "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": []
        }
      }
    },
    //Alert-Rules --> 403
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('ForbiddenRequests ', parameters('siteName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('ForbiddenRequests ', parameters('siteName'))]",
            "description": "[concat(parameters('siteName'), ' has some requests that are forbidden, status code 403.')]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]",
                    "metricName": "Http403"
                },
                "operator": "GreaterThan",
                "threshold": 0,
                "windowSize": "PT5M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    },
    //Alert-Rules --> High CPU
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('CPUHigh ', parameters('hostingPlanName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('CPUHigh ', parameters('hostingPlanName'))]",
            "description": "[concat('The average CPU is high across all the instances of ', parameters('hostingPlanName'))]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                    "metricName": "CpuPercentage"
                },
                "operator": "GreaterThan",
                "threshold": 90,
                "windowSize": "PT15M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    },
    //Alert-Rules --> HTTP Queue Length
    {
        "apiVersion": "2014-04-01",
        "name": "[concat('LongHttpQueue ', parameters('hostingPlanName'))]",
        "type": "microsoft.insights/alertrules",
        "location": "East US",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]": "Resource"
        },
        "properties": {
            "name": "[concat('LongHttpQueue ', parameters('hostingPlanName'))]",
            "description": "[concat('The HTTP queue for the instances of ', parameters('hostingPlanName'), ' has a large number of pending requests.')]",
            "isEnabled": false,
            "condition": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition",
                "dataSource": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource",
                    "resourceUri": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                    "metricName": "HttpQueueLength"
                },
                "operator": "GreaterThan",
                "threshold": 100.0,
                "windowSize": "PT5M"
            },
            "action": {
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction",
                "sendToServiceOwners": true,
                "customEmails": []
            }
        }
    }

### App Insights

    {
        "apiVersion": "2014-04-01",
        "name": "[parameters('siteName')]",
        "type": "microsoft.insights/components",
        "location": "Central US",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
        ],
        "tags": {
            "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', parameters('siteName'))]": "Resource"
        },
        "properties": {
            "ApplicationId": "[parameters('siteName')]"
        }
    }

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json


 

<!---HONumber=Oct15_HO3-->