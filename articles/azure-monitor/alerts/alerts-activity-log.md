---
title: Azure Monitor でアクティビティ ログ アラートを作成、表示、管理する
description: Azure portal、Azure Resource Manager テンプレート、および Azure PowerShell を使用してアクティビティ ログ アラートを作成します。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 08/12/2021
ms.openlocfilehash: 8f26806a08ca463350e54d5ce1319fe0323715e7
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220037"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する  

## <a name="overview"></a>概要

アクティビティ ログ アラートは、アラートで指定した条件と一致する新しいアクティビティ ログ イベントが発生したときにアクティブになるアラートです。

これらのアラートは Azure リソースに対応しており、Azure Resource Manager テンプレートを使用して作成できます。 これらは、Azure Portal で作成、更新、削除することもできます。 通常は、Azure サブスクリプション内のリソースに特定の変更が発生した場合に通知を受け取るためにアクティビティ ログ アラートを作成します。 アラートは、多くの場合、特定のリソース グループまたはリソースにスコープ指定されます。 たとえば、サンプル リソース グループ **myProductionResourceGroup** 内の仮想マシンが削除されたときに通知を受け取ることができます。 または、サブスクリプション内のユーザーに新しいロールが割り当てられた場合に通知を受け取ることもできます。

> [!IMPORTANT]
> サービス正常性通知に対するアラートは、アクティビティ ログ アラート作成用のインターフェイスでは作成できません。 サービス正常性通知の作成および使用の詳細については、[サービス正常性通知のアクティビティ ログ アラートの受け取り](../../service-health/alerts-activity-log-service-notifications-portal.md)に関する記事をご覧ください。

アラート ルールを作成するときには、次の点を確認してください。

- スコープ内のサブスクリプションが、アラートが作成されているサブスクリプションと同じである。
- 条件は、アラートが構成されているレベル/状態/呼び出し元/リソース グループ/リソース ID/リソースの種類/イベント カテゴリでなければならない。
- アラート構成 JSON に、"anyOf" 条件や入れ子になった条件がない。 基本的に、1 つの "allOf" 条件のみ (追加の "allOf" 条件または "anyOf" 条件はない) が許可されます。
- カテゴリが "管理" の場合、アラートに上記の条件を 1 つ以上指定する必要があります。 アクティビティ ログ内にイベントが作成されるたびにアクティブ化するアラートを作成することはできません。
- アクティビティ ログのアラートのカテゴリに含まれるイベントに対して、アラートを作成することはできません。

## <a name="azure-portal"></a>Azure portal

Azure portal を使用して、アクティビティ ログ アラート ルールを作成および変更できます。 このエクスペリエンスは Azure アクティビティ ログに統合され、対象とする特定のイベントに対するシームレスなアラート作成が保証されます。
Azure portal で、新しいアクティビティ ログ アラート ルールを、Azure Monitor のアラート ブレードから、または Azure Monitor アクティビティ ログ ブレードから作成できます。 


### <a name="create-an-alert-rule-from-the-azure-monitor-alerts-blade"></a>アラート ルールを Azure Monitor アラート ブレードから作成する

次の手順では、Azure portal でメトリック アラート ルールを作成する方法について説明します。

1. [Azure portal](https://portal.azure.com) で、 **[モニター]** をクリックします。 [モニター] ブレードでは、すべての監視設定とデータが 1 つのビューにまとめられています。

2. **[アラート]** をクリックして、 **[+ 新しいアラート ルール]** をクリックします。

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-button-new.png" alt-text="新しいアラート ルール ボタンのスクリーン ショット。":::
    > [!TIP]
    > ほとんどのリソース ブレードにも **[監視]** のリソース メニューに **[アラート]** があり、そこからもアラートを作成できます。

3. **[ターゲットの選択]** をクリックし、読み込まれるコンテキスト ウィンドウで、アラートを設定するターゲット リソースを選択します。 **サブスクリプション** と **リソースの種類** のドロップダウン リストを使用して、監視するリソースを検索します。 検索バーを使用して、リソースを検索することもできます。
    
    > [!NOTE]
    > ターゲットとして、サブスクリプション全体、リソース グループ、または特定のリソースを選択できます。 サブスクリプションまたはリソース グループをターゲットとして選択し、リソースの種類も選択した場合、ルールは選択したサブスクリプションまたはリソース グループ内のその種類のすべてのリソースに適用されます。 特定のターゲット リソースを選択した場合、ルールは、そのリソースにのみ適用されます。 ターゲット セレクターを使用して、複数のサブスクリプション、リソース グループ、またはリソースを明示的に選択することはできません。 

4. 選択したリソースにアラートを作成できるアクティビティ ログ操作がある場合は、右下の **[使用可能なシグナル]** にアクティビティ ログが表示されます。 アクティビティ ログ アラートでサポートされているリソースの種類の完全な一覧については、[こちらの記事](../../role-based-access-control/resource-provider-operations.md)をご覧ください。

    :::image type="content" source="media/alerts-activity-log/select-target-new.png" alt-text="ターゲット選択ブレードのスクリーン ショット。" lightbox="media/alerts-activity-log/select-target-new.png":::

5. ターゲット リソースを選択した後、 **[条件の追加]** をクリックします。

6. リソースでサポートされているシグナルの一覧が表示されます。これには、**アクティビティ ログ** のさまざまなカテゴリのものが含まれます。 アラートを作成するアクティビティ ログのシグナル/操作を選択します。

7. 過去 6 時間のアクティビティ ログ操作のグラフが表示されます。 **[グラフの期間]** ドロップダウンを使用して、操作のより長期間の履歴を確認することを選択します。

8. **[アラート ロジック]** では、必要に応じて、より多くのフィルター条件を定義できます。

    - **イベントのレベル**:イベントの重大度レベル: "_詳細_"、"_情報_"、"_警告_"、"_エラー_" または "_重大_"。
    - **状態**: イベントの状態: "_開始済み_"、"_失敗_" または "_成功_"。
    - **イベント開始者**: "呼び出し元" とも呼ばれます。 操作を実行したユーザーの電子メール アドレスまたは Azure Active Directory 識別子。

    > [!NOTE]
    >   高品質で効果的なルールにするには、アラート スコープがサブスクリプション全体であり、選択したシグナルが "すべての管理操作" である場合は、条件の定義の一環として、アラート ロジック ドロップダウン ("イベント レベル"、"状態" または "開始者") の 1 つを入力してルールをより具体的にすることをお勧めします。
        
9. **[Done]** をクリックします。

    :::image type="content" source="media/alerts-activity-log/condition-selected-new.png" alt-text="条件の選択ブレードのスクリーンショット。" lightbox="media/alerts-activity-log/condition-selected-new.png":::

10. **[アラート ルール名]** 、 **[説明]** 、および **[重大度]** などの **[アラートの詳細]** を指定します。

    > [!NOTE]
    >   アクティビティ ログ アラートのアラート重大度は現在はユーザーが構成することができず、既定では常に Sev4 です。

11. 既存のアクション グループを選択するか、新しいアクション グループを作成して、アラートにアクション グループを追加します。

12. **[完了]** をクリックして、アクティビティ ログ アラート ルールを保存します。
     
     
### <a name="create-an-alert-rule-from-the-azure-monitor-activity-log-blade"></a>アラート ルールを Azure Monitor アクティビティ ログ ブレードから作成する

アクティビティ ログ アラートを作成する別の方法は、 [Azure portal のアクティビティ ログ](../essentials/activity-log.md#view-the-activity-log)を使用して、既に発生したアクティビティ ログ イベントから開始することです。 

1. **[Azure Monitor - アクティビティ ログ]** ブレードでは、必要なイベントをフィルター処理または検索し、次に **[アクティビティ ログ アラートの追加]** ボタンを使用して将来の同様イベントに対するアラートを作成できます。 

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png" alt-text="アクティビティ ログ イベントからのアラート ルールの作成のスクリーンショット。" lightbox="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png":::

2. アラート ルールの作成ブレードが開き、アラート ルールのスコープと条件は、前に選択したアクティビティ ログ イベントに従って既に入力されています。 スコープと条件はこの段階で、必要に応じて編集および変更できます。 既定では、新しいルールの正確なスコープと条件は、元のイベント属性から '現状のまま' でコピーされます。 たとえば、イベントが発生した正確なリソースと、イベントを開始した特定のユーザー/サービス名は、既定で新しいアラート ルールに含まれます。 アラート ルールをより一般的にしたい場合は、上のステージ 3 ～ 9 で説明したように、スコープと条件を適宜変更する必要があります。 

3. 次に、前に示した手順 10 から 12 に従います。
    
### <a name="view-and-manage-in-the-azure-portal"></a>Azure portal での表示と管理

1. Azure portal で、 **[モニター]**  >  **[アラート]** の順に選択します。 ウィンドウの左上隅にある **[アラート ルールの管理]** を選択します。

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-button-new.png" alt-text="[アラート ルールの管理] ボタンのスクリーンショット。":::
    
    使用可能なルールの一覧が表示されます。

2. 変更するアクティビティ ログ ルールをフィルタリングまたは検索します。

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-new.png" alt-text="アラート ルール管理ブレードのスクリーンショット。" lightbox="media/alerts-activity-log/manage-alert-rules-new.png":::

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
前述のサンプル JSON は、たとえば sampleActivityLogAlert.json として保存でき、[Azure portal で Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md) を使用してデプロイできます。

> [!NOTE]
> 
> 定義できる最高レベルのアクティビティ ログ アラートはサブスクリプションであることに注意してください。
> つまり、いくつかのサブスクリプションに対してアラートを定義するオプションがないため、定義はサブスクリプションごとにアラートである必要があります。

次のフィールドは、Azure Resource Manager テンプレートで条件フィールドに対して使用できるオプションです。"Resource Health"、"Advisor"、および "Service Health" には、特別なフィールドに対して追加のプロパティ フィールドがあることに注意してください。 
1. resourceId:アラートを生成する必要があるアクティビティ ログ イベントの影響を受けるリソースのリソース ID。
2. category:アクティビティ ログ イベントのカテゴリ。 次に例を示します。Administrative、ServiceHealth、ResourceHealth、Autoscale、Security、Recommendation、Policy。
3. caller:アクティビティ ログ イベントの操作を実行したユーザーのメール アドレスまたは Azure Active Directory 識別子。
4. level:アラートを生成する必要があるアクティビティ ログ イベントのアクティビティのレベル。 次に例を示します。Critical、Error、Warning、Informational、Verbose。
5. operationName:アクティビティ ログ イベントの操作の名前。 次に例を示します。Microsoft.Resources/deployments/write
6. resourceGroup:アクティビティ ログ イベントの影響を受けるリソースのリソース グループの名前。
7. resourceProvider:[Azure リソース プロバイダーと種類の説明](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0)。 リソース プロバイダーを Azure サービスにマップされるリストについては、「[Resource providers for Azure services](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)」 (Azure サービスのリソースプロバイダー) を参照してください。
8. status:アクティビティ イベントの操作の状態を説明する文字列。 次に例を示します。Started、In Progress、Succeeded、Failed、Active、Resolved
9. subStatus:通常は対応する REST 呼び出しの HTTP 状態コードですが、サブステータスを示す他の文字列が含まれる場合もあります。   次に例を示します。OK (HTTP 状態コード: 200)、作成済み (HTTP 状態コード:201)、受理 (HTTP 状態コード:202)、コンテンツなし (HTTP 状態コード:204)、無効な要求 (HTTP 状態コード:400)、見つかりません (HTTP 状態コード:404)、競合 (HTTP 状態コード:409)、内部サーバー エラー (HTTP 状態コード:500)、サービス利用不可 (HTTP 状態コード:503)、Gateway Timeout (HTTP 状態コード: 504)。
10. resourceType:イベントの影響を受けたリソースの種類。 次に例を示します。Microsoft.Resources/deployments

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
アクティビティ ログのフィールドの詳細については、[こちら](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)を参照してください。



> [!NOTE]
> 新しいアクティビティ ログ アラート ルールがアクティブになるまで最大 5 分かかる場合があります。

## <a name="rest-api"></a>REST API 
[Azure Monitor Activity Log Alerts API](/rest/api/monitor/activitylogalerts) は REST API です。 これは、Azure Resource Manager REST API と完全に互換性があります。 また、Resource Manager コマンドレットまたは Azure CLI を使用して、Powershell を介して使用できます。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>PowerShell を使用した Resource Manager テンプレートのデプロイ
PowerShell を使用して、前の「[Azure Resource Manager テンプレート](#azure-resource-manager-template)」セクションで示したサンプル Resource Manager テンプレートをデプロイするには、次のコマンドを使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

ここで、sampleActivityLogAlert.parameters.json には、アラート ルールの作成時に必要とされたパラメーターに指定された値が含まれます。

### <a name="use-activity-log-powershell-cmdlets"></a>アクティビティ ログの PowerShell コマンドレットを使用する

アクティビティ ログ アラートでは、専用の PowerShell コマンドレットを使用できます。

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert):新しいアクティビティ ログ アラートを作成するか、既存のアクティビティ ログ アラートを更新します。
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert):1 つ以上のアクティビティ ログ アラート リソースを取得します。
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert):既存のアクティビティ ログ アラートを有効にし、そのタグを設定します。
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert):既存のアクティビティ ログ アラートを無効にし、そのタグを設定します。
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert):アクティビティ ログ アラートを削除します。

## <a name="azure-cli"></a>Azure CLI

アクティビティ ログ アラート ルールを管理する専用の Azure CLI コマンドが [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) セットの下に用意されています。

新しいアクティビティ ログ アラート ルールを作成するには、次のコマンドをこの順序で使用します。

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create):新規のアクティビティ ログ アラート ルールのリソースを作成します。
2. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope):作成したアクティビティ ログ アラート ルールのスコープを追加します。
3. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group):アクティビティ ログ アラート ルールにアクション グループを追加します。

1 つのアクティビティ ログ アラート ルールのリソースを取得するには、Azure CLI コマンド [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) を使用します。 リソースグループ内のすべてのアクティビティ ログ アラート ルールのリソースを表示するには、[az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list) を使用します。
アクティビティ ログ アラート ルールのリソースを削除するには、Azure CLI コマンド [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) を使用します。

## <a name="next-steps"></a>次のステップ

- [アクティビティ ログの Webhook スキーマ](./activity-log-alerts-webhook.md)について学習します。
- [アクティビティ ログの概要](./activity-log-alerts.md)をお読みください。
- [アクション グループ](../platform/action-groups.md)について学習します。  
- [サービス正常性の通知](../../service-health/service-notifications.md)について学習します。

