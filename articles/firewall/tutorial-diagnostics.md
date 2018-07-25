---
title: チュートリアル - Azure Firewall のログを監視する
description: このチュートリアルでは、Azure Firewall のログを有効にして管理する方法を学習します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991903"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>チュートリアル: Azure Firewall のログを監視する

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure Firewall の記事の例では、Azure Firewall のパブリック プレビューを既に有効にしていることを前提としています。 詳細については、「[Enable the Azure Firewall public preview (Azure Firewall パブリック プレビューを有効にする)](public-preview.md)」を参照してください。

Azure Firewall を監視するには、ファイアウォール ログを使用できます。 また、アクティビティ ログを使用して、Azure Firewall リソースに対する操作を監査することもできます。

一部のログにはポータルからアクセスできます。 ログを [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、Storage、および Event Hubs に送信し、Log Analytics や他のツール (Excel、Power BI など) で分析することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Portal を使用したログの有効化
> * PowerShell を使用したログの有効化
> * アクティビティ ログの表示と分析
> * ネットワークおよびアプリケーション ルール ログの表示と分析

## <a name="diagnostic-logs"></a>診断ログ

 Azure Firewall では、次の診断ログを利用できます。

* **アプリケーション ルール ログ**

   アプリケーション ルール ログは、Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信され、Log Analytics に送信されます。 構成されているアプリケーション ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます。 次の例に示すように、データは JSON 形式でログに記録されます。

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **ネットワーク ルール ログ**

   ネットワーク ルール ログは、Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信され、Log Analytics に送信されます。 構成されているネットワーク ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます。 次の例に示すように、データは JSON 形式でログに記録されます。

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

ログを保存するための 3 つのオプションがあります。

* **ストレージ アカウント**: ストレージ アカウントは、ログを長期間保存し、必要に応じて参照する場合に最適です。
* **イベント ハブ**: イベント ハブは、他のセキュリティ情報/イベント管理 (SEIM) ツールと統合してリソースに関するアラートを取得する場合に便利なオプションです。
* **Log Analytics**: Log Analytics は、アプリケーションをリアルタイムに監視したり、傾向を見たりする一般的な用途に最適です。

## <a name="activity-logs"></a>アクティビティ ログ

   アクティビティ ログ エントリは既定で収集され、Azure Portal で表示できます。

   [Azure アクティビティ ログ](../azure-resource-manager/resource-group-audit.md) (以前の操作ログと監査ログ) を使用して、Azure サブスクリプションに送信されるすべての操作を表示できます。

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Azure portal を使用した診断ログの有効化

この手順を完了して診断ログを有効にしてからデータがログに記録されるまでに数分かかることがあります。 最初に何も表示されない場合は、数分後にもう一度確認してください。

1. Azure portal で、ファイアウォール リソース グループを開き、ファイアウォールをクリックします。
2. **[監視]** の **[診断ログ]** をクリックします。

   Azure Firewall の場合、サービスに固有の 2 種類のログを使用できます。

   * アプリケーション ルール ログ
   * ネットワーク ルール ログ

3. データの収集を開始するには、**[診断を有効にする]** をクリックします。
4. **[診断設定]** ページに、診断ログの設定が表示されます。 
5. この例では、Log Analytics にログを保存するため、名前として「**Firewall log analytics**」と入力します。
6. **[Send to Log Analytics]\(Log Analytics に送信\)** をクリックして、ワークスペースを構成します。 イベント ハブとストレージ アカウントを使用して診断ログを保存することもできます。
7. **[Log Analytics]** で、**[構成]** をクリックします。
8. [OMS ワークスペース] ページで、**[新しいワークスペースの作成]** をクリックします。
9. **[Log Analytics ワークスペース]** ページで、新しい **OMS ワークスペース**の名前として「**firewall-oms**」と入力します。
10. サブスクリプションを選択し、既存のファイアウォール リソース グループ (**Test-FW-RG**) を使用します。場所として **[米国東部]** を選択し、**[Free]** 価格レベルを選択します。
11. Click **OK**.
   ![構成プロセスの開始][1]
12. アプリケーション ルールとネットワーク ルールのログを収集するために、**[ログ]** で **[AzureFirewallApplicationRule]** と **[AzureFirewallNetworkRule]** をクリックします。
   ![診断設定を保存する][2]
13. **[Save]** をクリックします。

## <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 診断ログで取得できるデータの収集を開始するには、診断ログを有効にする必要があります。

診断ログを有効にするには、次の手順を使用します。

1. ログ データを保存するストレージ アカウントのリソース ID をメモしておきます。 この値の形式は、*/subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Storage/storageAccounts/\<ストレージ アカウント名\>* です。

   サブスクリプション内の任意のストレージ アカウントを使用できます。 この情報は、Azure Portal で確認できます。 情報はリソースの**プロパティ** ページにあります。

2. ログが有効になっているファイアウォールのリソース ID に注意してください。 この値の形式は、*/subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Network/azureFirewalls/\<ファイアウォール名\>* です。

   この情報は、ポータルで確認できます。

3. 次の PowerShell コマンドレットを使用して、診断ログを有効にします。

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>診断ログでは別のストレージ アカウントは必要ありません。 アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。

## <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools:** Azure PowerShell、Azure CLI、Azure REST API、または Azure Portal を使用して、アクティビティ ログから情報を取得します。 それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../azure-resource-manager/resource-group-audit.md)」を参照してください。
* **Power BI**: [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure アクティビティ ログ コンテンツ パック](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>ネットワークおよびアプリケーション ルール ログの表示と分析

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) は、カウンターおよびイベント ログ ファイルを収集します。 このツールには、ログを分析するための視覚化と強力な検索機能が含まれています。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。


## <a name="next-steps"></a>次の手順

ログを収集するようにファイアウォールを構成した後は、Log Analytics を使用してデータを表示できます。

> [!div class="nextstepaction"]
> [Log Analytics のネットワーク監視ソリューション](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
