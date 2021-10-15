---
title: 適格な認可を作成する
description: 顧客を Azure Lighthouse にオンボードする際に、管理テナント内のユーザーに Just-In-Time ベースでロールを昇格させることができます。
ms.date: 09/08/2021
ms.topic: how-to
ms.openlocfilehash: b36c3a91c780c2d374ceafe14a671aaa135a2d3c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777832"
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

## <a name="eligible-authorization-elements"></a>適格な認可の要素

適格な認可は、Azure Resource Manager テンプレートを使用して顧客をオンボードするときに作成するか、管理サービス オファーを Azure Marketplace に発行することによって作成します。 適格な認可には、ユーザー、ロール、アクセス ポリシーという 3 つの要素が含まれている必要があります。

### <a name="user"></a>User

適格な認可ごとに、管理テナント内の個々のユーザーまたは Azure AD グループのプリンシパル ID を指定します。 プリンシパル ID と共に、認可ごとに任意の表示名を指定する必要があります。

適格な認可にグループが指定されている場合、そのグループのすべてのメンバーは、アクセス ポリシーに従って自分の個々のアクセス権をそのロールに昇格させることができます。

サービス プリンシパルで適格な認可を使用することはできません。これは、現在、サービス プリンシパル アカウントでそのアクセス権を昇格して適格なロールを使用するための方法がないからです。 また、ユーザー アクセス管理者が[マネージド ID に割り当てる](deploy-policy-remediation.md)ことができる `delegatedRoleDefinitionIds` で適格な認可を使用することもできません。

> [!NOTE]
> 適格な認可ごとに、同じプリンシパル ID に対して異なるロール (閲覧者 (または閲覧者アクセスを含む別の Azure 組み込みロール) など) を持つ永続的な (アクティブな) 認可も作成してください。 閲覧者アクセスを持つ永続的な認可を含めないと、ユーザーが Azure portal で自身のロールを昇格できなくなります。

### <a name="role"></a>Role

それぞれの適格な認可には、ユーザーが Just-In-Time ベースで使用できる [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)が含まれている必要があります。

ロールには、ユーザー アクセス管理者を除く、[Azure の委任されたリソース管理でサポートされている](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)任意の Azure 組み込みロールを指定できます。

> [!IMPORTANT]
> 同じロールを使用する複数の適格な認可を含める場合は、それぞれの適格な認可に同じアクセス ポリシー設定がある必要があります。

### <a name="access-policy"></a>アクセス ポリシー

アクセス ポリシーでは、多要素認証の要件と、ユーザーがロールでアクティブ化されてから期限切れになるまでの時間の長さ、承認者が必要であるかどうかを定義します。

#### <a name="multifactor-authentication"></a>多要素認証

適格なロールをアクティブ化するために [Azure AD 多要素認証](../../active-directory/authentication/concept-mfa-howitworks.md)を要求するかどうかを指定します。

#### <a name="maximum-duration"></a>最大継続期間

ユーザーが適格なロールを持つことができる合計時間を定義します。 最小値は 30 分、最大値は 8 時間です。

#### <a name="approvers"></a>承認者

承認者要素は省略可能です。 これを含める場合は、ユーザーからの要求を承認または拒否して適格なロールをアクティブ化できる最大 10 人のユーザーまたはユーザー グループを管理テナントに指定できます。

サービス プリンシパル アカウントを承認者として使用することはできません。 また、承認者は自分のアクセスを承認できません。承認者も適格な認可にユーザーとして含まれている場合は、ロールを昇格するためのアクセス権を別の承認者が付与する必要があります。

承認者を含めない場合、ユーザーが選択したときにいつでも適格なロールをアクティブ化できるようになります。

## <a name="create-eligible-authorizations-using-managed-services-offers"></a>管理サービス オファーを使用して適格な認可を作成する

顧客を Azure Lighthouse にオンボードするには、管理サービス オファーを Azure Marketplace に公開できます。 [パートナー センターでオファーを作成する](publish-managed-services-offers.md)ときに、各 [認可](../../marketplace/create-managed-service-offer-plans.md#authorizations)の **[アクセス タイプ]** が **[アクティブ]** または **[適格]** のいずれであるかを指定できるようになりました。

**[適格]** を選択すると、認可のユーザーは、構成したアクセス ポリシーに従ってロールをアクティブ化できます。 最大期間を 30 分から 8 時間に設定し、Azure 多要素認証を要求するかどうかを指定する必要があります。 また、承認者を使用することを選択した場合は、最大 10 人の承認者を追加して、それぞれに表示名とプリンシパル ID を指定できます。

パートナー センターで適格な認可を構成するときには、「[適格な認可の要素](#eligible-authorization-elements)」セクションの詳細を確認してください。

## <a name="create-eligible-authorizations-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して適格な認可を作成する

顧客を Azure Lighthouse にオンボードするには、[Azure Resource Manager テンプレートと、対応するパラメーター ファイル](onboard-customer.md#create-an-azure-resource-manager-template)を変更して使用します。 選択するテンプレートは、オンボードの対象がサブスクリプション全体、リソース グループ、サブスクリプション内の複数のリソース グループのいずれであるかによって異なります。

顧客をオンボードするときに適格な認可を含めるには、[サンプル レポジトリの delegated-resource-management-eligible-authorizations セクション](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations)のいずれかのテンプレートを使用します。 シナリオに最適なテンプレートを使用できるように、承認者が含まれているテンプレートと含まれていないテンプレートが提供されています。

|オンボード対象 (適格な認可付き)  |使用する Azure Resource Manager テンプレート  |変更するパラメーター ファイル |
|---------|---------|---------|
|サブスクリプション   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.parameters.json)    |
|サブスクリプション (承認者を使用)  |[subscription-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.json)  |[subscription-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.parameters.json)    |
|Resource group   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.parameters.json)    |
|リソース グループ (承認者を使用)  |[rg-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg-managing-tenant-approvers.json)  |[rg-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg-managing-tenant-approvers.parameters.json)    |
|サブスクリプション内の複数のリソース グループ   |[multiple-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.parameters.json)    |
|サブスクリプション内の複数のリソース グループ (承認者を使用)  |[multiple-rg-managing-tenant-approvers.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg-managing-tenant-approvers.json)  |[multiple-rg-managing-tenant-approvers.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg-managing-tenant-approvers.parameters.json)    |

適格な認可を使用してサブスクリプション (承認者を使用) をオンボードするために使用できる **subscription-managing-tenant-approvers.json** テンプレートを以下に示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "type": "string",
            "metadata": {
                "description": "Specify a unique name for your offer"
            }
        },
        "mspOfferDescription": {
            "type": "string",
            "metadata": {
                "description": "Name of the Managed Service Provider offering"
            }
        },
        "managedByTenantId": {
            "type": "string",
            "metadata": {
                "description": "Specify the tenant id of the Managed Service Provider"
            }
        },
        "authorizations": {
            "type": "array",
            "metadata": {
                "description": "Specify an array of objects, containing tuples of Azure Active Directory principalId, a Azure roleDefinitionId, and an optional principalIdDisplayName. The roleDefinition specified is granted to the principalId in the provider's Active Directory and the principalIdDisplayName is visible to customers."
            }
        },
        "eligibleAuthorizations": {
            "type": "array",
            "metadata": {
                "description": "Provide the authorizations that will have just-in-time role assignments on customer environments with support for approvals from the managing tenant"
            }
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

[subscription-managing-tenant-approvers.Parameters.json sample template サンプル テンプレート](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription-managing-tenant-approvers.parameters.json)を使用すると、サブスクリプションをオンボードするときに認可 (適格な認可を含む) を定義できます。

個々の適格な認可は、`eligibleAuthorizations` パラメーターで定義する必要があります。 この例には、1 つの適格な認可が含まれています。

このテンプレートには、`managedbyTenantApprovers` 要素も含まれています。この要素には、`eligibleAuthorizations` 要素で定義されている適格なロールをアクティブ化するためにすべての試行を承認する必要がある `principalId` が追加されます。

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
                            "maximumActivationDuration": "PT8H",
                            "managedByTenantApprovers": [ 
                                { 
                                    "principalId": "00000000-0000-0000-0000-000000000000", 
                                    "principalIdDisplayName": "PIM-Approvers" 
                                } 
                            ]
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

`eligibleAuthorizations` パラメーター内の各エントリには、適格な認可を定義する [3 つの要素](#eligible-authorization-elements) (`principalId`、`roleDefinitionId`、`justInTimeAccessPolicy`) が含まれています。

`principalId` では、この適格な認可が適用される Azure AD のユーザーまたはグループの ID を指定します。

`roleDefinitionId` には、ユーザーが Just-In-Time ベースで使用できる [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)のロール定義 ID が含まれています。 同じ `roleDefinitionId` を使用する複数の適格な認可を含める場合は、これらのそれぞれで `justInTimeAccessPolicy` の設定が同じである必要があります。

`justInTimeAccessPolicy` では、次の 3 つの要素を指定します。

- `multiFactorAuthProvider` は、Azure AD 多要素認証を使用した認証が必要になる **Azure** に設定するか、多要素認証が必要ない場合は **None** に設定できます。
- `maximumActivationDuration` では、ユーザーが適格なロールを持つことができる時間の合計を設定します。 この値には、ISO 8601 の期間の形式を使用する必要があります。 最小値は PT30M (30 分) で、最大値は PT8H (8 時間) です。 簡単にするために、30 分単位の値を使用することをお勧めします (たとえば、6 時間の場合は PT6H、6.5 時間の場合は PT6H30M)。
- `managedByTenantApprovers` はオプションです。 含める場合は、1 つ以上の principalId の組み合わせと、適格なロールのアクティブ化を承認するために必要な principalIdDisplayName が含まれている必要があります。

これらの要素の詳細については、上記の「[適格な認可の要素](#eligible-authorization-elements)」セクションを参照してください。

## <a name="elevation-process-for-users"></a>ユーザーの昇格プロセス

顧客を Azure Lighthouse にオンボードすると、指定したユーザー (または指定したグループのユーザー) は、含まれている適格なロールを使用できるようになります。

各ユーザーが、Azure portal の **[マイ カスタマー]** ページにアクセスし、委任を選択し、 **[資格のあるロールの管理]** を選択することで、アクセス権をいつでも昇格できます。 その後、Azure AD Privileged Identity Management の[ロールをアクティブ化する手順](../../active-directory/privileged-identity-management/pim-resource-roles-activate-your-roles.md)に従うことができます。

:::image type="content" source="../media/manage-eligible-roles.png" alt-text="Azure portal 内の [資格あるロールの管理] ボタンを示すスクリーンショット。":::

承認者が指定されている場合、指定された承認者によって承認が付与されるまで、ユーザーはロールにアクセスできません。 承認が要求されると、すべての承認者に通知されます。承認が付与されるまで、ユーザーは適格なロールを使用できません。 承認が付与されたときにも、承認者に通知されます。 承認プロセスの詳細については、「[Privileged Identity Management で Azure リソース ロールに対する要求を承認または拒否する](../../active-directory/privileged-identity-management/pim-resource-roles-approval-workflow.md)」を参照してください。

適格なロールがアクティブ化されると、ユーザーは、適格な認可に指定されている期間全体でそのロールを持つことになります。 その期間が過ぎると、昇格プロセスを繰り返して再びアクセス権を昇格しない限り、そのロールを使用できなくなります。

## <a name="next-steps"></a>次の手順

- [ARM テンプレートを使用して顧客を Azure Lighthouse にオンボードする](onboard-customer.md)方法について学習します。
- [管理対象サービス オファーを使用して顧客をオンボード](publish-managed-services-offers.md)する方法について説明します。
- [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) について詳しく学習します。
- [Azure Lighthouse のテナント、ユーザー、ロール](../concepts/tenants-users-roles.md)について詳しく学習します。
