---
title: Azure アクティビティ ログをエクスポートする
description: Azure アクティビティ ログをストレージにエクスポートして Azure Event Hubs をアーカイブし、Azure の外部にエクスポートします。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 0e5780561df121d3d5af3a9b754d774cc7d6cf76
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969655"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Azure アクティビティ ログをストレージまたは Azure Event Hubs にエクスポートする

> [!WARNING]
> リソース ログの収集方法と同様に、診断設定を使用してアクティビティ ログを Log Analytics ワークスペースに収集できるようになりました。 「[Collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor (Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する)](diagnostic-settings-legacy.md)」を参照してください。

[Azure アクティビティ ログ](platform-logs-overview.md)は、Azure サブスクリプションで発生したサブスクリプションレベルのイベントを分析します。 Azure portal でアクティビティ ログを表示したり、これを Azure Monitor によって収集された他のデータで分析できる Log Analytics ワークスペースにコピーしたりするだけでなく、ログ プロファイルを作成してアクティビティ ログを Azure ストレージ アカウントにアーカイブしたり、これをイベント ハブにストリーミングしたりすることができます。

## <a name="archive-activity-log"></a>アクティビティ ログをアーカイブする
ストレージ アカウントへのアクティビティ ログのアーカイブは、監査、静的分析、またはバックアップのために (保持ポリシーを完全に制御して) 90 日よりも長くログ データを保持する場合に便利です。 90 日以内でイベントを保持する必要があるだけの場合は、ストレージ アカウントにアーカイブを設定する必要はありません。アクティビティ ログのイベントは Azure プラットフォームに 90 日間保持されるためです。

## <a name="stream-activity-log-to-event-hub"></a>アクティビティ ログをイベント ハブにストリーミングする
[Azure Event Hubs](/azure/event-hubs/) はデータ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 アクティビティ ログのストリーミング機能は、次の 2 つの方法で使用できます。
* **サード パーティのログおよびテレメトリ システムにストリーミングする**:将来的に、Azure Event Hubs のストリーミングは、アクティビティ ログをサード パーティの SIEM やログ分析ソリューションにパイプ処理するためのメカニズムになります。
* **カスタムのテレメトリおよびログ プラットフォームを構築する**:カスタム構築されたテレメトリ プラットフォームが既にある場合や構築を検討している場合は、Event Hubs の非常にスケーラブルな発行/サブスクライブの特性により、アクティビティ ログを柔軟に取り込むことができます。

## <a name="prerequisites"></a>前提条件

### <a name="storage-account"></a>ストレージ アカウント
アクティビティ ログをアーカイブしていて、まだストレージ アカウントを持っていない場合は、[作成する](../../storage/common/storage-account-create.md)必要があります。 監視データへのアクセスをさらに制御するために他の非監視データが格納されている、既存のストレージ アカウントは使用しないでください。 ただし、ログとメトリックもストレージ アカウントにアーカイブする場合は、中央の場所にすべての監視データを保持するために、同じアカウントを使用することができます。

設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、ストレージ アカウントは、ログを出力するのと同じサブスクリプションに属している必要はありません。
> [!NOTE]
>  現在、セキュリティで保護された仮想ネットワークの背後にあるストレージ アカウントにデータをアーカイブすることはできません。

### <a name="event-hubs"></a>Event Hubs
アクティビティ ログをイベント ハブに送信していて、まだイベント ハブを持っていない場合は、[作成する](../../event-hubs/event-hubs-create.md)必要があります。 この Event Hubs 名前空間にアクティビティ ログ イベントをストリーミングしたことがある場合は、そのイベント ハブが再利用されます。

共有アクセス ポリシーでは、ストリーミング メカニズムのアクセス許可が定義されます。 Event Hubs にストリーミングするには、管理、送信、リッスンの各アクセス許可が必要です。 Event Hubs 名前空間の共有アクセス ポリシーは、Azure portal の Event Hubs 名前空間の [構成] タブで作成または変更できます。

アクティビティ ログのログ プロファイルにストリーミングを含めるよう更新するには、その Event Hubs の承認規則に対する ListKey アクセス許可が必要です。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っており、さらに両方のサブスクリプションが同じ AAD テナントに入っている限り、Event Hubs 名前空間は、ログを出力するサブスクリプションと同じサブスクリプションに属している必要はありません。

[ログ プロファイルを作成](#create-a-log-profile)して、アクティビティ ログをイベント ハブにストリーミングします。

## <a name="create-a-log-profile"></a>ログ プロファイルを作成する
**ログ プロファイル**を使用して Azure アクティビティ ログをエクスポートする方法を定義します。 1 つの Azure サブスクリプションで使用できるログ プロファイルは 1 つだけです。 これらの設定は、portal の [アクティビティ ログ] ブレードの **[エクスポート]** オプションで構成できます。 さらに、[Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell コマンドレット、または CLI を使えば、プログラムを使って構成することもできます。

ログ プロファイルでは、次の項目が定義されます。

**アクティビティ ログの送信先。** 現時点で使用可能なオプションは、ストレージ アカウントまたは Event Hubs です。

**送信するイベント カテゴリ。** ログ プロファイルでの*カテゴリ*の意味は、アクティビティ ログ イベントでの意味とは異なります。 ログ プロファイルでの*カテゴリ*は、操作の種類 (Write、Delete、Action) を指します。 アクティビティ ログ イベントでの*カテゴリ** プロパティは、イベントのソースまたは種類 (Administration、ServiceHealth、Alert など) を指します。

**エクスポートするリージョン (場所)。** アクティビティ ログのイベントの多くはグローバル イベントなので、すべての場所を含める必要があります。

**アクティビティ ログをストレージ アカウントに保持する期間。** リテンション期間が 0 日の場合、ログは永続的に保持されます。 または、1 日から 365 日の間の任意の日数を値として指定できます。

リテンション ポリシーが設定されていても、ストレージ アカウントへのログの保存は無効になっている場合、保持ポリシーへの影響はありません。 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。 削除プロセスは午前 0 時 (UTC) に開始されますが、ストレージ アカウントからのログの削除には最大で 24 時間かかる可能性があるので注意してください。


> [!IMPORTANT]
> Microsoft.Insights リソースプロバイダーが登録されていない場合、ログ プロファイルを作成するときにエラーが発生することがあります。 このプロバイダーの登録については、「[Azure リソースプロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。


### <a name="create-log-profile-using-the-azure-portal"></a>Azure portal を使用してログ プロファイルを作成する

Azure portal の **[イベント ハブにエクスポート]** オプションを使用してログ プロファイルを作成または編集します。

1. Azure portal の **[モニター]** メニューから、 **[イベント ハブにエクスポート]** を選択します。

    ![ポータルの [エクスポート] ボタン](media/activity-log-export/portal-export.png)

3. ブレードが表示されたら、次のように指定します。
   * エクスポートするイベントが含まれるリージョン。 アクティビティ ログはグローバルな (リージョン別ではない) ログであり、ほとんどのイベントがリージョンと関連付けられていないため、すべてのリージョンを選択して重要なイベントを確実に見逃さないようにする必要があります。
   * ストレージ アカウントに書き込む場合:
       * イベントの保存先となるストレージ アカウント。
       * ストレージでイベントを保持する日数。 日数を 0 にした場合には、ログが永久に保持されます。
   * イベント ハブに書き込む場合:
       * イベントのストリーミング用にイベント ハブを作成する Service Bus 名前空間。

     ![[Export Activity Log (アクティビティ ログのエクスポート)] ブレード](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. **[保存]** をクリックして設定を保存します。 設定はサブスクリプションにすぐに適用されます。


### <a name="configure-log-profile-using-powershell"></a>PowerShell を使用してログ プロファイルを構成する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

ログ プロファイルが既に存在する場合は、最初に既存のログ プロファイルを削除してから、新しいものを作成する必要があります。

1. `Get-AzLogProfile` を使用して、ログ プロファイルが存在するかどうかを確認します。  ログ プロファイルが存在する場合は、*name* プロパティを探します。

1. `Remove-AzLogProfile` を使用し、*name* プロパティの値を使用してログ プロファイルを削除します。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. `Add-AzLogProfile` を使用して、新しいログ プロファイルを作成します。

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | プロパティ | 必須 | [説明] |
    | --- | --- | --- |
    | Name |はい |ログ プロファイルの名前。 |
    | StorageAccountId |いいえ |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
    | serviceBusRuleId |いいえ |Event Hubs を作成する Service Bus 名前空間の Service Bus 規則 ID。 文字列の形式は `{service bus resource ID}/authorizationrules/{key name}` になります。 |
    | Location |はい |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 |
    | RetentionInDays |はい |ストレージ アカウントにイベントを保持する日数 (1 から 365 の範囲)。 値が 0 の場合、ログは無期限に保存されます。 |
    | カテゴリ |いいえ |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、_Write_、_Delete_、_Action_ です。 |

### <a name="example-script"></a>サンプル スクリプト
ストレージ アカウントとイベント ハブの両方にアクティビティ ログを書き込むログ プロファイルを作成する PowerShell スクリプトのサンプルを次に示します。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Azure CLI を使用してログ プロファイルを構成する

ログ プロファイルが既に存在する場合は、最初に既存のログ プロファイルを削除してから、新しいログ プロファイルを作成する必要があります。

1. `az monitor log-profiles list` を使用して、ログ プロファイルが存在するかどうかを確認します。
2. `az monitor log-profiles delete --name "<log profile name>` を使用し、*name* プロパティの値を使用してログ プロファイルを削除します。
3. `az monitor log-profiles create` を使用して、新しいログ プロファイルを作成します。

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | プロパティ | 必須 | [説明] |
    | --- | --- | --- |
    | name |はい |ログ プロファイルの名前。 |
    | storage-account-id |はい |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
    | locations |はい |アクティビティ ログ イベントを収集するリージョンのスペース区切りリスト。 `az account list-locations --query [].name` を使って、サブスクリプションのすべてのリージョンの一覧を見ることができます。 |
    | days |はい |イベントを保持する日数。1 -365 の範囲。 値が 0 の場合、ログは無期限に (いつまでも) 保存されます。  0 の場合は、enabled パラメーターを false に設定する必要があります。 |
    |enabled | はい |True または False。  アイテム保持ポリシーを有効または無効にするために使います。  True の場合は、days パラメーターを 0 より大きい値にする必要があります。
    | categories |はい |収集するイベント カテゴリのスペース区切りリスト。 指定できる値は、Write、Delete、Action です。 |



## <a name="next-steps"></a>次のステップ

* [アクティビティ ログについて詳しく学習します](../../azure-resource-manager/management/view-activity-logs.md)
* [Azure Monitor ログにアクティビティ ログを収集する](activity-log-collect.md)
