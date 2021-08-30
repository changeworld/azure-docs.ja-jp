---
title: Azure Monitor Application Insights スマート検出をアラートにアップグレードする (プレビュー) | Microsoft Docs
description: Azure Monitor Application Insights のスマート検出をアラート ルールにアップグレードするために必要な手順について説明します
ms.topic: conceptual
ms.date: 05/30/2021
ms.openlocfilehash: d0995c05af707c5a4fb94305ca1211e38ca59e3a
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587483"
---
# <a name="migrate-azure-monitor-application-insights-smart-detection-to-alerts-preview"></a>Azure Monitor Application Insights スマート検出をアラートに移行する (プレビュー)

この記事では、Application Insights スマート検出をアラートに移行するプロセスについて説明します。 移行によって、さまざまなスマート検出モジュールのアラート ルールが作成されます。 これらのルールは、他の Azure Monitor アラート ルールと同じように管理および構成できます。 また、これらのルールのアクション グループを構成すると、新しい検出に関するアクションや通知を行う複数の方法が提供されます。

## <a name="benefits-of-migration-to-alerts"></a>アラートに移行する利点

移行により、スマート検出では、次のような Azure Monitor アラートのすべての機能を活用できるようになります。

- **すべての検出機能のための豊富な通知オプション** - [アクション グループ](../alerts/action-groups.md)を使用すると、アラートの発生時にトリガーされる複数の種類の通知とアクションを構成できます。 通知は、電子メール、SMS、音声通話、またはプッシュ通知のほか、セキュリティで保護された Webhook の呼び出し、ロジック アプリ、Automation Runbook などのアクションによって構成できます。 アクション グループを使用すると、アクションを一度構成して複数のアラート ルールで使用できるようにすることで、大規模な管理が促進されます。
- Azure Monitor アラートのエクスペリエンスと API を使用した、スマート検出のアラートの **大規模な管理**。
- **通知のルールベースの抑制** - [アクション ルール](../alerts/alerts-action-rules.md)を使用すると、Azure Resource Manager の任意のスコープ (Azure サブスクリプション、リソース グループ、またはターゲット リソース) で、アクションを定義または抑制できます。 さまざまなフィルターを使用して、操作するアラート インスタンスの特定のサブセットを絞り込むことができます。 

## <a name="migrated-smart-detection-capabilities"></a>移行されるスマート検出機能

Application Insights リソースの移行時に、新しい一連のアラート ルールが作成されます。  移行されるスマート検出機能ごとに 1 つのルールが作成されます。 次の表は、移行前のスマート検出機能と移行後のアラート ルールの対応を示しています。

| スマート検出ルール名 <sup>(1)</sup>          | アラート ルール名 <sup>(2)</sup> |
| -------------------------                          | ------------------------------ |
| サーバー応答速度の低下                | 応答待機時間の劣化 - *\<Application Insights resource name\>*  |
| 依存関係の期間の減少                 | 依存関係の待機時間の劣化 - *\<Application Insights resource name\>*|
| トレースの重大度の比率の低下 (プレビュー)      | トレース重大度の劣化 - *\<Application Insights resource name\>*|
| 例外数の異常な上昇 (プレビュー)        | 例外の異常 - *\<Application Insights resource name\>*|
| Potential memory leak detected (潜在的なメモリ リークの検出) (プレビュー)           | メモリ リークの可能性 - *\<Application Insights resource name\>*|
| ページの読み込み速度が遅い                                | *廃止* <sup>(3)</sup>  |
| サーバーの応答速度が遅い                          | *廃止* <sup>(3)</sup>  |
| 依存関係の期間が長い                           | *廃止* <sup>(3)</sup>  |
| Potential security issue detected (潜在的なセキュリティの問題の検出) (プレビュー)        | *廃止* <sup>(3)</sup>  |
| 日次データ ボリュームの異常な上昇 (プレビュー)       | *廃止* <sup>(3)</sup>  |

<sup>(1)</sup> スマート検出の [設定] ブレードに表示されるルールの名前  
<sup>(2)</sup> 移行後の新しいアラート ルールの名前  
<sup>(3)</sup> これらのスマート検出機能は、使用率が低く、検出の有効性の再評価されたことにより、アラートに変換されません。 これらの検出機能は、移行が完了すると、このリソースに対してサポートされなくなります。 

移行によって、アルゴリズムの設計とスマート検出の動作が変更されることはありません。 変更の前後で、同じ検出のパフォーマンスを期待できます。

Application Insights の各リソースへの移行を個別に適用する必要があります。 明示的に移行されないリソースについては、スマート検出は以前と同様に引き続き動作します。

### <a name="action-group-configuration-for-the-new-smart-detection-alert-rules"></a>新しいスマート検出アラート ルールのアクション グループの構成

移行の一環として、新しいアラート ルールがそれぞれアクション グループで自動的に構成されます。 移行では、各ルールの既定のアクション グループを割り当てることができます。 既定のアクション グループは、移行前のルールの通知に従って構成されます。

- **スマート検出ルールに既定の電子メールが設定されているか、通知が構成されていない** 場合、新しいアラート ルールは "Application Insights スマート検出" という名前のアクション グループで構成されます。
    - 移行ツールでその名前の既存のアクション グループが検出された場合は、新しいアラート ルールがそのアクション グループにリンクされます。  
    - それ以外の場合は、その名前の新しいアクション グループが作成されます。 "Azure Resource Manager に電子メールを送信ロール" アクション用に新しいグループが構成され、Azure Resource Manager の共同作成者の監視およびリーダーの監視のユーザーに通知が送信されます。

- 移行前に **既定の電子メール通知を変更した** 場合は、"Application Insights スマート検出 \<n\>" というアクション グループが作成され、以前に構成された電子メール アドレスに通知を送信する電子メール アクションが設定されます。

既定のアクション グループを使用するのではなく、すべての新しいアラート ルールに対して構成される既存のアクション グループを選択します。

## <a name="executing-smart-detection-migration-process"></a>スマート検出の移行処理を実行する

### <a name="migrate-your-smart-detection-using-the-azure-portal"></a>Azure portal を使用してスマート検出を移行する

移行は、一度に 1 つの特定の Application Insights リソースに適用します。

リソース内のスマート検出を移行するには、次の手順を実行します。

1. Application Insights リソースの左側のメニューで、 **[調査]** の見出しの下にある **[スマート検出]** を選択します。

2. 「**スマート検出をアラートに移行する (プレビュー)** 」というバナーをクリックします。 移行ダイアログが開きます。

   ![スマート検出フィードのバナー](media/alerts-smart-detections-migration/smart-detection-feed-banner.png)

3. 新しいアラート ルール用に構成するアクション グループを選択します。 既定のアクション グループを使用するか (前述のように)、既存のアクション グループのいずれかを使用するかを選択できます。

4. **[移行]** を選択して、移行処理を開始します。

   ![スマート検出の移行のダイアログ](media/alerts-smart-detections-migration/smart-detection-migration-dialog.png)

移行後、前述のように Application insights リソースに対して新しいアラート ルールが作成されます。

### <a name="migrate-your-smart-detection-using-azure-cli"></a>Azure CLI を使用してスマート検出を移行する

スマート検出の移行を開始するには、次の Azure CLI コマンドを使用します。 前に説明したように、このコマンドによって、事前に構成された移行処理がトリガーされます。

```azurecli
az rest --method POST --uri /subscriptions/{subscriptionId}/providers/Microsoft.AlertsManagement/migrateFromSmartDetection?api-version=2021-01-01-preview --body @body.txt
```

ここで body.txt に次の内容を含める必要があります。

```json
{
      "scope": [
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName} /providers/microsoft.insights/components/{resourceName} "
      ],
      "actionGroupCreationPolicy" : "{Auto/Custom}",
      "customActionGroupName" : "{actionGroupName}"           
}
```

**ActionGroupCreationPolicy** では、スマート検出のルールの電子メール設定をアクション グループに移行するためのポリシーを選択します。 使用できる値は、以下のとおりです。

- **"Auto"** 。このドキュメントで説明されているように既定のアクション グループが使用されます
- **"Custom"** 。 **"customActionGroupName"** に指定されたアクション グループを使用して、すべてのアラート ルールが作成されます。
- *\<blank\>* - **ActionGroupCreationPolicy** が指定されていない場合は、"Auto" ポリシーが使用されます。

### <a name="migrate-your-smart-detection-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してスマート検出を移行する

Azure Resource Manager テンプレートを使用して、特定の Application Insights リソースのアラートへのスマート検出の移行をトリガーできます。 この方法を使用するには、次の操作を行う必要があります。

- サポートされている検出機能ごとに、スマート検出のアラート ルールを作成します
- 移行が完了したことを示すために、Application insights のプロパティを変更します

この方法では、作成するアラート ルールを制御したり、独自のアラート ルール名と説明を定義したり、ルールごとに必要なアクション グループを選択したりすることができます。

この目的では次のテンプレートを使用する必要があります (必要に応じて編集し、サブスクリプション ID と Application Insights リソース名を指定します)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "applicationInsightsResourceName": {
            "type": "string"
        },
        "actionGroupName": {
            "type": "string",
            "defaultValue": "Application Insights Smart Detection"
        },
        "actionGroupResourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().Name]"
        }
    },
    "variables": {
        "applicationInsightsResourceId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().Name,'/providers/microsoft.insights/components/',parameters('applicationInsightsResourceName'))]",
        "actionGroupId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',parameters('actionGroupResourceGroup'),'/providers/microsoft.insights/ActionGroups/',parameters('actionGroupName'))]",
        "requestPerformanceDegradationDetectorRuleName": "[concat('Response Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "dependencyPerformanceDegradationDetectorRuleName": "[concat('Dependency Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "traceSeverityDetectorRuleName": "[concat('Trace Severity Degradation - ', parameters('applicationInsightsResourceName'))]",
        "exceptionVolumeChangedDetectorRuleName": "[concat('Exception Anomalies - ', parameters('applicationInsightsResourceName'))]",
        "memoryLeakRuleName": "[concat('Potential Memory Leak - ', parameters('applicationInsightsResourceName'))]"
    },
    "resources": [
        {
            "name": "[variables('requestPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "RequestPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {   
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('dependencyPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Dependency Latency Degradation notifies you of an unusual increase in response by a dependency your app is calling (e.g. REST API or database)",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "DependencyPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('traceSeverityDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Trace Severity Degradation notifies you of an unusual increase in the severity of the traces generated by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "TraceSeverityDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('exceptionVolumeChangedDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Exception Anomalies notifies you of an unusual rise in the rate of exceptions thrown by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "ExceptionVolumeChangedDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('memoryLeakRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Potential Memory Leak notifies you of increased memory consumption pattern by your app which may indicate a potential memory leak.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "MemoryLeakDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[concat(parameters('applicationInsightsResourceName'),'/migrationToAlertRulesCompleted')]",
            "type": "Microsoft.Insights/components/ProactiveDetectionConfigs",
            "location": "[resourceGroup().location]",
            "apiVersion": "2018-05-01-preview",
            "properties": {
                "name": "migrationToAlertRulesCompleted",
                "sendEmailsToSubscriptionOwners": false,
                "customEmails": [],
                "enabled": true
            },
            "dependsOn": [
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('requestPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('dependencyPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('traceSeverityDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('exceptionVolumeChangedDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('memoryLeakRuleName'))]"
            ]
        }
    ]
}
```

## <a name="viewing-your-alerts-after-the-migration"></a>移行後にアラートを表示する

移行処理後に、Application Insights リソースの左側のメニューで [アラート] エントリを選択すると、スマート検出のアラートを表示できます。 **[シグナルの種類]** で **[スマート検出機能]** を選択し、フィルターを適用してスマート検出のアラートのみを表示します。 アラートを選択すると、その検出の詳細が表示されます。

![スマート検出アラート](media/alerts-smart-detections-migration/smart-detector-alerts.png)

Application Insights リソースのスマート検出フィードで利用可能な検出を確認することもできます。

![スマート検出フィード](media/alerts-smart-detections-migration/smart-detection-feed.png)

## <a name="managing-smart-detection-alert-rules-settings-after-the-migration"></a>移行後にスマート検出のアラート ルールの設定を管理する

### <a name="managing-alert-rules-settings-using-the-azure-portal"></a>Azure portal を使用してアラート ルールの設定を管理する

移行が完了したら、リソースに対して定義されている他のアラート ルールと同様の方法で、新しいスマート検出のアラート ルールにアクセスします。

1. Application Insights リソースの左側のメニューで、 **[監視]** の見出しの下にある **[アラート]** を選択します。

   ![[アラート] メニュー](media/alerts-smart-detections-migration/application-insights-alerts.png)

2. **[アラート ルールの管理]** を選択します

   ![アラート ルールの管理](media/alerts-smart-detections-migration/manage-alert-rules.png)

3. **[シグナルの種類]** で **[スマート検出機能]** を選択し、フィルターを適用してスマート検出のアラート ルールを表示します。

   ![スマート検出機能のルール](media/alerts-smart-detections-migration/smart-detector-rules.png)

### <a name="enabling--disabling-smart-detection-alert-rules"></a>スマート検出のアラート ルールの有効化/無効化 

スマート検出のアラート ルールは、他のアラート ルールと同様に、ポータルの UI またはプログラムを使用して有効または無効にすることができます。

移行前に特定のスマート検出のルールが無効にされていた場合は、新しいアラート ルールも無効になります。

### <a name="configuring-action-group-for-your-alert-rules"></a>アラート ルールのアクション グループの構成

新しいスマート検出のアラート ルールのアクション グループは、他の Azure Monitor のアラート ルールと同様に作成および管理できます。 

### <a name="managing-alert-rule-settings-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してアラート ルールの設定を管理する

移行が完了したら、Azure Resource Manager テンプレートを使用して、スマート検出のアラート ルールの設定を構成できます。

> [!NOTE]
> 移行が完了したら、スマート検出のアラート ルールのテンプレートを使用してスマート検出の設定を構成する必要があります。また、[Application Insights Resource Manager テンプレート](../app/proactive-arm-config.md#smart-detection-rule-configuration)を使用して構成することはできなくなりました。

この Azure Resource Manager テンプレートの例では、**応答待機時間の劣化** アラート ルールを重大度が 2 で **Enabled** の状態で構成する方法を示します。
* スマート検出はグローバル サービスであるため、ルールは **グローバル** な場所に作成されます。
* "id" プロパティは、構成されている特定の検出機能に従って変更する必要があります。 値は次のいずれかである必要があります。

  - **FailureAnomaliesDetector**
  - **RequestPerformanceDegradationDetector**
  - **DependencyPerformanceDegradationDetector**
  - **ExceptionVolumeChangedDetector**
  - **TraceSeverityDetector**  
  - **MemoryLeakDetector**
 
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Response Latency Degradation - my-app",
            "location": "global", 
            "properties": {
                  "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT24H",
                  "detector": {
                  "id": "RequestPerformanceDegradationDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```



## <a name="next-steps"></a>次の手順

- [Azure でのアラートについてさらに詳しく学習する](./alerts-overview.md)
- [Application Insights のスマート検出についてさらに詳しく学習する](../app/proactive-diagnostics.md)
