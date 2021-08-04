---
title: 適格な認可を作成する
description: 顧客を Azure Lighthouse にオンボードする際に、管理テナント内のユーザーに Just-In-Time ベースでロールを昇格させることができます。
ms.date: 06/11/2021
ms.topic: how-to
ms.openlocfilehash: 938b0ae8f2d105d79237164287b00ec4fdf4d607
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060753"
---
# <a name="create-eligible-authorizations"></a>適格な認可を作成する

顧客を Azure Lighthouse にオンボードする際に、管理テナント内のユーザーに、指定された Azure 組み込みロールを付与するための認可を作成します。 また、[Azure Active Directory (Azure AD) Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) を使用する適格な認可を作成して、管理テナント内のユーザーがロールを一時的に昇格できるようにすることもできます。 これにより、追加のアクセス許可を Just-In-Time ベースで付与することで、設定された期間だけユーザーがそれらのアクセス許可を持つことができます。

適格な認可を作成すると、特権ロールへのユーザーの永続的な割り当ての数を最小限に抑えることができ、テナント内のユーザーによる特権アクセスに関連するセキュリティ リスクを軽減するのに役立ちます。

このトピックでは、適格な認可がどのように機能し、[顧客を Azure Lighthouse にオンボードする](onboard-customer.md)際に作成する方法について説明します。

> [!IMPORTANT]
> 適格な認可は、現在パブリック プレビュー中です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="license-requirements"></a>ライセンスの要件

適格な認可を作成するには、Enterprise Mobility + Security E5 (EMS E5) または Azure AD Premium P2 ライセンスが必要です。 要件に対する適切なライセンスを確認するには、「[Free、Basic、および Premium エディションの一般公開されている機能の比較](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。

EMS E5 または Azure AD Premium P2 ライセンスは、顧客テナントではなく管理テナントによって保持されている必要があります。

適格なロールに関連付けられている追加コストは、ユーザーがそのロールにアクセス権を昇格する期間中にのみ適用されます。

ユーザーのライセンスについては、「[Privileged Identity Management を使用するためのライセンスの要件](../../active-directory/privileged-identity-management/subscription-requirements.md)」を参照してください。

## <a name="how-eligible-authorizations-work"></a>適格な認可のしくみ

適格な認可では、ユーザーが特権タスクを実行する必要があるときにユーザーにロールのアクティブ化を要求するロールの割り当てを定義します。 ユーザーが適格なロールをアクティブ化すると、指定された期間、そのロールによって付与される完全なアクセス権が与えられます。

顧客テナント内のユーザーは、オンボード プロセスの前に、適格な認可内のものを含むすべてのロールの割り当てを確認できます。

ユーザーは適格なロールを正常にアクティブ化すると、委任されたスコープに対する永続的なロールの割り当てに加えて、事前に構成された期間、そのスコープに対する昇格されたロールを持つことになります。

管理テナント内の管理者は、管理テナントの監査ログを表示することで、すべての Privileged Identity Management アクティビティを確認できます。 顧客は委任されたサブスクリプションの Azure アクティビティ ログでこれらのアクションを確認できます。

適格な認可を作成する場合は、ユーザー、ロール、およびアクセス ポリシーという 3 つの要素を定義します。

- **ユーザー** は、個々のユーザー、または管理テナントの Azure AD グループのいずれかになります。 グループが定義されている場合、そのグループのすべてのメンバーは、自分の個人のアクセス権を、アクセス ポリシーに従ってそのロールに昇格させることができます。 適格な認可をサービス プリンシパルで使用することはできません。
- **ロール** には、ユーザー アクセス管理者を除く、Azure の委任されたリソース管理でサポートされている任意の Azure 組み込みロールを指定できます。
- **アクセス ポリシー** では、多要素認証 (MFA) の要件と、ユーザーがロールでアクティブ化される有効期限の時間の長さを定義します。 任意のロールに対して指定できる最大の時間は 8 時間です。

これらの要素の詳細と定義方法については、以下で説明します。

## <a name="create-eligible-authorizations-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して適格な認可を作成する

顧客を Azure Lighthouse にオンボードするには、[Azure Resource Manager テンプレートと、対応するパラメーター ファイル](onboard-customer.md#create-an-azure-resource-manager-template)を変更して使用します。 選択するテンプレートは、オンボードの対象がサブスクリプション全体、リソース グループ、サブスクリプション内の複数のリソース グループのいずれであるかによって異なります。

> [!NOTE]
> Azure Marketplace の管理サービス オファーを使用して顧客をオンボードすることもできますが、現時点ではこれらのオファーに適格な認可を含めることはできません。

顧客をオンボードするときに適格な認可を含めるには、[サンプル レポジトリの delegated-resource-management-eligible-authorizations セクション](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations)のいずれかのテンプレートを使用します。

|オンボード対象 (適格な認可付き)  |使用する Azure Resource Manager テンプレート  |変更するパラメーター ファイル |
|---------|---------|---------|
|サブスクリプション   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.Parameters.json)    |
|Resource group   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.parameters.json)    |
|サブスクリプション内の複数のリソース グループ   |[multiple-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.parameters.json)    |

**subscription.json** テンプレート (適格な認可を使用してサブスクリプションをオンボードするために使用できるもの) を下に示します。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "type": "string",
            "metadata": {
                "description": "Specify a unique name for your offer"
            },
            "defaultValue": "<to be filled out by MSP> Specify a title for your offer"
        },
        "mspOfferDescription": {
            "type": "string",
            "metadata": {
                "description": "Name of the Managed Service Provider offering"
            },
            "defaultValue": "<to be filled out by MSP> Provide a brief description of your offer"
        },
        "managedByTenantId": {
            "type": "string",
            "metadata": {
                "description": "Specify the tenant id of the Managed Service Provider"
            },
            "defaultValue": "<to be filled out by MSP> Provide your tenant id"
        },
        "authorizations": {
            "type": "array",
            "metadata": {
                "description": "Specify an array of objects, containing tuples of Azure Active Directory principalId, a Azure roleDefinitionId, and an optional principalIdDisplayName. The roleDefinition specified is granted to the principalId in the provider's Active Directory and the principalIdDisplayName is visible to customers."
            },
            "defaultValue": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM_Group" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46",
                    "principalIdDisplayName": "PIM_Group" 
                }   
            ]
        }, 
        "eligibleAuthorizations": { 
            "type": "array", 
            "metadata": { 
                "description": "Provide the authorizations that will have just-in-time role assignments on customer environments" 
            },
           "defaultValue": [ 
                { 
                        "justInTimeAccessPolicy": { 
                            "multiFactorAuthProvider": "Azure", 
                            "maximumActivationDuration": "PT8H" 
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "PIM_Group",
                        "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608" 
                        
                }                    
            ]    

        }                 
    },
    "variables": {
        "mspRegistrationName": "[guid(parameters('mspOfferName'))]",
        "mspAssignmentName": "[guid(parameters('mspOfferName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedServices/registrationDefinitions",
            "apiVersion": "2020-02-01-preview",
            "name": "[variables('mspRegistrationName')]",
            "properties": {
                "registrationDefinitionName": "[parameters('mspOfferName')]",
                "description": "[parameters('mspOfferDescription')]",
                "managedByTenantId": "[parameters('managedByTenantId')]",
                "authorizations": "[parameters('authorizations')]", 
                "eligibleAuthorizations": "[parameters('eligibleAuthorizations')]" 
            }
        },
        {
            "type": "Microsoft.ManagedServices/registrationAssignments",
            "apiVersion": "2020-02-01-preview",
            "name": "[variables('mspAssignmentName')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
            ],
            "properties": {
                "registrationDefinitionId": "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
            }
        }
    ],
    
    "outputs": {
        "mspOfferName": {
            "type": "string",
            "value": "[concat('Managed by', ' ', parameters('mspOfferName'))]"
        },
        "authorizations": {
            "type": "array",
            "value": "[parameters('authorizations')]"
        }, 
        "eligibleAuthorizations": { 
            "type": "array", 
            "value": "[parameters('eligibleAuthorizations')]" 

        } 
    }
}
```

### <a name="define-eligible-authorizations-in-your-parameters-file"></a>パラメーター ファイルで適格な認可を定義する

[subscription.Parameters.json サンプル テンプレート](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.Parameters.json)を使用すると、サブスクリプションをオンボードするときに、適格な認可を含む認可を定義できます。

個々の適格な認可は、`eligibleAuthorizations` パラメーターで定義する必要があります。 この例には、1 つの適格な認可が含まれています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Relecloud Managed Services"
        },
        "mspOfferDescription": {
            "value": "Relecloud Managed Services"
        },
        "managedByTenantId": {
            "value": "<insert the managing tenant id>"
        },
        "authorizations": {
            "value": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM group"
                }
            ]
        }, 
        "eligibleAuthorizations":{
            "value": [
                {
                        "justInTimeAccessPolicy": {
                            "multiFactorAuthProvider": "Azure",
                            "maximumActivationDuration": "PT8H"
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "Tier 2 Support",
                        "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"

                }
            ]
        }
    }
}
```

`eligibleAuthorizations` パラメーター内で、`principalId` は、この適格な認可が適用される Azure AD のユーザーまたはグループの ID を指定します。 サービス プリンシパル アカウントの ID を使用しないでください。これは、サービス プリンシパル アカウントでそのアクセス権を昇格し、適格なロールを使用するための方法が現在は存在しないためです。

> [!IMPORTANT]
> 閲覧者 (または閲覧者アクセス権を含む別の Azure 組み込みロール) など、適格な認可とは異なるロールを使用して、テンプレートの `authorizations` セクションに同じ `principalId` を含める必要があります。 そうしない場合、ユーザーは Azure portal で自身のロールを昇格できなくなります。

`roleDefinitionId` には、ユーザーが Just-In-Time ベースで使用できる [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)のロール定義 ID が含まれています。

`justInTimeAccessPolicy` は次の 2 つの要素を指定します。

- `multiFactorAuthProvider` は、Azure 多要素認証 (MFA) を使用した認証が必要になる **Azure** に設定するか、多要素認証が必要ない場合は **None** に設定できます。
- `maximumActivationDuration` では、ユーザーが適格なロールを持つことができる時間の合計を設定します。 この値には、ISO 8601 の期間の形式を使用する必要があります。 最小値は PT30M (30 分) で、最大値は PT8H (8 時間) です。

> [!NOTE]
> 注: ジャストインタイム アクセスは、ユーザー アクセス管理者が[マネージド ID に割り当てる](deploy-policy-remediation.md)ことができる `delegatedRoleDefinitionIds` には適用されません。 これらのロールの割り当ては、適格な認可として作成できません。 同様に、ユーザー アクセス管理者ロール自体に対して適格な認可を作成することはできません。

## <a name="elevation-process-for-users"></a>ユーザーの昇格プロセス

顧客を Azure Lighthouse にオンボードすると、指定したユーザー (または指定したグループのユーザー) は、含まれている適格なロールを使用できるようになります。

各ユーザーが、Azure portal の **[マイ カスタマー]** ページにアクセスし、委任を選択し、 **[資格のあるロールの管理]** を選択することで、アクセス権をいつでも昇格できます。 その後、Azure AD Privileged Identity Management の[ロールをアクティブ化する手順](../../active-directory/privileged-identity-management/pim-how-to-activate-role.md)に従うことができます。

:::image type="content" source="../media/manage-eligible-roles.png" alt-text="Azure portal 内の [資格あるロールの管理] ボタンを示すスクリーンショット。":::

適格なロールがアクティブ化されると、ユーザーは、適格な認可に指定されている期間全体でそのロールを持つことになります。 その期間が過ぎると、昇格プロセスを繰り返して再びアクセス権を昇格しない限り、そのロールを使用できなくなります。

## <a name="next-steps"></a>次の手順

- [ARM テンプレートを使用して顧客を Azure Lighthouse にオンボードする](onboard-customer.md)方法について学習します。
- [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) について詳しく学習します。
- [Azure Lighthouse のテナント、ユーザー、ロール](../concepts/tenants-users-roles.md)について詳しく学習します。