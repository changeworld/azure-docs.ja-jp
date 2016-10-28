<properties 
	pageTitle="Logic Apps の料金モデル | Microsoft Azure" 
	description="Logic Apps の料金体系について詳しく取り上げます。" 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/27/2016"
	ms.author="klam"/>

# Logic Apps の料金モデル

Azure Logic Apps を使用すると、クラウドにおける統合ワークフローをスケーリングして実行することができます。以下、Logic Apps の課金と料金プランについて詳しく取り上げます。

## 従量課金

新しく作成された Logic Apps には、従量制の料金プランが使用されます。Logic Apps の従量課金モデルでは、お客様が使用した分のみが課金の対象となります。従量制の料金プランを使用している Logic Apps はスロットルされません。ロジック アプリのインスタンス実行中に行われたすべてのアクションが課金の対象となります。

### アクションの実行とは

ロジック アプリの定義に含まれる個々のステップがアクションです。たとえばトリガーや制御フローのステップ (条件、スコープ、for each ループ、do until ループなど)、コネクタの呼び出し、ネイティブ アクションの呼び出しが該当します。トリガーは、特定のイベントが発生したときにロジック アプリの新しいインスタンスを作成する特殊なアクションと考えることができます。トリガーにはさまざまな動作が存在し、それによってロジック アプリの課金方法が変わる場合があります。

-	**ポーリング トリガー** – ロジック アプリの新しいインスタンスを作成するうえでの基準を満たしたメッセージを受け取るまで絶えずエンドポイントをポーリングするトリガーです。ポーリング間隔は、Logic Apps デザイナーでトリガーの設定を変えることで調整できます。ロジック アプリの新しいインスタンスが作成されたかどうかに関係なく、個々のポーリング要求がアクションの実行としてカウントされます。

-	**Webhook トリガー** – クライアントから送信される要求を特定のエンドポイントで待機するトリガーです。Webhook のエンドポイントに送信された個々の要求がアクションの実行としてカウントされます。Request と HTTP Webhook トリガーはどちらも Webhook トリガーです。

-	**定期実行のトリガー** – トリガーに対して構成された繰り返しの間隔に基づいてロジック アプリの新しいインスタンスを作成するトリガーです。たとえば、3 日に 1 回や 1 分間に 1 回の頻度で実行するように定期実行のトリガーを構成することができます。

トリガーの実行は、Logic Apps のリソース ブレードの [トリガーの履歴] 領域で確認できます。

実行されたすべてのアクションは、成功か失敗かに関係なく、アクションの実行として課金されます。条件が満たされなかったためにスキップされたアクションや、ロジック アプリが完了前に強制終了されたために実行されなかったアクションは、アクションの実行としてカウントされません。

ループ内で実行されたアクションは、その繰り返しごとにカウントされます。たとえば、10 個の要素から成るリストを反復処理する for each ループに 1 つのアクションがあるとします。この場合、アクションの実行数は、リストの要素数 (10) にループ内のアクション数 (1) を掛け、ループの開始分に相当する 1 を足した値として計算されます。この例では (10 * 1) + 1 = 11 がアクションの実行数となります。

無効にされた Logic Apps は、新しいインスタンスを作成できないため、無効にされている間は、課金の対象になりません。ロジック アプリを無効化した後、そのインスタンスが休止状態となって完全に無効になるまでには、少し時間がかかるので注意してください。

## App Service プラン

ロジック アプリを作成するうえで App Service プランは不要になりました。既存のロジック アプリで App Service プランを参照することもできます。過去に App Service プランで作成したロジック アプリは、引き続き以前と同様に動作します。選択したプランによっては、1 日あたりの実行数が超過した時点でスロットルされます。この場合、アクションの実行数に基づく課金は適用されません。

App Service プランと 1 日に許可されているアクションの実行数:

| |Free/Shared/Basic|標準|プレミアム|
|---|---|---|---|
|1 日あたりのアクションの実行数| 200|10,000|50,000|

### 従量課金から App Service プランへの変換

従量課金の Logic Apps の App Service プランを参照するには、[以下の PowerShell スクリプト](https://github.com/logicappsio/ConsumptionToAppServicePlan)を実行します。最初に [Azure PowerShell ツール](https://github.com/Azure/azure-powershell)がインストールされていることを確認します。

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
	[string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### App Service プランから従量課金への変換

App Service プランが関連付けられているロジック アプリを従量制の課金モデルに変更するには、ロジック アプリの定義から App Service プランの参照を削除します。これは PowerShell コマンドレットで実行できます。次のコマンドレットを呼び出してください。

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## 価格

料金の詳細については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」を参照してください。

## 次のステップ

- [Logic Apps の概要][whatis]
- [初めてのロジック アプリの作成][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0810_2016-->