---
title: 管理グループのすべてのサブスクリプションをオンボードする
description: 管理グループ内のすべてのサブスクリプションを Azure Lighthouse 管理テナントに委任する Azure ポリシーをデプロイできます。
ms.date: 08/13/2021
ms.topic: how-to
ms.openlocfilehash: 07f2bb850120b1f27f090aec9d0d3f97d9643d05
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206542"
---
# <a name="onboard-all-subscriptions-in-a-management-group"></a>管理グループのすべてのサブスクリプションをオンボードする

[Azure Lighthouse](../overview.md) でサブスクリプションやリソース グループの委任が可能ですが、[管理グループ](../../governance/management-groups/overview.md)の委任はできません。 ただし、管理グループ内のすべてのサブスクリプションを Azure Lighthouse 管理テナントに委任する [Azure ポリシー](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups)をデプロイできます。

ポリシーにより、管理グループ内の各サブスクリプションが指定した管理テナントに委任されているかどうかが [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) 効果を使用して確認されます。 サブスクリプションがまだ委任されていない場合、ポリシーは Azure Lighthouse の割り当てを、パラメーターで指定した値に基づいて作成します。 その後、管理グループ内のすべてのサブスクリプションに、サブスクリプションのそれぞれが手動でオンボードされているように、アクセスできるようになります。

このポリシーを使用するときは、次のことを留意してください。

- 管理グループ内の各サブスクリプションが、同じ承認セットを持ちます。 アクセスが許可されているユーザーとロールを変更するには、サブスクリプションを手動でオンボードする必要があります。
- 管理グループ内のすべてのサブスクリプションがオンボードされますが、管理グループ リソースに対して Azure Lighthouse から操作を実行することはできません。 操作するサブスクリプションを、個別にオンボードされた場合と同様に、選択する必要があります。

以下で指定しない限り、これらの手順はすべて、顧客のテナント内の、適切なアクセス許可を持つユーザーが実行する必要があります。

> [!TIP]
> このトピックではサービス プロバイダーと顧客の場合について説明していますが、[複数のテナントを管理するエンタープライズ](../concepts/enterprise.md)も同じプロセスを使用できます。

## <a name="register-the-resource-provider-across-subscriptions"></a>サブスクリプション間でリソース プロバイダーを登録する

通常、**Microsoft.ManagedServices** リソース プロバイダーは、オンボード プロセスの一環としてサブスクリプションに登録されます。 ポリシーを使用して管理グループ内のサブスクリプションをオンボードするときは、リソース プロバイダーを事前に登録する必要があります。 これは、顧客のテナントの共同作成者または所有者ユーザー (またはリソース プロバイダーの  `/register/action`  操作を行うアクセス許可を持つすべてのユーザー) が行います。 詳細については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

Azure Logic App を使用すると、[サブスクリプション間でリソース プロバイダーを自動的に登録できます](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-customer)。 このロジック アプリは、管理グループ内の各サブスクリプションにリソース プロバイダーを登録できる、アクセス許可が制限された顧客のテナントにデプロイできます。

また、[サービス プロバイダーのテナント にデプロイできる Azure Logic App](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-partner) も用意されています。 このロジック アプリでは、ロジック アプリに[テナント全体の管理者の同意を付与](../../active-directory/manage-apps/grant-admin-consent.md)することで、複数のテナントのサブスクリプション間でリソース プロバイダーを割り当てできます。 テナント全体の管理者の同意を付与するには、組織を代表して同意する権限を持つユーザーとしてサインインする必要があります。 このオプションを使用して複数のテナントにプロバイダーを登録する場合でも、ポリシーは管理グループごとに個別にデプロイする必要があります。

## <a name="create-your-parameters-file"></a>自分のパラメーター ファイルを作成する

ポリシーを割り当てるには、サンプル リポジトリの [deployLighthouseIfNotExistManagementGroup.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistManagementGroup.json) ファイルを、特定のテナントと割り当ての詳細で編集する[deployLighthouseIfNotExistsManagementGroup.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistsManagementGroup.parameters.json) パラメーター ファイルと共にデプロイします。 これら 2 つのファイルには、[個々のサブスクリプション のオンボード](onboard-customer.md)に使用されるのと同じ詳細が含まれている。

次の例は、サブスクリプションを Relecloud Managed Services テナントに委任するパラメーター ファイルを示しています。1 つは階層 1 のサポート用で、1 つは自動化アカウントであり、このアカウントは[顧客テナント のマネージド ID に delegateRoleDefinitionIds を割り当てる](deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)ことができます。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": { 
        "managedByName": { 
            "value": "Relecloud Managed Services" 
        }, 
        "managedByDescription": { 
            "value": "Relecloud provides managed services to its customers" 
        }, 
        "managedByTenantId": { 
            "value": "00000000-0000-0000-0000-000000000000" 
        }, 
        "managedByAuthorizations": { 
            "value": [ 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Tier 1 Support", 
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Automation Account - Full access", 
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9", 
                    "delegatedRoleDefinitionIds": [ 
                        "b24988ac-6180-42a0-ab88-20f7382dd24c", 
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293", 
                        "91c1777a-f3dc-4fae-b103-61d183457e46" 
                    ] 
                }                 
            ] 
        } 
    } 
} 
```

## <a name="assign-the-policy-to-a-management-group"></a>ポリシーを管理グループに割り当てる  

ポリシーを編集して割り当てを作成したら、それを管理グループ レベルで割り当てることができます。 ポリシーを割り当ててコンプライアンス状態の結果を表示する方法については、「 [クイックスタート: ポリシー割り当てを作成する](../../governance/policy/assign-policy-portal.md)」を参照してください。

下の PowerShell スクリプトは、作成したテンプレートとパラメーター ファイルを使用して、指定した管理グループの下にポリシー定義を追加する方法を示しています。 既存のサブスクリプションの割り当てと修復タスクを作成する必要があります。

```azurepowershell-interactive
New-AzManagementGroupDeployment -Name <DeploymentName> -Location <location> -ManagementGroupId <ManagementGroupName> -TemplateFile <path to file> -TemplateParameterFile <path to parameter file> -verbose
```

## <a name="confirm-successful-onboarding"></a>オンボードが成功したことを確認する

サブスクリプションが正常にオンボードされたことを、さまざまな方法で確認できます。 詳細については、「[オンボードが成功したことを確認する](onboard-customer.md#confirm-successful-onboarding)」を参照してください。

ロジック アプリとポリシーを管理グループでアクティブな状態に維持すると、管理グループに追加された新しいサブスクリプションもオンボードされます。

## <a name="next-steps"></a>次のステップ

- [Azure Lighthouse への顧客のオンボード](onboard-customer.md) の詳細を確認する。
- [Azure Policy](../../governance/policy/index.yml) の詳細を確認する。
- [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) の詳細を確認する。
