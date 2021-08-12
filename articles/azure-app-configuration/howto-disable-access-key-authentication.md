---
title: Azure App Configuration インスタンスのアクセス キー認証を無効にする (プレビュー)
titleSuffix: Azure App Configuration
description: Azure App Configuration インスタンスのアクセス キー認証を無効にする方法を説明します (プレビュー)
ms.service: azure-app-configuration
author: jimmyca15
ms.author: jimmyca
ms.topic: how-to
ms.date: 5/14/2021
ms.openlocfilehash: 451d9701b62cf46fe81bdd17f4eded63a38d1dce
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483488"
---
# <a name="disable-access-key-authentication-for-an-azure-app-configuration-instance-preview"></a>Azure App Configuration インスタンスのアクセス キー認証を無効にする (プレビュー)

Azure App Configuration リソースに対するすべての要求は、認証されなければなりません。 既定では、Azure Active Directory (Azure AD) の資格情報、またはアクセス キーを使用して、要求を認証することができます。 これら 2 種類の認証スキームでは、Azure AD の方がセキュリティが優れ、アクセス キーより使いやすいので、Microsoft ではそちらをお勧めします。 要求の認証に Azure AD を使用するようクライアントに求めるには、Azure App Configuration リソースのアクセス キーの使用を無効にします。

Azure App Configuration リソースのアクセス キー認証を無効にすると、そのリソースの既存のアクセス キーは削除されます。 既存のアクセス キーを使用したリソースへの以降の要求は拒否されます。 Azure AD を使用して認証された要求のみが成功します。 Azure AD の使用に関する詳細については、「[Azure Active Directory を使用して Azure App Configuration へのアクセスを承認する](./concept-enable-rbac.md)」を参照してください。

## <a name="disable-access-key-authentication"></a>アクセス キー認証を無効にする

アクセス キー認証を無効にすると、すべてのアクセス キーが削除されます。 アクセス キー認証が無効になると、実行中のアプリケーションで認証にアクセス キーが使用されている場合、アクセス キー認証が無効になると失敗し始めます。 アクセス キー認証を再度有効にすると、新しいアクセス キーが生成され、古いアクセス キーを使おうとするアプリケーションは失敗します。

> [!WARNING]
> 現在、アクセス キーを使用して Azure App Configuration リソース内のデータにアクセスしているクライアントがある場合、Microsoft では、アクセス キー認証を無効にする前に、それらのクライアントを [Azure AD](./concept-enable-rbac.md) に移行することをお勧めしています。
> また、以下の「[制限事項](#limitations)」のセクションを読み、制限事項がリソースの使用目的に影響しないことを確認することをお勧めします。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal の Azure App Configuration リソースのアクセス キー認証を禁止するには、以下の手順に従います。

1. Azure portal で Azure App Configuration リソースに移動します。
2. **[設定]** から **[アクセス キー]** 設定を探します。

    :::image type="content" border="true" source="./media/access-keys-blade.png" alt-text="Azure App Configuration のリソース アクセス キー ブレードにアクセスする方法を示すスクリーンショット":::

3. **[アクセス キーを有効化]** トグルを **[無効]** に設定します。

    :::image type="content" border="true" source="./media/disable-access-keys.png" alt-text="Azure App Configuration のアクセス キー認証を無効にする方法を示すスクリーンショット":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してアクセス キー認証を無効にする機能は現在開発中です。

---

### <a name="verify-that-access-key-authentication-is-disabled"></a>アクセス キー認証が無効になっているか確認する

アクセス キー認証が禁止されていることを確認するために、Azure App Configuration リソースのアクセス キーを一覧表示するよう要求することができます。 アクセス キー認証が無効になっている場合、アクセス キーは表示されず、一覧表示の操作を行うと空のリストが返されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal の Azure App Configuration リソースのアクセス キー認証が無効になっていることを確認するには、以下の手順に従います。

1. Azure portal で Azure App Configuration リソースに移動します。
2. **[設定]** から **[アクセス キー]** 設定を探します。

    :::image type="content" border="true" source="./media/access-keys-blade.png" alt-text="Azure App Configuration のリソース アクセス キー ブレードにアクセスする方法を示すスクリーンショット":::

3. アクセスキーが表示されていないこと、および **[アクセス キーを有効化]** が **[無効]** に切り替わっていることを確認します。

    :::image type="content" border="true" source="./media/access-keys-disabled-portal.png" alt-text="Azure App Configuration リソースのアクセス キーが無効になっていることを示すスクリーンショット":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal の Azure App Configuration リソースのアクセス キー認証が無効になっていることを確認するには、以下のコマンドを使用します。 このコマンドは、Azure App Configuration リソースのアクセス キーを一覧表示し、アクセス キー認証が無効な場合は一覧が空になります。

```azurecli-interactive
az appconfig credential list \
    --name <app-configuration-name> \
    --resource-group <resource-group>
```

アクセス キー認証が無効になっている場合は、空の一覧が返されます。

```
C:\Users\User>az appconfig credential list -g <resource-group> -n <app-configuration-name>
[]
```

---

## <a name="permissions-for-allowing-or-disallowing-access-key-authentication"></a>アクセス キー認証を許可または禁止するための権限

Azure App Configuration リソースのアクセス キー認証の状態を変更するには、ユーザーが Azure App Configuration リソースを作成および管理する権限を持っている必要があります。 これらのアクセス許可を提供する Azure ロールベースのアクセス制御 (Azure RBAC) ロールには、**Microsoft.AppConfiguration/configurationStores/write** または **Microsoft.AppConfiguration/configurationStores/\*** アクションが含まれます。 このアクションの組み込みロールには、次のようなロールがあります。

- Azure Resource Manager の[所有者](../role-based-access-control/built-in-roles.md#owner)ロール
- Azure Resource Manager の[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロール

これらのロールでは、Azure Active Directory (Azure AD) を使用して Azure App Configuration リソースのデータにアクセスすることはできません。 ただし、リソースのアクセス キーへのアクセスを許可する **Microsoft.AppConfiguration/configurationStores/listKeys/action** が含まれています。 このアクセス許可では、ユーザーがアクセス キーを使用して、リソース内のすべてのデータにアクセスできます。

ユーザーがリソースのアクセス キー認証を許可または禁止できるようにするには、ロール割り当てのスコープを Azure App Configuration リソース以上のレベルにする必要があります。 ロール スコープの詳細については、「[Azure RBAC のスコープについて](../role-based-access-control/scope-overview.md)」を参照してください。

これらのロールを割り当てる際には、App Configuration リソース作成したり、そのプロパティを更新したりする機能を必要とするユーザーにのみ割り当てるように、注意してください。 最小限の特権の原則を使用して、ユーザーに、それぞれのタスクを実行するのに必要な最小限のアクセス許可を割り当てるようにします。 Azure RBAC でアクセスを管理する方法の詳細については、「[Azure RBAC のベスト プラクティス](../role-based-access-control/best-practices.md)」を参照してください。

> [!NOTE]
> 従来のサブスクリプション管理者ロールであるサービス管理者と共同管理者には、Azure Resource Manager の[所有者](../role-based-access-control/built-in-roles.md#owner)ロールと同等のものが含まれています。 **所有者** ロールにはすべてのアクションが含まれているため、これらの管理者ロールのいずれかを持つユーザーも、App Configuration リソースを作成および管理できます。 詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD 管理者ロール](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)に関する記事を参照してください。

## <a name="limitations"></a>制限事項

アクセス キー認証を無効にする機能は、プレビューとして利用できます。 次の制限事項が現在適用されています。

### <a name="arm-template-access"></a>ARM テンプレート アクセス

アクセスキー認証が無効になっている場合は、[ARM テンプレート](./quickstart-resource-manager.md)内のキー値の読み取りおよび書き込み機能も無効になります。 これは、ARM テンプレートで使用される Microsoft.AppConfiguration/configurationStores/keyValues リソースにアクセスするには、共同作成者や所有者などの Azure Resource Manager ロールが必要であるためです。 アクセス キー認証が無効になっている場合、リソースへのアクセスには Azure App Configuration [データ プレーン ロール](concept-enable-rbac.md)のいずれかが必要であるため、ARM テンプレートへのアクセスは拒否されます。

## <a name="next-steps"></a>次のステップ

- [カスタマー マネージド キーを使用して App Configuration データを暗号化する](concept-customer-managed-keys.md)
- [Azure App Configuration でのプライベート エンドポイントの使用](concept-private-endpoint.md)