---
title: 最新の API を使用してプログラムで Azure Enterprise Agreement サブスクリプションを作成する
description: 最新バージョンの REST API、Azure CLI、Azure PowerShell、および Azure Resource Manager テンプレートを使用して、プログラムによって Azure Enterprise Agreement サブスクリプションを作成する方法について説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/01/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 45bc6066152fbb83f5124b5ee157e6b16efa9bf9
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475172"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>最新の API を使用してプログラムで Azure Enterprise Agreement サブスクリプションを作成する

この記事は、最新の API バージョンを使用してプログラムで Enterprise Agreement (EA) 課金アカウントの Azure EA サブスクリプションを作成する場合に役に立ちます。 古いプレビュー バージョンをまだ使用している場合は、「[レガシ API を使用してプログラムで Azure サブスクリプションを作成する](programmatically-create-subscription-preview.md)」を参照してください。 

この記事では、Azure Resource Manager を使用してプログラムからサブスクリプションを作成する方法について説明します。

プログラムで Azure サブスクリプションを作成した場合、そのサブスクリプションには、お客様が Microsoft または正規の販売代理店と締結した Azure サービスの契約が適用されます。 詳細については、「[Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal/)」を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

サブスクリプションを作成するには、登録アカウントの所有者ロールがユーザーに必要です。 そのロールを取得する方法は 2 つあります。

* 登録のエンタープライズ管理者は、ユーザーを[アカウント所有者に設定](https://ea.azure.com/helpdocs/addNewAccount)できます (サインインが必要)。これにより、ユーザーは登録アカウントの所有者になります。
* 登録アカウントの既存の所有者が、[アクセス許可を付与](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put)できます。 

サービス プリンシパル (SPN) を使用して EA サブスクリプションを作成するには、登録アカウントの所有者が[そのサービス プリンシパルにサブスクリプションを作成する権限を付与する](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put)必要があります。 SPN を使用してサブスクリプションを作成するときに、[Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0&preserve-view=true ) または [Azure CLI](/cli/azure/ad/sp?view=azure-cli-latest&preserve-view=true#az_ad_sp_list) を使用して、Azure AD アプリケーション登録の ObjectId をサービス プリンシパルの ObjectId として使用します。 EA ロールの割り当て API 要求の詳細については、「[Azure Enterprise Agreement サービス プリンシパル名にロールを割り当てる](assign-roles-azure-service-principals.md)」を参照してください。 この記事には、SPN に割り当てることができるロール (およびロール定義 ID) の一覧が記載されています。

  > [!NOTE]
  > - 登録アカウントに対する所有者アクセス許可の付与には、必ず正しい API バージョンを使用してください。 この記事およびその中で記載されている API では、[2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) API を使用します。 
  > - 新しい API を使用するために移行している場合、[2015-07-01 バージョン](grant-access-to-create-subscription.md)を使用して行った以前の構成が新しい API で使用できるよう自動的に変換されることはありません。

## <a name="find-accounts-you-have-access-to"></a>アクセスできるアカウントを検索します。

アカウント所有者に関連付けられた登録アカウントに追加されると、アカウントと登録の関係を使用してサブスクリプション料金の請求先が決定されます。 このアカウント内で作成されたすべてのサブスクリプションは、アカウントが含まれる EA 登録に課金されます。 サブスクリプションを作成するには、サブスクリプションを所有するための登録アカウントおよびユーザー プリンシパルに関する値を受け渡す必要があります。

次のコマンドを実行するには、サブスクリプションが既定で作成されるディレクトリである、アカウント所有者の *ホーム ディレクトリ* にログインしている必要があります。

### <a name="rest"></a>[REST](#tab/rest)

自分がアクセスできるすべての登録アカウントを一覧表示するための要求:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API 応答で、自分がアクセスできるすべての登録アカウントが一覧表示されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

課金スコープと `id` の値は同じです。 登録アカウントの `id` は、サブスクリプション要求が開始される課金スコープです。 ID は、サブスクリプションを作成するために記事の後半で使用する必須パラメーターであるため、知っておくことが重要です。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

この値を取得するには、Azure CLI または REST API のいずれかを使用してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

自分がアクセスできるすべての登録アカウントを一覧表示するための要求:

```azurecli-interactive
> az billing account list
```

応答では、自分がアクセスできる登録アカウントが一覧表示されます

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

課金スコープと `id` の値は同じです。 登録アカウントの `id` は、サブスクリプション要求が開始される課金スコープです。 ID は、サブスクリプションを作成するために記事の後半で使用する必須パラメーターであるため、知っておくことが重要です。

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>特定の登録アカウントの中にサブスクリプションを作成する

次の例では、前のステップで選択した登録アカウントに *Dev Team Subscription* という名前のサブスクリプションを作成します。 

次のいずれかの方法を使用して、サブスクリプションのエイリアス名を作成します。 エイリアス名を作成するときは、以下を考慮することをお勧めします。

- 英数字、およびハイフンを使用する
- 先頭は文字、末尾は英数字にする
- ピリオドを使用しない


### <a name="rest"></a>[REST](#tab/rest)

PUT API を呼び出して、サブスクリプション作成の要求と別名を作成します。

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

要求本文で、`billingScope` としていずれかの `enrollmentAccounts` の `id` を指定します。

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

`Workload` に使用できる値は `Production` と `DevTest` です。

#### <a name="response"></a>Response

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

同じ URL で GET を実行して、要求の状態を取得できます。

### <a name="request"></a>Request

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Response

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

進行中の状態は、`provisioningState` で `Accepted` 状態として返されます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`New-AzSubscriptionAlias` コマンドレットを含む最新バージョンのモジュールをインストールするには、`Install-Module Az.Subscription` を実行します。 PowerShellGet の最新バージョンをインストールするには、[PowerShellGet モジュールの取得](/powershell/scripting/gallery/installing-psget)に関するページを参照してください。

課金スコープ `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"` を使用して、次の [New-AzSubscriptionAlias](/powershell/module/az.subscription/get-azsubscriptionalias) コマンドを実行します。 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

コマンドからの応答の一部として、subscriptionId を取得します。

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、`az extension add --name account` と `az extension add --name alias` を実行して、拡張機能をインストールします。

次の [az account alias create](/cli/azure/account/alias#az_account_alias_create) コマンドを実行して、`billing-scope` と、いずれかの `enrollmentAccounts` の `id` を指定します。 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

コマンドからの応答の一部として、subscriptionId を取得します。

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template-or-bicep"></a>ARM テンプレートまたは Bicep を使用する

前のセクションでは、PowerShell、CLI、または REST API を使用してサブスクリプションを作成する方法を説明しました。 サブスクリプションの作成を自動化する必要がある場合は、Azure Resource Manager テンプレート (ARM テンプレート) か [Bicep ファイル](../../azure-resource-manager/bicep/overview.md)の使用をご検討ください。

次の ARM テンプレートを使用すると、サブスクリプションを作成できます。 `billingScope` には、登録アカウント ID を指定します。 サブスクリプションはルート管理グループに作成されます。 サブスクリプションを作成した後、それを別の管理グループに移動できます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]"
            }
        }
    ],
    "outputs": {}
}
```

または、Bicep ファイルを使用してサブスクリプションを作成します。

```bicep
targetScope = 'managementGroup'

@description('Provide a name for the alias. This name will also be the display name of the subscription.')
param subscriptionAliasName string

@description('Provide the full resource ID of billing scope to use for subscription creation.')
param billingScope string

resource subscriptionAlias 'Microsoft.Subscription/aliases@2020-09-01' = {
  scope: tenant()
  name: subscriptionAliasName
  properties: {
    workload: 'Production'
    displayName: subscriptionAliasName
    billingScope: billingScope
  }
}
```

テンプレートを[管理グループ レベル](../../azure-resource-manager/templates/deploy-to-management-group.md)でデプロイします。 次の例では、JSON ARM テンプレートのデプロイを示しますが、代わりに Bicep ファイルをデプロイすることもできます。

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

要求本文:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321'
```

---

サブスクリプションを新しい管理グループに移動するには、次の ARM テンプレートを使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/",
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

または、次の Bicep ファイルを使用します。

```bicep
targetScope = 'managementGroup'

@description('Provide the ID of the management group that you want to move the subscription to.')
param targetMgId string

@description('Provide the ID of the existing subscription to move.')
param subscriptionId string

resource subToMG 'Microsoft.Management/managementGroups/subscriptions@2020-05-01' = {
  scope: tenant()
  name: '${targetMgId}/${subscriptionId}'
}
```

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure Enterprise のサブスクリプション作成 API の制限事項

- API を使用して作成されるのは、Azure Enterprise サブスクリプションのみです。
- 登録アカウントあたりのサブスクリプションの上限数は 2000 です。 その後、アカウントの追加のサブスクリプションは、Azure portal でのみ作成することができます。 API を使用してさらにサブスクリプションを作成するには、別の登録アカウントを作成します。 サブスクリプションは、2000 の上限数に達するまでキャンセル、削除、および譲渡できます。
- アカウント所有者ではないが、Azure RBAC 経由で登録アカウントに追加されたユーザーは、Azure portal でサブスクリプションを作成することはできません。
- サブスクリプションが作成されるテナントを選択することはできません。 サブスクリプションは常に、アカウント所有者のホーム テナント内に作成されます。 サブスクリプションを別のテナントに移動する場合は、[テナントのサブスクリプションの変更](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

* サブスクリプションを作成し終えたら、他のユーザーおよびサービス プリンシパルでその機能を利用できるようになります。 詳細については、「[Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)](grant-access-to-create-subscription.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法の詳細については、[Azure 管理グループによるリソースの整理](../../governance/management-groups/overview.md)に関する記事を参照してください。
* サブスクリプションの管理グループを変更するには、「[サブスクリプションの移動](../../governance/management-groups/manage.md#move-subscriptions)」を参照してください。
