---
title: Azure Enterprise サブスクリプションをプログラムで作成 | Microsoft Docs
description: 追加の Azure Enterprise または Enterprise Dev/Test サブスクリプションをプログラムで作成する方法について説明します。
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/18/2018
ms.author: jlian
ms.openlocfilehash: 8d495bf89697a5e14ff79953ab98f241ef8972e8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Azure Enterprise サブスクリプションをプログラムで作成する (プレビュー)

[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) を契約されているお客様の場合、EA (MS-AZR-0017P) および EA Dev/Test (MS-AZR-0148P) サブスクリプションをプログラミングによって作成できます。 別のユーザーまたはサービス プリンシパルが、お客様のアカウントに課金されるサブスクリプションを作成することを許可するには、お客様の登録アカウントへの[ロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) アクセス権限を付与します。 

> [!IMPORTANT]
> この API を使用して作成された Azure サブスクリプションには、お客様が Microsoft または正規の販売代理店と締結した Microsoft Azure サービス契約が適用されます。 詳細については、「[Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal/)」を参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure Resource Manager を使用してプログラムからサブスクリプションを作成する方法について説明します。
> * EA アカウントに課金されるサブスクリプションを作成する機能を、RBAC を使用して共有する方法について説明します。

[GitHub の .NET サンプル コード](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)もあわせてご覧ください。

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>アカウント所有者として追加してもらうよう EA 登録管理者に依頼する

まず、登録管理者に依頼して、[EA ポータルを使用する (ログインが必要) アカウントの所有者として自分を追加してもらいます](https://ea.azure.com/helpdocs/addNewAccount)。 受信した登録手続きのメールに記載されている指示に従って、初期サブスクリプションを手動で作成します。

> [!IMPORTANT]
> 次の手順に進む前に、アカウント所有権を確認し、初期 EA サブスクリプションを手動で作成する必要があります。 登録にアカウントを追加するだけでは不十分です。

## <a name="find-accounts-you-have-access-to"></a>アクセスできるアカウントを検索します。

アカウント所有者として Azure EA 登録に追加されると、アカウントと登録の関係を使用してサブスクリプション料金の請求先が決定されます。 サブスクリプションを作成するには、まず、自分がアクセスできる登録アカウントを検索します。 現時点で EA アカウント所有者であるユーザーが、この API を使用しようとすると、次の条件が照合されます。

- EA 登録にアカウントが追加されている。
- 1 つ以上の EA または EA Dev/Test サブスクリプションがある。つまり、最低 1 回は手動でサインアップされた。
- サブスクリプションが既定で作成されるディレクトリである、アカウント所有者の*ホーム ディレクトリ*にログインしている。

上記の 2 つの条件を満たしている場合、`enrollmentAccount` リソースは返却され、そのアカウント内でサブスクリプションの作成操作を始めることができます。 このアカウント内で作成されたすべてのサブスクリプションは、アカウントが含まれる EA 登録に課金されます。

# <a name="resttabrest"></a>[REST](#tab/rest)

すべての登録アカウントを一覧表示することを要求する:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

自分がアクセスできるすべての登録アカウントの一覧が Azure から返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileOnboardingEng@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileBackendEng@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

[Get-AzureRmEnrollmentAccount command](/powershell/module/azurerm.billing/get-azurermenrollmentaccount)を使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

アカウントのオブジェクト ID とメール アドレスの一覧が Azure から返されます。

```azurepowershell
ObjectId                               | PrincipalName
<enrollmentAccountId>   | MobileOnboardingEng@contoso.com
<enrollmentAccountId>   | MobileBackendEng@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) コマンドを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。

```azurecli-interactive 
az billing enrollment-account list
```
アカウントのオブジェクト ID とメール アドレスの一覧が Azure から返されます。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileOnboardingEng@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileBackendEng@contoso.com"
      }
    }
  ]
}
```

---

`principalName` プロパティを使用して、サブスクリプション料金の請求先アカウントを指定します。 次の手順で、`id` を `enrollmentAccount` 値として使用して、サブスクリプションを作成します。

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>特定の登録アカウントの中にサブスクリプションを作成する 

次の例では、*開発チーム サブスクリプション*という名前で、サブスクリプション プラン *MS-AZR-0017P* (正規 EA) のサブスクリプションを作成する要求を作成します。 登録アカウントは `<enrollmentAccountId>` で、これは MobileOnboardingEng@contoso.com の登録アカウントです。また、必要に応じて RBAC 所有者として 2 人のユーザーをサブスクリプションに追加することもできます。

# <a name="resttabrest"></a>[REST](#tab/rest)

サブスクリプションを作成する要求のパスに、`enrollmentAccount` の`id` を使用します。

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| 要素名  | 必須 | type   | [説明]                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | いいえ       | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `offerType`   | [はい]      | String | サブスクリプションのプランです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `owners`      | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |

監視対象の `subscriptionOperation` オブジェクトが返されます。 サブスクリプションの作成が完了すると、`subscriptionOperation` オブジェクトから、サブスクリプション ID を持つ `subscriptionLink` オブジェクトが返されます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

このプレビュー モジュールを使用するには、まず `Install-Module AzureRM.Subscription -AllowPrerelease` を実行してモジュールをインストールします。 `-AllowPrerelease` が正しく動作することを確認するには、[Get PowerShellGet Module](/powershell/gallery/psget/get_psget_module) から最新バージョンの PowerShellGet をインストールします。

新しいサブスクリプションを作成するには、`enrollmentAccount` 名を パラメーター `EnrollmentAccountObjectId` に設定し、[New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) を使用します。 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountId> -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| 要素名  | 必須 | type   | [説明]                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | いいえ       | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `OfferType`   | [はい]      | String | サブスクリプションのプランです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `OwnerObjectId`      | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |
| `OwnerSignInName`    | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーの、メール アドレス。 `OwnerObjectId` の代わりにこのパラメーターを使用することができます。|
| `OwnerApplicationId` | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するサービス プリンシパルのアプリケーション ID。 `OwnerObjectId` の代わりにこのパラメーターを使用することができます。| 

すべてのパラメーターの完全な一覧については、[New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) を参照してください。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

このプレビュー拡張機能を使用するには、まず `az extension add --name subscription` を実行して拡張機能をインストールします。

新しいサブスクリプションを作成するには、`enrollmentAccount` 名をパラメーター `enrollment_account_name` に設定し、[az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) を使用します。

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-name "<enrollmentAccountId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 要素名  | 必須 | type   | [説明]                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | いいえ       | String | サブスクリプションの表示名です。 指定されていない場合は、"Microsoft Azure エンタープライズ" などのプラン名に設定されます。                                 |
| `offer-type`   | [はい]      | String | サブスクリプションのプランです。 EA では、[MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (運用環境用) と [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開発/テスト用。[EA ポータルを使用してオンに設定する必要があります](https://ea.azure.com/helpdocs/DevOrTestOffer)) の 2 つのオプションがあります。                |
| `owner-object-id`      | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーのオブジェクト ID。  |
| `owner-upn`    | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するユーザーの、メール アドレス。 `owner-object-id` の代わりにこのパラメーターを使用することができます。|
| `owner-spn` | いいえ        | String | サブスクリプションの作成時にサブスクリプションに RBAC 所有者として追加するサービス プリンシパルのアプリケーション ID。 `owner-object-id` の代わりにこのパラメーターを使用することができます。| 

すべてのパラメーターの完全な一覧については、[az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) を参照してください。

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>RBAC を使用して登録アカウントへのアクセスを委任する

特定のアカウントに対してサブスクリプションを作成する権限を別のユーザーまたはサービス プリンシパルに付与するには、[登録アカウントのスコープで RBAC 所有者のロールを付与](../active-directory/role-based-access-control-manage-access-rest.md)します。 次の例では、`<userObjectId>` の `principalId` (MobileOnboardingEng@contoso.com の) のテナントであるユーザーに 登録アカウントの所有者ロールを付与します。 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
登録アカウントのスコープで所有者ロールが付与されると、ロール割り当ての情報が Azure から返されます。

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

登録アカウントへの所有者アクセス権限を他のユーザーに付与するには、[New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) を使用します。

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

登録アカウントへの所有者アクセス権限を他のユーザーに付与するには、[az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) を使用します。

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>
```

----

ユーザーが、登録アカウントの RBAC 所有者になると、そのアカウント内にサブスクリプションをプログラミングによって作成できます。 委任されたユーザーによって作成されたサブスクリプションには、元のアカウント所有者がサービス管理者として設定されたままになりますが、既定で委任されたユーザーも所有者として設定されています。 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>アクティビティ ログを使用してサブスクリプションを作成した監査

この API を使用して作成されたサブスクリプションを追跡するには、[Tenant Activity Log API](/rest/api/monitor/tenantactivitylogs) を使用します。 現時点では、PowerShell、CLI、または Azure Portal を使用してサブスクリプションの作成を追跡することはできません。

1. Azure AD テナントのテナント管理者は、[アクセス権限を昇格](../active-directory/role-based-access-control-tenant-admin-access.md)してから、スコープ内の監査ユーザーに閲覧者ロールを割り当てます`/providers/microsoft.insights/eventtypes/management`。
1. 監査ユーザーは、[Tenant Activity Log API](/rest/api/monitor/tenantactivitylogs) を呼び出して、サブスクリプションの作成活動を確認します。 例:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> コマンドラインからこの API を簡単に呼び出すには、[ARMClient](https://github.com/projectkudu/ARMClient) を使用します。

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure Enterprise のサブスクリプション作成 API の制限事項

- この API を使用して作成できるのは、Azure Enterprise サブスクリプションのみです。
- アカウントあたり作成できるサブスクリプションの上限数は 50 です。 50 個を超えるサブスクリプションは、アカウント センターを使用してのみ作成できます。
- アカウント内に 1 つ以上の EA または EA Dev/Test サブスクリプションがある必要があり、つまり、アカウント所有者が 1 回以上手動でサインアップしている必要があります。
- アカウント所有者ではないが、RBAC 経由で登録アカウントに追加されたユーザーは、アカウント センターを使用してサブスクリプションを作成できません。
- サブスクリプションが作成されるテナントを選択することはできません。 サブスクリプションは常に、アカウント所有者のホーム テナント内に作成されます。 サブスクリプションを別のテナントに移動する場合は、[テナントのサブスクリプションの変更](..\active-directory\active-directory-how-subscriptions-associated-directory.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* .NET を使用してサブスクリプションを作成する例については、[GitHub のサンプル コード](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)を参照してください。
* Azure Resource Manager とその API については、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法については、「[Azure 管理グループのリソースを整理する](management-groups-overview.md)」を参照してください。
* 大規模組織でのサブスクリプション管理に関する包括的なベスト プラクティス ガイダンスについては、「[Azure エンタープライズ スキャフォールディング - 規範的なサブスクリプション ガバナンス](resource-manager-subscription-governance.md)」を参照してください。
