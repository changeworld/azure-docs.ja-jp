---
title: Azure アプリケーションからの Web PubSub リソースへの要求を Azure AD を使用して承認する
description: この記事では、Azure アプリケーションからの Azure AD を使用した Web PubSub リソースの要求を承認する方法について説明します
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 7bca3e4840cf78b497f5907e780a267221dd1b8b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477941"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-azure-applications"></a>Azure アプリケーションからの Web PubSub リソースへの要求を Azure AD を使用して承認する

Azure Web PubSub サービスでは、[Azure アプリケーション](../active-directory/develop/app-objects-and-service-principals.md)からの Azure Active Directory (Azure AD) 承認要求がサポートされています。 

この記事では、Azure アプリケーションからの Web PubSub リソースへの要求を承認するように Web PubSub リソースとコードを構成する方法について説明します。

## <a name="register-an-application"></a>アプリケーションを登録する

最初の手順では、Azure アプリケーションを登録します。

1. [Azure portal](https://portal.azure.com/) で、 **[Azure Active Directory]** を検索して選びます
2. **[管理]** セクションで、 **[アプリの登録]** を選びます。
3. **[新規登録]** をクリックします。

    ![アプリケーションの登録のスクリーンショット](./media/aad-authorization/register-an-application.png)

4. アプリケーションの表示 **名** を入力します。
5. **[登録]** をクリックして登録を確定します。

アプリケーションを登録すると、[概要] ページに **アプリケーション (クライアント) ID** と **ディレクトリ (テナント) ID** が表示されます。 これらの GUID は、次の手順で役に立ちます。

![アプリケーションのスクリーンショット](./media/aad-authorization/application-overview.png)

アプリケーションの登録の詳細については、次を参照してください
- [クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)。

## <a name="add-credentials"></a>資格情報を追加する

証明書とクライアント シークレット (文字列) の両方を資格情報として Confidential クライアント アプリの登録に追加できます。

### <a name="client-secret"></a>クライアント シークレット

アプリケーションでは、トークンを要求するときに ID を証明するためにクライアント シークレットが必要です。 クライアント シークレットを作成するには、次の手順を実行します。

1. **[管理]** セクションで、 **[証明書とシークレット]** を選びます
1. **[クライアント シークレット]** タブで、 **[新しいクライアント シークレット]** をクリックします。
![クライアント シークレットの作成のスクリーンショット](./media/aad-authorization/new-client-secret.png)
1. クライアント シークレットの **説明** を入力し、**有効期限** を選びます。
1. **クライアント シークレット** の値をコピーし、安全な場所に貼り付けます。 
    > [!NOTE]
    > シークレットは 1 回だけ表示されます。
### <a name="certificate"></a>Certificate

クライアント シークレットを作成する代わりに、証明書をアップロードすることもできます。

![証明書のアップロードのスクリーンショット](./media/aad-authorization/upload-certificate.png)

資格情報の追加の詳細については、次を参照してください

- [資格情報を追加する](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Azure portal にロールの割り当てを追加する

このサンプルでは、`Web PubSub Service Owner` ロールを Web PubSub リソースのサービス プリンシパル (アプリケーション) に割り当てる方法を示します。 

> [!Note]
> ロールは、管理グループ、サブスクリプション、リソース グループ、または 1 つのリソースを含む任意のスコープに割り当てることができます。 スコープの詳細については、「[Azure RBAC のスコープについて](../role-based-access-control/scope-overview.md)」をご覧ください
1. [Azure portal](https://portal.azure.com/) 上で、お使いの Web PubSub リソースに移動します。

1. **[アクセス制御 (IAM)]** をクリックして、Azure Web PubSub のアクセス制御設定を表示します。

1. **[ロールの割り当て]** タブをクリックして、このスコープのロールの割り当てを表示します。

   次のスクリーンショットは、Web PubSub リソースの [アクセス制御 (IAM)] ページの例を示しています。

   ![アクセスの制御のスクリーンショット](./media/aad-authorization/access-control.png)

1. **[追加] > [ロールの割り当ての追加]** をクリックします。

1. **[ロール]** タブで `Web PubSub App Server` を選びます。

1. **[次へ]** をクリックします。

   ![ロールの割り当ての追加のスクリーンショット](./media/aad-authorization/add-role-assignment.png)

1. **[アクセス権の割り当て先]** セクションの **[メンバー]** タブで、 **[ユーザー、グループ、またはサービス プリンシパル]** を選びます。

1. **[メンバーの選択]** をクリックします

3. ロールを割り当てるアプリケーションを検索して選びます。

1. **[選択]** をクリックして選択内容を確定します。

4. **[次へ]** をクリックします。

   ![サービス プリンシパルへのロールの割り当てのスクリーンショット](./media/aad-authorization/assign-role-to-service-principals.png)

5. **[レビューと割り当て]** をクリックして変更内容を確認します。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。
Azure ロールの割り当てを割り当てて管理する方法の詳細については、次の記事を参照してください。
- [Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)
- [REST API を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-server"></a>サーバーを構成する

環境変数に ID と資格情報を構成することをお勧めします。

| 変数  | 説明 |
|------|------
| `AZURE_TENANT_ID` | Azure Active Directory のテナント (ディレクトリ) ID。 |
| `AZURE_CLIENT_ID` | テナント内のアプリの登録のクライアント (アプリケーション) ID。 |
| `AZURE_CLIENT_SECRET` | アプリの登録で生成されたクライアント シークレット。 |
| `AZURE_CLIENT_CERTIFICATE_PATH` | PEM または PFX 形式の証明書と秘密キーのペアへのパス。これにより、アプリの登録を認証できます。 |
| `AZURE_USERNAME`  | Azure Active Directory ユーザー アカウントのユーザー名。upn とも呼ばれます。 |
| `AZURE_PASSWORD`  | Azure Active Directory ユーザー アカウントのパスワード。 MFA が有効になっているアカウントはサポートされないことにご注意ください。 |

これにより、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) または [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) のいずれかを使用して、Web PubSub エンドポイントを構成できます。

### <a name="sample-codes"></a>サンプル コード

これらは C# 用のサンプル コードです。 その他のサポートされる言語については、JavaScript/Python/Java をご覧ください。

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new DefaultAzureCredential());
```

`DefaultAzureCredential` の動作方法の詳細については、「[DefaultAzureCredential クラス](/dotnet/api/azure.identity.defaultazurecredential)」を参照してください。

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new EnvironmentCredential());
```

必要に応じて、[ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) または [ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential) を直接使用することもできます。

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var credential = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
var client = new WebPubSubServiceClient(endpoint, "hub", credential);
```
```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var credential = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");
var client = new WebPubSubServiceClient(endpoint, "hub", credential);
```

Azure AD 承認用の `TokenCredential` の作成について詳しくは、これらの記事をご覧ください。

- [DefaultAzureCredential クラス](/dotnet/api/azure.identity.defaultazurecredential)
- [ClientSecretCredential コンストラクター](/dotnet/api/azure.identity.clientsecretcredential.-ctor)
- [ClientCertificateCredential コンストラクター](/dotnet/api/azure.identity.clientcertificatecredential.-ctor)

## <a name="next-steps"></a>次のステップ

次の関連記事を参照してください。
- [Azure AD for Web PubSub の概要](concept-azure-ad-authorization.md)
- [マネージド ID からの Web PubSub リソースへの要求を Azure AD を使用して承認する](howto-authorize-from-managed-identity.md)