---
title: Azure Active Directory B2C のカスタム ポリシーを使って Twitter を OAuth1 ID プロバイダーとして追加する | Microsoft Docs
description: OAuth1 プロトコルを使って ID プロバイダーとして Twitter を使用する。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 786f0dfd0cf3cf2e9ab0d16e26811fabd6bfc17c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440955"
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使用して Twitter を OAuth1 ID プロバイダーとして追加する
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して Twitter アカウントのユーザーのサインインを有効にする方法を示します。

## <a name="prerequisites"></a>前提条件
「[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」記事の手順を完了します。

## <a name="step-1-create-a-twitter-account-application"></a>手順 1: Twitter アカウント アプリケーションを作成する
Azure Active Directory B2C (Azure AD B2C) で ID プロバイダーとして Twitter を使用するには、Twitter アプリケーションを作成し、適切なパラメーターを指定する必要があります。 Twitter アプリケーションは、[Twitter のサインアップ ページ](https://twitter.com/signup)に移動して登録することができます。

1. [Twitter Developers](https://apps.twitter.com/) の Web サイトに移動し、Twitter アカウント資格情報でサインインして、**[Create New App]** を選択します。

    ![Twitter アカウント - 新しいアプリを作成する](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. **[Create an application]** ウィンドウで、次の操作を行います。
 
    a. 新しいアプリの **[Name]** と **[Description]** を入力します。 

    b. **[Web サイト]** ボックスに、**https://login.microsoftonline.com** を貼り付けます。 

    c. 4. **[Callback URL]** に「`https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`」と入力します。 **{tenant}** は実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換え、**{policyId}** は実際のポリシー ID (例: b2c_1_policy) に置き換える必要があります。  **コールバック URL は、すべて小文字である必要があります。** Twitter ログインを使用するすべてのポリシーのコールバック URL を追加する必要があります。 アプリケーションで使用する場合は、` login.microsoftonline.com` の代わりに `b2clogin.com` を使用してください。

    d. ページの下部に記載されている条項を読んで同意し、**[Create your Twitter application]** を選択します。

    ![Twitter アカウント - 新しいアプリケーションを追加する](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. **[B2C demo]** ウィンドウで、**[Settings]** を選択し、**[Allow this application to be used to sign in with Twitter]** チェック ボックスをオンにしてから **[Update Settings]** を選択します。

4. **[Keys and Access Tokens]** を選択し、**[Consumer Key (API Key)]** と **[Consumer Secret (API Secret)]** の値を書き留めます。

    ![Twitter アカウント - アプリケーションのプロパティを設定する](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >コンシューマー シークレットは、重要なセキュリティ資格情報です。 このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>手順 2: Azure AD B2C に Twitter アカウント アプリケーション キーを追加する
Twitter アカウントでのフェデレーションには、アプリケーションに代わって Azure AD B2C を信頼するために、Twitter アカウント用のコンシューマー シークレットが必要になります。 Twitter アプリケーションのコンシューマー シークレットを Azure AD B2C テナントに格納するには、次の操作を行います。 

1. Azure AD B2C テナントで、**[B2C Settings]\(B2C 設定\)** > **[Identity Experience Framework]** の順に選択します。

2. テナント内で使用できるキーを表示するには、**[ポリシー キー]** を選択します。

3. **[追加]** を選択します。

4. **[オプション]** ボックスで、**[手動]** を選択します。

5. **[名前]** ボックスで、**[TwitterSecret]** を選択します。  
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。

6. **[シークレット]** ボックスに、[アプリケーション登録ポータル](https://apps.dev.microsoft.com)からの Microsoft アプリケーション シークレットを入力します。

7. **[キー使用法]** には **[暗号化]** を使用します。

8. **[作成]** を選択します。

9. `B2C_1A_TwitterSecret` キーを作成したことを確認します。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>手順 3: 拡張ポリシーにクレーム プロバイダーを追加する

ユーザーが Twitter アカウントを使用してサインインできるようにするには、Twitter をクレーム プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用する一連の要求を提供します。

拡張ポリシー ファイルに `<ClaimsProvider>` ノードを追加することで、Twitter をクレーム プロバイダーとして定義します。

1. 作業ディレクトリで、*TrustFrameworkExtensions.xml* 拡張ポリシー ファイルを開きます。 

2. `<ClaimsProviders>` セクションを検索します。

3. `<ClaimsProviders>` ノードで、次の XML スニペットを追加します。  

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

4. *client_id* の値を Twitter アカウント アプリケーションのコンシューマー キーに置き換えます。

5. ファイルを保存します。

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>手順 4: サインアップまたはサインイン ユーザー体験に Twitter アカウント クレーム プロバイダーを登録する
ID プロバイダーをセットアップしました。 ただし、どのサインアップ/サインイン ウィンドウでもまだ使用できません。 ここで、Twitter アカウント ID プロバイダーをユーザーの `SignUpOrSignIn` ユーザー体験に追加する必要があります。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>手順 4.1: ユーザー体験のコピーを作成する
ユーザー体験を使用できるようにするには、既存のユーザー体験テンプレートの複製を作成してから、Twitter ID プロバイダーを追加します。

>[!NOTE]
>`<UserJourneys>` 要素をポリシーの基本ファイルから *TrustFrameworkExtensions.xml* 拡張ファイルにコピーした場合は、次のセクションにスキップできます。

1. ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。

2. `<UserJourneys>` 要素を検索し、`<UserJourney>` ノードのコンテンツ全体を選択してから **[切り取り]** を選択し、クリップボードに選択したテキストを移動します。

3. 拡張ファイル (TrustFrameworkExtensions.xml など) を開いてから、`<UserJourneys>` 要素を検索します。 要素が存在しない場合は追加します。

4. `<UserJourney>` ノードのコンテンツ全体 (手順 2 でクリップボードに移動したもの) を `<UserJourneys>` 要素に貼り付けます。

### <a name="step-42-display-the-button"></a>手順 4.2: "ボタン" を表示する
`<ClaimsProviderSelections>` 要素は、クレーム プロバイダーの選択オプションとその順序の一覧を定義します。 `<ClaimsProviderSelection>` ノードは、サインアップまたはサインイン ページの ID プロバイダーのボタンに類似しています。 Twitter アカウント用の `<ClaimsProviderSelection>` ノードを追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 この要素を追加するには、次の操作を行います。

1. コピーしたユーザー体験で `Id="SignUpOrSignIn"` を含む `<UserJourney>` ノードを検索します。

2. `Order="1"` を含む `<OrchestrationStep>` ノードを見つけます。

3. `<ClaimsProviderSelections>` 要素で、次の XML スニペットを追加します。

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>手順 4.3: ボタンをアクションにリンクする
ボタンが所定の位置に配置されたので、それをアクションにリンクする必要があります。 ここでのアクションは、Azure AD B2C が Twitter アカウントと通信してトークンを受信するためのものです。 Twitter アカウント クレーム プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1. `<UserJourney>` ノードで `Order="2"` を含む `<OrchestrationStep>` ノードを検索します。
2. `<ClaimsExchanges>` 要素で、次の XML スニペットを追加します。

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* `Id` の値が前のセクションの `TargetClaimsExchangeId` のものと同じであることを確認します。
    >* `TechnicalProfileReferenceId` ID が前に作成した技術プロファイル (Twitter-OAUTH1) に設定されていることを確認します。

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
    これで、Twitter アカウントを使用してサインインできます。

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>手順 7: (省略可能) プロファイル編集ユーザー体験に Twitter アカウント クレーム プロバイダーを登録する
Twitter アカウント ID プロバイダーを `ProfileEdit` ユーザー体験に追加することもできます。 ユーザー体験を使用できるようにするには、"手順 4" を繰り返します。 ここでは、`Id="ProfileEdit"` を含む `<UserJourney>` ノードを選択します。 ポリシーを保存し、アップロードしてテストします。


## <a name="optional-download-the-complete-policy-files"></a>(省略可能) 完全なポリシー ファイルをダウンロードする
「[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」チュートリアルの完了後に、独自のカスタム ポリシー ファイルを使用してシナリオを構築することをお勧めします。 参照用に[サンプルのポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)が提供されています。
