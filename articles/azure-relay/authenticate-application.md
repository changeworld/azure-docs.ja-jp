---
title: アプリケーションから認証する - Azure Relay (プレビュー)
description: この記事では、Azure Active Directory を使用して Azure Relay リソースにアクセスするためのアプリケーションを認証する方法について説明します
ms.topic: article
ms.date: 07/02/2021
ms.openlocfilehash: 2304dd58332cb95c40e7492451cf010ee46d76be
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654208"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-relay-entities-preview"></a>Azure Relay エンティティ (プレビュー) にアクセスするために Azure Active Directory を使用してアプリケーションを認証および承認する
Azure Relay は、Azure Active Directory (Azure AD) を使用して、Azure Relay エンティティ (ハイブリッド接続、WCF リレー) への要求を承認することをサポートしています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。 ロールとロールの割り当ての詳細については、[各種ロールの理解](../role-based-access-control/overview.md)に関するページを参照してください。   


[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="authenticate-from-an-app"></a>アプリからの認証
Azure Relay で Azure AD を使用する主な利点は、資格情報をコードに格納する必要がなくなることです。 代わりに、Microsoft ID プラットフォームから OAuth 2.0 アクセス トークンを要求することができます。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Relay への要求を承認できます。

次のセクションでは、Microsoft ID プラットフォーム 2.0 による認証を行うためにコンソール アプリケーションを構成する方法を説明します。 詳細については、「[Microsoft ID プラットフォーム (v2.0) の概要](../active-directory/develop/v2-overview.md)」を参照してください。

OAuth 2.0 コード付与フローの概要については、「[OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../active-directory/develop/v2-oauth2-auth-code-flow.md)」を参照してください。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>アプリケーションを Azure AD テナントに登録する
Azure AD を使用して Azure Relay エンティティを承認する最初の手順は、Azure portal からクライアント アプリケーションを Azure AD テナントに登録することです。 クライアント アプリケーションの登録では、アプリケーションに関する情報を AD に提供します。 これで Azure AD から、アプリケーションを Azure AD ランタイムと関連付ける際に使用できるクライアント ID (アプリケーション ID とも呼ばれます) が提供されます。 

アプリケーションを Azure AD に登録する詳細な手順については、[Azure AD へのアプリケーションの登録のクイックスタート](../active-directory/develop/quickstart-register-app.md#register-an-application)を参照してください。

> [!IMPORTANT]
> **[ディレクトリ (テナント) ID]** と **[アプリケーション (クライアント) ID]** をメモします。 サンプル アプリケーションを実行するためにこれらの値が必要になります。

### <a name="create-a-client-secret"></a>クライアント シークレットの作成   
アプリケーションでは、トークンを要求するときに ID を証明するためにクライアント シークレットが必要です。 上記のリンク先の同じ記事で、「[クライアント シークレットの追加](../active-directory/develop/quickstart-register-app.md#add-a-client-secret)」セクションを参照してクライアント シークレットを作成してください。 

> [!IMPORTANT]
> **[クライアント シークレット]** をメモします。 サンプル アプリケーションを実行するために必要になります。

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てる
目的のスコープ (Azure Relay 名前空間、リソース グループ、サブスクリプション) で、いずれかの Azure Relay ロールをアプリケーションのサービス プリンシパルに割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="run-the-sample"></a>サンプルを実行する

1. [GitHub](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol) からコンソール アプリケーション サンプルをからダウンロードします。
1. [README の記事](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)の指示に従って、コンピューター上のローカルでアプリケーションを実行します。

    > [!NOTE]
    > 上記と同じ手順に従って、[WCF Relay 用のサンプル コンソール アプリケーション](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl)を実行します。 

#### <a name="highlighted-code-from-the-sample"></a>強調表示されたサンプル コード
これは、Azure AD 認証を使用して Azure Relay サービスに接続する方法を示すサンプル コードです。  

1. `TokenProvider.CreateAzureActiveDirectoryTokenProvider` メソッドを使用して、[TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) オブジェクトを作成します。 

    まだアプリ登録を作成していない場合は、「[アプリケーションを Azure AD に登録する](#register-your-application-with-an-azure-ad-tenant)」セクションを参照して作成し、「[クライアント シークレットの作成](#create-a-client-secret)」セクションで説明したようにクライアント シークレットを作成します。

    既存のアプリ登録を使用する場合は、これらの手順に従って **アプリケーション (クライアント) ID** と **ディレクトリ (テナント) ID** を取得します。 

    1. [Azure portal](https://portal.azure.com) にサインインします。
    1. 上部にある検索バーを使用して、**Azure Active Directory** を検索して選択します。
    1. **Azure Active Directory** ページの左メニューの **[管理]** セクションで **[アプリの登録]** を選択します。 
    1. アプリの登録を選択します。 
    1. アプリの登録ページには、 **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** の値が表示されます。 
    
    **クライアント シークレット** を取得するには、次の手順を実行します。
    1. アプリの登録ページで、左メニューの **[証明書とシークレット]** を選択します。 
    1. **[クライアント シークレット]** セクションのシークレットの **[値]** 列のコピー ボタンを使用します。 

    
    ```csharp
    static TokenProvider GetAadTokenProvider(string clientId, string tenantId, string clientSecret)
    {
        return TokenProvider.CreateAzureActiveDirectoryTokenProvider(
            async (audience, authority, state) =>
            {
                IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
                    .WithAuthority(authority)
                    .WithClientSecret(clientSecret)
                    .Build();

                var authResult = await app.AcquireTokenForClient(new [] { $"{audience}/.default" }).ExecuteAsync();
                return authResult.AccessToken;
            },
            $"https://login.microsoftonline.com/{tenantId}");
    }
    ```
1. 前の手順で作成したハイブリッド接続 URI およびトークン プロバイダーを渡して、[HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_) または [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider)) オブジェクトを作成します。

    **リスナー:**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    **送信者:**
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>次のステップ
- Azure RBAC の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。
- Azure PowerShell、Azure CLI、または REST API で Azure ロールを割り当てて管理する方法については、次の記事を参照してください。
    - [Azure PowerShell を使用して Azure でのロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-cli.md)
    - [REST API を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-template.md)

Azure Relay の詳細については、次のトピックを参照してください。
- [リレーとは](relay-what-is-it.md)
- [Azure Relay ハイブリッド接続 WebSockets の概要](relay-hybrid-connections-dotnet-get-started.md)
- [Azure Relay ハイブリッド接続 HTTP 要求の概要](relay-hybrid-connections-http-requests-dotnet-get-started.md)








