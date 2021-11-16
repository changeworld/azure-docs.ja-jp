---
title: Azure Firewall のログとメトリックを監視する
description: この記事では、Azure Firewall のログとメトリックを有効にして管理する方法を学習します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 10/22/2021
ms.author: victorh
ms.openlocfilehash: f39a858b99bf21a17a250d7e62f4af39c7d8213d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343251"
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
   * AzureFirewallDnsProxy


3. **[診断設定の追加]** を選択します。 **[診断設定]** ページに、診断ログの設定が表示されます。
5. この例では、Azure Monitor ログにログを保存するため、名前として「**Firewall log analytics**」と入力します。
6. **[ログ]** で、 **[AzureFirewallApplicationRule]** 、 **[AzureFirewallNetworkRule]** 、 **[AzureFirewallDnsProxy]** を選択してログを収集します。
7. **[Log Analytics への送信]** を選択してワークスペースを構成します。
8. サブスクリプションを選択します。
9. **[保存]** を選択します。

## <a name="enable-diagnostic-logging-by-using-powershell"></a>Azure PowerShell を使用して診断ログを有効にする

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 診断ログで取得できるデータの収集を開始するには、診断ログを有効にする必要があります。

PowerShell を使用して診断ログを有効にするには、次の手順を使用します。

1. ログ データが保存されている Log Analytics ワークスペースのリソース ID をメモしておきます。 この値の形式は次のとおりです。

   `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>`

   サブスクリプション内の任意のワークスペースを使用できます。 この情報は、Azure Portal で確認できます。 この情報はリソースの **プロパティ** ページにあります。

2. ファイアウォールのリソース ID に注意してください。 この値の形式は次のとおりです。

   `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`

   この情報は、ポータルで確認できます。

3. 次の PowerShell コマンドレットを使用して、すべてのログとメトリックの診断ログを有効にします。

   ```azurepowershell
      $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
      }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>Azure CLI を使用して診断ログを有効にする

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 診断ログで取得できるデータの収集を開始するには、診断ログを有効にする必要があります。

Azure CLI を使用して診断ログを有効にするには、次の手順を使用します。

1. ログ データが保存されている Log Analytics ワークスペースのリソース ID をメモしておきます。 この値の形式は次のとおりです。

   `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>`

   サブスクリプション内の任意のワークスペースを使用できます。 この情報は、Azure Portal で確認できます。 この情報はリソースの **プロパティ** ページにあります。

2. ファイアウォールのリソース ID に注意してください。 この値の形式は次のとおりです。

   `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`

   この情報は、ポータルで確認できます。

3. 次の Azure CLI コマンドを使用して、すべてのログとメトリックの診断ログを有効にします。

   ```azurecli
      az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools**:Azure PowerShell、Azure CLI、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../azure-monitor/essentials/activity-log.md)」を参照してください。
* **Power BI**: [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure アクティビティ ログ コンテンツ パック](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。
* **Microsoft Sentinel**: Azure Firewall ログを Microsoft Sentinel に接続すると、ブック内でログ データを表示し、それを使用してカスタム アラートを作成し、組み込んで、調査を改善することができます。 Microsoft Sentinel の Azure Firewall データ コネクタは、現在パブリック プレビュー段階にあります。 詳細については、[Azure Firewall からのデータの接続](../sentinel/data-connectors-reference.md#azure-firewall)に関するページを参照してください。

   概要については、Mohit Kumar による次のビデオを参照してください。
   > [!VIDEO https://www.microsoft.com/videoplayer/embed/RWI4nn]


## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>ネットワークおよびアプリケーション ルール ログの表示と分析

[Azure Firewall ブック](firewall-workbook.md)により、Azure Firewall のデータ分析のための柔軟なキャンバスが提供されます。 これを使用して、Azure portal 内で高度な視覚的レポートを作成できます。 Azure 全体でデプロイされる複数のファイアウォールを活用し、それらを結合して、統合された対話型エクスペリエンスにすることができます。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## <a name="view-metrics"></a>メトリックを表示する
Azure Firewall に移動します。 **[監視]** で **[メトリック]** を選びます。 利用できる値を表示するには、 **[メトリック]** ドロップダウン リストを選択します。

## <a name="next-steps"></a>次のステップ

ログを収集するようにファイアウォールを構成した後、Azure Monitor ログを使用してデータを表示できます。

- [Azure Firewall ブックを使用してログを監視する](firewall-workbook.md)

- [Azure Monitor ログのネットワーク監視ソリューション](../azure-monitor/insights/azure-networking-analytics.md)
