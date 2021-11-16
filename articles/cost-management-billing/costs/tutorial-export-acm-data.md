---
title: チュートリアル - Azure Cost Management からデータをエクスポートし、管理する
description: この記事では、外部システムで使用できるように Cost Management データをエクスポートし、管理する方法を紹介します。
author: bandersmsft
ms.author: banders
ms.date: 11/03/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: fdefbf1de5e61d05379dd0cfce07a30038dea4f0
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502808"
---
# <a name="tutorial-create-and-manage-exported-data"></a>チュートリアル:データをエクスポートし、管理する

コスト分析のチュートリアルをお読みになっていれば、コスト管理データを手動でダウンロードできます。 その一方で、コスト管理データを Azure Storage に対して毎日、毎週、または毎月、自動的にエクスポートする定期タスクを作成することができます。 エクスポートしたデータは CSV 形式になり、コスト管理で収集したすべての情報がそれに含まれます。 エクスポート後は、Azure Storage に格納されたそのデータを外部システムで利用したり、独自のデータと組み合わせたりできます。 また、エクスポートしたデータを、ダッシュボードやその他の金融システムのような外部システムで利用できます。

Azure のコスト データを Azure Storage にエクスポートするスケジュールの作成については、[Cost Management でストレージへのエクスポートをスケジュールする方法](https://www.youtube.com/watch?v=rWa_xI1aRzo)に関するビデオをご覧ください。 他の動画を視聴するには、[Cost Management の YouTube チャンネル](https://www.youtube.com/c/AzureCostManagement)にアクセスしてください。

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

このチュートリアルの例では、段階的にコスト管理データをエクスポートし、データが正常にエクスポートされたことを確認します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 毎日のエクスポートを作成する
> * データが収集されたことを確認する

## <a name="prerequisites"></a>前提条件

データのエクスポートは、[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) や [Microsoft 顧客契約](get-started-partners.md)のお客様など、さまざまな Azure アカウントの種類で使用できます。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](understand-cost-mgt-data.md)」を参照してください。 ユーザーおよびグループによるデータのエクスポートについては、サブスクリプションに従い、次の Azure のアクセス許可、またはスコープがサポートされています。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。

- 所有者 - サブスクリプションのスケジュールされたエクスポートを作成、変更、または削除できます。
- 共同作成者 - スケジュールされたエクスポートを作成、変更、または削除できます。 他のユーザーが作成したスケジュールされたエクスポートの名前を変更できます。
- 閲覧者 - アクセス許可を持っているエクスポートをスケジュールできます。

**Enterprise Agreement および Microsoft 顧客契約のスコープのエクスポートを構成するために必要なアクセス権など、スコープの詳細については、「[スコープを理解して使用する](understand-work-scopes.md)」を参照してください。**

Azure Storage アカウントの場合:
- エクスポートに関するアクセス許可に関係なく、構成されているストレージ アカウントを変更するには書き込みアクセス許可が必要です。
- BLOB またはファイル ストレージに対して Azure ストレージ アカウントを構成する必要があります。
- このストレージ アカウントに、ファイアウォールを構成することはできません。

新しいサブスクリプションをご利用の場合、すぐには Cost Management 機能を使用できません。 すべての Cost Management 機能を使用できるようになるまでに、最大 48 時間かかる場合があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。

## <a name="create-a-daily-export"></a>毎日のエクスポートを作成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

データ エクスポートを作成または表示する場合、またはエクスポートをスケジュールする場合は、Azure portal でスコープを選択し、メニューで **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 [コスト分析] ページの上部で、 **[設定]** 、 **[エクスポート]** の順に選択します。

> [!NOTE]
> - サブスクリプション以外にも、リソース グループ、管理グループ、部門、および登録に対してエクスポートを作成できます。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。
> - 課金アカウント スコープまたは顧客のテナントでパートナーとしてサインインしている場合は、パートナーのストレージ アカウントにリンクされている Azure Storage アカウントにデータをエクスポートすることができます。 ただし、CSP テナントにアクティブなサブスクリプションが必要です。

1. **[追加]** を選択し、エクスポートの名前を入力します。
1. **[メトリック]** で、次のいずれかを選択します。
    - **[Actual cost (Usage and Purchases)]\(実際のコスト (使用量と購入額)\)** - 標準の使用量と購入額をエクスポートする場合に選択します
    - **[Amortized cost (Usage and Purchases)]\(分散コスト (使用量と購入額)\)** - 購入の分散コスト (Azure の予約など) をエクスポートする場合に選択します
1. **[エクスポートの種類]** で、次のいずれかを選択します。
    - **[月度累計コストの日単位のエクスポート]** - 月度累計コストに関する新しいエクスポート ファイルが毎日提供されます。 最新のデータは、以前の毎日のエクスポートから集計されます。
    - **[過去 7 日間のコストの週単位のエクスポート]** - 選択したエクスポートの開始日からさかのぼって 7 日間のコストに関するエクスポートが週単位で作成されます。
    - **[先月のコストに関する月単位のエクスポート]** - エクスポートを作成している月の前月のコストに関するエクスポートが提供されます。 それ以降は、スケジュールによって、毎月 5 日に前月分のコストを使用してエクスポートが実行されます。
    - **[ワンタイム エクスポート]** - 履歴データの日付範囲を選択して、Azure Blob Storage にエクスポートすることができます。 選択した日付から最大 90 日間の履歴コストをエクスポートできます。 このエクスポートはすぐに実行され、2 時間以内にストレージ アカウント内で利用できます。
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

プログラムによってエクスポートを作成する場合は、ストレージ アカウントが存在するサブスクリプションに `Microsoft.CostManagementExports` リソース プロバイダーを手動で登録する必要があります。 Azure portal を使用してエクスポートを作成すると、自動的に登録が行われます。 リソース プロバイダーの登録方法の詳細については、「[リソース プロバイダーの登録](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)」を参照してください。

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. サインイン後、現在のエクスポートを表示するには、[az costmanagement export list](/cli/azure/costmanagement/export#az_costmanagement_export_list) コマンドを使用します。

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

1. [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) コマンドを実行して、エクスポートを作成します。

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   **--type** パラメーターには、`ActualCost`、`AmortizedCost`、`Usage` のいずれかを選択できます。

   この例では、`MonthToDate` を使用します。 この場合、月度累計コストに関するエクスポート ファイルが毎日作成されます。 最新のデータは、当月における日単位のエクスポートをさかのぼって集計されます。

1. エクスポート操作の詳細を確認するには、[az costmanagement export show](/cli/azure/costmanagement/export#az_costmanagement_export_show) コマンドを使用します。

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. [az costmanagement export update](/cli/azure/costmanagement/export#az_costmanagement_export_update) コマンドを使用してエクスポートを更新します。

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   この例では、出力ディレクトリを変更します。

>[!NOTE]
>最初は、エクスポートが実行されるまで 12 時間から 24 時間かかることがあります。 ただし、エクスポートされたファイルにデータが表示されるまでに、さらに時間がかかる場合もあります。

エクスポートは、[az costmanagement export delete](/cli/azure/costmanagement/export#az_costmanagement_export_delete) コマンドを使用して削除できます。

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

プログラムによってエクスポートを作成する場合は、ストレージ アカウントが存在するサブスクリプションに `Microsoft.CostManagementExports` リソース プロバイダーを手動で登録する必要があります。 Azure portal を使用してエクスポートを作成すると、自動的に登録が行われます。 リソース プロバイダーの登録方法の詳細については、「[リソース プロバイダーの登録](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)」を参照してください。

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

マイクロソフト エンタープライズ契約をご利用の場合は、管理グループを使用して、サブスクリプションのコスト情報を 1 つのコンテナーに集約できます。 そのうえで、管理グループのコスト管理データをエクスポートできます。 管理グループのエクスポートでは、実際のコストのみがサポートされます。

他の種類のサブスクリプションでは、管理グループのエクスポートはサポートされません。

1. まだ管理グループを作成していない場合は、グループを作成し、それにサブスクリプションを割り当てます。
1. コスト分析で、管理グループにスコープを設定し、 **[Select this management group]\(この管理グループを選択する\)** を選択します。
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="[Select this management group]\(この管理グループを選択する\) オプションを示す例" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. そのスコープでエクスポートを作成し、管理グループに含まれるサブスクリプションのコスト管理データを取得します。
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="新しいエクスポートの作成オプションを示す例。スコープとして管理グループが設定されています":::

### <a name="file-partitioning-for-large-datasets"></a>大規模なデータセットのファイルのパーティション分割

Microsoft 顧客契約、Microsoft Partner Agreement、Enterprise Agreement のいずれかを結んでいる場合は、エクスポートを使用して、ファイルを複数の小さなファイル パーティションに分割し、データ インジェストに役立てることができます。 最初にエクスポートを構成するときに、 **[File Partitioning]\(ファイルのパーティション分割\)** 設定を **[オン]** に設定します。 既定では、この設定は **[オフ]** です。

:::image type="content" source="./media/tutorial-export-acm-data/file-partition.png" alt-text="[File Partitioning]\(ファイルのパーティション分割\) オプションを示すスクリーンショット。" lightbox="./media/tutorial-export-acm-data/file-partition.png" :::

Microsoft 顧客契約、Microsoft Partner Agreement、Enterprise Agreement のいずれも結んでいない場合、 **[File Partitioning]\(ファイルのパーティション分割\)** オプションは表示されません。

#### <a name="update-existing-exports-to-use-file-partitioning"></a>ファイルのパーティション分割を使用するよう既存のエクスポートを更新する

既存のエクスポートがあり、ファイルのパーティション分割を設定する場合は、新しいエクスポートを作成します。 ファイルのパーティション分割は、エクスポートの最新バージョンでのみ使用できます。 作成される使用状況ファイルの一部のフィールドに軽微な変更が加えられている場合があります。

既存のエクスポートでファイルのパーティション分割を有効にすると、ファイル出力のフィールドに対して軽微な変更が加えられる場合があります。 変更はすべて、最初に設定した後にエクスポートに加えられた更新に起因します。

#### <a name="partitioning-output"></a>パーティション分割の出力

ファイルのパーティション分割を有効にした場合、エクスポートのデータの各パーティションのファイルと _manifest.json ファイルが作成されます。 マニフェストには、完全なデータセットの概要と、その中の各ファイル パーティションに関する情報が含まれています。 各ファイル パーティションにはヘッダーがあり、完全なデータセットのサブセットのみが含まれています。 完全なデータセットを処理するには、エクスポートの各パーティションを取り込む必要があります。

マニフェスト ファイルの _manifest.json の例を示します。

```json
{
  "manifestVersion": "2021-01-01",
  "dataFormat": "csv",
  "blobCount": 1,
  "byteCount": 160769,
  "dataRowCount": 136,
  "blobs": [
    {
      "blobName": "blobName.csv",
      "byteCount": 160769,
      "dataRowCount": 136,
      "headerRowCount": 1,
      "contentMD5": "md5Hash"
    }
  ]
}
```

### <a name="export-versions"></a>エクスポート バージョン

スケジュールされたエクスポートを Azure portal で、または API を使用して作成すると、作成時に使用されるエクスポート バージョンで常に実行されます。 Azure では、以前に作成したエクスポートが、更新しない限り、同じバージョンで保持されます。 これにより、エクスポート バージョンが変更された場合に、料金と CSV フィールドの変更が防止されます。 エクスポート機能が時間の経過に伴い変化すると、フィールド名が変更され、新しいフィールドが追加される場合があります。

使用可能な最新のデータとフィールドを使用したい場合は、Azure portal で新しいエクスポートを作成することをお勧します。 既存のエクスポートを最新バージョンに更新するには、Azure portal または最新の Export API バージョンを使用して更新します。 既存のエクスポートを更新すると、その後生成されるファイルのフィールドと料金にわずかな違いが生じる可能性があります。


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
