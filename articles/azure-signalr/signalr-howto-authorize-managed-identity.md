---
title: マネージド ID からの Azure AD を使用した Azure SignalR Service リソースへの要求を承認する
description: この記事では、マネージド ID からの Azure AD を使用した Azure SignalR Service リソースの要求を承認する方法について説明します
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: b65dc12c3854a1730f74bf679a724e651985883b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716908"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-managed-identities"></a>マネージド ID からの Azure AD を使用した Azure SignalR Service リソースへの要求を承認する
Azure SignalR Service では、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) からの Azure Active Directory (Azure AD) 承認要求がサポートされています。

この記事では、SignalR リソースとコードを構成して、マネージド ID から SignalR リソースへの要求を承認する方法について説明します。

## <a name="configure-managed-identities"></a>マネージド ID の構成

最初の手順では、マネージド ID を構成します。

この例は、Azure portal を使用して `System-assigned managed identity` を `Virtual Machine` で構成するところを示しています。

1. [Azure portal](https://portal.azure.com/) を開き、仮想マシンを検索して選択します。
1. **[設定]** セクションの下で、 **[ID]** を選択します。
1. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えます。
   ![アプリケーションのスクリーンショット](./media/authenticate/identity-virtual-machine.png)
1. **[保存]** ボタンをクリックして変更を確認します。


ユーザー割り当てマネージド ID を作成する方法については、次の記事を参照してください。
- [ユーザー割り当てマネージド ID を作成する](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

マネージド ID の構成の詳細については、次のいずれかの記事を参照してください。

- [Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [テンプレートを使用して Azure VM で Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK で Azure リソースのマネージド ID を使用して VM を構成する](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-app-service-and-azure-functions"></a>App Service と Azure Functions の場合

「[App Service と Azure Functions でマネージド ID を使用する方法](../app-service/overview-managed-identity.md)」を参照してください。

## <a name="add-role-assignments-on-azure-portal"></a>Azure portal にロールの割り当てを追加する

このサンプルでは、SignalR リソースのシステム割り当て ID に `SignalR App Server` ロールを割り当てる方法を示します。 

> [!Note]
> ロールは、管理グループ、サブスクリプション、リソース グループ、または 1 つのリソースを含む任意のスコープに割り当てることができます。 スコープの詳細については、「[Azure RBAC のスコープについて](../role-based-access-control/scope-overview.md)」をご覧ください。

1. [Azure portal](https://portal.azure.com/) を開き、お使いの SignalR リソースに移動します。

1. **[アクセス制御 (IAM)]** をクリックして、Azure SignalR のアクセス制御設定を表示します。

   リソース グループの [アクセス制御 (IAM)] ページの例を次に示します。

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

   次のスクリーンショットは、SignalR リソースのアクセス制御 (IAM) ページの例を示しています。

   ![アクセスの制御のスクリーンショット](./media/authenticate/access-control.png)

1. **[追加] > [ロールの割り当ての追加]** をクリックします。

1. **[ロール]** タブで `SignalR App Server` を選びます。

1. **[次へ]** をクリックします。

   ![ロールの割り当ての追加のスクリーンショット](./media/authenticate/add-role-assignment.png)

1. **[メンバー]** タブの **[アクセス権の割り当て先]** セクションで、 **[マネージド ID]** を選択します。

1. **[メンバーの選択]** をクリックします。

1. **[マネージド ID の選択]** ペインで、 **[システム割り当てマネージド ID] > [仮想マシン]** を選択します

1. ロールを割り当てる仮想マシンを検索して選択します。

1. **[選択]** をクリックして選択内容を確定します。

2. **[次へ]** をクリックします。

   ![マネージド ID にロールを割り当てるスクリーンショット](./media/authenticate/assign-role-to-managed-identities.png)

3. **[レビューと割り当て]** をクリックして変更内容を確認します。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。

Azure ロールの割り当てを割り当てて管理する方法の詳細については、次の記事を参照してください。
- [Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)
- [REST API を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-app"></a>アプリの構成

### <a name="app-server"></a>App Server

#### <a name="using-system-assigned-identity"></a>システム割り当て ID を使用する

[DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential) または [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) のいずれかを使用して、SignalR エンドポイントを構成できます。


ただし、ベスト プラクティスは、`ManagedIdentityCredential` を直接使用する方法です。

システム割り当てマネージド ID は、既定で使用されますが、`DefaultAzureCredential` を使用している場合は、[EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) が予約する **すべての環境変数を構成しないようにしてください**。 そうしないと、要求を作成するために `EnvironmentCredential` を使用するように戻り、ほとんどの場合、`Unauthorized` 応答が返される結果になります。

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential()),
    };
});
```

#### <a name="using-user-assigned-identity"></a>ユーザー割り当て ID を使用する

`ManagedIdentityCredential` オブジェクトの作成中に、`ClientId` を指定します。

> [!IMPORTANT]
> オブジェクト (プリンシパル) ID ではなく、**クライアント ID** を使用します。両方とも GUID である場合でも同様です。

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        var clientId = "<your identity client id>";
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential(clientId)),
    };
```

### <a name="azure-functions-signalr-bindings"></a>Azure Functions SignalR のバインド

> [!WARNING]
> SignalR トリガー バインドでは、ID ベースの接続はまだサポートされていません。接続文字列はまだ必要です。

Azure Functions SignalR のバインドでは、ポータルまたは[`local.settings.json`](../azure-functions/functions-develop-local.md#local-settings-file)ローカルの[アプリケーション設定](../azure-functions/functions-how-to-use-azure-function-app-settings.md)を使用して、Azure SignalR Service リソースにアクセスするようにマネージド ID を構成します。

ID を構成するには、キーと値のペアのグループが必要な場合があります。 すべてのキーと値のペアのキーは、**接続名プレフィックス** (既定値は `AzureSignalRConnectionString`) と区切り記号 (ポータルで `__`、ローカルで `:`) で始まる必要があります。 プレフィックスは、バインド プロパティ [`ConnectionStringSetting`](../azure-functions/functions-bindings-signalr-service.md) を使用してカスタマイズできます。

#### <a name="using-system-assigned-identity"></a>システム割り当て ID を使用する

サービス URI のみを構成する場合は、`DefaultAzureCredential` が使用されます。 これは、Azure とローカルの開発環境で同じ構成を共有する場合に便利です。 `DefaultAzureCredential` の動作方法については、[DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential) を参照してください。

Azure portal で、次のように設定して `DefaultAzureCredential` を構成します。 [ここに記載されている環境変数](/dotnet/api/overview/azure/identity-readme#environment-variables)を構成しない場合は、システム割り当て ID が認証に使用されます。
```
<CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
```

`local.settings.json` ファイル内の `DefaultAzureCredential` の構成サンプルを次に示します。 ローカルではマネージド ID は使用されず、Visual Studio、Azure CLI、Azure PowerShell アカウントを使用した認証が順番に試行されます。
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net"
  }
}
```

システム割り当て ID を個別に使用し、[他の環境変数](/dotnet/api/overview/azure/identity-readme#environment-variables)の影響を受けずに使用する場合は、接続名プレフィックスを持つ `credential` キーを `managedidentity` に設定する必要があります。 アプリケーション設定のサンプルを次に示します。

```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
```

#### <a name="using-user-assigned-identity"></a>ユーザー割り当て ID を使用する

ユーザー割り当て ID を使用する場合は、システム割り当て ID と比較して、接続名プレフィックスを持つもう 1 つの `clientId` キーを割り当てる必要があります。 アプリケーション設定のサンプルを次に示します。
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
<CONNECTION_NAME_PREFIX>__clientId = <CLIENT_ID>
```
## <a name="next-steps"></a>次のステップ

次の関連記事を参照してください。
- [Azure AD for SignalR の概要](signalr-concept-authorize-azure-active-directory.md)
- [Azure アプリケーションからの Azure AD を使用した Azure SignalR Service リソースへの要求を承認する](signalr-howto-authorize-application.md)