<properties
	pageTitle="Azure Insights: Azure Insights CLI クイック スタート サンプル | Microsoft Azure"
	description="Azure Insights の監視機能にアクセスするのに役立つ CLI コマンドのサンプルを紹介しています。Azure Insights は、Microsoft Azure サービスです。Azure Insights では、AutoScale Cloud Services、Virtual Machines、Web Apps を使用できます。また、アラート通知の送信や、構成済みのテレメトリ データの値に基づく Web URL の呼び出しも可能です。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="ashwink"/>

# Azure Insights クロスプラットフォーム CLI のクイック スタート サンプル

この記事では、Azure Insights の監視機能にアクセスできるコマンド ライン インターフェイス (CLI) のサンプル コマンドを紹介します。Azure Insights では、AutoScale Cloud Services、Virtual Machines、Web Apps を使用できます。また、アラート通知の送信や、構成済みのテレメトリ データの値に基づく Web URL の呼び出しも可能です。


## 前提条件

Azure CLI をまだインストールしていない場合は、「[Azure CLI のインストール](../xplat-cli-install.md)」を参照してください。Azure CLI を使い慣れていない場合、その詳細について、「[Azure Resource Manager での、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)」を確認してください。


Windows で、[Node.js Web サイト](https://nodejs.org/)から npm をインストールします。インストールが完了したら、管理者特権で CMD.exe を使用し、npm のインストール フォルダーから次を実行します。

```
npm install azure-cli --global
```

次に、任意のフォルダー/場所に移動し、次のコマンド ラインを入力します。

```
azure help
```

## Azure へのログイン

まず、Azure アカウントにログインします。

```
azure login
```

そのためには、サインインする必要があります。サインインすると、アカウント、テナント ID、既定のサブスクリプション ID が表示されます。すべてのコマンドは、既定のサブスクリプションのコンテキストで動作します。

現在のサブスクリプションの詳細を一覧表示するには、次のコマンドを使用します。

```
azure account show
```

作業コンテキストを別のサブスクリプションに変更するには、次のコマンドを使用します。

```
azure account set "subscription ID or subscription name"
```

Azure Resource Manager と Azure Insights コマンドを使用するには、ARM モードにする必要があります

```
azure config mode arm
```

サポートされているすべての Azure Insights コマンドの一覧を表示するには、次のコマンドを実行します。

```
azure insights
```

## サブスクリプションの監査ログの表示

監査ログの一覧を表示するには、次のコマンドを実行します。

```
azure insights logs list [options]
```

次のコマンドを実行すると、利用可能なオプションがすべて表示されます。

```
azure insights logs list -help
```

resourceGroup を指定してログの一覧を表示する例を次に示します。

```
azure insights logs list --resourceGroup "myrg1"
```

caller を指定してログの一覧を表示する例

```
azure insights logs list --caller "myname@company.com"
```

caller を指定して、開始日から終了日までの期間のあるリソースの種類のログの一覧を表示する例

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## アラートの操作
このセクションの情報を使用すると、アラートを操作できます。

### リソース グループのアラート ルールの取得

```
node bin\azure insights alerts rule list abhingrgtest123
node bin\azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### メトリックのアラート ルールの作成

```
node bin\azure insights alerts actions email create --customEmails foo@microsoft.com
node bin\azure insights alerts actions webhook create https://someuri.com
node bin\azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### ログのアラート ルールの作成

```
insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### Web テストのアラート ルールの作成

```
node bin\azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### アラート ルールの削除

```
node bin\azure insights alerts rule delete abhingrgtest123 andy0323
```

## ログ プロファイル
このセクションの情報を使用すると、ログ プロファイルを操作できます。

### ログ プロファイルの取得

```
node bin\azure insights logprofile list
node bin\azure insights logprofile get -n default
```


### 保有期間を指定しないログ プロファイルの追加

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### ログ プロファイルの削除

```
node bin\azure insights logprofile delete --name default
```

### 保有期間を指定したログ プロファイルの追加

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### 保有期間とイベント ハブを指定したログ プロファイルの追加

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## 診断
このセクションの情報を使用すると、診断設定を操作できます。

### 診断設定の取得

```
node bin\azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### 診断設定の無効化

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### 保有期間を指定しない診断設定の有効化

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## Autoscale
このセクションの情報を使用すると、自動スケール設定を操作できます。これらの例は、変更が必要になります。

### リソース グループの自動スケール設定の取得

```
node bin\azure insights autoscale setting list montest2
```

### 名前によるリソース グループの自動スケール設定の取得

```
node bin\azure insights autoscale setting list montest2 -n setting2
```


### 自動スケール設定の実行

```
node bin\azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

<!---HONumber=AcomDC_0810_2016-->