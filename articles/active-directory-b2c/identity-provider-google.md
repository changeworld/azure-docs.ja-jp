---
title: Google アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで Google アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c8b942e66a76bcc3a095f9bd3d40b44bf4217e50
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584886"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Google アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>Google アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で [ID プロバイダー](authorization-code-flow.md)として Google アカウントを使用するには、Google Developers Console でアプリケーションを作成する必要があります。 まだ Google アカウントを持っていない場合は、[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) でサインアップできます。

1. Google アカウントの資格情報で [Google Developers Console](https://console.developers.google.com/) にサインインします。
1. ページの左上隅にあるプロジェクトの一覧を選択し、**[新しいプロジェクト]** を選択します。
1. **[プロジェクト名]** を入力し、**[作成]** を選択します。
1. 画面左上にあるプロジェクト ドロップダウンを選択して新しいプロジェクトを使用していることを確認し、名前でご自分のプロジェクトを選択し、**[開く]** を選択します。
1. 左側にあるメニューで **[OAuth 同意画面]** を選択し、**[外部]** を選択し、**[作成]** を選択します。
アプリケーションの **[名前]** を入力します。 **[承認済みドメイン]** セクションに「*b2clogin.com*」と入力し、**[保存]** を選択します。
1. 左側のメニューで **[Credentials (資格情報)]** を選択して、**[Create credentials (資格情報を作成)]** > **[Oauth client ID (Oauth クライアント ID)]** を選択します。
1. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。
1. アプリケーションの **名前** を指定します。**[承認済みの JavaScript 生成元]** に「`https://your-tenant-name.b2clogin.com`」と入力し、**[承認済みのリダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。
1. **Create** をクリックしてください。
1. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして Google を構成するには、両方の値が必要です。 **[クライアント シークレット]** は、重要なセキュリティ資格情報です。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-google-account-as-an-identity-provider"></a>ID プロバイダーとして Google アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、**[Google]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Google*」とします。
1. **[クライアント ID]** には、前に作成した Google アプリケーションのクライアント ID を入力します。
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
7. ポリシー キーの **名前** を入力します。 たとえば、「 `GoogleSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として [`Signature`] を選択します。
10. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーに Google アカウントを使用してサインインさせるには、そのアカウントを、Azure AD B2C がエンドポイント経由で通信できるクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

Google アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. **client_id** を、アプリケーションの登録で取得したアプリケーション ID に設定します。
5. ファイルを保存します。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C によって Google アカウントとの通信方法が認識されるようにポリシーを構成しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、それを Google ID プロバイダーも含まれるように変更します。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInGoogle` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップおよびサインイン画面の ID プロバイダー ボタンに類似しています。 Google アカウントのために **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`GoogleExchange` など) に設定します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Google アカウントと通信してトークンを受信します。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。**TargetClaimsExchangeId** に使用した ID と同じ値を必ずご使用ください。

    ```xml
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。 たとえば、「 `Google-OAuth` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-google-identity-provider-to-a-user-flow"></a>ユーザー フローに Google ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Google ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[Google]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、その名前を *SignUpSignInGoogle.xml* に変更します。
1. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInGoogle` 」のように入力します。
1. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_google` にします。
1. **DefaultUserJourney** 内の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignGoogle) に一致するように更新します。
1. 変更内容を保存し、ファイルをアップロードします。
1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. **[アプリケーションの選択]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** を選択し、Google を選択して Google でサインインし、カスタム ポリシーをテストします。

::: zone-end

## <a name="next-steps"></a>次のステップ

[Google トークンをアプリケーションに渡す](idp-pass-through-user-flow.md)方法について説明します。