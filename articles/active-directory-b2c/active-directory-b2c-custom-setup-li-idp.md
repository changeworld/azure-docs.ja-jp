---
title: "Azure Active Directory B2C: カスタム ポリシーを使用して LinkedIn を OAuth2 ID プロバイダーとして追加する"
description: "OAuth2 プロトコルとカスタム ポリシーを使用した LinkedIn プリケーションの設定に関するハウツー記事"
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
ms.date: 10/03/2017
ms.author: yoelh
ms.openlocfilehash: ac4c0212ffc13b24b6035756f1210d62b7b840c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使用して LinkedIn を ID プロバイダーとして追加する
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して LinkedIn アカウントからのユーザーのサインインを有効にする方法を示します。

## <a name="prerequisites"></a>前提条件
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

その手順は次のとおりです。
1.  LinkedIn アカウント アプリケーションを作成する
2.  Azure AD B2C に LinkedIn アカウント アプリケーション キーを追加する
3.  ポリシーに要求プロバイダーを追加する
4.  ユーザー体験に LinkedIn アカウント クレーム プロバイダーを登録する
5.  Azure AD B2C テナントにポリシーをアップロードしてテストする

## <a name="step-1-create-a-linkedin-account-application"></a>手順 1: LinkedIn アカウント アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして LinkedIn を使用するには、LinkedIn アプリケーションを作成し、適切なパラメーターを提供する必要があります。 LinkedIn アプリケーションは、こちら ([https://LinkedIn.com/signup](https://LinkedIn.com/signup)) で登録できます。

1.  [LinkedIn アプリケーション管理](https://www.linkedin.com/secure/developer?newapp=) Web サイトに移動し、LinkedIn アカウント資格情報でサインインし、**[アプリケーションの作成]** をクリックします。

    ![LinkedIn アカウント - アプリケーションの作成](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2.  1.  **[会社名]** を入力し、新しいアプリの説明的な **[名前]**と **[説明]** を入力します。
    2.  **[アプリケーションのロゴ]** をアップロードします。
    3.  **[Application Use]\(アプリケーションの用途\)** を選択します。
    4.  **[Web サイトの URL]** の値に `https://login.microsoftonline.com` を貼り付けます。
    5.  **[勤務先の電子メール]** と **[勤務先電話番号]** を入力します。
    6.  ページ下部の使用条件を確認して同意します。 その後、**[送信]** をクリックします。

    ![LinkedIn アカウント - アプリケーションのプロパティを構成する](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3.  メニューから **[認証]** をクリックします。 **[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

    **[Authorized redirect URLs]\(承認済みのリダイレクトURL URL\)** に、`https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` を貼り付けます。 {tenant} を実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換え、 HTTPS スキームを使用していることを確認した後、**[追加]** をクリックします。

    ![LinkedIn アカウント - 認証済みのリダイレクト URL の設定](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    > [!NOTE]
    >
    >クライアント シークレットは、重要なセキュリティ資格情報です。 このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

4.  **[設定]** タブをクリックし、**[アプリケーションの状態]** を **[ライブ]** に変更した後、**[更新]** をクリックします。

    ![LinkedIn アカウント - アプリケーションの状態の設定](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>手順 2: Azure AD B2C に LinkedIn アプリケーション キーを追加する
LinkedIn アカウントでのフェデレーションには、アプリケーションに代わって Azure AD B2C を信頼するために、LinkedIn アカウント用のクライアント シークレットが必要になります。 LinkedIn のアプリケーション シークレットを Azure AD B2C テナントに格納する必要があります。  

1.  Azure AD B2C テナントに移動し、**[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  **[+ 追加]** をクリックします。
4.  **[オプション]** には **[Manual] \(手動)** を使用します。
5.  **[名前]** には `LinkedInSecret` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。
6.  **[シークレット]** ボックスに、https://apps.dev.microsoft.com からの LinkedIn アプリケーション シークレットを入力します
7.  **[キー使用法]** には **[暗号化]** を使用します。
8.  **[作成]** 
9.  キー `B2C_1A_LinkedInSecret` を作成したことを確認します。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>手順 3: 拡張ポリシーにクレーム プロバイダーを追加する
ユーザーが LinkedIn アカウントを使用してサインインできるようにするには、LinkedIn をクレーム プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用する一連の要求を提供します。

拡張ポリシー ファイルに `<ClaimsProvider>` ノードを追加することで、LinkedIn をクレーム プロバイダーとして定義します。

1.  作業ディレクトリから拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。 
2.  `<ClaimsProviders>` セクションを探します。
3.  `<ClaimsProviders>` ノードの下に次の XML スニペットを追加します。  

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

4.  `client_id` 値を LinkedIn アプリケーション クライアント ID に置き換えます。
5.  ファイルを保存します。

## <a name="step-4-register-the-linkedin-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>手順 4: サインアップまたはサインイン ユーザー体験に LinkedIn アカウント クレーム プロバイダーを登録する
この時点で、ID プロバイダーが設定されました。 ただし、いずれのサインアップ/サインイン画面でも、使用可能ではありません。 LinkedIn アカウント ID プロバイダーをユーザーの `SignUpOrSignIn` ユーザー体験に追加する必要があります。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>手順 4.1. ユーザー体験のコピーを作成する
使用可能にするには、既存のテンプレート ユーザー体験の複製を作成します。 その後、LinkedIn ID プロバイダーを追加します。

> [!NOTE]
>
>`<UserJourneys>` 要素をポリシーの基本ファイルから拡張ファイル (TrustFrameworkExtensions.xml) にコピーした場合は、このセクションをスキップできます。

1.  ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2.  `<UserJourneys>` 要素を見つけて、`<UserJourneys>` ノードのコンテンツ全体をコピーします。
3.  拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
4.  コピーした `<UserJournesy>` ノードのコンテンツ全体を `<UserJourneys>` 要素の子として貼り付けます。

### <a name="step-42-display-the-button"></a>手順 4.2. "ボタン" を表示する
`<ClaimsProviderSelections>` 要素は、要求プロバイダーの選択オプションとその順序の一覧を定義します。  `<ClaimsProviderSelection>` 要素は、サインアップ/サインイン ページの ID プロバイダーのボタンに類似しています。 LinkedIn アカウント用の `<ClaimsProviderSelection>` 要素を追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 この要素を追加するには、次の手順を実行します。

1.  コピーしたばかりのユーザー体験内で、`Id="SignUpOrSignIn"` を含む `<UserJourney>` ノードを見つけます。
2.  `Order="1"` を含む `<OrchestrationStep>` ノードを見つける
3.  `<ClaimsProviderSelections>` ノード下に次の XML スニペットを追加します。
```xml
<ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
```

### <a name="step-43-link-the-button-to-an-action"></a>手順 4.3. ボタンをアクションにリンクする
ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 ここでのアクションは、Azure AD B2C が LinkedIn アカウントと通信してトークンを受信するためのアクションです。 LinkedIn アカウント クレーム プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  `<ClaimsExchanges>` ノード下に次の XML スニペットを追加します。

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    > [!NOTE]
    >
    > * `Id` が前のセクションの `TargetClaimsExchangeId` と同じ値であることを確認します
    > * `TechnicalProfileReferenceId` ID が前に作成した技術プロファイル (LinkedIn-OAuth) に設定されていることを確認します。

## <a name="step-5-upload-the-policy-to-your-tenant"></a>手順 5: ポリシーをテナントにアップロードする
1.  [Azure ポータル](https://portal.azure.com)で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替え、**[Azure AD B2C]** をクリックします。
2.  **[Identity Experience Framework]** を選択します。
3.  **[すべてのポリシー]** をクリックします。
4.  **[ポリシーのアップロード]** を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>手順 6: [今すぐ実行] を使用してカスタム ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

    > [!NOTE]
    >
    >**[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。
3.  LinkedIn アカウントを使用してサインインできます。

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-profile-edit-user-journey"></a>手順 7: [省略可能] プロファイル編集ユーザー体験に LinkedIn アカウント クレーム プロバイダーを登録する
LinkedIn アカウント ID プロバイダーをユーザーの `ProfileEdit` ユーザー体験に追加することもできます。 使用可能にするには、手順 4 を繰り返します。 今回は、`Id="ProfileEdit"` を含む `<UserJourney>` ノードをを選択します。 ポリシーを保存し、アップロードしてテストします。

## <a name="download-the-complete-policy-files"></a>完全なポリシー ファイルをダウンロードする
省略可能: これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシーを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)