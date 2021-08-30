---
title: Azure Relay リソースのマネージド ID を使用した認証 (プレビュー)
description: ここでは、マネージド ID を使用して Azure Relay リソースにアクセスする方法について説明します。
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: fa82f41056f3a5a8617bab572b8b4f312861419f
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654224"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-relay-resources-preview"></a>Azure Relay リソースにアクセスするために Azure Active Directory を使用してマネージド ID を認証する (プレビュー)
[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、デプロイに関連付けられ、その下でアプリケーション コードが実行されるセキュリティ保護された ID を作成できる Azure 間機能です。 この ID は、アプリケーションに必要な特定の Azure リソースにアクセスするためのカスタム アクセス許可を付与するアクセス制御ロールに関連付けることができます。

マネージド ID では、Azure プラットフォームがこのランタイム ID を管理します。 ID 自体やアクセスする必要のあるリソース用に、アクセス キーをアプリケーション コードや構成に保存して保護する必要はありません。 Azure リソース サポートに対してマネージド エンティティが有効にされている Azure App Service アプリケーション内または仮想マシンで実行されている Relay クライアント アプリは、SAS ルールと SAS キーや、その他のアクセス トークンを処理する必要はありません。 クライアント アプリに必要なのは、Relay 名前空間のエンドポイント アドレスだけです。 アプリが接続すると、Relay はこの記事で後述する例に示す操作で、マネージド エンティティのコンテキストをクライアントにバインドします。 マネージド ID に関連付けられると、Relay クライアントは承認済みのすべての操作を実行できます。 マネージド エンティティを Relay のロールに関連付けることによって承認が付与されます。

[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="enable-managed-identity"></a>マネージド ID の有効化
最初に、Azure Relay エンティティ (ハイブリッド接続または WCF リレー) にアクセスする必要のある Azure リソースのマネージド ID を有効にします。 たとえば、Relay クライアント アプリケーションが Azure VM で実行されている場合、「[Azure VM のマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)」の記事の手順に従って、VM のマネージド ID を有効にします。 この設定を有効にすると、新しいマネージド サービス ID が Azure Active Directory (Azure AD) に作成されます。

マネージド ID をサポートするサービスの一覧については、「[Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)」を参照してください。

## <a name="assign-an-azure-relay-role-to-the-managed-identity"></a>マネージド ID に Azure Relay ロールを割り当てる
マネージド ID を有効にした後、適切なスコープの ID に Azure Relay ロール (Azure Relay 所有者、Azure Relay リスナー、または Azure Relay 送信者) のいずれかを割り当てます。 Azure ロールがマネージド ID に割り当てられると、適切なスコープで Relay エンティティへのアクセス権がそのマネージド ID に付与されます。

次のセクションでは、Azure VM インスタンスのマネージド ID で実行され、Relay リソースにアクセスする単純なアプリケーションを使用します。

## <a name="sample-app-on-vm-accessing-relay-entities"></a>Relay エンティティにアクセスする VM 上のサンプル アプリ

1. [ハイブリッド接続サンプル コンソール アプリケーション](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol)を GitHub からコンピューターにダウンロードします。
1. [Azure VM を作成します](../virtual-machines/windows/quick-create-portal.md)。 このサンプルでは、Windows 10 のイメージを使用します。 
1. Azure VM 用のシステムによって割り当てられた ID、またはユーザーによって割り当てられた ID を有効にします。 手順については、「[VM の ID を有効にする](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)」を参照してください。 
1. 目的のスコープ (Relay エンティティ、Relay 名前空間、リソース グループ、サブスクリプション) で、いずれかの Relay ロールをマネージド サービス ID に割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。
1. [README ドキュメント](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)の手順に従って、ローカル コンピューター上にコンソール アプリをローカルにビルドします。 
1. \<your local path\>\RoleBasedAccessControl\bin\Debug フォルダーにある実行可能ファイルを VM にコピーします。 RDP を使用して、Azure VM に接続できます。 詳細については、「[Windows が実行されている Azure 仮想マシンに接続してサインオンする方法](../virtual-machines/windows/connect-logon.md)」を参照してください。
1. [README ドキュメント](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)の手順に従って、Azure VM で RoleBasedAccessControl.exe を実行します。 

    > [!NOTE]
    > 上記と同じ手順に従って、[WCF Relay 用のコンソール アプリケーション](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl)を実行します。

#### <a name="highlighted-code-from-the-sample"></a>強調表示されたサンプル コード
これは、Azure AD 認証を使用して Azure Relay サービスに接続する方法を示すサンプル コードです。  

1. `TokenProvider.CreateManagedIdentityTokenProvider` メソッドを使用して、[TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) オブジェクトを作成します。 
    
    - **システムによって割り当てられたマネージド ID** を使用している場合:
        ```csharp
        TokenProvider.CreateManagedIdentityTokenProvider();
        ```
    - **ユーザーによって割り当てられたマネージド ID** を使用している場合、Azure Portal の **[マネージド ID]** ページから、ユーザーによって割り当てられた ID の **クライアント ID** を取得します。 詳細については、「[ユーザー割り当てマネージド ID を一覧表示する](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md?pivots=identity-mi-methods-azp#list-user-assigned-managed-identities)」を参照してください。
        ```csharp
        var managedCredential = new ManagedIdentityCredential(clientId);
        tokenProvider = TokenProvider.CreateManagedIdentityTokenProvider(managedCredential);    
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
Azure Relay の詳細については、次のトピックを参照してください。
- [リレーとは](relay-what-is-it.md)
- [Azure Relay ハイブリッド接続 WebSockets の概要](relay-hybrid-connections-dotnet-get-started.md)
- [Azure Relay ハイブリッド接続 HTTP 要求の概要](relay-hybrid-connections-http-requests-dotnet-get-started.md)



