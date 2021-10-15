---
title: Azure Monitor でアクティビティ ログ アラートを作成、表示、管理する
description: Azure portal、Azure Resource Manager テンプレート、および Azure PowerShell を使用してアクティビティ ログ アラートを作成します。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 08/12/2021
ms.openlocfilehash: 5cd113692cfe0c024980cb95252acf9f7763a2eb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360148"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する  

"*アクティビティ ログ アラート*" は、アラートで指定した条件に一致するアクティビティ ログ イベントが新たに発生したときにアクティブになるアラートです。 このアラートは、Azure Resource Manager テンプレートを使用して、Azure リソースに対して作成します。 このアラートは、Azure portal で作成、更新、または削除することもできます。

通常は、Azure サブスクリプション内のリソースに特定の変更が発生した場合に通知を受け取るためにアクティビティ ログ アラートを作成します。 アラートは、多くの場合、特定のリソース グループまたはリソースにスコープ指定されます。 たとえば、サンプル リソース グループ `myProductionResourceGroup` 内の仮想マシンが削除されたときに通知を受け取ることができます。 または、サブスクリプション内のユーザーに新しいロールが割り当てられた場合に通知を受け取ることもできます。

> [!IMPORTANT]
> アクティビティ ログ アラートを作成するインターフェイスを使用して、サービス正常性通知に対するアラートを作成することはできません。 サービス正常性通知の作成および使用の詳細については、[サービス正常性通知のアクティビティ ログ アラートの受け取り](../../service-health/alerts-activity-log-service-notifications-portal.md)に関する記事をご覧ください。

アラート ルールを作成するときは、次のことを確認してください。

- スコープ内のサブスクリプションが、アラートが作成されているサブスクリプションと同じである。
- 条件は、アラートが構成されているレベル/状態/呼び出し元/リソース グループ/リソース ID/リソースの種類/イベント カテゴリでなければならない。
- アラート構成 JSON に、`anyOf` 条件または入れ子になった条件がない。 1 つの `allOf` 条件のみ許可され、追加の `allOf` または `anyOf` 条件は指定できない。
- カテゴリが `administrative` の場合、アラートに上記の条件を 1 つ以上指定する必要がある。 アクティビティ ログ内にイベントが作成されるたびにアクティブにするアラートを作成することはできません。
- アクティビティ ログの `alert` カテゴリに含まれるイベントに対してアラートを作成することはできない。

## <a name="azure-portal"></a>Azure portal

Azure portal を使用して、アクティビティ ログ アラート ルールを作成および変更できます。 このエクスペリエンスは Azure アクティビティ ログに統合され、対象とする特定のイベントに対するシームレスなアラート作成が保証されます。 Azure portal で、新しいアクティビティ ログ アラート ルールを作成するには、Azure Monitor のアラート ペインから、または Azure Monitor アクティビティ ログ ペインから作成できます。 

### <a name="create-an-alert-rule-from-the-azure-monitor-alerts-pane"></a>アラート ルールを Azure Monitor アラート ペインから作成する

次の手順で、Azure portal で、アクティビティ ログ アラート ルールを作成します。

1. [Azure Portal](https://portal.azure.com) で、 **[モニター]** を選択します。 [モニター] ウィンドウでは、すべての監視設定とデータが 1 つのビューにまとめられています。

2. **[アラート]**  >  **[+ 新しいアラート ルール]** を選択します。

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-button-new.png" alt-text="[+ 新しいアラート ルール] ボタンが表示されているスクリーンショット。":::
    > [!TIP]
    > また、ほとんどのリソース ペインには、 **[監視]** のリソース メニューに **[アラート]** があります。 そこからアラートを作成することもできます。

3. **[ターゲットの選択]** を選択し、アラートを作成するターゲット リソースを選択します。 監視するリソースを検索するには、 **[サブスクリプション]** と **[リソースの種類]** のリストを使用します。 検索バーを使用して、リソースを検索することもできます。
    
    > [!NOTE]
    > ターゲットとして、サブスクリプション全体、リソース グループ、または特定のリソースを選択できます。 サブスクリプションまたはリソース グループをターゲットとして選択し、リソースの種類も選択した場合、ルールは、選択したサブスクリプションまたはリソース グループ内のその種類のすべてのリソースに適用されます。 特定のターゲット リソースを選択した場合、ルールは、そのリソースにのみ適用されます。 ターゲット セレクターを使用して、複数のサブスクリプション、リソース グループ、またはリソースを明示的に選択することはできません。 

4. 選択したリソースに、アラートを作成できるアクティビティ ログ操作があると、 **[利用可能なシグナルの種類]** に **[アクティビティ ログ]** 一覧が表示されます。 アクティビティ ログ アラートでサポートされているリソースの種類の完全な一覧については、「[Azure リソース プロバイダーの操作](../../role-based-access-control/resource-provider-operations.md)」をご覧ください。

    :::image type="content" source="media/alerts-activity-log/select-target-new.png" alt-text="ターゲット選択ペインのスクリーンショット。" lightbox="media/alerts-activity-log/select-target-new.png":::

5. ターゲット リソースを選択した後、 **[条件の追加]** を選択します。

6. リソースでサポートされているシグナルの一覧が表示されます。これには、**アクティビティ ログ** のさまざまなカテゴリのものが含まれます。 アラートを作成するアクティビティ ログのシグナルまたは操作を選択します。

7. 過去 6 時間のアクティビティ ログ操作のグラフが表示されます。 その操作の履歴の表示期間を長くするには、 **[グラフの期間]** ドロップダウン リストを使用します。

8. **[アラート ロジック]** で、必要に応じて、より多くのフィルター条件を定義できます。

    - **イベントのレベル**:イベントの重大度レベル: "_詳細_"、"_情報_"、"_警告_"、"_エラー_" または "_重大_"。
    - **状態**: イベントの状態: "_開始済み_"、"_失敗_" または "_成功_"。
    - **イベント開始者**: "呼び出し元" とも呼ばれます。 操作を実行したユーザーの電子メール アドレスまたは Azure Active Directory 識別子。

    > [!NOTE]
    > これらの条件の少なくとも 1 つを定義すると、より効果的なルールを実現できます。 たとえば、アラートのスコープがサブスクリプション全体であり、選択したシグナルが `All Administrative Operations` の場合、イベント レベル、状態、または開始情報を指定すると、ルールがより具体的になります。
        
9. **[Done]** を選択します。

    :::image type="content" source="media/alerts-activity-log/condition-selected-new.png" alt-text="条件の選択ペインのスクリーンショット。" lightbox="media/alerts-activity-log/condition-selected-new.png":::

10. **[アラート ルール名]** 、 **[説明]** 、 **[重大度]** など、アラートの詳細を指定します。

    > [!NOTE]
    > アクティビティ ログ アラートのアラート重大度は、現在、ユーザーが構成することはできません。 重大度レベルは常に既定の **Sev4** に設定されます。

11. 既存のアクション グループを選択するか、新しいアクション グループを作成して、アラートにアクション グループを追加します。

12. **[完了]** を選択して、アクティビティ ログ アラート ルールを保存します。
     
     
### <a name="create-an-alert-rule-from-the-azure-monitor-activity-log-pane"></a>アラート ルールを Azure Monitor アクティビティ ログ ペインから作成する

アクティビティ ログ アラートを作成する別の方法は、 [Azure portal のアクティビティ ログ](../essentials/activity-log.md#view-the-activity-log)を使用して、既に発生したアクティビティ ログ イベントから開始することです。 

1. **[Azure Monitor - アクティビティ ログ]** ペインで、目的のイベントをフィルター処理または検索し、次に **[アクティビティ ログ アラートの追加]** を選択して、将来の類似イベントに対するアラートを作成できます。 

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png" alt-text="アクティビティ ログ イベントからのアラート ルールの作成のスクリーンショット。" lightbox="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png":::

2. **[アラート ルールの作成]** ペインが開きます。アラート ルールのスコープと条件が、前に選択したアクティビティ ログ イベントに従って既に設定されています。 必要に応じて、この段階でスコープと条件を編集および変更できます。 既定では、新しいルールのスコープと条件は、元のイベント属性からコピーされます。 たとえば、既定では、イベントが発生したリソースはそのまま、イベントを開始した特定のユーザーまたはサービス名と共に、新しいアラート ルールに挿入されます。 アラート ルールをより一般的なものにするには、スコープと条件を適宜変更します (「アラート ルールを Azure Monitor アラート ペインから作成する」セクションの手順 3 から 9 を参照してください)。 

3. 次に、「アラート ルールを Azure Monitor アラート ペインから作成する」セクションの手順 10 から 12 に従います。
    
### <a name="view-and-manage-in-the-azure-portal"></a>Azure portal での表示と管理

1. Azure portal で、 **[モニター]**  >  **[アラート]** の順に選択します。 次に、 **[アラート ルールの管理]** を選択します。

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-button-new.png" alt-text="[アラート ルールの管理] ボタンのスクリーンショット。":::
    
    使用可能なルールの一覧が表示されます。

2. 変更するアクティビティ ログ ルールをフィルタリングまたは検索します。

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-new.png" alt-text="アラート ルール管理ペインのスクリーンショット。" lightbox="media/alerts-activity-log/manage-alert-rules-new.png":::

    編集するアクティビティのルールを検索するには、用意されているフィルターとして _[サブスクリプション]_ 、 _[リソース グループ]_ 、 _[リソース]_ 、 _[シグナルの種類]_ または _[状態]_ を使用できます。
 
3. ルールを選択し、ダブルクリックしてルールのオプションを編集します。 必要な変更を行い、 **[保存]** を選択します。 

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
Azure Resource Manager テンプレートを使用してアクティビティ ログ アラート ルールを作成するには、`microsoft.insights/activityLogAlerts` 型のリソースを作成します。 次に、関連するすべてのプロパティを入力します。 アクティビティ ログ アラート ルールを作成するテンプレートを以下に示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
前述のサンプル JSON は、たとえば *sampleActivityLogAlert.json* として保存できます。 サンプルは、[Azure portal で Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md) を使用してデプロイできます。

> [!NOTE]
> そのアクティビティ ログ アラートを定義できる最高レベルはサブスクリプション レベルであることに注意してください。 2 つのサブスクリプションに対してアラートを定義するオプションはありません。 定義は、サブスクリプションごとにアラートにする必要があります。

次のフィールドは、Azure Resource Manager テンプレートで条件フィールドに対して使用できるオプションです。 (**Resource Health**、**Advisor**、および **Service Health** には、特別なフィールドに対して追加のプロパティ フィールドがあることに注意してください)。 

1. `resourceId`: アラートを生成する必要があるアクティビティ ログ イベントの影響を受けるリソースのリソース ID。
1. `category`: アクティビティ ログ イベントのカテゴリ。 例: `Administrative`、`ServiceHealth`、`ResourceHealth`、`Autoscale`、`Security`、`Recommendation`、`Policy`。
1. `caller`: アクティビティ ログ イベントの操作を実行したユーザーのメール アドレスまたは Azure Active Directory 識別子。
1. `level`: アラートを生成する必要があるアクティビティ ログ イベントのアクティビティのレベル。 例: `Critical`、`Error`、`Warning`、`Informational`、`Verbose`。
1. `operationName`: アクティビティ ログ イベントの操作の名前。 (例: `Microsoft.Resources/deployments/write`)。
1. `resourceGroup`: アクティビティ ログ イベントの影響を受けるリソースのリソース グループの名前。
1. `resourceProvider`: 詳細については、「[Azure リソース プロバイダーと種類](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0)」を参照してください。 リソース プロバイダーを Azure サービスにマップされるリストについては、「[Resource providers for Azure services](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)」 (Azure サービスのリソースプロバイダー) を参照してください。
1. `status`: アクティビティ イベントの操作の状態を説明する文字列。 例: `Started`、`In Progress`、`Succeeded`、`Failed`、`Active`、`Resolved`。
1. `subStatus`: 通常、このフィールドは、対応する REST 呼び出しの HTTP 状態コードです。 ただし、サブステータスを記述する他の文字列を含めることもできます。 HTTP 状態コードの例としては、`OK` (HTTP 状態コード: 200)、`No Content` (HTTP 状態コード: 204)、`Service Unavailable` (HTTP 状態コード: 503) などがあります。
1. `resourceType`: イベントの影響を受けたリソースの種類。 (例: `Microsoft.Resources/deployments`)。

次に例を示します。

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```

アクティビティ ログのフィールドの詳細については、「[Azure アクティビティ ログのイベント スキーマ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)」を参照してください。

> [!NOTE]
> 新しいアクティビティ ログ アラート ルールがアクティブになるまで最大 5 分かかる場合があります。

## <a name="rest-api"></a>REST API 
Azure Monitor Activity Log Alerts API は REST API です。 これは、Azure Resource Manager REST API と完全に互換性があります。 Resource Manager コマンドレットまたは Azure CLI を使用すると、PowerShell で使用できます。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>PowerShell を使用した Resource Manager テンプレートのデプロイ
PowerShell を使用して、前の「[Azure Resource Manager テンプレート](#azure-resource-manager-template)」セクションで示したサンプル Resource Manager テンプレートをデプロイするには、次のコマンドを使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

*sampleActivityLogAlert.parameters.json* ファイルには、アラート ルールの作成時に必要とされたパラメーターに指定された値が含まれます。

### <a name="use-activity-log-powershell-cmdlets"></a>アクティビティ ログの PowerShell コマンドレットを使用する

アクティビティ ログ アラートでは、専用の PowerShell コマンドレットを使用できます。

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert):新しいアクティビティ ログ アラートを作成するか、既存のアクティビティ ログ アラートを更新します。
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert):1 つ以上のアクティビティ ログ アラート リソースを取得します。
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert):既存のアクティビティ ログ アラートを有効にし、そのタグを設定します。
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert):既存のアクティビティ ログ アラートを無効にし、そのタグを設定します。
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert):アクティビティ ログ アラートを削除します。

### <a name="azure-cli"></a>Azure CLI

[az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) セットの下で専用の Azure CLI コマンドを使用して、アクティビティ ログ アラート ルールを管理できます。

新しいアクティビティ ログ アラート ルールを作成するには、次のコマンドを使用します。

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create):新規のアクティビティ ログ アラート ルールのリソースを作成します。
2. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope):作成したアクティビティ ログ アラート ルールのスコープを追加します。
3. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group):アクティビティ ログ アラート ルールにアクション グループを追加します。

1 つのアクティビティ ログ アラート ルールのリソースを取得するには、Azure CLI コマンド [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) を使用します。 リソースグループ内のすべてのアクティビティ ログ アラート ルールのリソースを表示するには、[az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list) を使用します。
Azure CLI コマンド [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) を使用して、アクティビティ ログ アラート ルールのリソースを削除できます。

## <a name="next-steps"></a>次のステップ

- [アクティビティ ログの Webhook スキーマ](./activity-log-alerts-webhook.md)について学習します。
- [アクティビティ ログの概要](./activity-log-alerts.md)をお読みください。
- [アクション グループ](./action-groups.md)について学習します。  
- [サービス正常性の通知](../../service-health/service-notifications.md)について学習します。