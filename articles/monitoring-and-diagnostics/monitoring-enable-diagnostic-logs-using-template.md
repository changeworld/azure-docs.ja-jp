<properties
    pageTitle="Resource Manager テンプレートを使用して診断設定を自動的に有効にする | Microsoft Azure"
    description="Resource Manager テンプレートを使用して、診断ログを Event Hubs にストリーミングしたり、ストレージ アカウントに保存したりできるようにするための診断設定を作成する方法について説明します。"
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>


# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用してリソースの作成時に診断設定を自動的に有効にする
この記事では、 [Azure Resource Manager テンプレート](../resource-group-authoring-templates.md) を使用して、リソースの作成時にリソースの診断設定を構成する方法について説明します。 これにより、リソースの作成時に、診断ログとメトリックの Event Hubs へのストリーミング、ストレージ アカウントへのアーカイブ、または Log Analytics への送信を自動的に開始できます。

Resource Manager テンプレートを使用して診断ログを有効にする方法は、リソースの種類によって異なります。

- **非コンピューティング** リソース (ネットワーク セキュリティ グループ、Logic Apps、Automation など) では、 [こちらの記事で説明する診断設定](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)を使用します。
- **コンピューティング** (WAD/LAD ベースの) リソースでは、 [こちらの記事で説明する WAD/LAD 構成ファイル](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)を使用します。

この記事では、いずれかの方法を使用して診断を構成する方法について説明します。

基本的な手順は次のとおりです。

1. リソースを作成し、診断を有効にする方法を記述した JSON ファイルとしてテンプレートを作成します。
2. [任意のデプロイ方法を使用してテンプレートをデプロイ](../resource-group-template-deploy.md)します。

生成する必要がある、非コンピューティング リソースおよびコンピューティング リソースのテンプレート JSON ファイルの例を以下に示します。

## <a name="non-compute-resource-template"></a>非コンピューティング リソース テンプレート
非コンピューティング リソースの場合、次の 2 つの手順を実行する必要があります。

1. (ストレージ アカウントへの診断ログのアーカイブ、Event Hubs へのログのストリーミング、Log Analytics へのログの送信を有効にするために) パラメーター BLOB に、ストレージ アカウント名、Service Bus 規則 ID、OMS Log Analytics ワークスペース ID のパラメーターを追加します。

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. 診断ログを有効にするリソースのリソース配列に、 `[resource namespace]/providers/diagnosticSettings`型のリソースを追加します。

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

診断設定のプロパティ BLOB は、 [こちらの記事で説明する形式](https://msdn.microsoft.com/library/azure/dn931931.aspx)に従います。

ネットワーク セキュリティ グループを作成し、Event Hubs およびストレージ アカウント内のストレージへのストリーミングを有効にする例を次に示します。

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>コンピューティング リソース テンプレート
コンピューティング リソース (Virtual Machine や Service Fabric など) で診断を有効にするには、次の手順を実行する必要があります。

1. VM のリソース定義に Azure 診断の拡張機能を追加します。
2. パラメーターとしてストレージ アカウントおよびイベント ハブを指定します。
3. すべての XML 文字を正しくエスケープして、WADCfg XML ファイルの内容を XMLCfg プロパティに追加します。

> [AZURE.WARNING] この最後の手順は理解するのが難しい場合があります。 [こちらの記事](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) をご覧ください。

このプロセス全体とサンプルについては、 [こちらのドキュメント](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)をご覧ください。


## <a name="next-steps"></a>次のステップ
- [Azure 診断ログの詳細を確認する](./monitoring-overview-of-diagnostic-logs.md)
- [Azure 診断ログを Event Hubs にストリーミングする](./monitoring-stream-diagnostic-logs-to-event-hubs.md)



<!--HONumber=Oct16_HO2-->


