---
title: カスタム ポリシーを使用して AD FS を OpenID Connect ID プロバイダーとして追加する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で OpenID Connect プロトコルとカスタム ポリシーを使用して AD FS 2016 を設定する
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ad00a74871373338a2b4e8b557297a808e8186fc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007298"
---
# <a name="add-ad-fs-as-an-openid-connect-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して AD FS を OpenID Connect プロバイダーとして追加する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]


## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-an-ad-fs-application"></a>AD FS アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で AD FS アカウントを持つユーザーのサインインを有効にするには、AD FS でアプリケーション グループを作成する必要があります。 詳細については、「[AD FS 2016 以降で OpenID Connect を使用する Web アプリケーションを構築する](/windows-server/identity/ad-fs/development/enabling-openid-connect-with-ad-fs)」を参照してください

アプリケーション グループを作成するには、以下の手順に従います。

1. **[サーバー マネージャー]** で **[ツール]** を選択してから、 **[AD FS の管理]** を選択します。
1. [AD FS の管理] で、 **[アプリケーション グループ]** を右クリックし、 **[アプリケーション グループの追加]** を選択します。
1. アプリケーション グループ ウィザードの **[ようこそ]** 画面で、以下を実行します。
    1. アプリケーションの **[名前]** を入力します。 たとえば、「*Azure AD B2C application*」とします。
    1. **[クライアント/サーバー アプリケーション]** で、 **[Web アプリケーション テンプレートにアクセスする Web ブラウザー]** テンプレートを選択します。
    1. **[次へ]** を選択します。
1. アプリケーション グループ ウィザードの **[ネイティブ アプリケーション]** 画面で、以下を実行します。
    1. **[クライアント識別子]** の値をコピーします。 クライアント識別子は、お使いの AD FS **アプリケーションの ID** です。 アプリケーション ID は、この記事の後の方で必要になります。
    1. **[リダイレクト URI]** に「 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 」と入力します。 [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` を実際のテナントの名前に、`your-domain-name` を実際のカスタム ドメインに置き換えます。
    1. **[次へ]** を選択してから、 **[完了]** を選択してアプリ登録ウィザードを完了します。 
    1. **[閉じる]** を選択します。


### <a name="configure-the-app-claims"></a>アプリ要求を構成する

この手順では、AD FS アプリケーションから Azure AD B2C に返される要求を構成します。  

1. **[アプリケーション グループ]** で、作成したアプリケーションを選択します。
1. アプリケーションのプロパティ ウィンドウの **[アプリケーション]** で、 **[Web アプリケーション]** を選択します。 次に、 **[編集]** を選択します。
    :::image type="content" source="./media/identity-provider-adfs/ad-fs-edit-app.png" alt-text="Web アプリケーションの編集方法を示しているスクリーンショット。":::
1. **[発行の変換規則]** タブを選択してから、 **[規則の追加]** を選択します。
1. **[要求規則テンプレート]** で、**[LDAP 属性を要求として送信]** を選択します。
1. **[要求規則名]** を指定します。 **[属性ストア]** で、 **[Active Directory の選択]** を選択し、以下の要求を追加します。

    | LDAP 属性 | 出力方向の要求の種類 |
    | -------------- | ------------------- |
    | User-Principal-Name | upn |
    | Surname | family_name |
    | Given-Name | given_name |
    | Display-Name | name |

    [出力方向の要求の種類] ドロップダウンには、一部の名前が表示されないことに注意してください。 手動で入力する必要があります  (ドロップダウンは編集可能です)。

1. **[完了]** を選択してから、 **[閉じる]** を選択します。


::: zone pivot="b2c-user-flow"

## <a name="configure-ad-fs-as-an-identity-provider"></a>AD FS を ID プロバイダーとして構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば、*Contoso* です。
1. **[メタデータ URL]** には、[AD FS OpenID Connect 構成ドキュメント](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints)の URL を入力してください。 次に例を示します。

    ```http
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```

1. **[クライアント ID]** には、前に記録したアプリケーション ID を入力します。
1. **[スコープ]** で、`openid` を入力します。
1. **[応答の種類]** で、 **[id_token]** を選択します。
1. (省略可能) **[ドメインのヒント]** に、「`contoso.com`」と入力します。 詳しくは、「[Azure Active Directory B2C を使用した直接サインインの設定](direct-signin.md#redirect-sign-in-to-a-social-provider)」をご覧ください。
1. **[ID プロバイダー要求のマッピング]** で、次の要求を入力します。

    - **ユーザー ID**: `upn`
    - **表示名**: `unique_name`
    - **名**: `given_name`
    - **姓**: `family_name`

1. **[保存]** を選択します。

## <a name="add-ad-fs-identity-provider-to-a-user-flow"></a>ユーザー フローに AD FS ID プロバイダーを追加する 

この時点では、AD FS (Contoso) ID プロバイダーは設定されていますが、どのサインイン ページでも、まだ使用できません。 AD FS ID プロバイダーをユーザー フローに追加するには、次のようにします。

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. AD FS ID プロバイダー (Contoso) を追加するユーザー フローを選択します。
1. **[ソーシャル ID プロバイダー]** から **[Contoso]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso]** を選択して Contoso アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="configure-ad-fs-as-an-identity-provider"></a>AD FS を ID プロバイダーとして構成する

ユーザーが AD FS アカウントを使用してサインインできるようにするには、AD FS を、Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-OpenIdConnect">
          <DisplayName>Contoso</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://your-adfs-domain/adfs/.well-known/openid-configuration</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your AD FS application ID</Item>
          </Metadata>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="unique_name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **[メタデータ URL]** には、[AD FS OpenID Connect 構成ドキュメント](/windows-server/identity/ad-fs/development/ad-fs-openid-connect-oauth-concepts#ad-fs-endpoints)の URL を入力します。 次に例を示します。

    ```
    https://adfs.contoso.com/adfs/.well-known/openid-configuration 
    ```
5. **client_id** を、アプリケーションの登録で取得したアプリケーション ID に設定します。
6. ファイルを保存します。

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

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](tutorial-register-applications.md) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Contoso]** を選択し、Contoso アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。


::: zone-end

## <a name="next-steps"></a>次のステップ

[AD FS トークンをアプリケーションに渡す](idp-pass-through-user-flow.md)方法を確認します。
