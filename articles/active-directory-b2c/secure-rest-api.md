---
title: Azure AD B2C で API コネクタとして使用される API をセキュリティで保護する
titleSuffix: Azure AD B2C
description: Azure AD B2C で API コネクタとして使用されるカスタム RESTful API をセキュリティで保護します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a738459e50ed87dbfbdc97838d70f049d998eca5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326806"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-b2c"></a>Azure AD B2C で API コネクタとして使用される API をセキュリティで保護する 

Azure AD B2C ユーザー フロー内で REST API を統合する場合は、認証を使用して REST API エンドポイントを保護する必要があります。 REST API 認証により、Azure AD B2C などの適切な資格情報を持つサービスだけが REST API エンドポイントを呼び出すことができます。 この記事では、REST API をセキュリティで保護する方法について説明します。 


## <a name="prerequisites"></a>必須コンポーネント

チュートリアル「[API コネクタをサインアップ ユーザー フローに追加する](add-api-connector.md)」ガイドの手順を完了している。

::: zone pivot="b2c-user-flow"

API エンドポイントを保護するには、HTTP 基本認証または HTTPS クライアント証明書認証を使用します。 どちらの場合も、API エンドポイントを呼び出すときに Azure AD B2C によって使用される資格情報を指定します。 次に、API エンドポイントは資格情報を確認し、承認の決定を行います。

::: zone-end

## <a name="http-basic-authentication"></a>HTTP 基本認証

HTTP 基本認証は [RFC 2617](https://tools.ietf.org/html/rfc2617) で定義されています。 基本認証は、次のように動作します。Azure AD B2C によって、クライアントの資格情報 (`username` と `password`) が `Authorization` ヘッダーに含まれた HTTP 要求が送信されます。 資格情報は、Base64 でエンコードされた文字列 `username:password` として書式設定されます。 その後、お使いの API によってこれらの値がチェックされ、他の承認決定が実行されます。

::: zone pivot="b2c-user-flow"

HTTP 基本認証を使用して API コネクタを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Azure サービス] で** **、B2C Azure AD選択するか、B2C** を検索 **Azure AD選択します**。
3. **[API コネクタ]** を選択し、構成する **[API コネクタ]** を選択します。
4. **[認証の種類]** で、 **[基本]** を選択します。
5. REST API エンドポイントの **[ユーザー名]** と **[パスワード]** を指定します。
    :::image type="content" source="media/add-api-connector/api-connector-config.png" alt-text="API コネクタの基本的な認証構成を提供する。":::
6. **[保存]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-rest-api-username-and-password-policy-keys"></a>REST API ユーザー名とパスワードのポリシーのキーを追加する

HTTP 基本認証を使用して REST API の技術プロファイルを構成するには、ユーザー名とパスワードを格納するために次の暗号化キーを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** には **[手動]** を選択します。
1. **[名前]** に「**RestApiUsername**」と入力します。
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。
1. **[秘密]** ボックスに、REST API ユーザー名を入力します。
1. **[キー使用法]** には **[暗号化]** を選択します。
1. **［作成］** を選択します
1. **[ポリシー キー]** を再度選択します。
1. **[追加]** を選択します。
1. **[オプション]** には **[手動]** を選択します。
1. **[名前]** に「**RestApiPassword**」と入力します。
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。
1. **[秘密]** ボックスに、REST API パスワードを入力します。
1. **[キー使用法]** には **[暗号化]** を選択します。
1. **［作成］** を選択します

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>HTTP 基本認証を使用するように REST API の技術プロファイルを構成する

必要なキーを作成した後、資格情報を参照するように REST API の技術プロファイル メタデータを構成します。

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
1. REST API の技術プロファイルを検索します。 例: `REST-ValidateProfile`、または `REST-GetProfile`。
1. `<Metadata>` 要素を見つけます。
1. *AuthenticationType* を `Basic` に変更します。
1. *AllowInsecureAuthInProduction* を `false` に変更します。
1. `</Metadata>` 要素の終了直後に、次の XML スニペットを追加します。
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

HTTP 基本認証を使用して構成された RESTful 技術プロファイルの例を次の XML スニペットに示します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="https-client-certificate-authentication"></a>HTTPS クライアント証明書認証

クライアント証明書の認証は証明書ベースの相互認証であり、クライアントである Azure AD B2C がクライアント証明書をサーバーに提供し、その ID を証明します。 これは、SSL ハンドシェイクの一部として発生します。 お使いの API によって、証明書が有効なクライアント (Azure AD B2C など) に属していることが確認され、承認の決定が実行されます。 クライアント証明書は、X.509 デジタル証明書です。 

> [!IMPORTANT]
> 運用環境では、この証明書は証明機関によって署名されている必要があります。

### <a name="create-a-certificate"></a>証明書を作成する

#### <a name="option-1-use-azure-key-vault-recommended"></a>オプション 1: Azure Key Vault を使用する (推奨)

証明書を作成するには、[Azure Key Vault](../key-vault/certificates/create-certificate.md) を使用できます。これには、自己署名証明書のオプションと、署名された証明書の証明書発行者プロバイダーとの統合があります。 推奨設定には次が含まれます。
- **[サブジェクト]** : `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **[コンテンツの種類]** : `PKCS #12`
- **[有効期間のアクション タイプ]** : `Email all contacts at a given percentage lifetime` または `Email all contacts a given number of days before expiry`
- **[キーの種類]** : `RSA`
- **[キー サイズ]** : `2048`
- **エクスポート可能な秘密キー**: `Yes` (`.pfx` ファイルをエクスポートできるようにするため)

これで、[証明書をエクスポート](../key-vault/certificates/how-to-export-certificate.md)できます。

#### <a name="option-2-prepare-a-self-signed-certificate-using-powershell-module"></a>オプション 2: PowerShell モジュールを使用して自己署名証明書を準備する

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

::: zone pivot="b2c-user-flow"

### <a name="configure-your-api-connector"></a>API コネクタを構成する

クライアント証明書認証を使用して API コネクタを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。
3. **[API コネクタ]** を選択し、構成する **[API コネクタ]** を選択します。
4. **[認証の種類]** で **[証明書]** を選択します。
5. **[証明書のアップロード]** ボックスで、秘密キーを備えた証明書の .pfx ファイルを選択します。
6. **[パスワードの入力]** ボックスに、証明書のパスワードを入力します。
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="API コネクタの証明書認証構成を提供する。":::
7. **[保存]** を選択します。

### <a name="perform-authorization-decisions"></a>承認の決定を実行する 
API エンドポイントを保護するために、API では、送信されたクライアント証明書に基づいて承認が実装される必要があります。 Azure App Service と Azure Functions については、[TLS 相互認証の構成](../app-service/app-service-web-configure-tls-mutual-auth.md)に関するページで、*API コードから証明書を有効化および検証* する方法をご覧ください。  または、Azure API Management をすべての API サービスの前のレイヤーとして使用して、[クライアント証明書のプロパティを目的の値と照合する](
../api-management/api-management-howto-mutual-certificates-for-clients.md)こともできます。

### <a name="renewing-certificates"></a>証明書を書き換える
証明書の有効期限がまもなく切れることを知らせるリマインダー アラートを設定することをお勧めします。 使用されている証明書の有効期限が迫っている場合は、新しい証明書を生成し、上の手順を繰り返す必要があります。 新しい証明書の使用を "ロール" するために、新しい証明書がデプロイされている間、一時的に、お使いの API サービスで引き続き古い証明書と新しい証明書を受け入れることができます。 

既存の API コネクタに新しい証明書をアップロードするには、 **[API コネクタ]** で API コネクタを選択し、 **[新しい証明書のアップロード]** をクリックします。 直近でアップロードされ、開始日を過ぎていて有効期限が切れていない証明書が、Azure AD B2C によって自動的に使用されます。

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text="API コネクタが既に存在する場合は、このコネクタに新しい証明書を提供します。":::

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-a-client-certificate-policy-key"></a>クライアント証明書ポリシー キーを追加する

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** ボックスで、 **[アップロード]** を選択します。
1. **[名前]** ボックスに「**RestApiClientCertificate**」と入力します。
    プレフィックス *B2C_1A_* が自動的に追加されます。
1. **[ファイルのアップロード]** ボックスで、秘密キーを備えた証明書の .pfx ファイルを選択します。
1. **[パスワード]** ボックスに、証明書のパスワードを入力します。
1. **［作成］** を選択します

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>クライアント証明書認証を使用するように REST API の技術プロファイルを構成する

必要なキーを作成した後、クライアント証明書を参照するように REST API の技術プロファイル メタデータを構成します。

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
1. REST API の技術プロファイルを検索します。 例: `REST-ValidateProfile`、または `REST-GetProfile`。
1. `<Metadata>` 要素を見つけます。
1. *AuthenticationType* を `ClientCertificate` に変更します。
1. *AllowInsecureAuthInProduction* を `false` に変更します。
1. `</Metadata>` 要素の終了直後に、次の XML スニペットを追加します。
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

HTTP クライアント証明書を使用して構成された RESTful 技術プロファイルの例を次の XML スニペットに示します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 ベアラー認証 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

ベアラー トークン認証の定義については、「[OAuth 2.0 Authorization Framework:Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。 ベアラー トークン認証では、Azure AD B2C は、Authorization ヘッダーにトークンを含む HTTP 要求を送信します。

```http
Authorization: Bearer <token>
```

ベアラー トークンは、不透明な文字列です。 これは、JWT アクセス トークン、または REST API が Azure AD B2C によって Authorization ヘッダーで送信されることを想定する任意の文字列です。 Azure AD B2C では、次の種類がサポートされています。

- **ベアラー トークン**。 ベアラー トークンを Restful 技術プロファイルで送信できるようにするには、ポリシーでベアラー トークンを取得してから、それを RESTful 技術プロファイルで使用する必要があります。  
- **静的ベアラー トークン**。 REST API が長期アクセス トークンを発行する場合に、このアプローチを使用します。 静的ベアラー トークンを使用するには、ポリシー キーを作成し、RESTful 技術プロファイルからポリシー キーへの参照を作成します。 


## <a name="using-oauth2-bearer"></a>OAuth2 ベアラーの使用  

次の手順では、クライアント資格情報を使用してベアラー トークンを取得し、それを REST API 呼び出しの Authorization ヘッダーに渡す方法について説明します。  

### <a name="define-a-claim-to-store-the-bearer-token"></a>ベアラー トークンを格納する要求を定義する

要求は、Azure AD B2C ポリシーの実行時に、データの一時的なストレージとなります。 [要求スキーマ](claimsschema.md)は、要求を宣言する場所です。 アクセス トークンは、後で使用するために、要求に格納する必要があります。 

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. 次の要求を **ClaimsSchema** 要素に追加します。  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>アクセス トークンの取得 

アクセス トークンは、[フェデレーション ID プロバイダーから](idp-pass-through-user-flow.md)取得する方法、アクセス トークンを返す REST API を呼び出す方法、[ROPC フロー](../active-directory/develop/v2-oauth-ropc.md)を使用する方法、または[クライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)を使用する方法のいずれかで取得できます。 クライアント資格情報フローは、一般的にバックグラウンドでの実行が必要なサーバー間の相互作用に使用され、ユーザーとの即時の相互動作は必要ありません。

#### <a name="acquiring-an-azure-ad-access-token"></a>Azure AD アクセス トークンの取得 

次の例では、REST API の技術プロファイルを使用し、HTTP 基本認証として渡されたクライアント資格情報を使用して Azure AD トークン エンドポイントに要求を行います。 詳しくは、「[Microsoft ID プラットフォームと OAuth 2.0 クライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)」をご覧ください。 

技術プロファイルがアクセストークンを取得するために Azure AD と対話できるようにするには、アプリケーションを登録する必要があります。 Azure AD B2C は、Azure AD プラットフォームに依存しています。 アプリは、Azure AD B2C テナント、または管理する任意の Azure AD テナントで作成できます。 アプリケーションを登録するには、以下を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページの **[ディレクトリ名]** の一覧で自分の Azure AD または Azure AD B2C ディレクトリを見つけて、 **[切り替え]** を選択します。
1. 左側のメニューで、 **[Azure Active Directory]** を選択します。 または、 **[すべてのサービス]** を選択してから、 **[Azure Active Directory]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*Client_Credentials_Auth_app* のように入力します。
1. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
1. **[登録]** を選択します。
1. **[アプリケーション (クライアント) ID]** を記録します。

クライアント資格情報フローの場合は、アプリケーション シークレットを作成する必要があります。 クライアント シークレットは、"アプリケーション パスワード" とも呼ばれます。 このシークレットは、アクセス トークンを取得するためにアプリケーションによって使用されます。

1. **[Azure AD - アプリの登録]** ページで、作成したアプリケーション (例: *Client_Credentials_Auth_app*) を選択します。
1. 左側のメニューで、 **[管理]** の **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。
1. **[説明]** ボックスにクライアント シークレットの説明を入力します。 たとえば、*clientsecret1* のようにします。
1. **[有効期限]** で、シークレットが有効な期間を選択してから、 **[追加]** を選択します。
1. クライアント アプリケーションのコードで使用できるように、シークレットの **値** を記録します。 このページからの移動後は、このシークレットの値は "二度と表示されません"。 アプリケーションのコード内で、この値をアプリケーション シークレットとして使用します。

#### <a name="create-azure-ad-b2c-policy-keys"></a>Azure AD B2C ポリシー キーの作成

Azure AD B2C テナントで前に記録したクライアント ID およびクライアント シークレットを格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します (`SecureRESTClientId`)。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. **[シークレット]** に、前に記録したクライアント ID を入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **［作成］** を選択します
1. 次の設定で別のポリシー キーを作成します。
    - **[名前]** : `SecureRESTClientSecret`。
    - **[シークレット]** : 前に記録したクライアント シークレットを入力します

ServiceUrl で、your-tenant-name を Azure AD テナントの名前に置き換えます。 使用可能なすべてのオプションについては、[RESTful 技術プロファイル](restful-technical-profile.md)のリファレンスを参照してください。

```xml
<TechnicalProfile Id="REST-AcquireAccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

> [!NOTE]
> その他の技術プロファイルで `grant_type` または `scope` 要求を使用する場合、さらに `DefaultValue` を指定し、`AlwaysUseDefaultValue="true"` を使用することで、正しくない値をバインドして競合が発生する可能性を回避することをお勧めします。

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>ベアラー トークン認証を使用するように REST 技術プロファイルを変更する

カスタム ポリシーでベアラー トークン認証をサポートするには、REST API の技術プロファイルを次のように変更します。

1. 作業ディレクトリで、*TrustFrameworkExtensions.xml* 拡張ポリシー ファイルを開きます。
1. `Id="REST-API-SignUp"` を含む `<TechnicalProfile>` ノードを探します。
1. `<Metadata>` 要素を見つけます。
1. 次のように、*AuthenticationType* を *Bearer* に変更します。
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 次のように、*UseClaimAsBearerToken* を *bearerToken* に変更または追加します。 *bearerToken* は、ベアラー トークンの取得元になる要求の名前 (`REST-AcquireAccessToken` からの出力要求) です。

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 上記で使用した要求を入力要求として追加します。

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

上記のスニペットを追加すると、技術プロファイルは次の XML コードのようになります。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>静的 OAuth2 ベアラーの使用 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>OAuth2 ベアラー トークン ポリシー キーを追加する

OAuth2 ベアラー トークンを使用して REST API の技術プロファイルを構成するには、REST API 所有者からアクセス トークンを取得します。 次に、ベアラー トークンを格納するための次の暗号化キーを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `RestApiBearerToken` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Encryption`] を選択します。
1. **［作成］** を選択します

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>ベアラー トークン ポリシー キーを使用するように REST API の技術プロファイルを構成する

必要なキーを作成した後、ベアラー トークンを参照するように REST API の技術プロファイル メタデータを構成します。

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
1. REST API の技術プロファイルを検索します。 例: `REST-ValidateProfile`、または `REST-GetProfile`。
1. `<Metadata>` 要素を見つけます。
1. *AuthenticationType* を `Bearer` に変更します。
1. *AllowInsecureAuthInProduction* を `false` に変更します。
1. `</Metadata>` 要素の終了直後に、次の XML スニペットを追加します。
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

ベアラー トークン認証を使用して構成された RESTful 技術プロファイルの例を次の XML スニペットに示します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end


## <a name="api-key-authentication"></a>API キー認証

::: zone pivot="b2c-user-flow"

一部のサービスは、"API キー" メカニズムを使用して、呼び出し元に HTTP ヘッダーまたは HTTP クエリ パラメーターとして一意のキーを含めることを要求することにより、開発中に HTTP エンドポイントへのアクセスを難読化します。 [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) に対しては、お使いの API コネクタの **エンドポイント URL** に `code` をクエリ パラメーターとして含めることで、これを実現できます。 たとえば、`https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b> です。 

これは、運用環境で単独で使用する必要があるメカニズムではありません。 そのため、基本認証または証明書認証の構成は常に必要です。 開発上の目的により、いずれの認証方法も実装しない場合 (推奨されません)、API コネクタの構成で "基本" 認証を選択し、適切な承認を実装する間、API が無視できる一時的な値を `username` と `password` に使用します。

::: zone-end

::: zone pivot="b2c-custom-policy"

API キーは、REST API エンドポイントにアクセスするユーザーを認証するために使用される一意の識別子です。 キーはカスタム HTTP ヘッダーで送信されます。 たとえば、[Azure Functions HTTP トリガー](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)は、`x-functions-key` HTTP ヘッダーを使用して要求者を識別します。  

### <a name="add-api-key-policy-keys"></a>API キーのポリシー キーを追加する

API キー認証を使用して REST API の技術プロファイルを構成するには、API キーを格納するために次の暗号化キーを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** には **[手動]** を選択します。
1. **[名前]** に「**RestApiKey**」と入力します。
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。
1. **[秘密]** ボックスに、REST API キーを入力します。
1. **[キー使用法]** には **[暗号化]** を選択します。
1. **［作成］** を選択します


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>API キー認証を使用するように REST API の技術プロファイルを構成する

必要なキーを作成した後、資格情報を参照するように REST API の技術プロファイル メタデータを構成します。

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
1. REST API の技術プロファイルを検索します。 例: `REST-ValidateProfile`、または `REST-GetProfile`。
1. `<Metadata>` 要素を見つけます。
1. *AuthenticationType* を `ApiKeyHeader` に変更します。
1. *AllowInsecureAuthInProduction* を `false` に変更します。
1. `</Metadata>` 要素の終了直後に、次の XML スニペットを追加します。
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

暗号化キーの **ID** によって、HTTP ヘッダーが定義されます。 この例では、API キーは **x-functions-key** として送信されます。

API キー認証で Azure 関数を呼び出すように構成された RESTful 技術プロファイルの例を次の XML スニペットに示します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="next-steps"></a>次のステップ

::: zone pivot="b2c-user-flow"
- [サンプル](api-connector-samples.md#api-connector-rest-api-samples)を使用して作業を開始します。
::: zone-end

::: zone pivot="b2c-custom-policy"
- カスタム ポリシー リファレンスで [Restful 技術プロファイル](restful-technical-profile.md)要素の詳細を確認します。
::: zone-end
