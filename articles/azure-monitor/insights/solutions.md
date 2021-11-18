---
title: Azure Monitor での監視ソリューション | Microsoft Docs
description: さまざまな問題領域のロジック、視覚化、データ取得の規則の事前パッケージ化コレクションに関する情報を取得します。
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 10/16/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 7c8a2fe0c2dd34bf07031c8f4d8e8472d67dea3f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707759"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure Monitor での監視ソリューション

> [!CAUTION]
> 監視ソリューションは、アクティブな開発ではなくなりました。  交換テクノロジは、[Azure Monitor 分析情報](../monitor-reference.md)です。 分析情報を使用し、ソリューションの新しいインスタンスをデプロイしないことをお勧めします。  

Azure Monitor の監視ソリューションでは、ある Azure アプリケーションまたはサービスの操作が分析されます。 この記事では、Azure の監視ソリューションの簡単な概要と、これの使用とインストールに関する詳細について説明します。 

使用する任意のアプリケーションおよびサービスの Azure Monitor に監視ソリューションを追加できます。 通常、これらは無料で使用できますが、利用料金が発生する可能性のあるデータが収集されます。

## <a name="use-monitoring-solutions"></a>監視ソリューションの使用

Azure Monitor の **[概要]** ページには、Log Analytics ワークスペースにインストールされているソリューション別にタイルが表示されます。 このページを開くには、[Azure portal](https://ms.portal.azure.com) で **Azure Monitor** に移動します。 **[Insights]** メニューでは、 **[詳細]** を選択して **[Insights Hub]** を開き、 **[Log Analytics ワークスペース]** を選択します。

[![Insights Hub を開くための選択を示すスクリーンショット。](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


画面上部のドロップダウン ボックスを使用して、タイルに使用するワークスペースまたは時間の範囲を変更します。 ソリューションのタイルを選択して、収集されたデータのより詳細な分析を含むビューを開きます。

[![Azure portal での監視ソリューションの統計情報を示すスクリーンショット。](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

監視ソリューションには、複数の種類の Azure リソースが含まれることがあります。 ソリューションに含まれる任意のリソースは、他のリソースと同様に表示できます。 たとえば、ソリューションに含まれているすべてのログ クエリは [クエリ エクスプローラー](../logs/log-analytics-tutorial.md)の **[ソリューション クエリ]** に表示されます。 これらのクエリは、[ログ クエリ](../logs/log-query-overview.md)を使用してアドホック分析を実行するときに使用できます。

## <a name="list-installed-monitoring-solutions"></a>インストールされている監視ソリューションを一覧表示する

### <a name="portal"></a>[ポータル](#tab/portal)

サブスクリプションにインストールされている監視ソリューションを一覧表示するには:

1. [Azure ポータル](https://ms.portal.azure.com)にアクセスします。 **[ソリューション]** を検索して選択します。

   すべてのワークスペースにインストールされているソリューションが一覧表示されます。 ソリューション名の後に、これがインストールされているワークスペースの名前が続けて表示されます。
1. 画面上部のドロップダイン ボックスを使用して、サブスクリプションまたはリソース グループごとにフィルター処理します。

![一覧表示されているソリューションを示すスクリーンショット。](media/solutions/list-solutions-all.png)

ソリューション名を選択して、その概要ページを開きます。 このページには、ソリューションに含まれるすべてのビューが表示され、ソリューション自体とそのワークスペースに対するオプションが用意されています。

![ソリューションの概要情報を示すスクリーンショット。](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

サブスクリプションにインストールされている監視ソリューションを一覧表示するには、[az monitor log-analytics solution list](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list) コマンドを使用します。 このコマンドを実行する前に、「[監視ソリューションをインストールする](#install-a-monitoring-solution)」に記載されている前提条件に従ってください。

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

サブスクリプションにインストールされている監視ソリューションを一覧表示するには、[Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) コマンドレットを使用します。 このコマンドレットを実行する前に、「[監視ソリューションをインストールする](#install-a-monitoring-solution)」に記載されている前提条件に従ってください。

```azurepowershell-interactive
# List all Log Analytics solutions in the current subscription
Get-AzMonitorLogAnalyticsSolution

# List all Log Analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all Log Analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>監視ソリューションをインストールする

### <a name="portal"></a>[ポータル](#tab/portal)

Microsoft およびパートナーの監視ソリューションは、[Azure Marketplace](https://azuremarketplace.microsoft.com) から入手できます。 次の手順を使用して、使用可能なソリューションを検索し、インストールできます。 ソリューションをインストールするときに、ソリューションがインストールされる [Log Analytics ワークスペース](../logs/manage-access.md)と、データが収集される場所を選択する必要があります。

1. [お使いのサブスクリプションのソリューションの一覧](#list-installed-monitoring-solutions)で、 **[追加]** を選択します。
1. ソリューションを参照または検索します。 [この検索リンク](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)も利用できます。
1. 目的の監視ソリューションを見つけ、その説明を読みます。
1. **[作成]** を選択して、インストール プロセスを開始します。
1. ダイアログが表示されたら、Log Analytics ワークスペースを指定し、ソリューションに必要な構成を提供します。

![Azure Marketplace でのソリューションを示すスクリーンショット。](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>コミュニティからソリューションをインストールする

コミュニティのメンバーは、Azure クイック スタート テンプレートに管理ソリューションを提出できます。 これらのソリューションを直接インストールするか、または後でインストールするためにテンプレートをダウンロードできます。

1. 「[Log Analytics ワークスペースと Automation アカウント](#log-analytics-workspace-and-automation-account)」で説明されているプロセスに従って、ワークスペースとアカウントをリンクします。
2. [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)にアクセスします。
3. 興味のあるソリューションを検索します。
4. 検索結果のソリューションを選択して、詳細を確認します。
5. **[Azure へのデプロイ]** ボタンを選択します。
6. ソリューションのパラメーターの値に加えて、リソース グループや場所などの情報を指定するように求められます。
7. **[購入]** を選択して、ソリューションをインストールします。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>環境を準備する

1. Azure CLI をインストールします。

   CLI 参照コマンドを実行する前に、[Azure CLI をインストールする](/cli/azure/install-azure-cli)必要があります。 必要に応じて、Azure Cloud Shell を使用してこの記事の手順を完了することもできます。 Azure Cloud Shell は、ブラウザーを介して使用する対話型のシェル環境です。 Cloud Shell を開くには、次のいずれかの方法を使用します。

   - [Cloud Shell Web ページ](https://shell.azure.com)にアクセスします。

   - **Azure portal** で、右上隅にあるメニュー バーの [[Cloud Shell]](https://portal.azure.com) ボタンを選択します。 

1. サインインします。

   CLI のローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az_login) コマンドを使用してサインインします。  ターミナルに表示される手順に従って、認証プロセスを完了します。

    ```azurecli
    az login
    ```

1. `log-analytics-solution` 拡張機能をインストールします。

   `log-analytics-solution` コマンドは、コア Azure CLI の試験段階の拡張機能です。 拡張機能のリファレンスの詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview?)」を参照してください。

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   次の警告が生成されます。

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Azure CLI を使用したソリューションのインストール

ソリューションをインストールするときに、ソリューションがインストールされる [Log Analytics ワークスペース](../logs/manage-access.md)と、データが収集される場所を選択する必要があります。  Azure CLI では、[az monitor log-analytics ワークスペース](/cli/azure/monitor/log-analytics/workspace) 参照コマンドを使用して、ワークスペースを管理します。  「[Log Analytics ワークスペースと Automation アカウント](#log-analytics-workspace-and-automation-account)」で説明されているプロセスに従って、ワークスペースとアカウントをリンクします。

[az monitor log-analytics solution create](/cli/azure/monitor/log-analytics/solution) コマンドを使用して、監視ソリューションをインストールします。  角かっこで囲まれているパラメーターは省略可能です。

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

OMSGallery/Containers のプラン製品の Log Analytics ソリューションを作成するコード サンプルを次に示します。

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>環境を準備する

1. Azure PowerShell をインストールします。

   Azure PowerShell 参照コマンドを実行する前に [Azure PowerShell をインストールする](/powershell/azure/install-az-ps)必要があります。 必要に応じて、Azure Cloud Shell を使用してこの記事の手順を完了することもできます。 Azure Cloud Shell は、ブラウザーを介して使用する対話型のシェル環境です。 Cloud Shell を開くには、次のいずれかの方法を使用します。

   - [Cloud Shell Web ページ](https://shell.azure.com)にアクセスします。

   - **Azure portal** で、右上隅にあるメニュー バーの [[Cloud Shell]](https://portal.azure.com) ボタンを選択します。

   > [!IMPORTANT]
   > **Az.MonitoringSolutions** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを個別にインストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. サインインします。

   PowerShell のローカル インストールを使用している場合、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用してサインインします。 PowerShell に表示される手順に従って認証プロセスを完了します。

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Azure PowerShell を使用したソリューションのインストール

ソリューションをインストールするときに、ソリューションがインストールされる [Log Analytics ワークスペース](../logs/manage-access.md)と、データが収集される場所を選択する必要があります。 Azure PowerShell では、[Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions) PowerShell モジュールのコマンドレットを使用してワークスペースを管理します。 「[Log Analytics ワークスペースと Automation アカウント](#log-analytics-workspace-and-automation-account)」で説明されているプロセスに従って、ワークスペースとアカウントをリンクします。

[New-AzMonitorLogAnalyticsSolution の](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) コマンドレットを使用し、監視ソリューションをインストールします。 角かっこで囲まれているパラメーターは省略可能です。

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

次の例では、Log Analytics ワークスペースの監視ソリューションが作成されます。

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics ワークスペースと Automation アカウント

収集されたデータを格納し、ログ検索とビューをホストするために、すべての監視ソリューションで [Log Analytics ワークスペース](../logs/manage-access.md)が必要です。 また、一部のソリューションでは、Runbook と関連するリソースを格納するために、[Automation アカウント](../../automation/automation-security-overview.md)も必要になります。 ワークスペースとアカウントでは、次の要件を満たす必要があります。

* ソリューションの各インストールで使うことができる Log Analytics ワークスペースと Automation アカウントは、それぞれ 1 つのみです。 複数のワークスペースにソリューションを個別にインストールできます。
* ソリューションに Automation アカウントが必要な場合は、Log Analytics ワークスペースと Automation アカウントを互いにリンクさせる必要があります。 1 つの Log Analytics ワークスペースは 1 つの Automation アカウントにのみリンクでき、1 つの Automation アカウントは 1 つの Log Analytics ワークスペースにのみリンクできます。

Azure Marketplace からソリューションをインストールする場合は、ワークスペースと Automation アカウントが求められます。 まだリンクされていない場合は、これらの間にリンクが作成されます。

Log Analytics ワークスペースと Automation アカウント間のリンクを検証するには:

1. Azure Portal で Automation アカウントを選択します。
1. メニューの **[関連リソース]** セクションまでスクロールし、 **[リンクされたワークスペース]** を選択します。
1. ワークスペースが Automation アカウントにリンクされている場合、リンクされているワークスペースがこのページに一覧表示されます。 一覧表示されるワークスペースの名前を選択すると、そのワークスペースの概要ページにリダイレクトされます。

## <a name="remove-a-monitoring-solution"></a>監視ソリューションを削除する

### <a name="portal"></a>[ポータル](#tab/portal)

ポータルを使用して、インストール済みのソリューションを削除するには、[インストールされているソリューションの一覧](#list-installed-monitoring-solutions)でそれを検索します。 ソリューション名を選択して [概要] ページを開き、 **[削除]** を選択します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、インストール済みのソリューションを削除するには、[az monitor log-analytics solution delete](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete) コマンドを使用します。

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell を使用してインストールされているソリューションを削除するには、[Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) コマンドレットを使用します。

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>次のステップ

* [Microsoft の監視ソリューションの一覧](../monitor-reference.md)を取得します。
* 監視ソリューションで収集されたデータを分析するための[クエリを作成する](../logs/log-query-overview.md)方法を確認します。
* [Azure Monitor のすべての Azure CLI コマンド](/cli/azure/azure-cli-reference-for-monitor)を確認します。
