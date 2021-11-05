---
title: Azure Automation の Hybrid Runbook Worker でジョブの実行を適用する
description: この記事では、カスタム Azure Policy 定義を使用して、Azure Automation の Hybrid Runbook Worker でジョブの実行を適用する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 05/24/2021
ms.topic: conceptual
ms.openlocfilehash: ced119e7835f6c7fd7bf6aea34a10ebea4af3fc5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026281"
---
# <a name="use-azure-policy-to-enforce-job-execution-on-hybrid-runbook-worker"></a>Azure Policy を使用して Hybrid Runbook Worker でジョブの実行を適用する

Hybrid Runbook Worker で Runbook を起動すると、Azure portal から開始するときに Hybrid Runbook Worker グループの名前を Azure PowerShell または REST API で指定できる **Run on** オプションが使用されます。 グループを指定すると、そのグループ内の worker の 1 つによって Runbook が取得されて実行されます。 Runbook でこのオプションが指定されていない場合は、Azure Automation によって Azure サンドボックスで Runbook が実行されます。 

[Automation ジョブ オペレーター](automation-role-based-access-control.md#automation-job-operator)以上のメンバーである組織内のすべてのユーザーは、Runbook ジョブを作成できます。 Automation アカウント内の Hybrid Runbook Worker グループをターゲットにした Runbook の実行を管理するには、[Azure Policy](../governance/policy/overview.md) を使用します。 これは、組織の標準を適用し、指定されたユーザーによってオートメーション ジョブが制御および管理され、Runbook の実行が Azure サンドボックス上ではできず、Hybrid Runbook Worker 上でのみできるようにするのに役立ちます。 

この記事には、次の Automation REST API 操作を使用して、これらのアクティビティを制御するのに役立つカスタム Azure Policy 定義が含まれています。 具体的な内容は次のとおりです。

* [ジョブの作成](/rest/api/automation/job/create)
* [ジョブ スケジュールの作成](/rest/api/automation/jobschedule/create)
* [Webhook の作成](/rest/api/automation/webhook/createorupdate)

このポリシーは、`runOn` プロパティに基づいています。 ポリシーは、プロパティの値を検証します。これには、既存の Hybrid Runbook Worker グループの名前が含まれます。 値が null の場合は、ジョブ、ジョブ スケジュール、または Webhook の作成要求が Azure サンドボックスを対象にすると解釈され、要求は拒否されます。

## <a name="permissions-required"></a>必要なアクセス許可

Azure Policy リソースへのアクセス許可を得るには、サブスクリプション レベルで[所有者](../role-based-access-control/built-in-roles.md#owner)ロールのメンバーである必要があります。

## <a name="create-and-assign-the-policy-definition"></a>ポリシー定義を作成して割り当てる

ここでは、ポリシー ルールを作成し、管理グループまたはサブスクリプションに割り当て、必要に応じてサブスクリプションでリソース グループを指定します。 ポリシー言語に慣れていない場合は、[ポリシー定義の構造](../governance/policy/concepts/definition-structure.md)に関するページでポリシー定義の構成方法を確認してください。

1. 次の JSON スニペットを使用して、AuditAutomationHRWJobExecution.json という名前の JSON ファイルを作成します。

    ```json
    {
        "properties": {
            "displayName": "Enforce job execution on Automation Hybrid Runbook Worker",
            "description": "Enforce job execution on Hybrid Runbook Workers in your Automation account.",
            "mode": "all",
            "parameters": {
                "effectType": {
                    "type": "string",
                    "defaultValue": "Deny",
                    "allowedValues": [
                       "Deny",
                       "Disabled"
                    ],
                    "metadata": {
                        "displayName": "Effect",
                        "description": "Enable or disable execution of the policy"
                    }
                }
            },
            "policyRule": {
          "if": {
            "anyOf": [
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobs"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/webhooks"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/webhooks/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobSchedules"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobSchedules/runOn'))]",
                    "less": 1
                  }
                ]
              }
            ]
          },
          "then": {
            "effect": "[parameters('effectType')]"
          }
        }
      }
    }
    ```

2. 次の Azure PowerShell または Azure CLI コマンドを実行して、AuditAutomationHRWJobExecution.json ファイルを使用してポリシー定義を作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az policy definition create --name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' --display-name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' --description 'This policy enforces job execution on Automation account user Hybrid Runbook Workers.' --rules 'AuditAutomationHRWJobExecution.json' --mode All
    ```

    このコマンドは、**Audit Enforce Jobs on Automation Hybrid Runbook Workers** という名前のポリシー定義を作成します。 使用できるその他のパラメーターの詳細については、「[az policy definition create](/cli/azure/policy/definition#az_policy_definition_create)」を参照してください。

    場所のパラメーターを指定せずに呼び出す場合、`az policy definition create` は、既定により、セッション コンテキストの選択されたサブスクリプションにポリシー定義を保存することになります。 定義を別の場所に保存するには、次のパラメーターを使用します。

    * **subscription** - 別のサブスクリプションに保存します。 サブスクリプション ID の *GUID* 値、またはサブスクリプション名の *string* 値を指定する必要があります。
    * **management-group** - 管理グループに保存します。 *string* 値が必要です。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -DisplayName 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' -Policy 'AuditAutomationHRWJobExecution.json'
    ```

    このコマンドは、**Audit Enforce Jobs on Automation Hybrid Runbook Workers** という名前のポリシー定義を作成します。 使用できるその他のパラメーターの詳細については、「[New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)」を参照してください。

    場所のパラメーターを指定せずに呼び出す場合、`New-AzPolicyDefinition` は、既定により、セッション コンテキストの選択されたサブスクリプションにポリシー定義を保存することになります。 定義を別の場所に保存するには、次のパラメーターを使用します。

    * **SubscriptionId** -別のサブスクリプションに保存します。 *GUID* 値が必要です。
    * **ManagementGroupName** -管理グループに保存します。 *string* 値が必要です。

    ---

3. ポリシー定義を作成したら、次のコマンドを実行してポリシー割り当てを作成できます。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
    ```

    `az policy assignment create` の **scope** パラメーターは、管理グループ、サブスクリプション、リソース グループ、または単一のリソースに使用できます。 このパラメーターは完全なリソース パスを使用します。 各コンテナーの **scope** のパターンは、次のとおりです。 `{rName}`、`{rgName}`、`{subId}`、および `{mgName}` を、それぞれリソース名、リソース グループ名、サブスクリプション ID、および管理グループ名と置き換えます。 `{rType}` は、そのリソースの **リソースの種類** (VM の場合は `Microsoft.Compute/virtualMachines` など) に置き換えられます。

    - リソース - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - リソース グループ - `/subscriptions/{subID}/resourceGroups/{rgName}`
    - サブスクリプション - `/subscriptions/{subID}`
    - 管理グループ - `/providers/Microsoft.Management/managementGroups/{mgName}`

    Azure Policy の定義 ID は、PowerShell で次のコマンドを実行して取得できます。
    
    ```azurecli
    az policy definition show --name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers'
    ```
    
    作成したポリシー定義のポリシー定義 ID は、次の例のようになります。
    
    ```output
    "/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Enforce Jobs on Automation Hybrid Runbook Workers"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $rgName = Get-AzResourceGroup -Name 'ContosoRG'
    $Policy = Get-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers'
    New-AzPolicyAssignment -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -PolicyDefinition $Policy -Scope $rg.ResourceId
    ```

    _ContosoRG_ を対象とするリソース グループの名前に置き換えます。

    `New-AzPolicyAssignment` の **Scope** パラメーターは、管理グループ、サブスクリプション、リソース グループ、または単一のリソースに使用できます。 このパラメーターは、`Get-AzResourceGroup` の **ResourceId** プロパティが返す完全なリソース パスを使用します。 各コンテナーの **Scope** のパターンは、次のとおりです。 `{rName}`、`{rgName}`、`{subId}`、および `{mgName}` を、それぞれリソース名、リソース グループ名、サブスクリプション ID、および管理グループ名と置き換えます。
    `{rType}` は、そのリソースの **リソースの種類** (VM の場合は `Microsoft.Compute/virtualMachines` など) に置き換えられます。

    - リソース - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - リソース グループ - `/subscriptions/{subId}/resourceGroups/{rgName}`
    - サブスクリプション - `/subscriptions/{subId}`
    - 管理グループ - `/providers/Microsoft.Management/managementGroups/{mgName}`

    ---

4. [Azure portal](https://portal.azure.com) にサインインします。
5. Azure portal で **[すべてのサービス]** を選択し、「**Policy**」を検索して選択することで、Azure Policy サービスを起動します。
6. ページの左側にある **[コンプライアンス]** を選択します。 そして、作成したポリシーの割り当てを探します。

   :::image type="content" source="./media/enforce-job-execution-hybrid-worker/azure-policy-dashboard-policy-status.png" alt-text="Azure Policy ダッシュボードのスクリーンショット。":::

いずれかの Automation REST 操作が、要求本文で Hybrid Runbook Worker を参照せずに実行されると、Azure サンドボックスで操作を実行しようとしたことを示す次の例のようなエラーで、403 応答コードが返されます。

```rest
{
  "error": {
    "code": "RequestDisallowedByPolicy",
    "target": "Start_VMS",
    "message": "Resource 'Start_VMS' was disallowed by policy. Policy identifiers: '[{\"policyAssignment\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917\"},\"policyDefinition\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc\"}}]'.",
    "additionalInfo": [
      {
        "type": "PolicyViolation",
        "info": {
          "policyDefinitionDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "evaluationDetails": {
            "evaluatedExpressions": [
              {
                "result": "True",
                "expressionKind": "Field",
                "expression": "type",
                "path": "type",
                "expressionValue": "Microsoft.Automation/automationAccounts/jobs",
                "targetValue": "Microsoft.Automation/automationAccounts/jobs",
                "operator": "Equals"
              },
              {
                "result": "True",
                "expressionKind": "Value",
                "expression": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                "expressionValue": 0,
                "targetValue": 1,
                "operator": "Less"
              }
            ]
          },
          "policyDefinitionId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionName": "4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionEffect": "Deny",
          "policyAssignmentId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917",
          "policyAssignmentName": "fd5e2cb3842d4eefbc857917",
          "policyAssignmentDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "policyAssignmentScope": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG",
          "policyAssignmentParameters": {}
        }
      }
    ]
  }
}
```

試行された操作は、次の例のように、Automation アカウントのアクティビティ ログにも記録されます。

:::image type="content" source="./media/enforce-job-execution-hybrid-worker/failed-job-activity-log-example.png" alt-text="失敗したジョブ実行のアクティビティ ログの例。":::

## <a name="next-steps"></a>次のステップ

Runbook を操作するには、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」を参照してください。