<properties 
	pageTitle="PowerShell を使用した Application Insights リソースの作成" 
	description="ビルドの一部として、プログラムで Application Insights リソースを作成します。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/13/2015" 
	ms.author="awills"/>
 
# PowerShell を使用した Application Insights リソースの作成

この記事では、Azure で [Application Insights](app-insights-overview.md) リソースを自動的に作成する方法を説明します。たとえば、ビルド プロセスの一部として実行します。Application Insights の基本的なリソースとともに、[可用性 Web テスト](app-insights-monitor-web-app-availability.md)、[アラートの設定](app-insights-alerts.md)、およびその他の Azure リソースを作成できます。

これらのリソースを作成するために重要となるのが、[Azure リソース マネージャー](powershell-azure-resource-manager.md) のJSON テンプレートです。簡単に言うと、既存のリソースの JSON 定義をダウンロードし、名前などの特定の値をパラメーター化して、新しいリソースを作成するときに、テンプレートを実行するという手順になります。いくつかのリソースをまとめてパッケージ化することで、すべてを一度に作成できます (例、可用性テスト、アラート、および連続エクスポート用の記憶域を使用したアプリの監視)。パラメーター化の一部には、いくつか細かい点があります。それについては、以降で説明します。

## 1 回限りのセットアップ

以前に Azure サブスクリプションで PowerShell を使用したことがない場合

スクリプトを実行するコンピューターに Azure PowerShell モジュールをインストールします。

1. [Microsoft Web Platform Installer (v5 以上)](http://www.microsoft.com/web/downloads/platform.aspx) をインストールします。
2. このインストーラーを使用して Microsoft Azure PowerShell をインストールします。

## 既存のリソース向けの JSON のコピー

1. 自動的に生成するプロジェクトに似たプロジェクトの [Application Insights](app-insights-overview.md) を設定します。必要に応じて、Web テストとアラートを追加します。
2. 新しい .json ファイルを作成します (この例では、`template1.json` と呼びます)。ファイルに、次のコンテンツをコピーします。


    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
			"testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }
 
            // Any other resources go here
          ]
        }
    
    ```

    このテンプレートは、メインのリソースに加えて、可用性テストを 1 つ設定します。


2. [Azure リソース マネージャー](https://resources.azure.com/)を開きます。サブスクリプション、resourceGroups、コンポーネントを通り抜けて、アプリ リソースまで下方向に移動します。

    ![](./media/app-insights-create-powershell/01.png)

    *コンポーネント* はアプリケーションを表示するための基本的な Application Insights リソースです。関連するアラート ルールおよび可用性 Web テストに対して別々 のリソースがあります。

3. コンポーネントの JSON を `template1.json` の適切な場所にコピーします。
6. 次のプロパティを削除します。
  * `id`
  * `InstrumentationKey`
  * `CreationDate`
4. webtests と alertrules セクションを開き、個々の項目の JSON をテンプレートにコピーします。(webtests または alertrules ノードからコピーせず、その下の項目に移動してください。)

    各 Web テストには、関連するアラート ルールがあるため、両方をコピーする必要があります。

    Web テストは、アラート ルールの前に配置する必要があります。

5. スキーマを満たすためには、各リソースに次の行を挿入します。

    `"apiVersion": "2014-04-01",`

    (スキーマにより、リソースの種類名 `Microsoft.Insights/*` の大文字化に対し、警告されますが、変更*しない*でください。)


## テンプレートのパラメーター化

ここでは、特定の名前をパラメーターで置き換える必要があります。[テンプレートをパラメーター化](resource-group-authoring-templates.md)するには、[ヘルパー関数のセット](resource-group-template-functions.md)を使用して、式を記述します。

文字列の一部のみをパラメーター化することはできません。そのため、`concat()` を使用して、文字列を構築します。

次に、作成する代替文字列の例を示します。それぞれの代替文字列は、複数回現れます。作成するテンプレートには、その他のものが必要になる場合があります。これらの例では、テンプレートの上部で定義したパラメーターと変数を使用しています。

find | 置き換え
---|---
`"hidden-link:/subscriptions/.../components/MyAppName"`| `"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"`
`"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` | `"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",`
`"/subscriptions/.../webtests/myTestName-myAppName",` | `"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",`
`"myWebTest-myAppName"` | `"[variables(testName)]"'`
`"myTestName-myAppName-subsId"` | `"[variables('alertRuleName')]"`
`"myAppName"` | `"[parameters('appName')]"`
`"myappname"` (小文字) | `"[toLower(parameters('appName'))]"`
`"<WebTest Name="myWebTest" ...`<br/>` Url="http://fabrikam.com/home" ...>"`|`[concat('<WebTest Name="',` <br/> `parameters('webTestName'),` <br/> `'" ... Url="', parameters('Url'),` <br/> `'"...>')]" `

## リソース間の依存関係の設定

Azure では、厳密な順序でリソースを設定する必要があります。次の設定を開始する前に、確実に 1 つの設定を完了するために、依存関係の行を追加します。

* Web テスト リソース

    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`

* アラート リソース

    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## Application Insights リソースの作成

1. PowerShell で Azure にサインインします。

    `Login-AzureRmAccount`

2. 次のようにコマンドを実行します。

    ```PS

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"

    ``` 

    * -ResourceGroupName は、新しいリソースを作成するグループです。
    * -templateFile は、カスタム パラメーターの前に置く必要があります。
    * -appName は、作成するリソースの名前です。
    * -webTestName は、作成する Web テストの名前です。
    * -Url は、Web アプリの url です。
    * -text は、Web ページに表示される文字列です。


## メトリック アラートの定義

[PowerShell を使用したアラートの設定方法](app-insights-alerts.md/#set-alerts-by-using-powershell) があります。


## 例

次に作成済みの Web テストおよびWeb テスト アラート テンプレートのコンポーネント一式を示します。

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name="', 
                parameters('webTestName'), 
              '"  Id="32cfc791-aaad-4b50-9c8d-993c21beb218"   Enabled="True"         CssProjectStructure=""    CssIteration=""  Timeout="120"  WorkItemIds=""         xmlns="http://microsoft.com/schemas/VisualStudio/TeamTest/2010"         Description=""  CredentialUserName=""  CredentialPassword=""         PreAuthenticate="True"  Proxy="default"  StopOnError="False"         RecordedResultFile=""  ResultsLocale="">  <Items>  <Request Method="GET"         Guid="a6f2c90b-61bf-b28hh06gg969"  Version="1.1"  Url="', 
              parameters('Url'), 
              '" ThinkTime="0"  Timeout="300" ParseDependentRequests="True"         FollowRedirects="True" RecordResult="True" Cache="False"         ResponseTimeGoal="0"  Encoding="utf-8"  ExpectedHttpStatusCode="200"         ExpectedResponseUrl="" ReportingName="" IgnoreHttpStatusCode="False" />        </Items>  <ValidationRules> <ValidationRule  Classname="Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" DisplayName="Find Text"         Description="Verifies the existence of the specified text in the response."         Level="High"  ExectuionOrder="BeforeDependents">  <RuleParameters>        <RuleParameter Name="FindText" Value="', 
              parameters('text'), 
              '" />  <RuleParameter Name="IgnoreCase" Value="False" />  <RuleParameter Name="UseRegularExpression" Value="False" />  <RuleParameter Name="PassIfTextFound" Value="True" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

<!---HONumber=Nov15_HO4-->