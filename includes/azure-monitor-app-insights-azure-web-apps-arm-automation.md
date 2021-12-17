---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b88ee4d53f137f98aa82ee736043943f844f36ca
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154597"
---
### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager を使用した App Service のアプリケーション設定

App Services のアプリケーション設定は、<bpt id="p1">[</bpt>Azure Resource Manager テンプレート<ept id="p1">](../articles/azure-resource-manager/templates/syntax.md)</ept>を使用して管理および構成できます。 この手法は、Azure Resource Manager オートメーションで新しい App Service リソースをデプロイするとき、または既存のリソースの設定を変更するときに使用できます。

アプリ サービスに使用されるアプリケーション設定 JSON の基本構造を次に示します。

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

アプリケーション設定が Application Insights 用に構成されている Azure Resource Manager テンプレートの例として、この<bpt id="p1">[</bpt>テンプレート<ept id="p1">](https://github.com/Andrew-MSFT/BasicImageGallery)</ept>は役立ちます。<bpt id="p2">[</bpt>238 行目<ept id="p2">](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)</ept>以降のセクションは特に有益です。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Application Insights のリソース、および新しく作成された App Service へのリンクの作成を自動化します。

Application Insights の設定をすべて既定の設定にして Azure Resource Manager テンプレートを作成するには、Application Insights を有効にして新しい Web アプリを作成する場合のようにプロセスを開始します。 

1. 目的の Web アプリ情報を含む新しい App Service リソースを作成します。 **[監視]** タブで Application Insights が有効になります。

2. **[確認と作成]** を選択し、下部にある **[Automation のテンプレートをダウンロードする]** を選択します。

    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/create-web-app.png" alt-text="App Service Web アプリの作成メニューのスクリーンショット。" :::

    このオプションにより、必要な設定がすべて設定済みの、最新の Azure Resource Manager テンプレートが生成されます。
    
    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/arm-template.png" alt-text="App Service Web アプリのテンプレートのスクリーンショット。" border="false":::
    

以下はサンプルです。すべての <ph id="ph1">`AppMonitoredSite`</ph> をサイト名に置き換えてください。

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enable-through-powershell"></a>PowerShell を使用して有効にする

PowerShell を使用してアプリケーションの監視を有効にするために必要な操作は、基になるアプリケーション設定の変更のみです。 以下は、リソース グループ "AppMonitoredRG" 内の "AppMonitoredSite" という Web サイトのアプリケーションの監視を有効にし、データを "012345678-abcd-ef01-2345-6789abcd" インストルメンテーション キーに送信するように構成するサンプルです。

[!INCLUDE [updated-for-az](updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```