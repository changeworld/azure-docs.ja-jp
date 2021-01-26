---
title: Weibo アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで Weibo アカウントを持つ顧客にサインアップとサインインを提供します。
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
ms.openlocfilehash: 24e267b66d11cb3c5ca2b70ed09b7acb3653da99
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653607"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Weibo アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>[前提条件]

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-weibo-application"></a>Weibo アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で ID プロバイダーとして Weibo アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Weibo アカウントを持っていない場合は、[https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us) でサインアップできます。

1. Weibo アカウントの資格情報を使用して[Weibo 開発者ポータル](https://open.weibo.com/)にサインインします。
1. サインインしたら、右上隅にある表示名を選択します。
1. ドロップダウンで **[编辑开发者信息 (開発者情報の編集)]** を選択します。
1. 必要な情報を入力し、 **[提交 (送信)]** を選択します。
1. 電子メールによる確認プロセスを完了します。
1. [ID の確認ページ](https://open.weibo.com/developers/identity/edit)に移動します。
1. 必要な情報を入力し、 **[提交 (送信)]** を選択します。

### <a name="register-a-weibo-application"></a>Weibo アプリケーションを登録する

1. [新しい Weibo アプリの登録ページ](https://open.weibo.com/apps/new)に移動します。
1. 必要なアプリケーション情報を入力します。
1. **[创建 (作成)]** を選択します。
1. **アプリ キー** と **アプリケーション シークレット** の値をコピーします。 ID プロバイダーをテナントに追加するには、これらの両方が必要です。
1. 必要な写真をアップロードし、必要な情報を入力します。
1. **[保存以上信息 (保存)]** を選択します。
1. **[高级信息 (詳細情報)]** を選択します。
1. OAuth2.0 の **[授权设置 (リダイレクト URL)]** フィールドの横の **[编辑 (編集)]** を選択します。
1. OAuth2.0 の **[授权设置 (リダイレクト URL)]** に、「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 たとえば、テナント名が contoso の場合、URL を`https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`に設定します。
1. **[提交 (送信)]** を選択します。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>ID プロバイダーとして Weibo アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[Weibo (プレビュー)]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Weibo*」とします。
1. **[クライアント ID]** には、前に作成した Weibo アプリケーションのアプリ キーを入力します。
1. **[クライアント シークレット]** には、記録したアプリ シークレットを入力します。
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
7. ポリシー キーの **名前** を入力します。 たとえば、「 `WeiboSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として [`Signature`] を選択します。
10. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーに Weibo アカウントを使用してサインインさせるには、そのアカウントを、Azure AD B2C がエンドポイント経由で通信できるクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

Weibo アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>Weibo.com</Domain>
      <DisplayName>Weibo</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Weibo-OAUTH">
          <DisplayName>Weibo</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">Weibo</Item>
            <Item Key="authorization_endpoint">https://accounts.Weibo.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.Weibo.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.Weiboapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Weibo application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeiboSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="Weibo.com" />
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
    <ClaimsProvider>
      <Domain>weibo.com</Domain>
      <DisplayName>Weibo (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Weibo-OAUTH">
          <DisplayName>Weibo</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">weibo</Item>
            <Item Key="authorization_endpoint">https://api.weibo.com/oauth2/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.weibo.com/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weibo.com/2/account/get_uid.json</Item>
            <Item Key="scope">email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">uid</Item>
            <Item Key="client_id">Your Weibo application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeiboSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="uid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="weibo.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="Weibo User" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="UserId" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
    ```

4. **client_id** を、アプリケーションの登録で取得したアプリケーション ID に設定します。
5. ファイルを保存します。

### <a name="add-the-claims-transformations"></a>要求変換の追加

GitHub の技術プロファイルを使用するには、**CreateIssuerUserId** 要求変換を ClaimsTransformations の一覧に追加する必要があります。 ファイルに **ClaimsTransformations** 要素が定義されていない場合、親 XML 要素を次のように追加します。 要求変換では、**numericUserId** という名前の新しい要求の種類が定義されている必要があります。

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. **ClaimsSchema** 要素に numericUserId 要求を追加します。
1. [ClaimsTransformations](claimstransformations.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. CreateIssuerUserId 要求変換を **ClaimsTransformations** 要素に追加します。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C によって Weibo アカウントとの通信方法が認識されるようにポリシーを構成しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、それを Weibo ID プロバイダーも含まれるように変更します。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInWeibo` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップおよびサインイン画面の ID プロバイダー ボタンに類似しています。 Weibo アカウントのために **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`WeiboExchange` など) に設定します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="WeiboExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Weibo アカウントと通信してトークンを受信します。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。**TargetClaimsExchangeId** に使用した ID と同じ値を必ずご使用ください。

    ```xml
    <ClaimsExchange Id="WeiboExchange" TechnicalProfileReferenceId="Weibo-OAuth" />
    ```

    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。 たとえば、「 `Weibo-OAuth` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-weibo-identity-provider-to-a-user-flow"></a>ユーザー フローに Weibo ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Weibo ID プロバイダーを追加するユーザー フローをクリックします。
1. **[ソーシャル ID プロバイダー]** から、 **[Weibo]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、その名前を *SignUpSignInWeibo.xml* に変更します。
1. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInWeibo` 」のように入力します。
1. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_Weibo` にします。
1. **DefaultUserJourney** 内の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignWeibo) に一致するように更新します。
1. 変更内容を保存し、ファイルをアップロードします。
1. **[カスタム ポリシー]** ページで、**B2C_1A_signup_signin** を選択します。
1. **[アプリケーションの選択]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** を選択し、Weibo を選択して Weibo でサインインし、カスタム ポリシーをテストします。

::: zone-end
