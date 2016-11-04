---
title: リソース マネージャーの監査操作 | Microsoft Docs
description: Resource Manager のアクティビティ ログを使用してユーザーの操作やエラーを確認します。Azure ポータル、PowerShell、Azure CLI、および REST を表示します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz

---
# リソース マネージャーの監査操作
アクティビティ ログを使用すると、次の内容を判断することができます。

* サブスクリプション内のリソースで行われた操作
* 操作を開始したユーザー (バックエンド サービスによって開始された操作は、呼び出し側としてユーザーを返しません)
* 操作が発生した時間
* 操作の状態
* 操作を調査するために役立つ可能性のあるその他のプロパティの値

[!INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

このトピックでは、監査の操作に重点を置いて説明します。デプロイのトラブルシューティングを行うためにアクティビティ ログを使用する方法については、[Azure でのリソース グループのデプロイのトラブルシューティング](resource-manager-troubleshoot-deployments-portal.md)に関するページを参照してください。

ポータル、PowerShell、Azure CLI、Insights REST API、または [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/) を利用し、アクティビティ ログから情報を取得できます。

## アクティビティ ログを表示するポータル
1. ポータルからアクティビティ ログを表示するには、**[その他のサービス]**、**[アクティビティ ログ]** の順に選択します。
   
    ![select activity logs](./media/resource-group-audit/select-audit-logs.png)
2. **[アクティビティ ログ]** ブレードに、サブスクリプション内のすべてのリソース グループに対する最近の操作の概要が表示されます。ここには、最近の操作の一覧が含まれます。
   
    ![アクションの表示](./media/resource-group-audit/audit-summary.png)
3. 表示される操作の数を制限するには、異なる条件を選択します。たとえば次の画像には、ある特定のユーザーまたはアプリケーションによって前月実行された操作を表示するように変更された **[期間]** フィールドと **[イベント開始者]** フィールドが示されています。
   
    ![フィルター オプションの設定](./media/resource-group-audit/set-filter.png)
4. **[適用]** を選択してクエリの結果を表示します。
5. 後でもう一度クエリを実行する必要がある場合、**[保存]** を選択してクエリの名前を入力します。
   
    ![save query](./media/resource-group-audit/save-query.png)
6. 特定のリソースまたはリソース グループに対して自動的にフィルター処理を行うには、このリソース ブレードで **[アクティビティ ログ]** を選択します。アクティビティ ログが選択したリソースで自動的にフィルター処理されることに注意してください。
   
    ![リソースによるフィルター](./media/resource-group-audit/filtered-by-resource.png)

## アクティビティ ログを表示する PowerShell
1. ログ エントリを取得するには、**Get-AzureRmLog** コマンドを実行します。パラメーターを追加し、エントリの一覧を絞り込むことができます。開始時間と終了時間を指定しない場合は、過去 1 時間のエントリが返されます。たとえば、過去 1 時間のリソース グループの操作を取得するには、次を実行します。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup
   
    次の例は、監査ログを利用し、指定した時間に行われた操作を調査する方法を示しています。開始日と終了日は、日付の形式で指定されます。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
   
    または、日付関数を使用して、最後の 14 日など、日付の範囲を指定することができます。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
2. 指定した開始時刻によっては、前のコマンドを実行したときに、そのリソース グループの操作が長い一覧で返されることがあります。検索基準を指定すると、探しものの結果を絞り込むことができます。たとえば、Web アプリが停止した理由を調査する場合は、次のコマンドを実行します。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
   
    この例では、someone@contoso.com によって停止アクションが実行されたことが示されています。
   
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

## アクティビティ ログを表示する Azure CLI
1. ログ エントリを取得するには、**azure group log show** コマンドを実行します。
   
        azure group log show ExampleGroup
2. [jq](http://stedolan.github.io/jq/download/) など、JSON ユーティリティで結果を絞り込むことができます。次の例では、Web 構成ファイルを更新した操作を探す方法を示しています。
   
        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"
3. 特定のユーザーのアクションを検索することができます。
   
        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## 監査ログを表示する REST API
アクティビティ ログを利用するための REST 操作は [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) に含まれています。アクティビティ ログのイベントを取得するには、[サブスクリプション内の管理イベントの一覧表示](https://msdn.microsoft.com/library/azure/dn931934.aspx)に関するページを参照してください。

## 次のステップ
* Azure アクティビティ ログは、サブスクリプション内のアクションに関してさらに洞察を得るために、Power BI で使用できます。[Power BI などでの Azure アクティビティ ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関する記事をご覧ください。
* セキュリティ ポリシーを設定する方法については、「[Azure のロールベースのアクセス制御](active-directory/role-based-access-control-configure.md)」を参照してください。
* デプロイのトラブルシューティングに使用するコマンドについては、[Azure でのリソース グループのデプロイのトラブルシューティング](resource-manager-troubleshoot-deployments-portal.md)に関するページをご覧ください。
* すべてのユーザーのリソースに対する削除を回避する方法については、「[Azure Resource Manager によるリソースのロック](resource-group-lock-resources.md)」をご覧ください。

<!---HONumber=AcomDC_0824_2016-->