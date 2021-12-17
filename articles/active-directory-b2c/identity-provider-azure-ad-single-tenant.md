---
title: Azure AD 組織用のサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a63c89174103613c722d463e32085600019e9ea0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026009"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定する

この記事では、Azure AD B2C のユーザー フローを使用して、特定の Azure AD 組織のユーザーがサインインできるようにする方法について説明します。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="verify-the-applications-publisher-domain"></a>アプリケーションのパブリッシャー ドメインを検証する
2020 年 11 月現在、新しいアプリケーション登録は、[アプリケーションの発行元ドメインが検証済み](../active-directory/develop/howto-configure-publisher-domain.md)で、"***かつ***" 会社の ID が Microsoft Partner Network で検証され、アプリケーションに関連付けられている場合を除き、ユーザーの同意プロンプトで未検証として表示されます。 (この変更の詳細については[こちら](../active-directory/develop/publisher-verification-overview.md)を参照してください)。Azure AD B2C ユーザー フローの場合、発行元のドメインは、[Microsoft アカウント](../active-directory-b2c/identity-provider-microsoft-account.md)またはその他の Azure AD テナントを ID プロバイダーとして使用する場合にのみ表示されることに注意してください。 これらの新しい要件を満たすには、以下を実行します。

1. [自分の Microsoft Partner Network (MPN) アカウントを使用して会社 ID を確認します](/partner-center/verification-responses)。 このプロセスにより、会社と会社の主要連絡先に関する情報が検証されます。
1. 発行元の確認プロセスを完了し、次のいずれかのオプションを使用して、MPN アカウントをアプリ登録に関連付けます。
   - Microsoft アカウント ID プロバイダーのアプリの登録が Azure AD テナント内にある場合は、[アプリ登録ポータルでアプリを検証](../active-directory/develop/mark-app-as-publisher-verified.md)します。
   - Microsoft アカウント ID プロバイダーのアプリの登録が Azure AD B2C テナント内にある場合は、[Microsoft Graph API を使用して発行元により検証済みとしてアプリをマーク](../active-directory/develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)します (たとえば、Graph Explorer を使用)。 アプリの検証済み発行元を設定するための UI は、現在、Azure AD B2C テナントでは無効になっています。

## <a name="register-an-azure-ad-app"></a>Azure AD アプリの登録

Azure Active Directory B2C (Azure AD B2C) で、特定の Azure AD 組織の Azure AD アカウントを持つユーザーのサインインを有効にするには、[Azure portal](https://portal.azure.com) でアプリケーションを作成する必要があります。 詳細については、[Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)方法に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 組織の Azure AD テナント (Contoso など) が含まれているディレクトリを使用していることを確認します。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[スイッチ]** を選択します。
1. **[Azure サービス]** で、 **[アプリの登録]** を選択するか、検索して **[アプリの登録]** を選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
1. このアプリケーションには、 **[この組織ディレクトリのみに含まれるアカウント (既定のディレクトリのみ - シングル テナント)]** という既定の選択をそのまま使用します。
1. **[リダイレクト URL]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    たとえば、「 `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp` 」のように入力します。

    [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-domain-name` を実際のカスタム ドメインに、`your-tenant-name` を実際のテナントの名前に置き換えます。

1. **[登録]** を選択します。 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。
1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。
1. シークレットの **説明** を入力し、有効期限を選択して、 **[追加]** を選択します。 後の手順で使用するために、シークレットの **値** を記録しておきます。

### <a name="configuring-optional-claims"></a>省略可能な要求の構成

Azure AD から `family_name` および `given_name` 要求を取得する場合は、ご利用のアプリケーションに対して省略可能な要求を Azure portal UI またはアプリケーション マニフェストで構成できます。 詳細については、[Azure AD アプリに省略可能な要求を提供する方法](../active-directory/develop/active-directory-optional-claims.md)に関するページを参照してください。

1. 組織の Azure AD テナントを使用して [Azure portal](https://portal.azure.com) にサインインします。 **Azure Active Directory** を検索して選択します。
1. **[管理]** セクションで、 **[アプリの登録]** を選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から選択します。
1. **[管理]** セクションで、 **[トークン構成]** を選択します。
1. **[省略可能な要求を追加]** を選択します。
1. **[トークンの種類]** で、 **[ID]** を選択します。
1. 追加する省略可能な要求 (`family_name` と `given_name`) を選択します。
1. **[追加]** を選択します。 **[Microsoft Graph 電子メールのアクセス許可を有効にします (要求がトークンに表示されるために必要)]** が表示される場合は、それを有効にしてから再度 **[追加]** を選択します。

## <a name="optional-verify-your-app-authenticity"></a>[省略可能] アプリの信頼性を確認する

[発行元の確認](../active-directory/develop/publisher-verification-overview.md)により、ユーザーは、[登録](#register-an-azure-ad-app)したアプリの信頼性を把握できます。 検証済みアプリは、アプリの発行元が、Microsoft Partner Network (MPN) を使用して ID を[検証](/partner-center/verification-responses)したことを意味します。 [アプリを発行者確認済みとしてマークする](../active-directory/develop/mark-app-as-publisher-verified.md)方法についてご確認ください。 

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

1. Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば､「*Contoso Azure AD*」と入力します。
1. **[メタデータ URL]** には、次の URL を入力します。`{tenant}` は、利用する Azure AD テナントのドメイン名に置き換えます。

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

 たとえば、「 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` 」のように入力します。 カスタム ドメインを使用する場合は、`https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration` に自分のカスタム ドメインを入れて `contoso.com` を置き換えます。

1. **[クライアント ID]** には、前に記録したアプリケーション ID を入力します。
1. **[クライアント シークレット]** には、前に記録したクライアント シークレットを入力します。
1. **[スコープ]** には、「`openid profile`」と入力します。
1. **[応答の種類]** および **[応答モード]** の既定値はそのままにします。
1. (省略可能) **[ドメインのヒント]** に、「`contoso.com`」と入力します。 詳しくは、「[Azure Active Directory B2C を使用した直接サインインの設定](direct-signin.md#redirect-sign-in-to-a-social-provider)」をご覧ください。
1. **[ID プロバイダー要求のマッピング]** で、次の要求を入力します。

    - **[ユーザー ID]** : *oid*
    - **[表示名]** : *name*
    - **[名]** : *given_name*
    - **[姓]** : *family_name*
    - **[電子メール]** : *email*

1. **[保存]** を選択します。

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>ユーザー フローに Azure AD ID プロバイダーを追加する 

この時点では、Azure AD ID プロバイダーはセットアップされていますが、サインイン ページではまだ使用できません。 ユーザー フローに Azure AD ID プロバイダーを追加するには:

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Azure AD ID プロバイダーを追加するユーザー フローをクリックします。
1. **[設定]** で **[ID プロバイダー]** を選択します
1. **[カスタム ID プロバイダー]** で **[Contoso Azure AD]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、[前に登録した](tutorial-register-applications.md) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。 
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso Azure AD]** を選択し、Azure AD Contoso アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

作成したアプリケーション キーを Azure AD B2C テナントに格納する必要があります。

1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `ContosoAppSecret` 」のように入力します。  作成時に、プレフィックス `B2C_1A_` がキーの名前に自動的に追加されるため、次のセクションの XML での参照は *B2C_1A_ContosoAppSecret* になります。
1. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **［作成］** を選択します

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

ユーザーが Azure AD アカウントを使用してサインインできるようにするには、エンドポイント経由で Azure AD B2C が通信できるクレーム プロバイダーとして Azure AD を定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

ポリシーの拡張ファイル内で Azure AD を **ClaimsProvider** 要素に追加することで、Azure AD をクレーム プロバイダーとして定義できます。

1. *TrustFrameworkExtensions.xml* ファイルを開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
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

4. **ClaimsProvider** 要素の下で、**Domain** の値を、他の ID プロバイダーと区別するために使用できる一意の値に更新します。 たとえば、「 `Contoso` 」のように指定します。 `.com` はこのドメイン設定の最後に配置しません。
5. **ClaimsProvider** 要素の下で、**DisplayName** の値を、クレーム プロバイダーを表すわかりやすい名前に更新します。 この値は現在使用されていません。

### <a name="update-the-technical-profile"></a>技術プロファイルの更新

Azure AD エンドポイントからトークンを取得するには、Azure AD B2C で Azure AD との通信に使用するプロトコルを定義する必要があります。 これは、**ClaimsProvider** の **TechnicalProfile** 要素の中で実行します。

1. **TechnicalProfile** 要素の ID を更新します。 この ID は、`AADContoso-OpenIdConnect` など、ポリシーの他の部分からこの技術プロファイルを参照するために使用します。
1. **DisplayName** の値を更新します。 この値は、サインイン画面のサインイン ボタン上に表示されます。
1. **Description** の値を更新します。
1. Azure AD では OpenID Connect プロトコルを使用するため、**Protocol** の値が `OpenIdConnect` になっていることを確認してください。
1. **METADATA** の値を `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` に設定します。`tenant-name` は Azure AD テナント名です。 たとえば、`https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` のように指定します。
1. **client_id** を、アプリケーションの登録で取得したアプリケーション ID に設定します。
1. **CryptographicKeys** で、**StorageReferenceId** の値を、前に作成したポリシー キーの名前に更新します。 たとえば、「 `B2C_1A_ContosoAppSecret` 」のように入力します。


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](tutorial-register-applications.md) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso Employee]** を選択し、Azure AD Contoso アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

## <a name="next-steps"></a>次のステップ

[Azure AD トークンをアプリケーションに渡す](idp-pass-through-user-flow.md)方法について学習する。
