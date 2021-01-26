---
title: LinkedIn アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで LinkedIn アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bde7c1adefea88ed5b5d86e2c0e17f475be1bc71
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654372"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して LinkedIn アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>LinkedIn アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で [ID プロバイダー](authorization-code-flow.md)として LinkedIn アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ LinkedIn アカウントを持っていない場合は、[https://www.linkedin.com/](https://www.linkedin.com/) でサインアップできます。

1. LinkedIn アカウントの資格情報を使用して、[LinkedIn 開発者の Web サイト](https://www.developer.linkedin.com/)にサインインします。
1. **[マイ アプリ]** を選択してから、 **[アプリの作成]** をクリックします。
1. **[アプリ名]** 、 **[LinkedIn ページ]** 、 **[プライバシー ポリシーの URL]** 、 **[アプリのロゴ]** を入力します。
1. LinkedIn **API の使用条件** に同意し、 **[アプリの作成]** をクリックします。
1. **[Auth]\(認証\)** タブを選択します。 **[認証キー]** の下にある **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして LinkedIn を構成するには、両方の値が必要です。 **[Client Secret]** は、重要なセキュリティ資格情報です。
1. **[アプリの承認済みのリダイレクト URL]** の横にある編集用の鉛筆を選択してから、 **[リダイレクト URL を追加する]** を選択します。 「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力し、`your-tenant-name` をお使いのテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。 **[Update]\(更新\)** を選択します。
2. 既定では、サインインに関連するスコープに LinkedIn アプリが承認されていません。 レビューを要求するには、 **[製品]** タブを選択してから、 **[LinkedIn でサインインする]** を選択します。 レビューが完了すると、必要なスコープがアプリケーションに追加されます。
   > [!NOTE]
   > **[OAuth 2.0 のスコープ]** セクションの **[Auth] (承認)** タブで、アプリに現在許可されているスコープを確認できます。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>ID プロバイダーとして LinkedIn アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、**[LinkedIn]** を選択します。
1. **[名前]** を入力します。 たとえば、「*LinkedIn*」とします。
1. **[クライアント ID]** には、前に作成した LinkedIn アプリケーションのクライアント ID を入力します。
1. **[クライアント シークレット]** には、記録したクライアント シークレットを入力します。
1. **[保存]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録したクライアント シークレットを格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション** については、`Manual`を選択します。
7. ポリシー キーの **名前** を入力します。 たとえば、「 `LinkedInSecret` 」のように入力します。 プレフィックス *B2C_1A_* がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として [`Signature`] を選択します。
10. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが LinkedIn アカウントを使用してサインインするようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

LinkedIn アカウントをクレーム プロバイダーとして定義するには、それをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. エディターで *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ ファイルを開きます。 このファイルは、前提条件の 1 つの一部としてダウンロードした[カスタム ポリシー スターター パック][starter-pack]に含まれています。
1. _ *ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **client_id** の値を、前に記録した LinkedIn アプリケーションのクライアント ID に置き換えます。
1. ファイルを保存します。

### <a name="add-the-claims-transformations"></a>要求変換の追加

LinkedIn 技術プロファイルでは、**ExtractGivenNameFromLinkedInResponse** と **ExtractSurNameFromLinkedInResponse** 要求変換が ClaimsTransformations のリストに追加されている必要があります。 ファイルに **ClaimsTransformations** 要素が定義されていない場合、親 XML 要素を次のように追加します。 要求変換では、**nullStringClaim** という名前の新しい要求の種類が定義されている必要があります。

*TrustFrameworkExtensions.xml* ファイルの先頭付近に **BuildingBlocks** 要素を追加します。 例として、*TrustFrameworkBase.xml* を参照します。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

これで、Azure AD B2C が LinkedIn アカウントと通信する方法を認識できるようにポリシーが構成されました。 ポリシーの拡張ファイルをアップロードしてみて、現時点で問題がないことを確認します。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
1. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
1. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点で、ID プロバイダーは設定されていますが、まだどのサインアップまたはサインイン画面でも使用できません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、それを LinkedIn ID プロバイダーも含まれるように変更します。

1. スターター パック内の *TrustFrameworkBase.xml* ファイルを開きます。
1. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
1. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
1. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
1. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInLinkedIn` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップまたはサインイン画面の ID プロバイダーのボタンに類似しています。 LinkedIn アカウントのために **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelections** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`LinkedInExchange` など) に設定します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションは、Azure AD B2C がトークンを受信するために LinkedIn アカウントと通信することです。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
1. 次の **ClaimsExchange** 要素を追加します。ID には、**TargetClaimsExchangeId** に使用したのと同じ値を使用するようにしてください。

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。 たとえば、「 `LinkedIn-OAUTH` 」のように入力します。

1. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

::: zone-end

:: zone pivot = "b2c-user flow"

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>ユーザー フローに LinkedIn ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. LinkedIn ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[LinkedIn]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、その名前を *SignUpSignInLinkedIn.xml* に変更します。
1. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInLinkedIn` 」のように入力します。
1. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_linkedin` にします。
1. **DefaultUserJourney** 内の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignLinkedIn) に一致するように更新します。
1. 変更を保存し、ファイルをアップロードし、一覧から新しいポリシーを選択します。
1. 作成した Azure AD B2C アプリケーションが **[アプリケーションの選択]** フィールドで選択されていることを確認し、 **[今すぐ実行]** をクリックしてテストします。


## <a name="migration-from-v10-to-v20"></a>v1.0 から v2.0 に移行する

LinkedIn では最近、[API が v1.0 から v2.0 に更新](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)されました。 既存の構成を新しい構成に移行するには、次のセクションに記載された情報を使用して技術プロファイル内の要素を更新します。

### <a name="replace-items-in-the-metadata"></a>Metadata 内の項目を置き換える

**TechnicalProfile** の既存の **Metadata** 要素内で、次の **Item** 要素を変更します。

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

変更後:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Metadata に項目を追加する

**TechnicalProfile** の **Metadata** 内に、次の **Item** 要素を追加します。

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>OutputClaims を更新する

**TechnicalProfile** の既存の **OutputClaims** 要素内で、次の **OutputClaim** 要素を変更します。

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

変更後:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>新しい OutputClaimsTransformation 要素を追加する

**TechnicalProfile** の **OutputClaimsTransformations** 内に、次の **OutputClaimsTransformation** 要素を追加します。

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>新しい要求変換と要求の種類を定義する

前の手順で、新しい要求変換を追加しました。この要求変換を定義する必要があります。 要求変換を定義するには、**ClaimsTransformations** のリストにその要求変換を追加します。 ファイルに **ClaimsTransformations** 要素が定義されていない場合、親 XML 要素を次のように追加します。 要求変換では、**nullStringClaim** という名前の新しい要求の種類が定義されている必要があります。

**BuildingBlocks** 要素をファイルの先頭付近に追加する必要があります。 例として、*TrustframeworkBase.xml* を参照します。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>メール アドレスを取得する

LinkedIn の v1.0 から v2.0 への移行の一部として、メール アドレスを取得するために別の API への追加の呼び出しが必要です。 サインアップ中にメール アドレスを取得する必要がある場合、以下を実行します。

1. 上の手順を完了して Azure AD B2C が LinkedIn とフェデレーションできるようにすることで、ユーザーのサインインを可能にします。 フェデレーションの一部として、Azure AD B2C は LinkedIn のアクセス トークンを受信します。
1. LinkedIn アクセス トークンを要求内に保存します。 [手順についてはこちらを参照してください](idp-pass-through-user-flow.md)。
1. LinkedIn の `/emailAddress` API への要求を作成する次のクレーム プロバイダーを追加します。 この要求を承認するために、LinkedIn アクセス トークンが必要です。

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. ユーザーが LinkedIn を使用してサインインするときに API クレーム プロバイダーがトリガーされるように、ユーザー体験に次のオーケストレーション手順を追加します。 `Order` 番号を適切に更新するようにしてください。 この手順を、LinkedIn 技術プロファイルをトリガーするオーケストレーション手順の直後に追加します。

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

サインアップ中の LinkedIn からのメール アドレス取得は、オプションです。 メール アドレスを LinkedIn から取得せず、サインアップ中に必要になるように選択した場合、ユーザーはメール アドレスを手動で入力して検証する必要があります。

LinkedIn ID プロバイダーを使用するポリシーの完全なサンプルについては、[カスタム ポリシー スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)に関するページを参照してください。

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>v1.0 から v2.0 に移行する

LinkedIn では最近、[API が v1.0 から v2.0 に更新](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)されました。 移行の一環として、Azure AD B2C がサインアップ中に取得できるのは LinkedIn ユーザーのフル ネームのみとなります。 メール アドレスが、サインアップ時に収集される属性の 1 つである場合、ユーザーがメール アドレスを手動で入力して検証する必要があります。

::: zone-end
