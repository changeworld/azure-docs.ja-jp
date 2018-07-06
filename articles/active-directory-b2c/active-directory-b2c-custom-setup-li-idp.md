---
title: Azure Active Directory B2C のカスタム ポリシーを使って LinkedIn を OAuth2 ID プロバイダーとして追加する | Microsoft Docs
description: OAuth2 プロトコルとカスタム ポリシーを使用した LinkedIn アプリケーションの設定に関するハウツー記事。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 334f696d79cf801facf7c5301b2240b69f7134f7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444380"
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使用して LinkedIn を ID プロバイダーとして追加する
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して LinkedIn アカウントのユーザーのサインインを有効にする方法を示します。

## <a name="prerequisites"></a>前提条件
「[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」記事の手順を完了します。

## <a name="step-1-create-a-linkedin-account-application"></a>手順 1: LinkedIn アカウント アプリケーションを作成する
Azure Active Directory B2C (Azure AD B2C) で ID プロバイダーとして LinkedIn を使用するには、LinkedIn アプリケーションを作成し、適切なパラメーターを提供する必要があります。 LinkedIn アプリケーションは、[LinkedIn のサインアップ ページ](https://www.linkedin.com/start/join)に移動して登録することができます。

1. [LinkedIn アプリケーション管理](https://www.linkedin.com/secure/developer?newapp=) Web サイトに移動し、LinkedIn アカウント資格情報でサインインし、**[アプリケーションの作成]** を選択します。

    ![LinkedIn アカウント - アプリケーションの作成](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. **[新しいアプリケーションの作成]** ページで次の手順を実行します。

    a. **会社名**を入力し、会社の説明的な**名前**と新しいアプリの**説明**を入力します。

    b. **アプリケーションのロゴ**をアップロードします。

    c. **アプリケーションの用途**を選択します。

    d. **[Web サイトの URL]** ボックスに、**https://login.microsoftonline.com** を貼り付けます。

    e. **勤務先の電子メール** アドレスと**勤務先電話番号**を入力します。

    f. ページ下部の使用条件を確認して同意し、**[送信]** を選択します。

    ![LinkedIn アカウント - アプリケーションのプロパティを構成する](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. **[認証]** を選択し、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

4. **[Authorized redirect URLs]\(承認済みのリダイレクトURL\)** ボックスに、**https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp** を貼り付けます。 {*tenant*} を実際のテナントの名前に置き換えます (例: contosob2c.onmicrosoft.com)。 HTTPS スキームを使用していることを確認します。 

    ![LinkedIn アカウント - 認証済みのリダイレクト URL の設定](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >クライアント シークレットは、重要なセキュリティ資格情報です。 このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

5. **[追加]** を選択します。

6. **[設定]** を選択して、**[アプリケーションの状態]** を **[ライブ]** に変更し、**[更新]** を選択します。

    ![LinkedIn アカウント - アプリケーションの状態の設定](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>手順 2: Azure AD B2C に LinkedIn アプリケーション キーを追加する
LinkedIn アカウントでのフェデレーションには、アプリケーションに代わって Azure AD B2C を信頼するために、LinkedIn アカウント用のクライアント シークレットが必要になります。 LinkedIn のアプリケーション シークレットを Azure AD B2C テナントに格納するには次の手順を実行します。  

1. Azure AD B2C テナントで、**[B2C Settings]\(B2C 設定\)** > **[Identity Experience Framework]** の順に選択します。

2. テナント内で使用できるキーを表示するには、**[ポリシー キー]** を選択します。

3. **[追加]** を選択します。

4. **[オプション]** ボックスで、**[アップロード]** を選択します。

5. **[名前]** ボックスに「**B2cRestClientCertificate**」と入力します。  
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。

6. **[シークレット]** ボックスに、[アプリケーション登録ポータル](https://apps.dev.microsoft.com)からの LinkedIn アプリケーション シークレットを入力します。

7. **[キー使用法]** には **[暗号化]** を選択します。

8. **[作成]** を選択します。 

9. キー `B2C_1A_LinkedInSecret` を作成したことを確認します。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>手順 3: 拡張ポリシーにクレーム プロバイダーを追加する
ユーザーが自分の LinkedIn アカウントを使用してサインインできるようにするには、LinkedIn をクレーム プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用する一連の要求を提供します。

拡張ポリシー ファイルに `<ClaimsProvider>` ノードを追加することで、LinkedIn をクレーム プロバイダーとして定義します。

1. 作業ディレクトリで、*TrustFrameworkExtensions.xml* 拡張ポリシー ファイルを開きます。 

2. `<ClaimsProviders>` 要素を検索します。

3. `<ClaimsProviders>` 要素で、次の XML スニペットを追加します。 

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

4. *client_id* 値を LinkedIn アプリケーション クライアント ID に置き換えます。

5. ファイルを保存します。

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>手順 4: LinkedIn アカウント クレーム プロバイダーを登録する
ID プロバイダーはセットアップしました。 ただし、どのサインアップ/サインイン ウィンドウでもまだ使用できません。 LinkedIn アカウント ID プロバイダーをユーザーの `SignUpOrSignIn` ユーザー体験に追加する必要があります。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>手順 4.1: ユーザー体験のコピーを作成する
ユーザー体験を使用できるようにするには、既存のユーザー体験テンプレートの複製を作成してから、LinkedIn ID プロバイダーを追加します。

>[!NOTE]
>`<UserJourneys>` 要素をポリシーの基本ファイルから *TrustFrameworkExtensions.xml* 拡張ファイルにコピーした場合は、このセクションはスキップできます。

1. ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。

2. `<UserJourneys>` 要素を検索し、`<UserJourney>` ノードのコンテンツ全体を選択してから **[切り取り]** を選択し、クリップボードに選択したテキストを移動します。

3. 拡張ファイル (TrustFrameworkExtensions.xml など) を開き、`<UserJourneys>` 要素を検索します。 要素が存在しない場合は追加します。

4. `<UserJourney>` ノードのコンテンツ全体 (手順 2 でクリップボードに移動したもの) を `<UserJourneys>` 要素に貼り付けます。

### <a name="step-42-display-the-button"></a>手順 4.2: "ボタン" を表示する
`<ClaimsProviderSelections>` 要素は、クレーム プロバイダーの選択オプションとその順序の一覧を定義します。 `<ClaimsProviderSelection>` ノードは、サインアップまたはサインイン ページの ID プロバイダーのボタンに類似しています。 LinkedIn アカウント用の `<ClaimsProviderSelection>` ノードを追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 この要素を追加するには、次の操作を行います。

1. コピーしたユーザー体験で `Id="SignUpOrSignIn"` を含む `<UserJourney>` ノードを検索します。

2. `Order="1"` を含む `<OrchestrationStep>` ノードを見つけます。

3. `<ClaimsProviderSelections>` 要素で、次の XML スニペットを追加します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>手順 4.3: ボタンをアクションにリンクする
ボタンが所定の位置に配置されたので、それをアクションにリンクする必要があります。 ここでのアクションは、Azure AD B2C が LinkedIn アカウントと通信してトークンを受信するためのアクションです。 LinkedIn アカウント クレーム プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1. `<UserJourney>` ノードで `Order="2"` を含む `<OrchestrationStep>` ノードを検索します。

2. `<ClaimsExchanges>` 要素で、次の XML スニペットを追加します。

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* `Id` の値が前のセクションの `TargetClaimsExchangeId` のものと同じであることを確認します。
    >* `TechnicalProfileReferenceId` ID が前に作成した技術プロファイル (LinkedIn-OAuth) に設定されていることを確認します。

## <a name="step-5-upload-the-policy-to-your-tenant"></a>手順 5: ポリシーをテナントにアップロードする
1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替えてから、**[Azure AD B2C]** を選択します。

2. **[Identity Experience Framework]** を選択します。

3. **[すべてのポリシー]** を選択します。

4. **[ポリシーのアップロード]** を選択します。

5. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。

6. *TrustFrameworkBase.xml* ファイルと *TrustFrameworkExtensions.xml* ファイルをアップロードし、検証に合格することを確認します。

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>手順 6: [今すぐ実行] を使用してカスタム ポリシーをテストする
1. **[Azure AD B2C の設定]** を選択してから、**[Identity Experience Framework]** を選択します。

    >[!NOTE]
    >[今すぐ実行] を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2. アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開いてから、**[今すぐ実行]** を選択します。  
    これで、LinkedIn アカウントを使用してサインインできます。

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>手順 7: (省略可能) プロファイル編集ユーザー体験に LinkedIn アカウント クレーム プロバイダーを登録する
LinkedIn アカウント ID プロバイダーを `ProfileEdit` ユーザー体験に追加することもできます。 ユーザー体験を使用できるようにするには、"手順 4" を繰り返します。 ここでは、`Id="ProfileEdit"` を含む `<UserJourney>` ノードを選択します。 ポリシーを保存し、アップロードしてテストします。

## <a name="optional-download-the-complete-policy-files"></a>(省略可能) 完全なポリシー ファイルをダウンロードする
「[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」チュートリアルの完了後に、独自のカスタム ポリシー ファイルを使用してシナリオを構築することをお勧めします。 参照用に[サンプルのポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)が提供されています。
