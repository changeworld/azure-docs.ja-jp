---
title: 最新の API を使用してプログラムで Microsoft Partner Agreement の Azure サブスクリプションを作成する
description: 最新バージョンの REST API、Azure CLI、Azure PowerShell、および Azure Resource Manager テンプレートを使用して、プログラムによって Microsoft Partner Agreement の Azure サブスクリプションを作成する方法について説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/01/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 985f649d864e0fad250a5b2342b8cb96c049c0ec
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478358"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>最新の API を使用してプログラムで Microsoft Partner Agreement の Azure サブスクリプションを作成する

この記事は、最新の API バージョンを使用してプログラムで Microsoft Partner Agreement の Azure サブスクリプションを作成する場合に役に立ちます。 古いプレビュー バージョンをまだ使用している場合は、「[レガシ API を使用してプログラムで Azure サブスクリプションを作成する](programmatically-create-subscription-preview.md)」を参照してください。 

この記事では、Azure Resource Manager を使用してプログラムからサブスクリプションを作成する方法について説明します。

プログラムで Azure サブスクリプションを作成した場合、そのサブスクリプションには、お客様が Microsoft または正規の販売代理店と締結した Azure サービスの契約が適用されます。 詳細については、「[Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal/)」を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

課金アカウントのサブスクリプションを作成するには、組織のクラウド ソリューション プロバイダー アカウントにグローバル管理者ロールまたは管理エージェント ロールが必要です。 詳細については、「[パートナー センター - ユーザー ロールとアクセス許可の割り当て](/partner-center/permissions-overview)」を参照してください。

Microsoft Partner Agreement アカウントへのアクセス権があるかどうかわからない場合は、「[Microsoft Partner Agreement へのアクセスの確認](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement)」のセクションを参照してください。

以下の例では REST API を使用します。 現時点では、PowerShell と Azure CLI はサポートされていません。

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>アクセス権のある課金アカウントを検索する

自分がアクセスできるすべての課金アカウントの一覧を表示するには、以下の要求を行います。

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API の応答には、課金アカウントの一覧が含まれます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

`displayName` プロパティを使用して、サブスクリプションを作成する課金アカウントを指定します。 アカウントの agreementType が *MicrosoftPartnerAgreement* であることを確認します。 アカウントの `name` をコピーします。 たとえば、`Contoso` 課金アカウントのサブスクリプションを作成するには、`99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` をコピーします。 次の手順で使用できるように、値をどこかに貼り付けておきます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

この値を取得するには、Azure CLI または REST API のいずれかを使用してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing account list
```
アクセスできるすべての課金アカウントの一覧が返されます。

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Partner",
    "agreementType": "MicrosoftPartnerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

displayName プロパティを使用して、サブスクリプションを作成する課金アカウントを特定します。 アカウントの agreementType が MicrosoftPartnerAgreement であることを確認します。 アカウントの名前をコピーします。 たとえば、課金アカウント Contoso にサブスクリプションを作成する場合は、99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx をコピーします。 次の手順で使用できるように、値をどこかに貼り付けておきます。

---

## <a name="find-customers-that-have-azure-plans"></a>Azure プランの顧客を検索する

最初の手順でコピーした `name` (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) を使用して次の要求を行い、課金アカウントのすべての顧客のうち、自分が Azure サブスクリプションを作成できるものの一覧を取得します。

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

API 応答には、課金アカウントの顧客のうち、Azure プランを利用している顧客が一覧表示されます。 これらの顧客のサブスクリプションを作成できます。

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

`displayName` プロパティを使用して、サブスクリプションを作成する対象の顧客を指定します。 顧客の `id` をコピーします。 たとえば、`Fabrikam toys` のサブスクリプションを作成するには、`/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx` をコピーします。 後の手順で使用するので、値をどこかに貼り付けておきます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

この値を取得するには、Azure CLI または REST API のいずれかを使用してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing customer list --account-name 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

API 応答には、課金アカウントの顧客のうち、Azure プランを利用している顧客が一覧表示されます。 これらの顧客のサブスクリプションを作成できます。

```json
[
  {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Fabrikam toys",
    "id": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  },
  {
    "billingProfileDisplayName": "Contoso toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Contoso toys",
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "d49c364c-f866-4cc2-a284-d89f369b7951",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  }
]

```

`displayName` プロパティを使用して、サブスクリプションを作成する対象の顧客を指定します。 顧客の `id` をコピーします。 たとえば、`Fabrikam toys` のサブスクリプションを作成するには、`/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx` をコピーします。 後の手順で使用するので、値をどこかに貼り付けておきます。

---

## <a name="get-the-resellers-for-a-customer"></a>顧客のリセラーを取得する

このセクションは、間接プロバイダーだけのためのオプションです。

CSP の 2 層モデルの間接プロバイダーである場合は、顧客のサブスクリプションを作成するときにリセラーを指定できます。

### <a name="rest"></a>[REST](#tab/rest)

2 番目の手順でコピーした `id` (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) を使用して次の要求を行い、顧客に対して利用できるすべてのリセラーの一覧を取得します。

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
API 応答には、顧客向けのリセラーが一覧表示されます。

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

`description` プロパティを使用して、サブスクリプションに関連付けられているリセラーを特定します。 リセラーの `resellerId` をコピーします。 たとえば、`Wingtip` を関連付けるには、`3xxxxx` をコピーします。 次の手順で使用できるように、値をどこかに貼り付けておきます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

この値を取得するには、Azure CLI または REST API のいずれかを使用してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の要求を行います。このとき、`name` は最初の手順でコピーしたもの (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)、顧客の `name` は 1 つ前の手順でコピーしたもの (```acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) を、それぞれ使用します。

```azurecli
 az billing customer show --expand "enabledAzurePlans,resellers" --account-name "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --name "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

API 応答には、顧客向けのリセラーが一覧表示されます。

```json
{
  "billingProfileDisplayName": "Fabrikam toys Billing Profile",
  "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
  "displayName": "Fabrikam toys",
  "enabledAzurePlans": [
    {
      "skuDescription": "Microsoft Azure Plan",
      "skuId": "0001"
    }
  ],
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resellers": [
    {
      "description": "Wingtip",
      "resellerId": "3xxxxx"
    }
  ],
  "type": "Microsoft.Billing/billingAccounts/customers"
}

```

`description` プロパティを使用して、サブスクリプションに関連付けられているリセラーを特定します。 リセラーの `resellerId` をコピーします。 たとえば、`Wingtip` を関連付けるには、`3xxxxx` をコピーします。 次の手順で使用できるように、値をどこかに貼り付けておきます。

---

## <a name="create-a-subscription-for-a-customer"></a>顧客のサブスクリプションを作成する

次の例では、*Dev Team subscription* という名前のサブスクリプションを *Fabrikam toys* 用に作成し、*Wingtip* リセラーをそのサブスクリプションに関連付けます。 前の手順でコピーした課金スコープ `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` を使用します。 

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>要求本文

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>Response

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

2 番目の手順でコピーしたオプションの *resellerId* を、API の要求本文で渡します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`New-AzSubscriptionAlias` コマンドレットを含む最新バージョンのモジュールをインストールするには、`Install-Module Az.Subscription` を実行します。 PowerShellGet の最新バージョンをインストールするには、[PowerShellGet モジュールの取得](/powershell/scripting/gallery/installing-psget)に関するページを参照してください。

課金スコープ `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` を使用して、次の New-AzSubscriptionAlias コマンドを実行します。 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

コマンドからの応答の一部として、subscriptionId を取得します。

```json
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

2 番目の手順でコピーしたオプションの *resellerId* を、`New-AzSubscriptionAlias` の呼び出しで渡します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、`az extension add --name account` と `az extension add --name alias` を実行して、拡張機能をインストールします。

次の [az account alias create](/cli/azure/account/alias#az_account_alias_create) コマンドを実行します。 

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
```

コマンドからの応答の一部として、subscriptionId を取得します。

```json
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

2 番目の手順でコピーしたオプションの *resellerId* を、`az account alias create` の呼び出しで渡します。

---

## <a name="use-arm-template-or-bicep"></a>ARM テンプレートまたは Bicep を使用する

前のセクションでは、PowerShell、CLI、または REST API を使用してサブスクリプションを作成する方法を説明しました。 サブスクリプションの作成を自動化する必要がある場合は、Azure Resource Manager テンプレート (ARM テンプレート) か [Bicep ファイル](../../azure-resource-manager/bicep/overview.md)の使用をご検討ください。

次の ARM テンプレートを使用すると、サブスクリプションを作成できます。 `billingScope` には、顧客 ID を指定します。 サブスクリプションはルート管理グループに作成されます。 サブスクリプションを作成した後、それを別の管理グループに移動できます。

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
        "value": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
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

## <a name="next-steps"></a>次のステップ

* サブスクリプションを作成し終えたら、他のユーザーおよびサービス プリンシパルでその機能を利用できるようになります。 詳細については、「[Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)](grant-access-to-create-subscription.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法の詳細については、[Azure 管理グループによるリソースの整理](../../governance/management-groups/overview.md)に関する記事を参照してください。
