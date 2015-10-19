<properties 
	pageTitle="リソース マネージャーの監査操作 | Microsoft Azure" 
	description="リソース マネージャーの監査ログを使用し、ユーザーの操作やエラーを確認します。PowerShell、Azure、REST を表示します。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="tomfitz"/>

# リソース マネージャーの監査操作

ソリューションのデプロイメント中または使用中に問題を見つけたら、その原因を究明する必要があります。リソース マネージャーでは、発生した問題とその原因を 2 通りの方法で発見できます。デプロイメント コマンドを利用し、特定のデプロイメントと操作に関する情報を取得できます。あるいは、監査ログを利用し、デプロイメントとソリューションの使用期間中に行われたその他のアクションに関する情報を取得できます。このトピックでは、監査ログに重点を置いて説明します。

監査ログには、リソースで実行されたすべての操作が含まれています。そのため、組織のユーザーがリソースを変更した場合、そのアクション、時刻、ユーザーを特定できます。

Azure PowerShell、Azure CLI、REST API、Azure プレビュー ポータルを利用し、監査ログから情報を取得できます。

## PowerShell

ログ エントリを取得するには、**Get-AzureRmLog** コマンドを実行します (1.0 プレビューよりも前のバージョンの PowerShell では **Get-AzureResourceGroupLog** を実行します)。パラメーターを追加し、エントリの一覧を絞り込むことができます。

次の例は、監査ログを利用し、ソリューションの使用期間中に行われたアクションを調査する方法を示すものです。アクションが発生したタイミングとそれを要請したユーザーを確認できます。

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00

指定した開始時刻によっては、前のコマンドを実行したとき、そのリソース グループのアクションが長い一覧で返されることがあります。検索基準を指定すると、探しものの結果を絞り込むことができます。たとえば、Web アプリが停止した理由を調査する場合、次のコマンドを実行すると、someone@example.com が停止アクションを実行したことが判明します。

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

次の例では、指定した開始時刻後に失敗したアクションがわかります。エラー メッセージを表示するには、**DetailedOutput** パラメーターも追加します。

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-27T12:00 -Status Failed –DetailedOutput
    
このコマンドで返されるエントリとプロパティの数が多すぎる場合、**properties** プロパティを取得することで監査に集中できます。

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

また、ステータス メッセージを見て、結果をさらに絞り込むことができます。

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Azure CLI

ログ エントリを取得するには、**azure group log show** コマンドを実行します。

    azure group log show ExampleGroup

[jq](http://stedolan.github.io/jq/download/) など、JSON ユーティリティで結果を絞り込むことができます。次の例では、Web 構成ファイルの更新を含む操作を探す方法を示しています。

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

**–-last-deployment** パラメーターを追加すれば、返されるエントリを最後のデプロイメントからの操作のみに制限できます。

    azure group log show ExampleGroup --last-deployment

最後のデプロイメントからの操作の一覧が長すぎる場合は、失敗した操作の結果だけに絞り込むことができます。

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == "Failed")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## REST API

監査ログを利用するための REST 操作は [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) に含まれています。監査ログのイベントを取得するには、「[サブスクリプションで管理イベントを一覧表示する](https://msdn.microsoft.com/library/azure/dn931934.aspx)」を参照してください。

## プレビュー ポータル

プレビュー ポータルでログに記録された操作を表示することもできます。監査ログのブレードを選択するだけです。

![監査ログの選択](./media/resource-group-audit/select-audit.png)

また、最新の操作の一覧を表示します。

![アクションの表示](./media/resource-group-audit/show-actions.png)

任意の操作を選択し、その詳細を表示できます。

## 次のステップ

- セキュリティ ポリシーの設定方法の詳細については、「[リソースへのアクセスの管理](./azure-portal/resource-group-rbac.md)」を参照してください。
- サービス プリンシパルにアクセスを付与する方法については、「[Azure リソース マネージャーでのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md)」を参照してください。
- すべてのユーザーのリソースに対するアクションについては、「[Azure リソース マネージャーによるリソースのロック](resource-group-lock-resources.md)」を参照してください。

<!---HONumber=Oct15_HO2-->