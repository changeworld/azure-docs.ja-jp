---
title: マネージド ID からの Azure AD を使用した Web PubSub リソースへの要求を承認する
description: この記事では、マネージド ID からの Azure AD を使用した Web PubSub リソースへの要求を承認する方法について説明します
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 8df789836bd0067868d4f9e6d90e640473a96f33
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477903"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-managed-identities"></a>マネージド ID からの Azure AD を使用した Web PubSub リソースへの要求を承認する
Azure Web PubSub サービスでは、[Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) からの Azure Active Directory (Azure AD) 承認要求がサポートされています。 

この記事では、マネージド ID からの Web PubSub リソースへの要求を承認するように Web PubSub リソースとコードを構成する方法について説明します。

## <a name="configure-managed-identities"></a>マネージド ID の構成

最初の手順では、マネージド ID を構成します。

この例は、Azure portal を使用して `System-assigned managed identity` を `Virtual Machine` で構成するところを示しています。

1. [Azure portal](https://portal.azure.com/) を開き、仮想マシンを検索して選択します。
1. **[設定]** セクションの下で、 **[ID]** を選択します。
1. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えます。
   ![仮想マシンのスクリーンショット - [ID]](./media/aad-authorization/identity-virtual-machine.png)
1. **[保存]** ボタンをクリックして変更を確定します。

### <a name="how-to-create-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を作成する方法
- [ユーザー割り当てマネージド ID を作成する](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

### <a name="how-to-configure-managed-identities-on-other-platforms"></a>他のプラットフォームでマネージド ID を構成する方法

- [Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [テンプレートを使用して Azure VM で Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK で Azure リソースのマネージド ID を使用して VM を構成する](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="how-to-configure-managed-identities-for-app-service-and-azure-functions"></a>App Service と Azure Functions 用のマネージド ID を構成する方法

- [App Service と Azure Functions でマネージド ID を使用する方法](../app-service/overview-managed-identity.md)。

## <a name="add-role-assignments-on-azure-portal"></a>Azure portal でロールの割り当てを追加する  

このサンプルでは、Web PubSub リソースのシステム割り当て ID に `Web PubSub Service Owner` ロールを割り当てる方法を示します。 

> [!Note]
> ロールは、管理グループ、サブスクリプション、リソース グループ、または 1 つのリソースを含む任意のスコープに割り当てることができます。 スコープの詳細については、「[Azure RBAC のスコープについて](../role-based-access-control/scope-overview.md)」を参照してください
1. [Azure portal](https://portal.azure.com/) を開き、お使いの Web PubSub リソースに移動します。

1. **[アクセス制御 (IAM)]** をクリックして、Azure Web PubSub のアクセス制御設定を表示します。

   リソース グループの [アクセス制御 (IAM)] ページの例を次に示します。

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

   次のスクリーンショットは、Web PubSub リソースの [アクセス制御 (IAM)] ページの例を示しています。

   ![アクセスの制御のスクリーンショット](./media/aad-authorization/access-control.png)

1. **[追加] > [ロールの割り当ての追加]** をクリックします。

1. **[ロール]** タブで `Web PubSub Service Owner` を選択します。

1. **[次へ]** をクリックします。

   ![ロールの割り当ての追加のスクリーンショット](./media/aad-authorization/add-role-assignment.png)

1. **[メンバー]** タブの **[アクセス権の割り当て先]** セクションで、 **[マネージド ID]** を選択します。

1. **[メンバーの選択]** をクリックします。

1. **[マネージド ID の選択]** ペインで、 **[システム割り当てマネージド ID] > [仮想マシン]** を選択します

1. ロールを割り当てる仮想マシンを検索して選択します。

1. **[選択]** をクリックして選択内容を確定します。

2. **[次へ]** をクリックします。

   ![マネージド ID にロールを割り当てる方法のスクリーンショット](./media/aad-authorization/assign-role-to-managed-identities.png)

3. **[レビューと割り当て]** をクリックして変更を確定します。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。
Azure ロールを割り当てる方法および割り当てを管理する方法の詳細については、これらの記事を参照してください。
- [Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)
- [REST API を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](../role-based-access-control/role-assignments-template.md)

## <a name="sample-codes-while-configuring-your-server"></a>サーバーの構成中のサンプル コード

### <a name="using-system-assigned-identity"></a>システム割り当て ID を使用する

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) または [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) のいずれかを使用して、システム割り当て ID を使用しているときに Web PubSub エンドポイントを構成できます。

ただし、ベスト プラクティスは、`ManagedIdentityCredential` を直接使用する方法です。

既定ではシステム割り当てマネージド ID が使用されますが、`DefaultAzureCredential` を使用している場合は、[EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) によって保持される **環境変数が構成されていないことを確認してください**。 そうでないと、`EnvironmentCredential` を使用して要求を行うようにフォールバックし、ほとんどの場合、`401 Unauthorized` 応答が返される結果になります。

C# のサンプル コードを示します。

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new ManagedIdentityCredential());
```

サポートされている他の言語のサンプルもあります ([Java]()、[JavaScript]()、[Python]() に関する記事を参照してください)。

### <a name="using-user-assigned-identity"></a>ユーザー割り当て ID を使用する

`ManagedIdentityCredential` オブジェクトの作成中に、`ClientId` を指定するだけです。

> [!IMPORTANT]
> 似ているように見えても、オブジェクト (プリンシパル) ID ではなく、**クライアント ID** を使用してください。

C# のサンプル コードを示します。

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var clientId = "<your user-assigned identity client id>";
var client = new WebPubSubServiceClient(endpoint, "hub", new ManagedIdentityCredential(clientId));
```

サポートされている他の言語のサンプルもあります ([Java]()、[JavaScript]()、[Python]() に関する記事を参照してください)。

## <a name="next-steps"></a>次のステップ

次の関連記事を参照してください。
- [Azure AD for Web PubSub の概要](concept-azure-ad-authorization.md)
- [Azure アプリケーションからの Azure AD を使用した Web PubSub リソースへの要求を承認する](howto-authorize-from-application.md)