---
title: "Azure Active Directory B2C: カスタム ポリシーを使用して Twitter を OAuth1 ID プロバイダーとして追加する"
description: "OAuth1 プロトコルを使用する ID プロバイダーとしての Twitter の使用サンプル"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/02/2017
ms.author: yoelh
ms.openlocfilehash: 8d7bd2ed1cba62677a7eff19ba7018cd25a4345c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使用して Twitter を OAuth1 ID プロバイダーとして追加する
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して Twitter アカウントからのユーザーのサインインを有効にする方法を示します。

## <a name="prerequisites"></a>前提条件
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

その手順は次のとおりです。

1.  Twitter アカウント アプリケーションを作成する
2.  Azure AD B2C に Twitter アカウント アプリケーション キーを追加する
3.  ポリシーに要求プロバイダーを追加する
4.  ユーザー体験に Twitter アカウント クレーム プロバイダーを登録する
5.  Azure AD B2C テナントにポリシーをアップロードし、テストする

## <a name="step-1-create-a-twitter-account-application"></a>手順 1: Twitter アカウント アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Twitter を使用するには、Twitter アプリケーションを作成し、適切なパラメーターを指定する必要があります。 Twitter アプリケーションは、こちら ([https://twitter.com/signup](https://twitter.com/signup)) で登録できます。

1.  [Twitter Developers](https://apps.twitter.com/) の Web サイトに移動し、Twitter アカウント資格情報でサインインして、 **[Create New App]**をクリックします。

    ![Twitter アカウント - 新しいアプリを作成する](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2.  新しいアプリの **[Name (名前)]** と **[Description (説明)]** を入力します。 **[Web サイト]** の値に `https://login.microsoftonline.com` を貼り付けます。 次に、**[コールバック URL]** に、`https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` を貼り付けます。 {tenant} を実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換え、 HTTPS スキームを使用していることを確認します。 ページの下部で、条項を読み、同意します。 **[Create your Twitter application]**をクリックします。

    ![Twitter アカウント - 新しいアプリケーションを追加する](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3.  **[Settings (設定)]** タブをクリックし、**[Allow this application to be used to sign in with Twitter (このアプリケーションが Twitter でのサインインに使用されることを許可する)]** チェック ボックスをオンにして、**[Update Settings (設定の更新)]** をクリックします。
4.  **[Keys and Access Tokens]** タブをクリックします。**[Consumer Key (API Key) (コンシューマー キー (API キー))]** と **[Consumer secret (API Secret) (コンシューマー シークレット (API シークレット))]** の値を書き留めます。

    ![Twitter アカウント - アプリケーションのプロパティを設定する](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

> [!NOTE]
>
>コンシューマー シークレットは、重要なセキュリティ資格情報です。 このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

## <a name="step-2-add-the-twitter-account-application-key-to-azure-ad-b2c"></a>手順 2: Azure AD B2C に Twitter アカウント アプリケーション キーを追加する
Twitter アカウントでのフェデレーションには、アプリケーションに代わって Azure AD B2C を信頼するために、Twitter アカウント用のクライアント シークレットが必要になります。 Twitter アプリケーションのコンシューマー シークレットを Azure AD B2C テナントに格納する必要があります。  

1.  Azure AD B2C テナントに移動し、**[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  **[+ 追加]** をクリックします。
4.  **[オプション]** には **[Manual] \(手動)** を使用します。
5.  **[名前]** には `TwitterSecret` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。
6.  **[シークレット]** ボックスに、https://apps.dev.microsoft.com からの Microsoft アプリケーション シークレットを入力します
7.  **[キー使用法]** には **[暗号化]** を使用します。
8.  **[作成]**
9.  キー `B2C_1A_TwitterSecret` を作成したことを確認します。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>手順 3: 拡張ポリシーにクレーム プロバイダーを追加する

ユーザーが Twitter アカウントを使用してサインインできるようにするには、Twitter をクレーム プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用する一連の要求を提供します。

拡張ポリシー ファイルに `<ClaimsProvider>` ノードを追加することで、Twitter をクレーム プロバイダーとして定義します。

1.  作業ディレクトリから拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。 
2.  `<ClaimsProviders>` セクションを探します。
3.  `<ClaimsProviders>` ノードの下に次の XML スニペットを追加します。  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
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

4.  `client_id` 値を Twitter アカウント アプリケーションのコンシューマー キーに置き換えます。

5.  ファイルを保存します。

## <a name="step-4-register-the-twitter-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>手順 4: サインアップまたはサインイン ユーザー体験に Twitter アカウント クレーム プロバイダーを登録する
この時点で、ID プロバイダーが設定されました。 ただし、いずれのサインアップ/サインイン画面でも、使用可能ではありません。 Twitter アカウント ID プロバイダーをユーザーの `SignUpOrSignIn` ユーザー体験に追加する必要があります。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>手順 4.1. ユーザー体験のコピーを作成する
使用可能にするには、既存のテンプレート ユーザー体験の複製を作成します。 その後、Twitter ID プロバイダーを追加します。

> [!NOTE]
>
>`<UserJourneys>` 要素をポリシーの基本ファイルから拡張ファイル (TrustFrameworkExtensions.xml) にコピーした場合は、次のセクションをスキップできます。

1.  ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2.  `<UserJourneys>` 要素を見つけて、`<UserJourneys>` ノードのコンテンツ全体をコピーします。
3.  拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
4.  コピーした `<UserJournesy>` ノードのコンテンツ全体を `<UserJourneys>` 要素の子として貼り付けます。

### <a name="step-42-display-the-button"></a>手順 4.2. "ボタン" を表示する
`<ClaimsProviderSelections>` 要素は、要求プロバイダーの選択オプションとその順序の一覧を定義します。  `<ClaimsProviderSelection>` 要素は、サインアップ/サインイン ページの ID プロバイダーのボタンに類似しています。 Twitter アカウント用の `<ClaimsProviderSelection>` 要素を追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 この要素を追加するには、次の手順を実行します。

1.  コピーしたばかりのユーザー体験内で、`Id="SignUpOrSignIn"` を含む `<UserJourney>` ノードを見つけます。
2.  `Order="1"` を含む `<OrchestrationStep>` ノードを見つける
3.  `<ClaimsProviderSelections>` ノード下に次の XML スニペットを追加します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>手順 4.3. ボタンをアクションにリンクする
ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 ここでのアクションは、Azure AD B2C が Twitter アカウントと通信してトークンを受信するためのアクションです。 Twitter アカウント クレーム プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  `<ClaimsExchanges>` ノード下に次の XML スニペットを追加します。

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    > [!NOTE]
    >
    > * `Id` が前のセクションの `TargetClaimsExchangeId` と同じ値であることを確認します
    > * `TechnicalProfileReferenceId` ID が前に作成した技術プロファイル (Twitter-OAUTH1) に設定されていることを確認します。

## <a name="step-5-upload-the-policy-to-your-tenant"></a>手順 5: ポリシーをテナントにアップロードする
1.  [Azure ポータル](https://portal.azure.com)で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替え、**[Azure AD B2C]** をクリックします。
2.  **[Identity Experience Framework]** を選択します。
3.  **[すべてのポリシー]** をクリックします。
4.  **[Upload Policy]**(ポリシーのアップロード) を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>手順 6: [今すぐ実行] を使用してカスタム ポリシーをテストする

1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

    > [!NOTE]
    >
    >**[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。
3.  Twitter アカウントを使用してサインインできます。

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-profile-edit-user-journey"></a>手順 7: [省略可能] プロファイル編集ユーザー体験に Twitter アカウント クレーム プロバイダーを登録する
Twitter アカウント ID プロバイダーをユーザーの `ProfileEdit` ユーザー体験にも追加することができます。 使用可能にするには、手順 4 を繰り返します。 今回は、`Id="ProfileEdit"` を含む `<UserJourney>` ノードをを選択します。 ポリシーを保存し、アップロードしてテストします。


## <a name="download-the-complete-policy-files"></a>完全なポリシー ファイルをダウンロードする
省略可能: これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシーを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)