---
title: カスタム ポリシーを使用して Azure Active Directory B2C で LinkedIn アカウントでのサインインを設定する | Microsoft Docs
description: カスタム ポリシーを使用して Azure Active Directory B2C で Google アカウントでのサインインを設定します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b5022e1475b9f15738dd015e16946b754fcd49c9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887310"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して LinkedIn アカウントでのサインインを設定する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して LinkedIn アカウントからのユーザーのサインインを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

- 「[Azure Active Directory B2C のカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了します。
- まだ LinkedIn アカウントを持っていない場合は、[LinkedIn のサインアップ ページ](https://www.linkedin.com/start/join)でこのアカウントを作成します。
- LinkedIn アプリケーションでは、アプリケーションを表す 80 X 80 ピクセルのロゴ イメージを指定する必要があります。

## <a name="create-an-application"></a>アプリケーションの作成

Azure AD B2C で ID プロバイダーとして LinkedIn を使用するには、LinkedIn アプリケーションを作成する必要があります。

1. LinkedIn アカウントの資格情報を使用して、[LinkedIn アプリケーション管理](https://www.linkedin.com/secure/developer?newapp=) の Web サイトにサインインします。
2. **[アプリケーションの作成]** を選択します。
3. **[会社名]**、**[アプリケーション名]**、および **[アプリケーションの説明]** を入力します。
4. 作成した **[アプリケーションのロゴ]** をアップロードします。
5. 提供される一覧から **[Application Use] (アプリケーションの用途)** を選択します。
6. **[Web サイトの URL]** には「`https://your-tenant.b2clogin.com`」と入力します。  `your-tenant`を Azure AD B2C テナントの名前に置き換えます。 たとえば、「contoso.b2clogin.com」とします。
7. **[勤務先の電子メール]** のアドレスおよび **[勤務先電話番号]** を入力します。
8. ページ下部の使用条件を確認して同意し、**[送信]** を選択します。
9. **[認証]** を選択し、**[クライアント ID]** と **[クライアント シークレット]** の値を後で使用するために記録します。
10. **[Authorized Redirect URLs] (認証済みのリダイレクト URL)** に「`https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` をテナントの名前に置き換えます。 テナントが Azure AD B2C に大文字で定義されている場合でも、テナント名を入力するときに、すべての小文字を使用する必要があります。 
11. **[Update]\(更新\)** を選択します。
12. **[設定]** を選択して、**[アプリケーションの状態]** を **[ライブ]** に変更し、**[更新]** を選択します。

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録したクライアント シークレットを格納する必要があります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、**[Identity Experience Framework - プレビュー]** を選択します。
5. **[ポリシー キー]** を選択し、**[追加]** を選択します。
6. **オプション**については、`Manual`を選択します。
7. ポリシー キーの **[名前]** を入力します。 たとえば、「 `LinkedInSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として `Signature` を選択します。
10. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが LinkedIn アカウントを使用してサインインするようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。 

LinkedIn アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

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
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4. **client_id** の値を前に記録したクライアント ID に置き換えます。
5. ファイルを保存します。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C が LinkedIn アカウントと通信する方法を認識できるようにポリシーを構成しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、**[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点で、ID プロバイダーは設定されていますが、まだどのサインアップまたはサインイン画面でも使用できません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、それを LinkedIn ID プロバイダーも含まれるように変更します。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInLinkedIn` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップまたはサインイン画面の ID プロバイダーのボタンに類似しています。 LinkedIn アカウントのために **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`LinkedInExchange` など) に設定します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションは、Azure AD B2C がトークンを受信するために LinkedIn アカウントと通信することです。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。**Id** には、**TargetClaimsExchangeId** に使用したのと同じ値を使用するようにしてください。

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```
    
    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの **Id** に更新します。 たとえば、「 `LinkedIn-OAUTH` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、検証のために再度アップロードします。

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

Azure AD B2C との通信は、テナントで作成したアプリケーション経由で行われます。 このセクションでは、テスト アプリケーションをまだ作成していない場合、それを作成するために実行できる省略可能な手順を示します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]**、**[追加]** の順に選択します。
5. アプリケーションの名前 (*testapp1* など) を入力します。
6. **[Web アプリ / Web API]** で `Yes` を選択し、**[応答 URL]** に「`https://jwt.ms`」と入力します。
7. **Create** をクリックしてください。

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新およびテストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリ内に *SignUpOrSignIn.xml* のコピーを作成し、その名前を変更します。 たとえば、その名前を *SignUpSignInLinkedIn.xml* に変更します。
2. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値に更新します。 たとえば、「 `SignUpSignInLinkedIn` 」のように入力します。
3. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_linkedin` とします。
4. **DefaultUserJourney** 内の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignLinkedIn) に一致するように更新します。
5. 変更を保存し、ファイルをアップロードしてから、一覧内の新しいポリシーを選択します。
6. 作成した Azure AD B2C アプリケーションが **[アプリケーションの選択]** フィールドで選択されていることを確認し、**[今すぐ実行]** をクリックしてそれをテストします。
