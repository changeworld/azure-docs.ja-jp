---
title: "Azure Active Directory B2C: カスタム ポリシーを使用して id プロバイダーとして Microsoft アカウント (MSA) を追加します。"
description: "OpenID 接続 (OIDC) プロトコルを使用して id プロバイダーとして Microsoft を使用してサンプル"
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
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 8c981046ff41d3927ff60d6dc4f40366ae25ba74
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使用して id プロバイダーとして Microsoft アカウント (MSA) を追加します。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事は、Microsoft アカウント (MSA) からユーザーのサインインを使用すると有効にする方法を示します[カスタム ポリシー](active-directory-b2c-overview-custom.md)です。

## <a name="prerequisites"></a>前提条件
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

その手順は次のとおりです。

1.  Microsoft アカウントのアプリケーションを作成します。
2.  Azure AD B2C への Microsoft アカウントのアプリケーション キーの追加
3.  ポリシーに追加の要求プロバイダー
4.  ユーザーの旅に Microsoft アカウントの要求プロバイダーの登録
5.  テナントのポリシーを Azure AD B2C にアップロードし、テスト

## <a name="create-a-microsoft-account-application"></a>Microsoft アカウント アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Microsoft アカウントを使用するには、Microsoft アカウント アプリケーションを作成し、適切なパラメーターを提供する必要があります。 Microsoft アカウントが必要です。 いずれかがあるない場合は、次を参照してください。 [https://www.live.com/](https://www.live.com/)です。

1.  [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) に移動し、Microsoft アカウントの資格情報でサインインします。
2.  **[アプリの追加]**をクリックします。

    ![Microsoft アカウント - アプリの追加](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  提供、**名前**、アプリケーションの**連絡先のメール アドレス**、ボックスをオフに**役立つどうぞ** をクリック**作成**です。

    ![Microsoft アカウント - アプリケーションの登録](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  **[アプリケーション ID]**の値をコピーします。Microsoft アカウント、テナント id プロバイダーとして構成する必要があります。

    ![Microsoft アカウント - アプリケーション Id の値のコピー](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  をクリックして**追加プラットフォーム**

    ![Microsoft account - Add platform](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  プラットフォームの一覧から選択**Web**です。

    ![Microsoft アカウント - プラットフォームの一覧から Web を選択します。](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  **[リダイレクト URI]** フィールドに、「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。

    ![Microsoft アカウント - セット リダイレクト Url](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  **[アプリケーション シークレット]** セクションの **[新しいパスワードを生成]** をクリックします。 画面に表示される新しいパスワードをコピーします。 Microsoft アカウント、テナント id プロバイダーとして構成する必要があります。 このパスワードは重要なセキュリティ資格情報です。

    ![Microsoft account - Generate new password](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Microsoft アカウント - 新しいパスワードのコピー](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  **[詳細オプション]** セクションの **[Live SDK サポート]** というチェック ボックスをオンにします。 [ **Save**] をクリックします。

    ![Microsoft account - Live SDK support](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Azure AD B2C に Microsoft アカウントのアプリケーション キーを追加します。
Microsoft アカウントとのフェデレーションには、信頼 Azure AD B2C、アプリケーションの代わりに Microsoft アカウントのクライアント シークレットが必要です。 Azure AD B2C テナントに、Microsoft アカウント アプリケーション secert を格納する必要があります。   

1.  Azure AD B2C テナントに移動し、選択**B2C 設定** > **Identity エクスペリエンス フレームワーク**
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  をクリックして**+ 追加**です。
4.  **[オプション]** には **[Manual] \(手動)** を使用します。
5.  **[名前]** には `MSASecret` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。
6.  **シークレット**https://apps.dev.microsoft.com から Microsoft アプリケーション シークレットを入力
7.  **[キー使用法]** には **[署名]** を使用します。
8.  **[作成]**
9.  キー `B2C_1A_MSASecret` を作成したことを確認します。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>拡張ポリシーの要求プロバイダーを追加します。
ユーザーが Microsoft アカウントを使用してサインインする場合は、要求プロバイダーとしての Microsoft アカウントを定義する必要があります。 つまり、Azure AD B2C と通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されたことを確認する Azure AD B2C で使用されるクレームのセットを提供します。

Microsoft アカウントを要求プロバイダーとして追加して定義`<ClaimsProvider>`拡張機能のポリシー ファイル内のノード。

1.  自分の作業ディレクトリから、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。
2.  検索、`<ClaimsProviders>`セクション
3.  次の下にある XML スニペットを追加、`ClaimsProviders`要素。

    ```xml
<ClaimsProvider>
    <Domain>live.com</Domain>
    <DisplayName>Microsoft Account</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="MSA-OIDC">
        <DisplayName>Microsoft Account</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <Metadata>
        <Item Key="ProviderName">https://login.live.com</Item>
        <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="scope">openid profile email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="email" />
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

4.  置き換える`client_id`Microsoft アカウント アプリケーション クライアント Id を持つ値

5.  ファイルを保存します。

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Microsoft アカウントでは、サインオン プロバイダーを要求を登録またはサインイン ユーザー向けの資料

この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 ユーザーに Microsoft アカウントの id プロバイダーを追加する必要があります`SignUpOrSignIn`ユーザー旅です。 利用できるように、既存のテンプレート ユーザー旅の複製を作成します。  次の Microsoft アカウントの id プロバイダーを追加します。

> [!NOTE]
>
>以前にコピーした場合、`<UserJourneys>`拡張ファイルに、ポリシーの基本ファイルから要素`TrustFrameworkExtensions.xml`、このセクションにスキップできます。

1.  ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2.  検索、`<UserJourneys>`要素の内容全体をコピーおよび`<UserJourneys>`ノード。
3.  拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
4.  内容全体を貼り付ける`<UserJournesy>`ノードの子としてコピーした、`<UserJourneys>`要素。

### <a name="display-the-button"></a>ボタンを表示します。
`<ClaimsProviderSelections>`要素が要求プロバイダーの選択 オプションとその順序のリストを定義します。  `<ClaimsProviderSelection>`要素は、サインアップ-増/サインイン ページの id プロバイダーのボタンに似ています。 追加する場合、 `<ClaimsProviderSelection>` Microsoft アカウントの要素は、新しいボタン表示 ページで、ユーザーが入るです。 この要素を追加するには、次の手順を実行します。

1.  検索、`<UserJourney>`ノードを含む`Id="SignUpOrSignIn"`コピーしたユーザーの旅にします。
2.  検索、`<OrchestrationStep>`ノードを含む`Order="1"`
3.  次の下にある XML スニペットを追加`<ClaimsProviderSelections>`ノード。

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク
ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 アクション、ここでは、トークンを受信する Microsoft アカウントを使用して通信するために Azure AD B2C です。 Microsoft アカウントは、要求プロバイダーの技術的なプロファイルをリンクすることで、ボタンをアクションにリンクします。

1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  次の下にある XML スニペットを追加`<ClaimsExchanges>`ノード。

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * 確認、`Id`のと同じ値を持つ`TargetClaimsExchangeId`前のセクション
>   * 確認してください`TechnicalProfileReferenceId`ID が設定されて、技術的なプロファイルを以前 (MSA OIDC) を作成します。

## <a name="upload-the-policy-to-your-tenant"></a>ポリシーをテナントにアップロードします。
1.  [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、**[Azure AD B2C]** ブレードを開きます。
2.  **[Identity Experience Framework]** を選択します。
3.  開く、**すべてのポリシー**ブレードです。
4.  選択**ポリシーをアップロード**です。
5.  確認**存在する場合、ポリシーを上書き**ボックス。
6.  **アップロード**TrustFrameworkExtensions.xml 検証が失敗していないを確認してください

## <a name="test-the-custom-policy-by-using-run-now"></a>[今すぐ実行] を使用してカスタム ポリシーをテストする

1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
> [!NOTE]
>
>**[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。
2.  開いている**B2C_1A_signup_signin**、アップロードした証明書利用者 (rp) カスタム ポリシー。 **[今すぐ実行]** を選択します。
3.  Microsoft アカウントを使用してサインインすることができます。

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[オプション]プロファイル編集ユーザー旅に Microsoft アカウントの要求プロバイダーを登録します。
ユーザーにも、Microsoft アカウントの id プロバイダーを追加することがあります`ProfileEdit`ユーザー旅です。 利用できるようには、最後の 2 つの手順を繰り返します。

### <a name="display-the-button"></a>ボタンを表示します。
1.  (たとえば、TrustFrameworkExtensions.xml) ポリシーの拡張機能ファイルを開きます。
2.  検索、`<UserJourney>`ノードを含む`Id="ProfileEdit"`コピーしたユーザーの旅にします。
3.  検索、`<OrchestrationStep>`ノードを含む`Order="1"`
4.  次の下にある XML スニペットを追加`<ClaimsProviderSelections>`ノード。

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク
1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  次の下にある XML スニペットを追加`<ClaimsExchanges>`ノード。

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>今すぐ実行を使用してカスタム ポリシーのプロファイル編集のテストします。
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
2.  開いている**B2C_1A_ProfileEdit**、アップロードした証明書利用者 (rp) カスタム ポリシー。 **[今すぐ実行]** を選択します。
3.  Microsoft アカウントを使用してサインインすることができます。

## <a name="download-the-complete-policy-files"></a>完全なポリシー ファイルをダウンロードします。
省略可能: ことをお勧めこれらのサンプル ファイルを使用する代わりにカスタム ポリシーの概要の完了後のファイルがウォークスルー独自のカスタム ポリシーを使用して、シナリオを構築します。  [参照でポリシー ファイルのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
