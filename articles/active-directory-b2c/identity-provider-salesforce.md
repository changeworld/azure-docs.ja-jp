---
title: Salesforce アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用して、Salesforce アカウントでのアプリケーションへのサインアップとサインインをお客様に提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 129809a83bcebdcf80b05a7300dd9acf862e5886
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900401"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Salesforce アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Salesforce アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で Salesforce アカウントを使用するには、Salesforce **アプリ マネージャ** でアプリケーションを作成する必要があります。 詳細については、「[接続アプリケーションの基本設定](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)」と「[API インテグレーション用の OAuth 設定の有効化](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)」を参照してください。

1. [Salesforce にサインインします](https://login.salesforce.com/)。
1. メニューから **[Setup]\(設定\)** を選択します。
1.  **[Apps]\(アプリ\)** を展開し、 **[App Manager]\(アプリ マネージャ\)** を選択します。
1. **[New Connected App]\(新しく接続されたアプリ\)** を選択します。
1. **[Basic Information]\(基本情報\)** で、次のように入力します。
    1. **[Connected App Name]\(接続されているアプリの名前\)** - 接続されているアプリ名が、アプリ マネージャーと [App Launcher]\(アプリ起動\) タイルに表示されます。 名前は、組織内で一意である必要があります。 
    1. **[API Name]\(アプリ名\)** 
    1. **[Contact Email]\(連絡先の電子メール\)** - Salesforce での連絡先の電子メール
1. **[API (Enable OAuth Settings)]\(API (OAuth 設定を有効にする)\)** で、 **[Enable OAuth Settings]\(OAuth 設定を有効にする\)** を選択します。
    1. **[Callback URL]\(コールバック URL\)** に、「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
    1. **[Selected OAuth Scopes]\(選択された OAuth のスコープ\)** で、 **[Access your basic information (id, profile, email, address, phone)]\(基本情報 (ID、プロファイル、電子メール、アドレス、電話) にアクセスする\)** と **[Allow access to your unique identifier (openid)]\(一意識別子 (openid) へのアクセスを許可する\)** を選択します。
    1. **[Require Secret for Web Server Flow]\(Web サーバー フロー用のシークレットが必要\)** を選択します。
1. **[Configure ID Token]\(ID トークンの構成\)** を選択します 
    1. **[Token Valid for]\(トークンの有効期限\)** を 5 分に設定します。
    1. **[Include Standard Claims]\(標準要求を含める\)** を選択します。
1. **[保存]** をクリックします。
1. **[Consumer Key]\(コンシューマー キー\)** と **[Consumer Secret]\(コンシューマー シークレット\)** の値をコピーします。 テナントで Salesforce を ID プロバイダーとして構成するには、両方の値が必要です。 **[クライアント シークレット]** は、重要なセキュリティ資格情報です。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-salesforce-account-as-an-identity-provider"></a>Salesforce アカウントを ID プロバイダーとして構成する

1. Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Salesforce*」と入力します。
1. **[メタデータ URL]** には、[Salesforce OpenID Connect 構成ドキュメント](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)の URL を入力してください。 サンドボックスの場合、login.salesforce.com は test.salesforce.com に置き換えられます。 コミュニティの場合、login.salesforce.com は username.force.com/.well-known/openid-configuration などのコミュニティ URL に置き換えられます。 この URL は HTTPS である必要があります。

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. **[クライアント ID]** には、前に記録したアプリケーション ID を入力します。
1. **[クライアント シークレット]** には、前に記録したクライアント シークレットを入力します。
1. **[スコープ]** で、`openid id profile email` を入力します。
1. **[応答の種類]** および **[応答モード]** の既定値はそのままにします。
1. (省略可能) **[ドメインのヒント]** に、「`contoso.com`」と入力します。 詳しくは、「[Azure Active Directory B2C を使用した直接サインインの設定](direct-signin.md#redirect-sign-in-to-a-social-provider)」をご覧ください。
1. **[ID プロバイダー要求のマッピング]** で、次の要求を入力します。

    - **[ユーザー ID]** : *sub*
    - **[表示名]** : *name*
    - **[名]** : *given_name*
    - **[姓]** : *family_name*
    - **[電子メール]** : *email*

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
7. ポリシー キーの **名前** を入力します。 たとえば、「 `SalesforceSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として [`Signature`] を選択します。
10. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが Salesforce アカウントを使用してサインインできるようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できるクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

Salesforce アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OIDC">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
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

4. **METADATA** には、[Salesforce OpenID Connect 構成ドキュメント](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)の URL が設定されます。 サンドボックスの場合、login.salesforce.com は test.salesforce.com に置き換えられます。 コミュニティの場合、login.salesforce.com は username.force.com/.well-known/openid-configuration などのコミュニティ URL に置き換えられます。 この URL は HTTPS である必要があります。
5. **client_id** を、アプリケーションの登録で取得したアプリケーション ID に設定します。
6. ファイルを保存します。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C が Salesforce アカウントと通信する方法を認識できるようにポリシーを構成しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、Salesforce ID プロバイダーも含まれるようにそれを変更します。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInSalesforce` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップおよびサインイン画面の ID プロバイダー ボタンに類似しています。 Salesforce アカウント用の **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`SalesforceExchange` など) に設定します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 ここでのアクションは、Azure AD B2C が Salesforce アカウントと通信してトークンを受信するためのアクションです。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。**TargetClaimsExchangeId** に使用した ID と同じ値を必ずご使用ください。

    ```xml
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OIDC" />
    ```

    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。 たとえば、「 `Salesforce-OIDC` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>ユーザー フローに Salesforce ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Salesforce ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[Salesforce]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、*SignUpSignInSalesforce.xml* に名前を変更します。
1. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInSalesforce` 」のように入力します。
1. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_Salesforce` にします。
1. **DefaultUserJourney** の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignSalesforce) と一致するように更新します。
1. 変更内容を保存し、ファイルをアップロードします。
1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. **[アプリケーションの選択]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** を選択し、Salesforce でサインインする Salesforce を選択し、カスタム ポリシーをテストします。

::: zone-end

## <a name="next-steps"></a>次のステップ

[Salesforce トークンをアプリケーションに渡す](idp-pass-through-user-flow.md)方法について説明します。
