---
title: Azure アプリケーションからの Azure AD を使用した Azure SignalR Service リソースへの要求を承認する
description: この記事では、Azure アプリケーションからの Azure AD を使用した Azure SignalR Service リソースの要求を承認する方法について説明します
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 2ad3fd5a6a4aa3d002c75558e798e6deedf043df
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716927"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-azure-applications"></a>Azure アプリケーションからの Azure AD を使用した Azure SignalR Service リソースへの要求を承認する

Azure SignalR Service では、[Azure アプリケーション](../active-directory/develop/app-objects-and-service-principals.md)からの Azure Active Directory (Azure AD) 承認要求がサポートされています。

この記事では、SignalR リソースとコードを構成して、Azure アプリケーションから SignalR リソースへの要求を承認する方法について説明します。

## <a name="register-an-application"></a>アプリケーションを登録する

最初の手順では、Azure アプリケーションを登録します。

1. [Azure portal](https://portal.azure.com/) で、 **[Azure Active Directory]** を検索して選びます
2. **[管理]** セクションで、 **[アプリの登録]** を選びます。
3. **[新規登録]** をクリックします。

    ![アプリケーションの登録のスクリーンショット](./media/authenticate/register-an-application.png)

4. アプリケーションの表示 **名** を入力します。
5. **[登録]** をクリックして登録を確定します。

アプリケーションを登録すると、[概要] ページに **アプリケーション (クライアント) ID** と **ディレクトリ (テナント) ID** が表示されます。 これらの GUID は、次の手順で役に立ちます。

![アプリケーションのスクリーンショット](./media/authenticate/application-overview.png)

アプリケーションの登録の詳細については、次を参照してください
- [クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)。


## <a name="add-credentials"></a>資格情報を追加する

証明書とクライアント シークレット (文字列) の両方を資格情報として Confidential クライアント アプリの登録に追加できます。

### <a name="client-secret"></a>クライアント シークレット

アプリケーションでは、トークンを要求するときに ID を証明するためにクライアント シークレットが必要です。 クライアント シークレットを作成するには、次の手順を実行します。

1. **[管理]** セクションで、 **[証明書とシークレット]** を選びます
1. **[クライアント シークレット]** タブで、 **[新しいクライアント シークレット]** をクリックします。
![クライアント シークレットの作成のスクリーンショット](./media/authenticate/new-client-secret.png)
1. クライアント シークレットの **説明** を入力し、**有効期限** を選びます。
1. **クライアント シークレット** の値をコピーし、安全な場所に貼り付けます。 
    > [!NOTE]
    > シークレットは 1 回だけ表示されます。

### <a name="certificate"></a>Certificate

クライアント シークレットを作成する代わりに、証明書をアップロードすることもできます。

![証明書のアップロードのスクリーンショット](./media/authenticate/upload-certificate.png)

資格情報の追加の詳細については、次を参照してください

- [資格情報を追加する](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Azure portal にロールの割り当てを追加する

このサンプルでは、`SignalR App Server` ロールを Azure SignalR Service リソースのサービス プリンシパル (アプリケーション) に割り当てる方法を示します。 

> [!Note]
> ロールは、管理グループ、サブスクリプション、リソース グループ、または 1 つのリソースを含む任意のスコープに割り当てることができます。 スコープの詳細については、「[Azure RBAC のスコープについて](../role-based-access-control/scope-overview.md)」をご覧ください。

1. [Azure portal](https://portal.azure.com/) で、お使いの SignalR リソースに移動します。

1. **[アクセス制御 (IAM)]** をクリックして、Azure SignalR のアクセス制御設定を表示します。

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

   次のスクリーンショットは、SignalR リソースのアクセス制御 (IAM) ページの例を示しています。

   ![アクセスの制御のスクリーンショット](./media/authenticate/access-control.png)

1. **[追加] > [ロールの割り当ての追加]** をクリックします。

1. **[ロール]** タブで `SignalR App Server` を選びます。

1. **[次へ]** をクリックします。
    
   ![ロールの割り当ての追加のスクリーンショット](./media/authenticate/add-role-assignment.png)

1. **[アクセス権の割り当て先]** セクションの **[メンバー]** タブで、 **[ユーザー、グループ、またはサービス プリンシパル]** を選びます。

1. **[メンバーの選択]** をクリックします

3. ロールを割り当てるアプリケーションを検索して選びます。

1. **[選択]** をクリックして選択内容を確定します。

4. **[次へ]** をクリックします。
    
   ![サービス プリンシパルへのロールの割り当てのスクリーンショット](./media/authenticate/assign-role-to-service-principals.png)

5. **[レビューと割り当て]** をクリックして変更内容を確認します。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。

Azure ロールの割り当てを割り当てて管理する方法の詳細については、次の記事を参照してください。
- [Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)
- [REST API を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](../role-based-access-control/role-assignments-template.md)

## <a name="configure-you-app"></a>アプリを構成する

### <a name="app-server"></a>アプリ サーバー

環境変数に ID と資格情報を構成することをお勧めします。

| 変数  | 説明 |
|------|------
| `AZURE_TENANT_ID` | Azure Active Directory のテナント (ディレクトリ) ID。 |
| `AZURE_CLIENT_ID` | テナント内のアプリの登録のクライアント (アプリケーション) ID。 |
| `AZURE_CLIENT_SECRET` | アプリの登録で生成されたクライアント シークレット。 |
| `AZURE_CLIENT_CERTIFICATE_PATH` | PEM または PFX 形式の証明書と秘密キーのペアへのパス。これにより、アプリの登録を認証できます。 |
| `AZURE_USERNAME`  | Azure Active Directory ユーザー アカウントのユーザー名。upn とも呼ばれます。 |
| `AZURE_PASSWORD`  | Azure Active Directory ユーザー アカウントのパスワード。 ただし、MFA が有効になっているアカウントはサポートされません。 |

これにより、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) または [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) のいずれかを使用して、Azure SignalR Service エンドポイントを構成できます。

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new DefaultAzureCredential())
    };
});
```

または、`EnvironmentalCredential` を直接使用します。

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new EnvironmentCredential())
    };
});
```

`DefaultAzureCredential` の動作方法の詳細については、「[DefaultAzureCredential クラス](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)」を参照してください。

#### <a name="use-different-credentials-while-using-multiple-endpoints"></a>複数のエンドポイントを使用する場合は、異なる資格情報を使用します。

理由によっては、エンドポイントごとに異なる資格情報を使用することが必要になる場合があります。

このシナリオでは、[ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) または [ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential) を使用できます。

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    var credential1 = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
    var credential2 = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");

    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), credential1),
        new ServiceEndpoint(new Uri("https://<resource2>.service.signalr.net"), credential2),
    };
});
```

### <a name="azure-functions-signalr-bindings"></a>Azure Functions SignalR のバインド

> [!WARNING]
> SignalR トリガー バインドでは、ID ベースの接続はまだサポートされていません。接続文字列はまだ必要です。

Azure Functions SignalR のバインドでは、ポータルまたは[`local.settings.json`](../azure-functions/functions-develop-local.md#local-settings-file)ローカルの[アプリケーション設定](../azure-functions/functions-how-to-use-azure-function-app-settings.md)を使用して、Azure SignalR Service リソースにアクセスするように Azure アプリケーション ID を構成します。

まず、Azure SignalR Service のサービス URI を指定する必要があります。このサービスのキーは `serviceUri` であり、**接続名のプレフィックス** (既定値は `AzureSignalRConnectionString`) と区切り記号 (Azure portal では `__` であり、local.settings.json ファイル内では `:`) で始まります。 接続名は、binding プロパティ[`ConnectionStringSetting`](../azure-functions/functions-bindings-signalr-service.md)を使用してカスタマイズできます。 引き続きサンプルを参照してください。

次に、[事前定義済みの環境変数](#configure-identity-in-pre-defined-environment-variables)または [Azure SignalR Service によって指定された変数](#configure-identity-in-signalr-specified-variables)で Azure アプリケーションの ID を構成します。

#### <a name="configure-identity-in-pre-defined-environment-variables"></a>事前定義済みの環境変数に ID を構成する

事前定義済みの環境変数の一覧については、「[環境変数](/dotnet/api/overview/azure/identity-readme#environment-variables)」を参照してください。 複数のサービスがある場合は、同じ Azure アプリケーション ID に依存する SignalR が含まれているため、各サービスの ID を構成する必要がありません。 これらの環境変数は、他のサービスの設定によっては、他のサービスによって使用されることもあります。

たとえば、クライアント シークレットの資格情報を使用するには、`local.settings.json` ファイルで次のように構成します。
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "AZURE_CLIENT_ID":"...",
    "AZURE_CLIENT_SECRET":"...",
    "AZURE_TENANT_ID":"..."
  }
}
```
Azure portal で、次のように設定を追加します。
```
 <CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
AZURE_CLIENT_ID = ...
AZURE_TENANT_ID = ...
AZURE_CLIENT_SECRET = ...
 ```

#### <a name="configure-identity-in-signalr-specified-variables"></a>SignalR 指定された変数での ID の構成

指定された Azure SignalR Service 変数は、`serviceUri` キーと同じキー プレフィックスを共有します。 使用する可能性がある変数の一覧を次に示します。
* clientId
* clientSecret
* tenantId

クライアント シークレットの資格情報を使用するサンプルは次のとおりです。

`local.settings.json` ファイルで次の操作を行います。
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "<CONNECTION_NAME_PREFIX>:clientId": "...",
    "<CONNECTION_NAME_PREFIX>:clientSecret": "...",
    "<CONNECTION_NAME_PREFIX>:tenantId": "..."
  }
}
```

Azure portal で、次のように設定を追加します。
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__clientId = ...
<CONNECTION_NAME_PREFIX>__clientSecret = ...
<CONNECTION_NAME_PREFIX>__tenantId = ...
```
## <a name="next-steps"></a>次のステップ

次の関連記事を参照してください。
- [Azure AD for SignalR の概要](signalr-concept-authorize-azure-active-directory.md)
- [マネージド ID からの Azure AD を使用した Azure SignalR Service リソースへの要求を承認する](signalr-howto-authorize-managed-identity.md)