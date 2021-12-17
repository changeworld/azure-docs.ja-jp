---
title: OpenID Connect でのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で任意の OpenID Connect ID プロバイダー (IdP) でのサインアップとサインインを設定します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 688210352385e95c7253ac82d95154eaf707d216
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066347"
---
# <a name="set-up-sign-up-and-sign-in-with-generic-openid-connect-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して汎用 OpenID Connect でのサインアップおよびサインインを設定する

[OpenID Connect](openid-connect.md) は OAuth 2.0 を基盤として作成された認証プロトコルであり、セキュリティで保護されたユーザー サインインに使用できます。 Azure AD B2C では、このプロトコルを使用するほとんどの ID プロバイダーがサポートされています。 

この記事では、カスタム OpenID Connect ID プロバイダーをユーザー フローに追加する方法について説明します。

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="add-the-identity-provider"></a>ID プロバイダーの追加

::: zone pivot="b2c-user-flow"

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Contoso*」と入力します。

::: zone-end

::: zone pivot="b2c-custom-policy"

OpenId Connect ID プロバイダーを定義するには、それをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Login with Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://your-identity-provider.com/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <!-- <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
          </CryptographicKeys> -->
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

::: zone-end

## <a name="configure-the-identity-provider"></a>[ID プロバイダー] を構成します。

どの OpenID Connect ID プロバイダーでも、サインインを実行するために必要な情報の多くを含むメタデータ ドキュメントを記述します。 メタデータ ドキュメントには、使用する URL、サービスの公開署名キーの場所などの情報が含まれます。 OpenID Connect メタデータ ドキュメントは、常に `.well-known/openid-configuration` で終わるエンドポイントに配置されます。 追加する OpenID Connect ID プロバイダーについては、そのメタデータの URL を入力します。

::: zone pivot="b2c-user-flow"

**[メタデータ URL]** には、OpenID Connect メタデータ ドキュメントの URL を入力します。

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="METADATA">` 技術プロファイル メタデータで、OpenID Connect メタデータ ドキュメントの URL を入力します。

::: zone-end

## <a name="client-id-and-secret"></a>クライアントの ID とシークレット

ユーザーのサインインを許可するために、ID プロバイダーは、開発者が自身のサービスにアプリケーションを登録する必要があります。 このアプリケーションには、**クライアント ID** および **クライアント シークレット** と呼ばれる ID があります。 

クライアント シークレットはオプションです。 ただし、[応答の種類](#response-type)が `code` である場合は、クライアント シークレットを指定する必要があります。そのシークレットを使用して、コードがトークンと交換されます。

::: zone pivot="b2c-user-flow"

クライアント ID とクライアント シークレットを追加するには、これらの値を ID プロバイダーからコピーし、対応するフィールドに入力します。

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="client_id">` 技術プロファイル メタデータで、クライアント ID を入力します。

### <a name="create-a-policy-key"></a>ポリシー キーを作成する

クライアント シークレットが必須である場合は、ご利用の Azure AD B2C テナントで前に記録したクライアント シークレットを格納してください。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[ディレクトリ + サブスクリプション]** フィルターを選択します。
3. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
4. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
5. [概要] ページで、 **[Identity Experience Framework]** を選択します。
6. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
7. **オプション** については、`Manual`を選択します。
8. ポリシー キーの **名前** を入力します。 たとえば、「 `ContosoSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
9. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
10. **[キー使用法]** として [`Signature`] を選択します。
11. **Create** をクリックしてください。
12. `CryptographicKeys` XML 要素で、次の要素を追加します。
    
    ```xml
    <CryptographicKeys>
      <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoSecret"/>
    </CryptographicKeys>
    ```

::: zone-end

## <a name="scope"></a>スコープ

スコープは、ID プロバイダーから収集する情報およびアクセス許可、たとえば `openid profile` を定義します。 ID プロバイダーから ID トークンを受け取るには、`openid` スコープを指定する必要があります。 

ID トークンがないと、ユーザーはカスタム ID プロバイダーを使用して Azure AD B2C にサインインすることはできません。 別のスコープをスペースで区切って追加することもできます。 使用可能な他のスコープについては、カスタム ID プロバイダーのドキュメントを参照してください。

::: zone pivot="b2c-user-flow"

**[スコープ]** で、ID プロバイダーからのスコープを入力します。 たとえば、「 `openid profile` 」のように入力します。

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="scope">` 技術プロファイル メタデータで、ID プロバイダーからのスコープを入力します。 たとえば、「 `openid profile` 」のように入力します。

::: zone-end

## <a name="response-type"></a>応答の種類

応答の種類は、最初の呼び出しで、カスタム ID プロバイダーの`authorization_endpoint`に送信される情報の種類を表します。 次の応答の種類を使用できます。

* `code`: [承認コード フロー](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)に従って、Azure AD B2C にコードが返されます。 次に Azure AD B2C が`token_endpoint`を呼び出して、トークンのコードを交換します。
* `id_token`: ID トークンが、カスタム ID プロバイダーから、Azure AD B2C に返されます。

::: zone pivot="b2c-user-flow"

**[応答の種類]** で、自分の ID プロバイダー設定に従って、`code` または `id_token` を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="response_types">` 技術プロファイル メタデータで、自分の ID プロバイダー設定に従って、`code` または `id_token` を選択します。

::: zone-end

## <a name="response-mode"></a>応答モード

応答モードは、カスタム ID プロバイダーから Azure AD B2C へのデータの返送に使用する方法を定義します。 次の応答モードを使用できます。

* `form_post`: 最高のセキュリティを得るには、この応答モードをお勧めします。 この応答は、HTTP の `POST` メソッドによって送信され、本文には、`application/x-www-form-urlencoded` 形式を使用してエンコードされたコードまたはトークンが含まれます。
* `query`: このコードまたはトークンは、クエリ パラメーターとして返されます。

::: zone pivot="b2c-user-flow"

**[応答モード]** で、自分の ID プロバイダー設定に従って、`form_post` または `query` を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Item Key="response_mode">` 技術プロファイル メタデータで、自分の ID プロバイダー設定に従って、`form_post` または `query` を選択します。

::: zone-end

## <a name="domain-hint"></a>ドメインのヒント

[ドメインのヒント](direct-signin.md)を使用して、ユーザーは利用可能な ID プロバイダーのリストから選択するのではなく、指定した ID プロバイダーのサインイン ページに直接スキップできます。 

このような動作を許可にするには、ドメインのヒントの値を入力します。 カスタム ID プロバイダーにジャンプするには、サインインのために Azure AD B2C を呼び出すときに、要求の最後にパラメーター `domain_hint=<domain hint value>` を追加します。

::: zone pivot="b2c-user-flow"

**[ドメイン ヒント]** で、ドメイン ヒントで使用されるドメイン名を入力します。

::: zone-end

::: zone pivot="b2c-custom-policy"

`<Domain>contoso.com</Domain>` 技術プロファイル XML 要素で、ドメイン ヒントで使用されるドメイン名を入力します。 たとえば、「 `contoso.com` 」のように入力します。

::: zone-end

## <a name="claims-mapping"></a>要求のマッピング

カスタム ID プロバイダーが Azure AD B2C に ID トークンを返送した後、Azure AD B2C は、受け取ったトークンからのクレームを Azure AD B2C が認識して使用するクレームにマッピングできる必要があります。 以下の各マッピングについては、カスタム ID プロバイダーのドキュメントを参照して、ID プロバイダーのトークンに返される要求をご理解ください。

::: zone pivot="b2c-user-flow"

* **ユーザー ID**: サインインしたユーザーの "*一意の識別子*" を指定する要求を入力します。
* **表示名**: ユーザーの "*表示名*" または "*フル ネーム*" を指定する要求を入力します。
* **名**: ユーザーの "*名*" を指定する要求を入力します。
* **姓**: ユーザーの "*姓*" を指定する要求を入力します。
* **メール**: ユーザーの "*メール アドレス*" を指定する要求を入力します。


::: zone-end

::: zone pivot="b2c-custom-policy"

`OutputClaims` 要素には、ご利用の ID プロバイダーにより返されるクレームの一覧が含まれます。 ご利用のポリシーに定義されているクレームの名前を、ID プロバイダーで定義されている名前にマップします。 `<OutputClaims>` 要素で、ご利用の ID プロバイダーで定義されているように、対応するクレーム名を使用して `PartnerClaimType` 属性を構成します。 

|ClaimTypeReferenceId  |PartnerClaimType  |
|---------|---------|
| `issuerUserId`| サインインしたユーザーの "*一意の識別子*" を指定する要求を入力します。|
| `displayName`| ユーザーの "*表示名*" または "*フル ネーム*" を指定する要求を入力します。 |
| `givenName`| ユーザーの "*名*" を指定する要求を入力します。|
| `surName`| ユーザーの "*姓*" を指定する要求を入力します。|
| `email`| ユーザーの "*メール アドレス*" を指定する要求を入力します。|
| `identityProvider` | トークン発行者名が指定されているクレームを入力します。 たとえば、「 `iss` 」のように入力します。 ID プロバイダーによってトークンに発行者クレームが含まれていない場合は、ご利用の ID プロバイダーの一意識別子を使用して `DefaultValue` 属性を設定します。 たとえば、「 `DefaultValue="contoso.com"` 」のように入力します。|


::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-the-identity-provider-to-a-user-flow"></a>ユーザー フローに ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. ID プロバイダーを追加するユーザー フローをクリックします。 
1. **[ソーシャル ID プロバイダー]** の下で、追加した ID プロバイダーを選択します。 たとえば、*Contoso* です。
1. **[保存]** を選択します。

## <a name="test-your-user-flow"></a>ユーザー フローをテストする

1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで、サインインする ID プロバイダーを選択します。 たとえば、*Contoso* です。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](tutorial-register-applications.md) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso]** を選択して、Google アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

## <a name="next-steps"></a>次のステップ

詳細については、[OpenId Connect 技術プロファイル](openid-connect-technical-profile.md)のリファレンス ガイドを参照してください。

::: zone-end
