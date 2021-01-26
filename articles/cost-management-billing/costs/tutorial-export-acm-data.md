---
title: チュートリアル - Azure Cost Management からデータをエクスポートし、管理する
description: この記事では、外部システムで使用できるように Azure Cost Management データをエクスポートし、管理する方法を紹介します。
author: bandersmsft
ms.author: banders
ms.date: 12/7/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: e3c1fa071cd23b871f754e89d6f17eb2cc44b394
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400354"
---
# <a name="tutorial-create-and-manage-exported-data"></a>チュートリアル:データをエクスポートし、管理する

コスト分析のチュートリアルをお読みになっていれば、コスト管理データを手動でダウンロードできます。 その一方で、コスト管理データを Azure Storage に対して毎日、毎週、または毎月、自動的にエクスポートする定期タスクを作成することができます。 エクスポートしたデータは CSV 形式になり、コスト管理で収集したすべての情報がそれに含まれます。 エクスポート後は、Azure Storage に格納されたそのデータを外部システムで利用したり、独自のデータと組み合わせたりできます。 また、エクスポートしたデータを、ダッシュボードやその他の金融システムのような外部システムで利用できます。

Azure のコスト データを Azure Storage にエクスポートするスケジュールの作成については、[Azure Cost Management でストレージへのエクスポートをスケジュールする方法](https://www.youtube.com/watch?v=rWa_xI1aRzo)に関するビデオをご覧ください。 他の動画を視聴するには、[Cost Management の YouTube チャンネル](https://www.youtube.com/c/AzureCostManagement)にアクセスしてください。

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

このチュートリアルの例では、段階的にコスト管理データをエクスポートし、データが正常にエクスポートされたことを確認します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 毎日のエクスポートを作成する
> * データが収集されたことを確認する

## <a name="prerequisites"></a>前提条件
データのエクスポートは、[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) や [Microsoft 顧客契約](get-started-partners.md)のお客様など、さまざまな Azure アカウントの種類で使用できます。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](understand-cost-mgt-data.md)」を参照してください。 ユーザーおよびグループによるデータのエクスポートについては、サブスクリプションに従い、次の Azure のアクセス許可、またはスコープがサポートされています。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。

- 所有者: サブスクリプションのスケジュールされたエクスポートを作成、変更、または削除できます。
- 共同作成者: スケジュールされたエクスポートを作成、変更、または削除できます。 他のユーザーが作成したスケジュールされたエクスポートの名前を変更できます。
- 閲覧者: アクセス許可を持っているエクスポートをスケジュールできます。

Azure Storage アカウントの場合:
- エクスポートに関するアクセス許可に関係なく、構成されているストレージ アカウントを変更するには書き込みアクセス許可が必要です。
- BLOB またはファイル ストレージに対して Azure ストレージ アカウントを構成する必要があります。

新しいサブスクリプションをご利用の場合、すぐには Cost Management 機能を使用できません。 すべての Cost Management 機能を使用できるようになるまでに、最大 48 時間かかる場合があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。

## <a name="create-a-daily-export"></a>毎日のエクスポートを作成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

データ エクスポートを作成または表示する場合、またはエクスポートをスケジュールする場合は、Azure portal で目的のスコープを開き、メニューで **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 [コスト分析] ページの上部で、 **[設定]** 、 **[エクスポート]** の順に選択します。

> [!NOTE]
> - サブスクリプション以外にも、リソース グループ、管理グループ、部門、および登録に対してエクスポートを作成できます。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。
>- 課金アカウント スコープまたは顧客のテナントでパートナーとしてサインインしている場合は、パートナーのストレージ アカウントにリンクされている Azure Storage アカウントにデータをエクスポートすることができます。 ただし、CSP テナントにアクティブなサブスクリプションが必要です。

1. **[追加]** を選択し、エクスポートの名前を入力します。
1. **[メトリック]** で、次のいずれかを選択します。
    - **[Actual cost (Usage and Purchases)]\(実際のコスト (使用量と購入額)\)** - 標準の使用量と購入額をエクスポートする場合に選択します
    - **[Amortized cost (Usage and Purchases)]\(分散コスト (使用量と購入額)\)** - 購入の分散コスト (Azure の予約など) をエクスポートする場合に選択します
1. **[エクスポートの種類]** で、次のいずれかを選択します。
    - **[月度累計コストの日単位のエクスポート]** - 月度累計コストに関する新しいエクスポート ファイルが毎日提供されます。 最新のデータは、以前の毎日のエクスポートから集計されます。
    - **[Weekly export of cost for the last 7 days]\(過去 7 日間のコストに関する週単位のエクスポート\)** - 選択した開始日からさかのぼって 7 日間のコストに関するエクスポートが週単位で作成されます。
    - **[Monthly export of last month's costs]\(先月のコストに関する月単位のエクスポート\)** - エクスポートを作成している月の前月のコストに関するエクスポートが提供されます。 それ以降は、スケジュールによって、毎月 5 日に前月分のコストを使用してエクスポートが実行されます。
    - **[One-time export]\(1 回限りのエクスポート\)** - 履歴データの日付範囲を選択して、Azure Blob Storage にエクスポートすることができます。 選択した日付から最大 90 日間の履歴コストをエクスポートできます。 このエクスポートはすぐに実行され、2 時間以内にストレージ アカウント内で利用できます。
        エクスポートの種類に応じて、開始日を選択するか、 **[開始日]** と **[To]\(終了日\)** の日付を選択します。
1. Azure ストレージ アカウントのサブスクリプションを指定し、お使いのリソース グループを選択するか、新しいリソース グループを作成します。
1. ストレージ アカウント名を選択するか、新しいアカウントを作成します。
1. 場所 (Azure リージョン) を選択します。
1. エクスポート ファイルの保存先となるストレージ コンテナーとディレクトリ パスを指定します。
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="新しいエクスポートの例" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. エクスポートの詳細を確認して **[作成]** を選択します。

新しいエクスポートがエクスポートの一覧に表示されます。 既定では、新しいエクスポートが有効になっています。 スケジュールされたエクスポートを無効にするか、削除する場合、一覧にあるエクスポートを選択し、 **[無効化]** または **[削除]** を選択します。

最初は、エクスポートが実行されるまで 12 時間から 24 時間かかることがあります。 ただし、エクスポートされたファイルにデータが表示されるまでに、さらに時間がかかる場合もあります。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. サインイン後、現在のエクスポートを表示するには、[az costmanagement export list](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_list) コマンドを使用します。

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* サブスクリプション以外にも、リソース グループおよび管理グループに対してエクスポートを作成できます。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。
   >* 課金アカウント スコープまたは顧客のテナントでパートナーとしてサインインしている場合は、パートナーのストレージ アカウントにリンクされている Azure Storage アカウントにデータをエクスポートすることができます。 ただし、CSP テナントにアクティブなサブスクリプションが必要です。

1. リソース グループを作成するか、または既存のリソース グループを使用します。 リソース グループを作成するには、[az group create](/cli/azure/group#az_group_create) コマンドを使用します。

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. エクスポートを受信するためのストレージ アカウントを作成するか、既存のストレージ アカウントを使用します。 ストレージ アカウントを作成するには、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用します。

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create) コマンドを実行して、エクスポートを作成します。

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   **--type** パラメーターには、`ActualCost`、`AmortizedCost`、`Usage` のいずれかを選択できます。

   この例では、`MonthToDate` を使用します。 この場合、月度累計コストに関するエクスポート ファイルが毎日作成されます。 最新のデータは、当月における日単位のエクスポートをさかのぼって集計されます。

1. エクスポート操作の詳細を確認するには、[az costmanagement export show](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_show) コマンドを使用します。

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. [az costmanagement export update](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_update) コマンドを使用してエクスポートを更新します。

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   この例では、出力ディレクトリを変更します。

>[!NOTE]
>最初は、エクスポートが実行されるまで 12 時間から 24 時間かかることがあります。 ただし、エクスポートされたファイルにデータが表示されるまでに、さらに時間がかかる場合もあります。

エクスポートは、[az costmanagement export delete](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_delete) コマンドを使用して削除できます。

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

まず、Azure PowerShell の環境を準備します。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.CostManagement** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを別途インストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. サインイン後、現在のエクスポートを表示するには、[Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport) コマンドレットを使用します。

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* サブスクリプション以外にも、リソース グループおよび管理グループに対してエクスポートを作成できます。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。
   >* 課金アカウント スコープまたは顧客のテナントでパートナーとしてサインインしている場合は、パートナーのストレージ アカウントにリンクされている Azure Storage アカウントにデータをエクスポートすることができます。 ただし、CSP テナントにアクティブなサブスクリプションが必要です。

1. リソース グループを作成するか、または既存のリソース グループを使用します。 リソース グループを作成するには、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用します。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. エクスポートを受信するためのストレージ アカウントを作成するか、既存のストレージ アカウントを使用します。 ストレージ アカウントを作成するには、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットを使用します。

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) コマンドレットを実行して、エクスポートを作成します。

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   **DefinitionType** パラメーターには、`ActualCost`、`AmortizedCost`、`Usage` のいずれかを選択できます。

   この例では、`MonthToDate` を使用します。 この場合、月度累計コストに関するエクスポート ファイルが毎日作成されます。 最新のデータは、当月における日単位のエクスポートをさかのぼって集計されます。

1. エクスポート操作の詳細を表示するには、`Get-AzCostManagementExport` コマンドレットを使用します。

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) コマンドレットを使用して、エクスポートを更新します。

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   この例では、出力ディレクトリを変更します。

>[!NOTE]
>最初は、エクスポートが実行されるまで 12 時間から 24 時間かかることがあります。 ただし、エクスポートされたファイルにデータが表示されるまでに、さらに時間がかかる場合もあります。

[Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) コマンドレットを使用して、エクスポートを削除できます。

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>エクスポートのスケジュール

スケジュールされたエクスポートは、エクスポートを最初に作成したときの時刻と曜日の影響を受けます。 スケジュールされたエクスポートを作成すると、それ以降、毎回同じ頻度でエクスポートが実行されます。 たとえば、1 日 1 回の頻度に設定されている "月度累計コストの日単位のエクスポート" の場合、エクスポートが毎日実行されます。 同様に、週単位のエクスポートでは、スケジュールに従って毎週同じ曜日にエクスポートが実行されます。 エクスポートの正確な配信時間は保証されておらず、エクスポートされたデータは実行後 4 時間以内に利用できるようになります。

エクスポートごとに新しいファイルが作成されるため、古いエクスポートは上書きされません。

#### <a name="create-an-export-for-multiple-subscriptions"></a>複数のサブスクリプションのエクスポートを作成する

マイクロソフト エンタープライズ契約をご利用の場合は、管理グループを使用して、サブスクリプションのコスト情報を 1 つのコンテナーに集約できます。 そのうえで、管理グループのコスト管理データをエクスポートできます。

他の種類のサブスクリプションでは、管理グループのエクスポートはサポートされません。

1. まだ管理グループを作成していない場合は作成し、そのグループにサブスクリプションを割り当てます。
1. コスト分析で、管理グループにスコープを設定し、 **[Select this management group]\(この管理グループを選択する\)** を選択します。
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="[Select this management group]\(この管理グループを選択する\) オプションを示す例" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. そのスコープでエクスポートを作成し、管理グループに含まれるサブスクリプションのコスト管理データを取得します。
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="新しいエクスポートの作成オプションを示す例。スコープとして管理グループが設定されています":::

## <a name="verify-that-data-is-collected"></a>データが収集されたことを確認する

コスト管理データが収集されていることは簡単に確認できます。また、Azure Storage Explorer を利用し、エクスポートした CSV データを簡単に表示できます。

エクスポートの一覧で、ストレージ アカウント名を選択します。 ストレージ アカウント ページで、Explorer の [開く] を選択します。 確認ボックスが表示されたら、 **[はい]** を選択し、Azure Storage Explorer でファイルを開きます。

![情報の例と、[Explorer で開く] へのリンクを示すストレージ アカウント ページ](./media/tutorial-export-acm-data/storage-account-page.png)

Storage Explorer で、開くコンテナーに移動し、当月のフォルダーを選択します。 CSV ファイルの一覧が表示されます。 1 つを選択して、 **[開く]** を選択します。

![ストレージ エクスプローラーに表示される情報の例](./media/tutorial-export-acm-data/storage-explorer.png)

ファイルは、CSV ファイル拡張子を開くように設定されているプログラムまたはアプリケーションで開きます。 今回の例では Excel が使用されます。

![Excel に表示されるエクスポートした CSV データの例](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>エクスポートされた CSV データ ファイルをダウンロードする

エクスポートされた CSV ファイルを Azure portal からダウンロードすることもできます。 次の手順では、それを [コスト分析] から探す方法を説明しています。

1. [コスト分析] で **[設定]** を選択し、 **[エクスポート]** を選択します。
1. エクスポートの一覧から、エクスポートに使用するストレージ アカウントを選択します。
1. ストレージ アカウントの **[コンテナー]** を選択します。
1. コンテナーの一覧で、コンテナーを選択します。
1. ディレクトリとストレージ BLOB を辿って目的の日付まで移動します。
1. CSV ファイルを選択して **[ダウンロード]** を選択します。

[![エクスポートのダウンロード例](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>エクスポートの実行履歴を表示する

エクスポートの一覧ページで個々のエクスポートを選択することによって、スケジュールされたエクスポートの実行履歴を表示できます。 エクスポートの一覧ページでは、前回のエクスポートの実行日時と、次回のエクスポートの実行日時をすばやく確認することもできます。 実行履歴の表示例を次に示します。

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="[エクスポート] ウィンドウを示すスクリーンショット。":::

実行履歴を表示するエクスポートを選択します。

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="エクスポートの実行履歴を示すスクリーンショット。":::

## <a name="access-exported-data-from-other-systems"></a>エクスポートしたデータに他のシステムからアクセスする

コスト管理データをエクスポートする目的の 1 つは、外部システムからそれにアクセスすることにあります。 ダッシュボード システムやその他の金融システムを使用することがあります。 そのようなシステムは幅広く存在し、例を示すことは実際的ではありません。  それでも、[Azure Storage の概要](../../storage/common/storage-introduction.md)に関するページで、自分のアプリケーションからデータにアクセスすることから始めることはできます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 毎日のエクスポートを作成する
> * データが収集されたことを確認する

次のチュートリアルに進み、活動休止状態のリソースや十分に活用されていないリソースを特定することで効率性を最適化し、改善してください。

> [!div class="nextstepaction"]
> [最適化に関する推奨事項を確認して対処する](tutorial-acm-opt-recommendations.md)
