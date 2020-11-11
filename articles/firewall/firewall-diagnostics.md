---
title: Azure Firewall のログとメトリックを監視する
description: この記事では、Azure Firewall のログとメトリックを有効にして管理する方法を学習します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 2899121db4b6a3f202be4860e2e4f43027cdef7c
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348769"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Azure Firewall のログとメトリックを監視する

Azure Firewall を監視するには、ファイアウォール ログを使用できます。 また、アクティビティ ログを使用して、Azure Firewall リソースに対する操作を監査することもできます。 メトリックを使用して、ポータルにパフォーマンス カウンターを表示できます。

一部のログにはポータルからアクセスできます。 ログを [Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)、Storage、および Event Hubs に送信し、Azure Monitor ログや他のツール (Excel、Power BI など) で分析することができます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

開始する前に、[Azure Firewall のログとメトリック](logs-and-metrics.md)に関する記事で、Azure Firewall で入手できる診断ログとメトリックの概要を確認してください。

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Azure portal を使用した診断ログの有効化

この手順を完了して診断ログを有効にしてからデータがログに記録されるまでに数分かかることがあります。 最初に何も表示されない場合は、数分後にもう一度確認してください。

1. Azure portal で、ファイアウォール リソース グループを開き、ファイアウォールを選択します。
2. **[監視]** で **[診断設定]** を選択します。

   Azure Firewall の場合、サービスに固有の 4 種類のログを使用できます。

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. **[診断設定の追加]** を選択します。 **[診断設定]** ページに、診断ログの設定が表示されます。
5. この例では、Azure Monitor ログにログを保存するため、名前として「 **Firewall log analytics** 」と入力します。
6. **[ログ]** の下で、 **[AzureFirewallApplicationRule]** 、 **[AzureFirewallNetworkRule]** 、 **[AzureFirewallThreatIntelLog]** 、および **[AzureFirewallDnsProxy]** を選択してログを収集します。
7. **[Log Analytics への送信]** を選択してワークスペースを構成します。
8. サブスクリプションを選択します。
9. **[保存]** を選択します。

## <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 診断ログで取得できるデータの収集を開始するには、診断ログを有効にする必要があります。

診断ログを有効にするには、次の手順を使用します。

1. ログ データを保存するストレージ アカウントのリソース ID をメモしておきます。 この値の形式は */subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>* です。

   サブスクリプション内の任意のストレージ アカウントを使用できます。 この情報は、Azure Portal で確認できます。 情報はリソースの **プロパティ** ページにあります。

2. ログが有効になっているファイアウォールのリソース ID に注意してください。 この値の形式は */subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/azureFirewalls/\<Firewall name\>* です。

   この情報は、ポータルで確認できます。

3. 次の PowerShell コマンドレットを使用して、診断ログを有効にします。

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>診断ログでは別のストレージ アカウントは必要ありません。 アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。

## <a name="enable-diagnostic-logging-by-using-azure-cli"></a>Azure CLI を使用して診断ログを有効にする

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 診断ログで取得できるデータの収集を開始するには、診断ログを有効にする必要があります。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="enable-diagnostic-logging"></a>診断ログの有効化

診断ログを有効にするには次のコマンドを使用します。

1. 診断ログを有効にするには、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) コマンドを実行します。

   ```azurecli
   az monitor diagnostic-settings create –name AzureFirewallApplicationRule \
     --resource Firewall07 --storage-account MyStorageAccount
   ```

   リソースの診断設定を見るには、[az monitor diagnostic-settings list](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_list) コマンドを実行します。

   ```azurecli
   az monitor diagnostic-settings list --resource Firewall07
   ```

   リソースのアクティブな診断設定を見るには、[az monitor diagnostic-settings show](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_show) を使用します。

   ```azurecli
   az monitor diagnostic-settings show --name AzureFirewallApplicationRule --resource Firewall07
   ```

1. 設定を更新するには、[az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) コマンドを実行します。

   ```azurecli
   az monitor diagnostic-settings update --name AzureFirewallApplicationRule --resource Firewall07 --set retentionPolicy.days=365
   ```

   診断設定を削除するには、[az monitor diagnostic-settings delete](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_delete) コマンドを使用します。

   ```azurecli
   az monitor diagnostic-settings delete --name AzureFirewallApplicationRule --resource Firewall07
   ```

> [!TIP]
>診断ログでは別のストレージ アカウントは必要ありません。 アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。

## <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools** :Azure PowerShell、Azure CLI、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md)」を参照してください。
* **Power BI** : [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure アクティビティ ログ コンテンツ パック](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。
* **Azure Sentinel** :Azure Firewall ログを Azure Sentinel に接続すると、ブック内でログ データを表示し、それを使用してカスタム アラートを作成し、組み込んで、調査を改善することができます。 Azure Sentinel の Azure Firewall データ コネクタは、現在パブリック プレビュー段階にあります。 詳細については、[Azure Firewall からのデータの接続](../sentinel/connect-azure-firewall.md)に関するページを参照してください。

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>ネットワークおよびアプリケーション ルール ログの表示と分析

[Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)は、カウンターおよびイベント ログ ファイルを収集します。 このツールには、ログを分析するための視覚化と強力な検索機能が含まれています。

Azure Firewall のログ分析のサンプル クエリについては、「[Azure Firewall Log Analytics のサンプル](log-analytics-samples.md)」を参照してください。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## <a name="view-metrics"></a>メトリックを表示する
Azure Firewall に移動し、 **[監視]** の **[メトリック]** を選択します。 利用できる値を表示するには、 **[メトリック]** ドロップダウン リストを選択します。

## <a name="next-steps"></a>次のステップ

ログを収集するようにファイアウォールを構成した後、Azure Monitor ログを使用してデータを表示できます。

[Azure Monitor ログのネットワーク監視ソリューション](../azure-monitor/insights/azure-networking-analytics.md)
