---
title: Azure Lighthouse への顧客のオンボード
description: Azure Lighthouse に顧客をオンボードする方法について説明します。これによりそのリソースは、テナント内のユーザーがアクセスして管理できます。
ms.date: 09/30/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dbd3eb285a0135ca1b86294c5d3a41ef88a8472
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353299"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Azure Lighthouse への顧客のオンボード

この記事では、サービス プロバイダーが顧客を Azure Lighthouse にオンボードする方法について説明します。 これを行う場合、顧客の Azure Active Directory (Azure AD) テナント内の委任されたリソース (サブスクリプション、リソース グループ、またはこれらの両方) は、[Azure の委任されたリソース管理](../concepts/architecture.md)を通じてテナントのユーザーが管理できます。

> [!TIP]
> このトピックではサービス プロバイダーと顧客に言及しますが、[複数のテナントを管理している企業](../concepts/enterprise.md)では、同じプロセスを使用して Azure Lighthouse を設定し、自社の管理エクスペリエンスを強化することができます。

複数の顧客に対して、オンボード プロセスを繰り返すことができます。 適切なアクセス許可を持つユーザーが、管理しているテナントにサインインすると、そのユーザーは、個々の顧客テナントにサインインしなくても管理操作を実行することが顧客のテナント スコープ全体で承認されます。

> [!NOTE]
> または、[Azure Marketplace に公開](publish-managed-services-offers.md)したマネージド サービス オファー (パブリックまたはプライベート) を顧客が購入したときに、顧客を Azure Lighthouse にオンボードすることもできます。 また、ここで説明されているオンボード プロセスは、Azure Marketplace に公開されているオファーと共に使用できます。

オンボード プロセスでは、サービス プロバイダーのテナントと顧客のテナント両方の中からアクションを実行する必要があります。 これらの手順はすべて、この記事で説明します。

## <a name="gather-tenant-and-subscription-details"></a>テナントとサブスクリプションの詳細を収集する

顧客のテナントをオンボードするには、アクティブな Azure サブスクリプションが必要です。 次のことを知っている必要があります。

- サービス プロバイダーのテナント (顧客のリソースを管理する場所) のテナント ID。 [Azure portal でテンプレートを作成する場合](#create-your-template-in-the-azure-portal)、この値は自動的に指定されます。
- 顧客のテナント (リソースはサービス プロバイダーで管理されます) のテナント ID。
- サービス プロバイダーで管理される (またはサービス プロバイダーで管理されるリソース グループを含む) 顧客のテナントにある特定のサブスクリプションそれぞれのサブスクリプション ID

テナントの ID がわからない場合は、[Azure portal、Azure PowerShell、または Azure CLI を使用してそれを取得](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)できます。

## <a name="define-roles-and-permissions"></a>ロールとアクセス許可を定義する

サービス プロバイダーは、1 人の顧客に対して複数のタスクを実行でき、その場合は、スコープごとに異なるアクセスが必要になります。 適切な [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)を割り当てるために、必要な数の承認を定義することができます。 各承認には、管理テナント内の Azure AD ユーザー、グループ、またはサービス プリンシパルを指す **principalId** が含まれています。

> [!NOTE]
> 明示的に指定されていない限り、Azure Lighthouse ドキュメント内で言及されている "ユーザー" は、承認に含まれる Azure AD ユーザー、グループ、またはサービス プリンシパルに該当します。

承認を定義するために、アクセスを許可する管理テナントの各ユーザー、各ユーザー グループ、または各サービス プリンシパルの ID 値を知っておく必要があります。 管理テナント内から [Azure portal、Azure PowerShell、または Azure CLI を使用してこれらの ID を取得](../../role-based-access-control/role-assignments-template.md#get-object-ids)できます。 また、割り当てる各[組み込みロール](../../role-based-access-control/built-in-roles.md)のロール定義 ID も必要になります。

> [!TIP]
> テナント内のユーザーが必要に応じて後から[委任へのアクセスを削除](remove-delegation.md)できるように、顧客をオンボードするときに、[マネージド サービスの登録割り当ての削除ロール](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)を割り当てることをお勧めします。 このロールが割り当てられていない場合、委任されたリソースは顧客のテナント内のユーザーによってのみ削除できます。

可能な限り、個々のユーザーではなく、ロールごとに Azure AD ユーザー グループを使用することをお勧めします。 これにより、アクセス権を持つグループに個々のユーザーを柔軟に追加または削除できるようになるため、ユーザー変更を行うためにオンボード プロセスを繰り返す必要がなくなります。 サービス プリンシパルにロールを割り当てることもできます。これは、自動化のシナリオで役立ちます。

> [!IMPORTANT]
> Azure AD グループのアクセス許可を追加するには、 **[グループの種類]** を **[セキュリティ]** に設定する必要があります。 このオプションは、グループの作成時に選択します。 詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

承認を定義する場合は、ユーザーがジョブの完了に必要なアクセス許可のみを持つように、必ず最小限の特権の原則に従ってください。 サポートされているロールとベスト プラクティスについては、「[Azure Lighthouse のシナリオにおけるテナント、ユーザー、ロール](../concepts/tenants-users-roles.md)」を参照してください。

顧客エンゲージメント全体におけるご自身の影響を追跡して評価を受けるには、オンボードされた各サブスクリプションにアクセスできる少なくとも 1 つのユーザー アカウントに Microsoft Partner Network (MPN) ID を関連付けます。 サービス プロバイダー テナントでこの関連付けを実行する必要があります。 MPN ID に関連付けられているテナントにサービス プリンシパル アカウントを作成し、顧客をオンボードするときに毎回そのサービス プリンシパルを含めることをお勧めします。 詳細については、[パートナー ID をリンクして、委任されたリソースでパートナー獲得クレジットを有効にする](partner-earned-credit.md)に関する記事をご覧ください。

> [!TIP]
> *適格認可* を作成して、管理中のテナントのユーザーが一時的にロールを昇格できるようにすることもできます。 この機能は現在パブリック プレビュー中であり、特定のライセンス要件があります。 詳細については、「[適格認可を作成する](create-eligible-authorizations.md)」を参照してください。

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成

顧客をオンボードするには、プラン用に次の情報を含む [Azure Resource Manager](../../azure-resource-manager/index.yml) テンプレートを作成する必要があります。 顧客のテナント内にテンプレートがデプロイされると、**mspOfferName** および **mspOfferDescription** の値は、Azure portal の [[サービス プロバイダー] ページ](view-manage-service-providers.md)で顧客に表示されます。

|フィールド  |定義  |
|---------|---------|
|**mspOfferName**     |この定義を説明する名前。 この値は、オファーのタイトルとして顧客に表示され、一意の値である必要があります。        |
|**mspOfferDescription**     |自分のオファーの簡単な説明 (例: "Contoso VM 管理オファー")。 このフィールドは省略可能ですが、顧客がオファーを明確に理解するために推奨されます。   |
|**managedByTenantId**     |テナント ID。          |
|**authorizations**     |**principalId** 値はテナントのユーザー、グループ、または SPN を表し、それぞれに、顧客が承認の目的を理解するのに役立つ **principalIdDisplayName** が指定されているほか、アクセス レベルを指定するための組み込みの **roleDefinitionId** 値がマップされています。      |

このテンプレートは、Azure portal 内で、または[サンプル リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)で提供されているテンプレートを手動で変更することで作成できます。 

> [!IMPORTANT]
> ここで説明するプロセスでは、同じ顧客テナントにサブスクリプションをオンボードしている場合でも、オンボードするサブスクリプションごとに個別のデプロイが必要です。 また、同じ顧客テナントで異なるサブスクリプション内の複数のリソース グループをオンボードする場合も個別のデプロイが必要です。 ただし、1 つのサブスクリプション内の複数のリソース グループをオンボードする場合は、1 つのデプロイで実行できます。
>
> また、同じサブスクリプション (またはサブスクリプション内のリソース グループ) に適用される複数のプランに対しても個別のデプロイが必要です。 適用される各プランに、それぞれ異なる **mspOfferName** を使用する必要があります。

### <a name="create-your-template-in-the-azure-portal"></a>Azure portal でテンプレートを作成する

Azure portal でテンプレートを作成するには、 **[マイ カスタマー]** にアクセスし、概要ページで **[ARM テンプレートの作成]** を選択します。

**[Create ARM Template offer]\(ARM テンプレート オファーの作成\)** ページで、**名前** とオプションの **説明** を入力します。 これらの値はテンプレートの **mspOfferName** および **mspOfferDescription** に使用されます。 **managedByTenantId** 値は、ログインしている Azure AD テナントに基づいて自動的に提供されます。

次に、オンボードする顧客スコープに基づいて、 **[サブスクリプション]** または **[リソース グループ]** のいずれかを選択します。 **[リソース グループ]** を選択した場合、オンボードするリソース グループの名前を入力する必要があります。 **+** アイコンを選択して、必要に応じてリソース グループを追加できます。 (すべてのリソース グループが、同じ顧客サブスクリプション内にある必要があります)。

最後に、 **[+ 承認の追加]** を選択して承認を作成します。 各承認について、次の詳細を指定します。

1. 承認に含めるアカウントの種類に基づいて **[プリンシパルの種類]** を選択します。 これは、 **[ユーザー]** 、 **[グループ]** 、または **[サービス プリンシパル]** のいずれかにすることができます。 この例では、 **[ユーザー]** を選択します。
1. **[+ ユーザーの選択]** リンクを選択して、選択ウィンドウを開きます。 検索フィールドを使用して、追加するユーザーを検索できます。 これを行ったら、 **[選択]** をクリックします。 ユーザーの **[プリンシパル ID]** が自動的に入力されます。
1. **[表示名]** フィールド (選択したユーザーに基づいて入力される) を確認し、必要に応じて変更します。
1. このユーザーに割り当てる **[ロール]** を選択します。
1. **[アクセス]** の種類で、 **[永続]** または **[対象]** を選択します。 **[対象]** を選択した場合は、最大期間、多要素認証、承認が必要かどうかのオプションを指定する必要があります。 これらのオプションの詳細については、「[適格な認可を作成する](create-eligible-authorizations.md)」を参照してください。 適格な認可機能は現在パブリック プレビュー中であり、サービス プリンシパルでは使用できません。
1. **[追加]** を選択して認可を作成します。

:::image type="content" source="../media/add-authorization.png" alt-text="Azure portal の [承認の追加] セクションのスクリーンショット。":::

**[追加]** を選択した後、 **[Create ARM Template offer]\(ARM テンプレート オファーの作成\)** 画面に戻ります。 **[+ 承認の追加]** を再び選択して、必要な数だけ承認を追加できます。

すべての承認を追加した後、 **[テンプレートの表示]** を選択します。 この画面には、入力した値に対応する .json ファイルが表示されます。 **[ダウンロード]** を選択して、この .json ファイルのコピーを保存します。 このテンプレートは、[顧客のテナントにデプロイ](#deploy-the-azure-resource-manager-template)できます。 変更が必要な場合は、手動で編集することもできます。 このファイルは、Azure portal に格納されないことに注意してください。

### <a name="create-your-template-manually"></a>テンプレートを手動で作成する

Azure Resource Manager テンプレート ([サンプル リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)に提供されています) と、お使いの構成に合わせるための変更と承認の定義を行う対応するパラメーター ファイルを使用して、テンプレートを作成できます。 必要に応じて、別のパラメーター ファイルを使用するのではなく、すべての情報をテンプレートに直接含めることができます。

選択するテンプレートは、オンボードの対象がサブスクリプション全体、リソース グループ、サブスクリプション内の複数のリソース グループのいずれであるかによって異なります。 また、この方法でサブスクリプションをオンボードしたい方のために、Azure Marketplace に公開したマネージド サービス オファーを購入した顧客に使用できるテンプレートも用意されています。

|オンボードの対象  |使用する Azure Resource Manager テンプレート  |変更するパラメーター ファイル |
|---------|---------|---------|
|サブスクリプション   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/subscription/subscription.parameters.json)    |
|Resource group   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/rg.parameters.json)    |
|サブスクリプション内の複数のリソース グループ   |[multi-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/multi-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/rg/multiple-rg.parameters.json)    |
|サブスクリプション (Azure Marketplace に公開されたオファーの使用時)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

[適格認可](create-eligible-authorizations.md#create-eligible-authorizations-using-azure-resource-manager-templates) (現在はパブリック プレビュー) を含めるには、[サンプル レポジトリの delegated-resource-management-eligible-authorizations セクション](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations)から対応するテンプレートを選択します。

> [!TIP]
> 1 回のデプロイで管理グループ全体をオンボードすることはできませんが、[管理グループ内の各サブスクリプションをオンボードする](onboard-management-group.md)ためのポリシーをデプロイすることはできます。 その後、管理グループ内のすべてのサブスクリプションにアクセスできるようになります。ただし、(管理グループ リソースに直接アクションを実行するのではなく) 個々のサブスクリプションとして処理する必要があります。

次の例では、サブスクリプションをオンボードする際に使用される、変更後の **subscription.parameters.json** ファイルを示しています。 ([rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg) フォルダー内にある) リソース グループのパラメーター ファイルは似ていますが、オンボードの対象となる特定のリソース グループを識別するための **rgName** パラメーターも含まれています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "00000000-0000-0000-0000-000000000000"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

上記の例の最後の承認では、ユーザー アクセス管理者ロール (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) が設定された **principalId** が追加されます。 このロールを割り当てる際は、**delegatedRoleDefinitionIds** プロパティと 1 つ以上のサポートされている Azure 組み込みロールを含める必要があります。 この承認で作成されたユーザーは、これらのロールを顧客テナント内の[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に割り当てることができます。これは、[修復可能なポリシーをデプロイする](deploy-policy-remediation.md)ために必要です。  ユーザーは、サポート インシデントを作成することもできます。 ユーザー アクセス管理者ロールに通常関連付けられている他のアクセス許可は、この **principalId** に適用されません。

## <a name="deploy-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートのデプロイ

テンプレートを作成したら、顧客のテナント内のユーザーが、それをテナント内にデプロイする必要があります。 オンボードするサブスクリプションごと (または、オンボードするリソース グループを含むサブスクリプションごと) に個別のデプロイが必要です。

ここで説明するプロセスを使用してサブスクリプション (またはサブスクリプション内の 1 つまたは複数のリソース グループ) をオンボードすると、そのサブスクリプションに対して **Microsoft.ManagedServices** リソースプロバイダーが登録されます。

> [!IMPORTANT]
> このデプロイは、ゲスト以外のアカウントが、オンボード対象のサブスクリプションで[所有者](../../role-based-access-control/built-in-roles.md#owner)などの `Microsoft.Authorization/roleAssignments/write` アクセス許可を含むロールを持っている (またはオンボード対象のリソース グループを含む) 顧客のテナント内で実行する必要があります。 サブスクリプションを委任できるユーザーを見つけるには、顧客のテナント内のユーザーが Azure portal 上でサブスクリプションを選択し、 **[アクセス制御 (IAM)]** を開くと、[所有者ロールを持つすべてのユーザーを表示](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)することができます。
>
> サブスクリプションが[クラウド ソリューション プロバイダー (CSP) プログラム](../concepts/cloud-solution-provider.md)を使用して作成されている場合、サービス プロバイダー テナントの[管理エージェント](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) ロールを持つユーザーがデプロイを実行できます。

デプロイは、次に示すように、PowerShell を使用するか、Azure CLI を使用するか、または Azure portal 内で行うことができます。

### <a name="powershell"></a>PowerShell

1 つのテンプレートをデプロイするには、次のようにします。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -Verbose
```

別個のパラメーター ファイルを使用するテンプレートをデプロイするには、次のようにします。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

1 つのテンプレートをデプロイするには、次のようにします。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --verbose
```

別個のパラメーター ファイルを使用するテンプレートをデプロイするには、次のようにします。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

### <a name="azure-portal"></a>Azure portal

このオプションを使用すると、テンプレートを Azure portal 内で直接変更してデプロイできます。 これは顧客テナント内のユーザーが行う必要があります。

1. [GitHub リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)で、使用するテンプレートの横に表示される **[Deploy to Azure]\(Azure へのデプロイ\)** ボタンを選択します。 Azure portal でテンプレートが開きます。
1. **[Msp Offer Name]\(Msp オファー名\)** **[Msp Offer Description]\(Msp オファーの説明\)** 、 **[Managed by Tenant Id]\(テナント ID による管理\)** 、および **[Authorizations]\(承認\)** の値を入力します。 必要に応じて、 **[パラメーターの編集]** を選択してパラメーター ファイルに `mspOfferName`、`mspOfferDescription`、`managedbyTenantId`、および `authorizations` の値を直接入力できます。 テンプレートの既定値を使用するのではなく、必ず、これらの値を更新してください。
1. **[確認と作成]** を選択し、次に **[作成]** を選択します。

数分後に、デプロイが完了したという通知が表示されます。

## <a name="confirm-successful-onboarding"></a>オンボードが成功したことを確認する

顧客サブスクリプションが Azure Lighthouse に正常にオンボードされたら、サービス プロバイダーのテナント内のユーザーは、サブスクリプションとそのリソースを表示できるようになります (上記のプロセスを通じて、個人として、または適切なアクセス許可を持つ Azure AD グループのメンバーとして、それに対するアクセスが許可されている場合)。 これを確認するには、次のいずれかの方法でサブスクリプションが表示されることを確認します。  

### <a name="azure-portal"></a>Azure portal

サービス プロバイダーのテナントで

1. [[マイ カスタマー] ページ](view-manage-customers.md)に移動します。
2. **[顧客]** を選択します。
3. Resource Manager テンプレートで指定したオファー名の付いたサブスクリプションが表示されることを確認します。

> [!IMPORTANT]
> [[マイ カスタマー]](view-manage-customers.md) にある委任されたサブスクリプションを表示するためには、サブスクリプションがオンボードされたときに、サービス プロバイダーのテナント内のユーザーに[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロール (または、閲覧者アクセスを含む別の組み込みロール) が付与されている必要があります。

顧客のテナントで

1. [[サービス プロバイダー] ページ](view-manage-service-providers.md)に移動します。
2. **[サービス プロバイダーのオファー]** を選択します。
3. Resource Manager テンプレートで指定したオファー名の付いたサブスクリプションが表示されることを確認します。

> [!NOTE]
> デプロイが完了してから Azure portal に更新が反映されるまで最大 15 分かかる場合があります。 ブラウザーを更新するか、サインインしてからサインアウトするか、または新しいトークンを要求することで Azure Resource Manager トークンを更新すると、更新プログラムをすぐに確認できる場合があります。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

顧客がオンボードされた後に変更を加える必要がある場合は、[委任を更新](update-delegation.md)できます。 完全に[委任へのアクセスを削除する](remove-delegation.md)こともできます。

## <a name="troubleshooting"></a>トラブルシューティング

顧客を正常にオンボードできない場合、またはユーザーが委任されたリソースにアクセスするときに問題が生じた場合、次のヒントと要件を確認して、もう一度試みてください。

- Azure portal に顧客リソースを表示する必要のあるユーザーは、オンボード プロセス中に[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロール (または閲覧者アクセス権を含む別の組み込みロール) が付与されている必要があります。
- `managedbyTenantId` 値は、オンボードされているサブスクリプションのテナント ID と同じにすることはできません。
- 同じスコープの同じ `mspOfferName` を持つ複数の割り当てを行うことはできません。
- 委任されたサブスクリプションに対して、**Microsoft.ManagedServices** リソース プロバイダーを登録する必要があります。 これはデプロイ中に自動的に行われますが、行われない場合は、[手動で登録](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)できます。
- 認可には、"[所有者](../../role-based-access-control/built-in-roles.md#owner)" 組み込みロール、または "[DataActions](../../role-based-access-control/role-definitions.md#dataactions)" を持つ組み込みロールが割り当てられたユーザーを含めることはできません。
- グループは、[**グループの種類**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types)を **Microsoft 365** ではなく **Security** に設定して作成する必要があります。
- アクセスがグループに与えられた場合、ユーザーがそのグループに属するか必ずご確認ください。 属さない場合、別のデプロイを実行しなくても [Azure AD を使用し、グループにユーザーを追加](../../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)できます。 [グループ所有者](../../active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners.md)はその人が管理するグループに属するとは限らず、場合によっては、アクセスを与えるために追加する必要があることにご注意ください。
- [入れ子になったグループ](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)では、アクセスが有効になるまでにさらなる遅延が発生する場合があります。
- 認可に含める [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)には、非推奨のロールを含めることはできません。 Azure 組み込みロールが非推奨になると、そのロールでオンボードされたすべてのユーザーがアクセス権を失います。また、追加の委任をオンボードすることはできなくなります。 この問題を解決するには、サポートされている組み込みロールのみを使用するようにテンプレートを更新してから、新しいデプロイを実行します。

## <a name="next-steps"></a>次のステップ

- [テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。
- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](view-manage-customers.md)します。
- 委任を[更新](update-delegation.md)または[削除](remove-delegation.md)する方法について説明します。
