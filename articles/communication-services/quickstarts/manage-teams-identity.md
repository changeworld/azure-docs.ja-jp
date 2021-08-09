---
title: Teams アクセス トークンを設定および作成する
titleSuffix: An Azure Communication Services quickstart
description: Teams アクセス トークンを提供するサービスの構築
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e9e58659cfaa5b459a28278362aac002a1a87db5
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113223862"
---
# <a name="quickstart-set-up-and-manage-teams-access-tokens"></a>クイックスタート: Teams アクセス トークンを設定して管理する

> [!IMPORTANT]
> カスタム Teams エンドポイント エクスペリエンスを有効または無効にするには、[このフォーム](https://forms.office.com/r/B8p5KqCH19)に入力します。

このクイックスタートでは、MSAL ライブラリを使用して AAD ユーザー トークンを認証するための .NET コンソール アプリケーションを作成します。 次に、Azure Communication Services Identity SDK を使用して、そのトークンを Teams アクセス トークンと交換します。 その後、Teams アクセス トークンを Azure Communication Services Calling SDK で使用して、カスタム Teams エンドポイントを構築できます。

> [!NOTE]
> 運用環境では、交換の要求がシークレットで署名されるため、この交換メカニズムをバックエンド サービスに実装することをお勧めします。


## <a name="prerequisites"></a>前提条件
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](./create-communication-resource.md)。
- [このフォーム](https://forms.office.com/r/B8p5KqCH19)を使用して、カスタム Teams エンドポイント エクスペリエンスを有効にする
- Azure Active Directory と Teams ライセンスを持つユーザー

## <a name="introduction"></a>はじめに

Teams ID は Azure Active Directory のテナントにバインドされます。 アプリケーションは、同じ、または任意のテナントのユーザーが使用できます。 このクイックスタートでは、複数のアクター (架空の企業である Contoso および Fabrikam のユーザー、開発者、管理者) を含むマルチテナントのユース ケースについて説明します。 このユース ケースでは、Contoso は Fabrikam 用の SaaS ソリューションを構築する企業です。 

次のセクションでは、管理者、開発者、およびユーザー向けの手順について説明します。 この図は、マルチテナントのユース ケースを示しています。 単一テナントで作業する場合は、単一テナントで Contoso と Fabrikam のすべての手順を行います。

## <a name="admin-actions"></a>管理者の操作

管理者ロールには AAD での拡張アクセス許可があります。 このロールのメンバーはリソースをプロビジョニングし、Azure portal から情報を読み取ることができます。 次の図では、管理者によって実行される必要があるすべての操作を確認できます。

![カスタム Teams エンドポイント エクスペリエンスを有効にするための管理者の操作](./media/teams-identities/teams-identity-admin-overview.png)

1. Contoso の管理者は、Azure Active Directory で既存の ''*アプリケーション*'' を作成または選択します。 プロパティ *[Supported account types]\(サポートされているアカウントの種類\)* では、異なるテナントのユーザーが ''*アプリケーション*'' に対して認証できるかどうかを定義します。 プロパティ *[リダイレクト URI]* では、Contoso の ''*サーバー*'' に成功した認証要求をリダイレクトします。
1. Contoso の管理者は、Azure Communication Services の VoIP アクセス許可を使用して、''*アプリケーション*'' のマニフェストを拡張します。 
1. Contoso の管理者は、''*アプリケーション*'' に対してパブリック クライアント フローを許可します
1. Contoso の管理者は必要に応じて更新することができます
1. Contoso の管理者は、[このフォーム](https://forms.office.com/r/B8p5KqCH19)を使用してエクスペリエンスを有効にします
1. Contoso の管理者は既存の Communication Services を作成するか選択します。これは交換要求の認証に使用されます。 AAD ユーザー トークンは Teams アクセス トークンと交換されます。 Azure Communication Services の新しいリソースの作成の詳細については、[こちら](./create-communication-resource.md)を参照してください。
1. Fabrikam の管理者は、Fabrikam のテナントで Azure Communication Services 用の新しいサービス プリンシパルをプロビジョニングします
1. Fabrikam の管理者は、Azure Communication Services VoIP アクセス許可を Contoso の ''*アプリケーション*'' に付与します。 この手順は、Contoso の ''*アプリケーション*'' が検証されていない場合にのみ必要です。

### <a name="1-create-aad-application-registration-or-select-aad-application"></a>1. AAD アプリケーションの登録を作成するか、AAD アプリケーションを選択する 

ユーザーは、Azure Communication Services の `VoIP` アクセス許可を持つ AAD アプリケーションに対して認証される必要があります。 このクイックスタートで使用する既存のアプリケーションがない場合は、新しいアプリケーション登録を作成できます。 

次のアプリケーション設定がエクスペリエンスに影響します。
- プロパティ *[Supported account types]\(サポートされているアカウントの種類\)* では、''*アプリケーション*'' がシングル テナント ([この組織のディレクトリ内のアカウントのみ]) であるか、マルチテナント ([任意の組織のディレクトリ内のアカウント]) であるかを定義します。 このシナリオでは、マルチテナントを使用できます。
- *[リダイレクト URL]* では、認証後に認証要求がリダイレクトされる URI を定義します。 このシナリオでは、[パブリック クライアント/ネイティブ (モバイルとデスクトップ)] を使用して、URI として「 http://localhost 」を入力できます。

[こちらの詳細なドキュメントをご覧ください](/azure/active-directory/develop/quickstart-register-app#register-an-application)。 

''*アプリケーション*'' が登録されると、概要に識別子が表示されます。 この識別子は以下の手順で使用されます: **アプリケーション (クライアント) ID**。

### <a name="2-allow-public-client-flows"></a>2. パブリック クライアント フローを許可する

ご利用の ''*アプリケーション*'' の *[認証]* ペインには、 *[パブリック クライアント/ネイティブ (モバイルとデスクトップ)]* に構成されたプラットフォームが *localhost* を指すリダイレクト URI と共に表示されます。 画面の下部に、 *[パブリック クライアント フローを許可する]* のトグルがあります。このクイックスタートでは、 **[はい]** に設定されます。

### <a name="3-update-publisher-domain-optional"></a>3. パブリッシャー ドメインを更新する (省略可能)
*[ブランド]* ペインで、''*アプリケーション*'' のパブリッシャー ドメインを更新することができます。 これは、アプリケーションが Azure によって検証済みとしてマークされるマルチテナント アプリケーションの場合に便利です。 パブリッシャーの検証方法と、アプリケーションのドメインを更新する方法の詳細については、[こちら](/azure/active-directory/develop/howto-configure-publisher-domain)を参照してください。

### <a name="4-define-azure-communication-services-voip-permission-in-application"></a>4. アプリケーションでの Azure Communication Services の VoIP アクセス許可を定義する

''*アプリケーション*'' の詳細に移動し、[マニフェスト] ペインを選択します。 そのマニフェストで、 *"requiredResourceAccess"* というプロパティを見つけます。 これは、''*アプリケーションの*'' アクセス許可を定義するオブジェクトの配列です。 ファースト パーティ アプリケーション Azure Communication Services の VoIP アクセス許可を使用して、マニフェストを拡張します。 次のオブジェクトを配列に追加します。

> [!NOTE] 
> アプリケーションとアクセス許可を一意に識別するため、スニペット内の GUID を変更しないでください。

```json
{
   "resourceAppId": "1fd5118e-2576-4263-8130-9503064c837a",
   "resourceAccess": [
      {
         "id": "31f1efa3-6f54-4008-ac59-1bf1f0ff9958",
         "type": "Scope"
      }
   ]
}
```

次に、 *[保存]* ボタンを選択して変更を保持します。 *[API のアクセス許可]* ペインに Azure Communication Services - VoIP アクセス許可が表示されるようになりました。

### <a name="5-enable-custom-teams-endpoint-experience-for-application"></a>5. ''*アプリケーション*'' に対してカスタム Teams エンドポイント エクスペリエンスを有効にする

AAD 管理者は次の [フォーム](https://forms.office.com/r/B8p5KqCH19)に入力し、''*アプリケーション*'' に対してカスタム Teams エンドポイント エクスペリエンスを有効にします。

### <a name="6-create-or-select-communication-services-resource"></a>6. Communication Services リソースを作成または選択する

Azure Communication Services リソースは、AAD ユーザー トークンを Teams アクセス トークンと交換するためのすべての要求の認証に使用されます。 この交換は、アクセス キーまたは Azure RBAC で認証されている Azure Communication Services Identity SDK を使用してトリガーできます。 Azure portal でアクセス キーを取得したり、 *[アクセス制御 (IAM)]* ペインで Azure RBAC を構成したりできます。

新しい Communication Services リソースを作成する場合は、[こちらのガイド](./create-communication-resource.md)に従ってください。

### <a name="7-provision-azure-communication-services-service-principal"></a>7. Azure Communication Services のサービス プリンシパルをプロビジョニングする

Fabrikam のテナントでカスタム Teams エンドポイント エクスペリエンスを有効にするには、Fabrikam の AAD 管理者は、アプリケーション ID: *1fd5118e-2576-4263-8130-9503064c837a* を使用して Azure Communication Services という名前のサービス プリンシパルをプロビジョニングする必要があります。 Azure Active Directory の [エンタープライズ アプリケーション] ペインにこのアプリケーションが表示されない場合は、手動で追加する必要があります。

Fabrikam の AAD 管理者は、PowerShell を介して Azure のテナントに接続します。 

> [!NOTE]
> [Tenant_ID] は、Azure portal の AAD の概要ページにあるテナントの ID に置き換えてください。

```azurepowershell
Connect-AzureAD -TenantId "[Tenant_ID]"
```

コマンドが見つからない場合は、AzureAD モジュールが PowerShell にインストールされていません。 PowerShell を閉じ、管理者権限で実行します。 その後、次のコマンドを使用して Azure AD パッケージをインストールできます。

```azurepowershell
Install-Module AzureAD
```

Azure に接続して認証した後、次のコマンドを実行して Communication Services のサービス プリンシパルをプロビジョニングします。 

> [!NOTE]
> パラメーター AppId は、ファースト パーティ アプリケーション Azure Communication Services を示します。 この値は変更しないでください。

```azurepowershell
New-AzureADServicePrincipal -AppId "1fd5118e-2576-4263-8130-9503064c837a"
```

### <a name="8-provide-admin-consent"></a>8. 管理者の同意を付与する

Contoso の ''*アプリケーション*'' が検証されていない場合、AAD 管理者は、Azure Communication Services の VoIP アクセス許可を得るために Contoso の ''*アプリケーション*'' にアクセス許可を付与する必要があります。 Fabrikam の AAD 管理者は、一意のリンクを使用して同意します。 管理者の同意リンクを作成するには、次の手順に従います。

1. この *https://login.microsoftonline.com/{Tenant_ID}/adminconsent?client_id={Application_ID}* リンクを使用します
1. {Tenant_ID} を Fabrikam のテナント ID に置き換えます
1. {Application_ID} を Contoso のアプリケーション ID に置き換えます
1. Fabrikam の AAD 管理者がブラウザーのリンクに移動します。 
1. Fabrikam の AAD 管理者が組織を代表してログインし、アクセス許可を付与します

同意が得られた場合は、Fabrikam のテナントにおける Contoso の ''*アプリケーション*'' のサービス プリンシパルが作成されます。 Fabrikam の管理者は AAD で同意を確認できます。

1. 管理者として Azure portal にサインインします
1. Azure Active Directory に移動します
1. [エンタープライズ アプリケーション] ペインに移動します
1. フィルターの [アプリケーションの種類] を [すべてのアプリケーション] に設定します
1. アプリケーションをフィルター処理するフィールドに、Contoso のアプリケーションの名前を挿入します
1. [適用] を選択して結果をフィルター処理します
1. 必要な名前のサービス プリンシパルを選択します 
1. *[アクセス許可]* ペインに移動します

Azure Communication Services の VoIP アクセス許可の状態が *[{Directory_name} に付与されました]* になりました。

## <a name="developer-actions"></a>開発者の操作

Contoso の開発者は、ユーザーの認証用に ''*クライアント アプリケーション*'' を設定する必要があります。 次に開発者は、リダイレクト後に AAD ユーザー トークンを処理するために、バックエンド ''*サーバー*'' にエンドポイントを作成する必要があります。 AAD ユーザー トークンが受信されると、Teams アクセス トークンと交換され、''*クライアント アプリケーション*'' に返されます。 開発者に必要な操作を次の図に示します。

![カスタム Teams エンドポイント エクスペリエンスを有効にするための開発者の操作](./media/teams-identities/teams-identity-developer-overview.png)

1. Contoso の開発者は、Azure Communication Services VoIP アクセス許可を得るために管理者によって前の手順で作成された ''*アプリケーション*'' のユーザーを認証するように MSAL ライブラリを構成します
1. Contoso の開発者は ACS Identity SDK を初期化し、SDK を使用して受信 AAD ユーザー トークンを Teams のアクセス トークンと交換します。 その後、Teams のアクセス トークンが ''*クライアント アプリケーション*'' に返されます。

Microsoft Authentication Library (MSAL) を使用すると、ユーザーを認証し、セキュリティで保護された Web API にアクセスするために、開発者は Microsoft ID プラットフォーム エンドポイントから AAD ユーザー トークンを取得できます。 これは、Azure Communication Services へのセキュリティで保護されたアクセスを提供するために使用できます。 MSAL は、.NET、JavaScript、Java、Python、Android、iOS などの、さまざまなアプリケーション アーキテクチャとプラットフォームをサポートします。

さまざまな環境を設定する方法の詳細については、パブリック ドキュメント 「[Microsoft Authentication Library (MSAL) の概要](/azure/active-directory/develop/msal-overview)」を参照してください。

> [!NOTE]
> 以下のセクションでは、.NET でコンソール アプリケーションの Teams アクセス トークンと AAD アクセス トークンを交換する方法について説明します。

### <a name="create-new-application"></a>新しいアプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、dotnet new コマンドを使用し、*TeamsAccessTokensQuickstart* という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。*Program.cs*。

```console
dotnet new console -o TeamsAccessTokensQuickstart
```

新しく作成したアプリ フォルダーにディレクトリを変更し、dotnet build コマンドを使用して自分のアプリケーションをコンパイルします。

```console
cd TeamsAccessTokensQuickstart
dotnet build
```
#### <a name="install-the-package"></a>パッケージをインストールする
引き続きアプリケーション ディレクトリで、dotnet add package コマンドを使用して、.NET 用の Azure Communication Services ID ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Communication.Identity
dotnet add package Microsoft.Identity.Client
```

#### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

- テキスト エディターで、Program.cs ファイルを開きます
- using ディレクティブを追加して、次の名前空間を含めます。 
    - Azure.Communication
    - Azure.Communication.Identity
    - Microsoft.Identity.Client
- 非同期コードをサポートするように Main メソッドの宣言を更新します

次のコードを使用して開始します。

```csharp
using System;
using System.Text;
using Azure.Communication;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace TeamsAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services – Teams access tokens quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="1-receive-aad-user-token-via-msal-library"></a>1. MSAL ライブラリを使用して AAD ユーザー トークンを受け取る

MSAL ライブラリを使用し、Azure Communication Services VoIP アクセス許可を持つ Contoso の ''*アプリケーション*'' の AAD に対してユーザーを認証します。 パブリック クラウド (''*パラメーター authority*'') で Contoso の ''*アプリケーション*'' (''*パラメーター applicationId*'') 用にクライアントを構成します。 AAD ユーザー トークンは、リダイレクト URI (''*パラメーター redirectUri*'') に返されます。 資格情報は、既定のブラウザーで開く対話型のポップアップ ウィンドウから取得されます。

> [!NOTE] 
> リダイレクト URI は、''*アプリケーション*'' で定義されている値と一致する必要があります。 リダイレクト URI を構成する方法については、管理者ガイドの最初の手順を確認してください。

```csharp
const string applicationId = "Contoso's_Application_ID";
const string authority = "https://login.microsoftonline.com/common";
const string redirectUri = "http://localhost";

var client = PublicClientApplicationBuilder
                .Create(applicationId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

const string scope = "https://auth.msft.communication.azure.com/VoIP";

var aadUserToken = await client.AcquireTokenInteractive(new[] { scope }).ExecuteAsync();

Console.WriteLine("\nAuthenticated user: " + aadUserToken.Account.Username);
Console.WriteLine("AAD user token expires on: " + aadUserToken.ExpiresOn);
```

変数 *aadUserToken* には、交換に使用される有効な Azure Active Directory ユーザー トークンが格納されるようになりました。

### <a name="2-exchange-aad-user-token-for-teams-access-token"></a>2. AAD ユーザー トークンを Teams アクセス トークンと交換する

有効な AAD ユーザー トークンで、Azure Communication Services の VoIP アクセス許可を持つサード パーティ アプリケーション用の AAD に対してユーザーを認証します。 次のコードでは ACS Identity SDK が使用されています。これにより、AAD ユーザー トークンの Teams アクセス トークンへの交換が容易になります。

> [!NOTE]
> 値 "&lt; Connection-String&gt;" を有効な接続文字列に置き換えるか、認証に Azure RBAC を使用します。 詳細については、[こちらのクイックスタート](./access-tokens.md)を参照してください。

```csharp
var identityClient = new CommunicationIdentityClient("<Connection-String>");
var teamsAccessToken = identityClient.ExchangeTeamsToken(aadUserToken.AccessToken);

Console.WriteLine("\nTeams access token expires on: " + teamsAccessToken.Value.ExpiresOn);
```

要件で定義されている条件がすべて満たされている場合は、有効な Teams アクセス トークンが 24 時間有効になります。

#### <a name="run-the-code"></a>コードの実行
dotnet run コマンドを使用して、アプリケーション ディレクトリからアプリケーションを実行します。

```console
dotnet run
```

アプリの出力に、完了した各アクションが表示されます。

```console
Azure Communication Services - Teams access tokens quickstart

Authenticated user: john.smith@contoso.com
AAD user token expires on: 6/10/2021 10:13:17 AM +00:00

Teams access token expires on: 6/11/2021 9:13:18 AM +00:00
```

## <a name="user-actions"></a>ユーザー操作

ユーザーは、Contoso の ''*アプリケーション*'' の Fabrikam のユーザーを表します。 ユーザー エクスペリエンスを次の図に示します。

![カスタム Teams エンドポイント エクスペリエンスを有効にするためのユーザーの操作](./media/teams-identities/teams-identity-user-overview.png)

1. Fabrikam のユーザーは Contoso の ''*クライアント アプリケーション*'' を使用し、認証するように求められます。
1. Contoso の ''*クライアント アプリケーション*'' では MSAL ライブラリを使用して、Azure Communication Services の VoIP アクセス許可を持つ Contoso の ''*アプリケーション*'' 用の Fabrikam の Azure Active Directory テナントに対してユーザーを認証します。 
1. 認証は、MSAL と Contoso の ''*アプリケーション*'' のプロパティ *[リダイレクト URI]* で定義されている ''*サーバー*'' にリダイレクトされます
1. Contoso の ''*サーバー*'' では、ACS Identity SDK を使用して Teams のアクセス トークンと AAD ユーザー トークンを交換し、Teams のアクセス トークンを ''*クライアント アプリケーション*'' に返します。

''*クライアント アプリケーション*'' で有効な Teams のアクセス トークンを使用すると、開発者は ACS Calling SDK を統合し、カスタム Teams エンドポイントを構築できます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * Azure Active Directory でアプリケーションを作成して構成する
> * MSAL ライブラリを使用して Azure Active Directory ユーザー トークンを発行する
> * ACS Identity SDK を使用して、Azure Active Directory ユーザー トークンを Teams アクセス トークンと交換する

次のドキュメントもご覧ください。

- カスタム Teams エンドポイントの詳細については、[こちら](../concepts/teams-endpoint.md)を参照してください
- Teams の相互運用性の詳細については、[こちら](../concepts/teams-interop.md)を参照してください
