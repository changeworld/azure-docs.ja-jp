<properties
	pageTitle="リソース マネージャーの監査操作 | Microsoft Azure"
	description="リソース マネージャーの監査ログを使用し、ユーザーの操作やエラーを確認します。Azure ポータル、PowerShell、Azure CLI、および REST を表示します。"
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tomfitz"/>

# リソース マネージャーの監査操作

監査ログを使用すると、次の内容を判断することができます。

- サブスクリプション内のリソースで行われた操作
- 操作を開始したユーザー (バックエンド サービスによって開始された操作は、呼び出し側としてユーザーを返しません)
- 操作が発生した時間
- 操作の状態
- 操作を調査するために役立つ可能性のあるその他のプロパティの値

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

このトピックでは、監査の操作に重点を置いて説明します。デプロイのトラブルシューティングを行うために監査ログを使用する方法については、「[Azure でのリソース グループのデプロイのトラブルシューティング](resource-manager-troubleshoot-deployments-portal.md)」を参照してください。

Azure ポータル、Azure PowerShell、Azure CLI、Insights REST API、または [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/) を利用し、監査ログから情報を取得できます。

## 監査ログを表示するポータル

1. ポータルで監査ログを表示するには、**[参照]**、**[監査ログ]** の順に選択します。

    ![監査ログの選択](./media/resource-group-audit/select-audit-logs.png)

2. **[監査ログ]** ブレードで、サブスクリプション内のすべてのリソース グループに対する最近の操作の概要が表示されます。これには、時間のグラフィック表示、操作の状態、および操作の一覧が含まれます。

    ![アクションの表示](./media/resource-group-audit/audit-summary.png)

3. アクションの特定の種類を検索するには、[監査ログ] ブレードに表示される操作をフィルター処理できます。ブレードの上部にある **[フィルター]** をクリックします。

    ![ログのフィルター](./media/resource-group-audit/filter-logs.png)

4. **[フィルター]** ブレードで、表示する操作の数を制限するために、多くのさまざまな条件を選択することができます。たとえば、前の週に特定のユーザーによって実行されたすべてのアクションを表示できます。

    ![フィルター オプションの設定](./media/resource-group-audit/set-filter.png)

監査ログのビューを更新すると、指定した条件に一致する操作のみが表示されます。これらの設定は、次に監査ログを表示するまで保持されるため、操作のビューの範囲を広げるために、これらの値を変更する必要がある場合があります。

また、リソース ブレードから監査ログを選択して、特定のリソースに対して自動的にフィルター処理することもできます。ポータルで監査するリソースを選択し、**[監査ログ]** を選択します。

![リソースの監査](./media/resource-group-audit/audit-by-resource.png)

監査ログは前の週に選択したリソースによって、自動的にフィルター処理されることに注意してください。

![リソースによるフィルター](./media/resource-group-audit/filtered-by-resource.png)

## 監査ログを表示する PowerShell

1. ログ エントリを取得するには、**Get-AzureRmLog** コマンドを実行します。パラメーターを追加し、エントリの一覧を絞り込むことができます。開始時間と終了時間を指定しない場合は、過去 1 時間のエントリが返されます。たとえば、過去 1 時間のリソース グループの操作を取得するには、次を実行します。

        Get-AzureRmLog -ResourceGroup ExampleGroup

    次の例は、監査ログを利用し、指定した時間に行われた操作を調査する方法を示しています。開始日と終了日は、日付の形式で指定されます。

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

    または、日付関数を使用して、最後の 14 日など、日付の範囲を指定することができます。

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)

2. 指定した開始時刻によっては、前のコマンドを実行したときに、そのリソース グループの操作が長い一覧で返されることがあります。検索基準を指定すると、探しものの結果を絞り込むことができます。たとえば、Web アプリが停止した理由を調査する場合は、次のコマンドを実行します。

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
        
    この例では、これは someone@contoso.com によって停止アクションが実行されたことを示しています。
        
        Authorization     :
        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Action    : Microsoft.Web/sites/stop/action
        Role      : Subscription Admin
        Condition :
        Caller            : someone@contoso.com
        CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
        EventSource       : Administrative
        EventTimestamp    : 8/28/2015 4:08:18 PM
        OperationName     : Microsoft.Web/sites/stop/action
        ResourceGroupName : ExampleGroup
        ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Status            : Succeeded
        SubscriptionId    : xxxxx
        SubStatus         : OK

3. 存在しなくなったリソース グループであっても、特定のユーザーが行ったアクションを検索できます。

        Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## 監査ログを表示する Azure CLI

1. ログ エントリを取得するには、**azure group log show** コマンドを実行します。

        azure group log show ExampleGroup

2. [jq](http://stedolan.github.io/jq/download/) など、JSON ユーティリティで結果を絞り込むことができます。次の例では、Web 構成ファイルを更新した操作を探す方法を示しています。

        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

3. 特定のユーザーのアクションを検索することができます。

        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## 監査ログを表示する REST API

監査ログを利用するための REST 操作は [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) に含まれています。監査ログのイベントを取得するには、「[サブスクリプションで管理イベントを一覧表示する](https://msdn.microsoft.com/library/azure/dn931934.aspx)」を参照してください。

## 次のステップ

- Azure 監査ログは、サブスクリプション内のアクションに関してさらに洞察を得るために、Power BI で使用できます。「[View and analyze Azure Audit Logs in Power BI and more (Power BI などでの Azure 監査ログの表示と分析)](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)」をご覧ください。
- セキュリティ ポリシーを設定する方法については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。
- デプロイのトラブルシューティングに使用するコマンドについては、「[Azure でのリソース グループのデプロイのトラブルシューティング](resource-manager-troubleshoot-deployments-portal.md)」をご覧ください。
- すべてのユーザーのリソースに対する削除を回避する方法については、「[Azure Resource Manager によるリソースのロック](resource-group-lock-resources.md)」をご覧ください。

<!---HONumber=AcomDC_0413_2016-->