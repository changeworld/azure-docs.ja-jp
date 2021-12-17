---
title: ログのための Azure AD 認証
description: Azure Monitor で Log Analytics のための Azure Active Directory (Azure AD) 認証を有効にする方法について説明します。
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 526312df5c1bbf5e21a4017177752fb4f476ae5c
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868709"
---
# <a name="azure-ad-authentication-for-logs"></a>ログのための Azure AD 認証

Azure Monitor では、仮想マシン上のエージェント、Application Insights、Azure リソースの診断設定、データ コレクター API など、[複数のソースからのデータをログに収集](data-platform-logs.md#data-collection)できます。

Log Analytics エージェントでは、初期アクセスを確認し、さらにエージェントと Azure Monitor 間にセキュリティで保護された接続を確立するために使用される証明書をプロビジョニングするため、ワークスペース キーが登録キーとして使用されます。 詳細については、[エージェントからデータを送信する](data-security.md#2-send-data-from-agents)ことに関するページを参照してください。 データ コレクター API では、[アクセスを承認する](data-collector-api.md#authorization)ために、同じワークスペース キーが使用されます。

資格情報を管理することは困難であるため、これらのオプションは煩雑で、リスクとなる可能性があります。ワークスペース キーを大規模に管理する場合は特にそうです。 ローカル認証を使用せず、マネージド ID と Azure Active Directory を使用して排他的に認証されたテレメトリのみが Azure Monitor に取り込まれるようにすることを明示的に選択できます。 この機能により、運用上とビジネス上の両方の重要な決定を下すために使用されるテレメトリのセキュリティと信頼性が高められています。

以下の手順に従って、Azure Monitor ログの Azure Active Directory 統合を有効にして、これらの共有シークレットへの依存を解消します。

1. Azure Monitor エージェントにキーは不要ですが、代わりに[システム マネージド ID](../agents/azure-monitor-agent-overview.md#security) が必要です。 Log Analytics エージェントから [Azure Monitor エージェントに移行](../agents/azure-monitor-agent-migration.md)します。
2. [Log Analytics ワークスペースのローカル認証を無効にします](#disable-local-authentication-for-log-analytics)。
3. [Application Insights の Azure AD 認証 (プレビュー)](../app/azure-ad-authentication.md) を使用して、認証されたテレメトリのみが Application Insights リソースに取り込まれるようにします。

## <a name="disable-local-authentication-for-log-analytics"></a>Log Analytics のローカル認証を無効にする

Log Analytics エージェントへの依存を解消した後で、Log Analytics ワークスペースのローカル認証を無効にすることを選択できます。 これにより、Azure AD によって排他的に認証されたテレメトリを取り込み、クエリを実行できるようになります。

ローカル認証を無効にすると、使用可能な機能の一部が制限される場合があります。具体的には以下のとおりです。

- 既存の Log Analytics エージェントは機能を停止します。Azure Monitor エージェント (AMA) のみがサポートされます。 Azure Monitor エージェントには、Log Analytics エージェントで利用できる一部の機能がありません (カスタム ログ コレクション、IIS ログ収集など)。
- データ コレクター API (プレビュー) では Azure AD 認証がサポートされておらず、データの取り込みには使用できなくなります。

ローカル認証は、Azure Policy を使用するか、Azure Resource Manager テンプレート、PowerShell、または CLI を使用してプログラムから無効にすることができます。

### <a name="azure-policy"></a>Azure Policy

Azure Policy の "DisableLocalAuth" では、このプロパティが "true" に設定されていないと、ユーザーが新しい Log Analytics ワークスペースを作成することが拒否されます。 ポリシー名は `Log Analytics Workspaces should block non-Azure Active Directory based ingestion` です。 このポリシー定義をサブスクリプションに適用するには、[新しいポリシー割り当てを作成してポリシーを割り当てます](../../governance/policy/assign-policy-portal.md)。 

ポリシー テンプレートの定義を次に示します。

```json
{
  "properties": {
    "displayName": "Log Analytics Workspaces should block non-Azure Active Directory based ingestion.",
    "policyType": "BuiltIn",
    "mode": "Indexed",
    "description": "Enforcing log ingestion to require Azure Active Directory authentication prevents unauthenticated logs from an attacker which could lead to incorrect status, false alerts, and incorrect logs stored in the system.",
    "metadata": {
      "version": "1.0.0",
      "category": "Monitoring"
    },
    "parameters": {
      "effect": {
        "type": "String",
        "metadata": {
          "displayName": "Effect",
          "description": "Enable or disable the execution of the policy"
        },
        "allowedValues": [
          "Deny",
          "Audit",
          "Disabled"
        ],
        "defaultValue": "Audit"
      }
    },
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.OperationalInsights/workspaces"
          },
          {
            "field": "Microsoft.OperationalInsights/workspaces/features.disableLocalAuth",
            "notEquals": "true"
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
  },
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e15effd4-2278-4c65-a0da-4d6f6d1890e2",
  "type": "Microsoft.Authorization/policyDefinitions",
  "name": "e15effd4-2278-4c65-a0da-4d6f6d1890e2"
}
```

### <a name="azure-resource-manager"></a>Azure Resource Manager

プロパティ `DisableLocalAuth` は、Log Analytics ワークスペースでローカル認証を無効にするために使用されます。 このプロパティを true に設定すると、すべてのアクセスで Azure AD 認証を使用することが必須になります。 

以下に、ローカル認証を無効にするために使用できる Azure Resource Manager テンプレートの例を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaces_name": {
            "defaultValue": "workspace-name",
            "type": "string"
        },
        "workspace_location": {
          "defaultValue": "region-name",
          "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaces_name')]",
            "location": "[parameters('workspace_location')]",
            "properties": {
                 "sku": {
                    "name": "PerGB2018"
                },
                "retentionInDays": 30,
                "features": {
                    "disableLocalAuth": false,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
}

```


### <a name="cli"></a>CLI

プロパティ `DisableLocalAuth` は、Log Analytics ワークスペースでローカル認証を無効にするために使用されます。 このプロパティを true に設定すると、すべてのアクセスで Azure AD 認証を使用することが必須になります。 

次に、ローカル認証を無効にするために使用できる CLI コマンドの例を示します。

```azurecli
    az resource update --ids "/subscriptions/[Your subscription ID]/resourcegroups/[Your resource group]/providers/microsoft.operationalinsights/workspaces/[Your workspace name]--api-version "2021-06-01" --set properties.features.disableLocalAuth=True
```

### <a name="powershell"></a>PowerShell

プロパティ `DisableLocalAuth` は、Log Analytics ワークスペースでローカル認証を無効にするために使用されます。 このプロパティを true に設定すると、すべてのアクセスで Azure AD 認証を使用することが必須になります。 

以下に、ローカル認証を無効にするために使用できる PowerShell コマンドの例を示します。

```powershell
    $workspaceSubscriptionId = "[You subscription ID]"
    $workspaceResourceGroup = "[You resource group]"
    $workspaceName = "[Your workspace name]"
    $disableLocalAuth = $false
    
    # login
    Connect-AzAccount
    
    # select subscription
    Select-AzSubscription -SubscriptionId $workspaceSubscriptionId
    
    # get private link workspace resource
    $workspace = Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ResourceGroupName $workspaceResourceGroup -ResourceName $workspaceName -ApiVersion "2021-06-01"
    
    # set DisableLocalAuth
    $workspace.Properties.Features | Add-Member -MemberType NoteProperty -Name DisableLocalAuth -Value $disableLocalAuth -Force
    $workspace | Set-AzResource -Force
```

## <a name="next-steps"></a>次の手順
* [Application Insights に対する Azure AD Authentication (プレビュー)](../app/azure-ad-authentication.md)