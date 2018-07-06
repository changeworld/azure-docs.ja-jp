---
title: Azure Active Directory B2C のカスタム ポリシーを使ってマルチテナントの Azure AD ID プロバイダーを追加する | Microsoft Docs
description: カスタム ポリシーを使用してマルチテナントの Azure AD ID プロバイダーを追加する - Azure Active Directory B2C。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2a8a23245a17c9a80c70860588a8312dbbb5e926
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446077"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使用して、ユーザーがマルチテナント Azure AD ID プロバイダーにサインインできるようにする

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して、Azure Active Directory (Azure AD) のマルチテナント エンドポイントによってユーザーのサインインを有効にする方法について説明します。 これにより、複数の Azure AD テナントのユーザーが、テナントごとにテクニカル プロバイダーを構成せずに Azure AD B2C にサインインできるようになります。 ただし、これらのテナントのいずれのゲスト メンバーもサインインは**できません**。 このためには、[各テナントを個別に構成](active-directory-b2c-setup-aad-custom.md)する必要があります。

>[!NOTE]
> 以下の手順では、組織の Azure AD テナントには "Contoso.com"を使用して、Azure AD B2C テナントとして "fabrikamb2c.onmicrosoft.com" を使用します。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

その手順は次のとおりです。
     
1. Azure Active Directory B2C (Azure AD B2C) テナントの作成。
1. Azure AD B2C アプリケーションの作成。    
1. 2 つのポリシー エンジン アプリケーションの登録。  
1. キーのセットアップ。 
1. スターター パックのセットアップ。

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>手順 1. マルチテナントの Azure AD アプリを作成する

マルチテナントの Azure AD エンドポイントを使用してユーザーのサインインを有効にするには、いずれかの Azure AD テナントにマルチテナント アプリケーションが登録されている必要があります。 この記事では、Azure AD B2C テナントにマルチテナントの Azure AD アプリケーションを作成する方法を説明します。 その後、そのマルチテナントの Azure AD アプリケーションを使用して、ユーザーのサインインを有効にします。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のバーで、自分のアカウントを選択します。 **[ディレクトリ]** の一覧で Azure AD B2C テナントを選択して、Azure AD アプリケーション (fabrikamb2c.onmicrosoft.com) を登録します。
1. 左側のウィンドウで **[More Services] \(その他のサービス)** をクリックし、[App registrations] \(アプリの登録) を検索します。
1. **[新しいアプリケーションの登録]** を選択します。
1. アプリケーションの名前を入力します (`Azure AD B2C App` など)。
1. アプリケーション タイプとして **[Web app / API] \(Web アプリ/API)** を選択します。
1. **[サインオン URL]** に次の URL を入力します。`yourtenant`は、Azure AD B2C テナントの名前 (`fabrikamb2c.onmicrosoft.com`) で置き換えられます。

    >[!NOTE]
    >**[サインイン URL]** の "yourtenant" の値は、すべて小文字にする必要があります。

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. アプリケーション ID を保存します。
1. 新しく作成された Web アプリケーションを選択します。
1. **[設定]** ブレードで、**[プロパティ]** を選択します。
1. **[マルチテナント]** を **[はい]** に設定します。
1. **[設定]** ブレードで **[キー]** を選択します。
1. 新しいキーを作成して保存します。 そのキーは次のセクションの手順で使用します。

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>手順 2. Azure AD キーの Azure AD B2C への追加

Azure AD B2C 設定で、アプリケーション キーを登録する必要があります。 これを行うには、次の手順を実行します。

1. Azure AD B2C の [設定] メニューに移動します。
1. **[Identity Experience Framework]** > **[ポリシー キー]** の順にクリックします。
1. **[+追加]** を選択します。
1. 以下のオプションを選択または入力します。
   * **[Manual] \(手動)** を選択します。
   * **[名前]** には、Azure AD テナント名に一致する名前 (例: `AADAppSecret`) を選択します。  プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
   * アプリケーション キーを **[シークレット]** ボックスに貼り付けます。
   * **[署名]** を選択します。
1. **[作成]** を選択します。
1. キー `B2C_1A_AADAppSecret` を作成したことを確認します。

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>手順 3. 基本ポリシーでの要求プロバイダーの追加

ユーザーが Azure AD を使用してサインインできるようにするには、Azure AD を要求プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用する一連の要求を提供します。 

ポリシーの拡張ファイル内で Azure AD を `<ClaimsProvider>` ノードに追加することで、Azure AD を要求プロバイダーとして定義できます。

1. 作業ディレクトリから拡張ファイル (TrustFrameworkExtensions.xml) を開きます。
1. セクション `<ClaimsProviders>` を探します。 存在しない場合は、ルート ノードの下に追加します。
1. 次のように新しい `<ClaimsProvider>` ノードを追加します。

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

1. `<ClaimsProvider>` ノードの下で、`<Domain>` の値を、他の ID プロバイダーと区別するために使用できる一意の値に更新します。
1. `<TechnicalProfile>` ノードで、`<DisplayName>` の値を更新します。 この値は、サインイン画面のサインイン ボタン上に表示されます。
1. `<Description>` の値を更新します。
1. `<Item Key="client_id">` を、Azure AD マルチテナント アプリ登録のアプリケーション ID に設定します。

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>手順 3.1 Azure AD テナントの特定の一覧へのアクセスを制限する

> [!NOTE]
> **ValidTokenIssuerPrefixes** の値として `https://sts.windows.net` を使用すると、すべての Azure AD ユーザーがアプリにサインインできるようになります。

有効なトークン発行者の一覧を更新し、ユーザーがサインインできる Azure AD テナントの特定の一覧へのアクセスを制限したい場合があります。 値を取得するには、サインインするユーザーの特定の Azure AD テナントごとのメタデータを調べる必要があります。 データの形式は、`https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration` のようになります。ここで、`yourAzureADtenant` は Azure AD テナント (contoso.com またはその他の Azure AD テナント) の名前です。
1. ブラウザーを開き、メタデータ URL に移動します。
1. ブラウザーで '発行者' オブジェクトを検索し、その値をコピーします。 値は `https://sts.windows.net/{tenantId}/` のようになります。
1. `ValidTokenIssuerPrefixes` キーの値を貼り付けます。 コンマで区切って複数の値を追加することもできます。 その例は、上のサンプル XML でコメントされています。

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>手順 4. Azure AD アカウント クレーム プロバイダーを登録する

### <a name="step-41-make-a-copy-of-the-user-journey"></a>手順 4.1. ユーザー体験のコピーを作成する

ここで、Azure AD の ID プロバイダーをいずれかのユーザー体験に追加する必要があります。 この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。

これを行うには、既存のテンプレート ユーザー体験の複製を作成してから、Azure AD の ID プロバイダーも含まれるように変更します。

1. ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
1. `<UserJourneys>` 要素を見つけて、`Id="SignUpOrSignIn"` を含む `<UserJourney>` ノード全体をコピーします。
1. 拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
1. コピーした `<UserJourney>` ノード全体を `<UserJourneys>` 要素の子として貼り付けます。
1. 新しいユーザー体験の ID の名前を変更します (たとえば `SignUpOrSignUsingAzureAD` に名前を変更します)。 

### <a name="step-42-display-the-button"></a>手順 4.2. "ボタン" を表示する

`<ClaimsProviderSelection>` 要素は、サインアップ/サインイン画面の ID プロバイダーのボタンに類似しています。 Azure AD のために `<ClaimsProviderSelection>` 要素を追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 この要素を追加するには、次の手順を実行します。

1. 作成したユーザー体験内で、`Order="1"` を含む `<OrchestrationStep>` ノードを見つけます。
1. 以下を追加します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. `TargetClaimsExchangeId` を適切な値に設定します。 他の場合と同じ規則に従うことをお勧めします: *\[ClaimProviderName\]Exchange*。

### <a name="step-43-link-the-button-to-an-action"></a>手順 4.3. ボタンをアクションにリンクする

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Azure AD と通信してトークンを受信します。 Azure AD 要求プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1. `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
1. 以下を追加します。

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. `Id` を、前のセクションの `TargetClaimsExchangeId` と同じ値に更新します。
1. `TechnicalProfileReferenceId` を、前に作成した技術プロファイルの ID に更新します (Common-AAD)。

## <a name="step-5-create-a-new-rp-policy"></a>手順 5: 新しい RP ポリシーを作成する

ここで、作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新する必要があります。
 
1. 作業ディレクトリに SignUpOrSignIn.xml のコピーを作成してその名前を変更します (たとえば、その名前を SignUpOrSignInWithAAD.xml に変更します)。  
1. 新しいファイルを開き、`<TrustFrameworkPolicy>` の `PolicyId` 属性を一意な値 (例: SignUpOrSignInWithAAD) で更新します。 これがポリシーの名前になります (例: B2C\_1A\_SignUpOrSignInWithAAD)。 
1. `<DefaultUserJourney>` の `ReferenceId` 属性を変更して、作成した新しいユーザー体験の ID と一致するようにします (例: SignUpOrSignUsingAzureAD)。 
1. 変更内容を保存し、ファイルをアップロードします。 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>手順 6: ポリシーをテナントにアップロードする

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替えてから、**[Azure AD B2C]** を選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[すべてのポリシー]** を選択します。
1. **[ポリシーのアップロード]** を選択します。
1. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
1. `TrustFrameworkExtensions.xml` ファイルと RP ファイル (例: `SignUpOrSignInWithAAD.xml`) をアップロードし、検証に合格したことを確認します。

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>手順 7: [今すぐ実行] を使用してカスタム ポリシーをテストする

1. **[Azure AD B2C の設定]** を選択してから、**[Identity Experience Framework]** を選択します。
    > [!NOTE]
    > [今すぐ実行] を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

1. アップロードした証明書利用者 (RP) カスタム ポリシー (*B2C\_1A\_SignUpOrSignInWithAAD*) を開き、**[今すぐ実行]** を選択します。
1. これで、Azure AD アカウントを使用してサインインできるようになりました。

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(省略可能) 手順 8: Azure AD アカウント クレーム プロバイダーをプロファイル編集ユーザー体験に登録する

Azure AD アカウント ID プロバイダーを `ProfileEdit` ユーザー体験に追加したい場合もあります。 ユーザー体験を使用できるようにするには、手順 4. から 6. を繰り返します。 ここでは、`Id="ProfileEdit"` を含む `<UserJourney>` ノードを選択します。 ポリシーを保存し、アップロードしてテストします。

## <a name="troubleshooting"></a>トラブルシューティング

問題を診断するには、[トラブルシューティング](active-directory-b2c-troubleshoot-custom.md)についての記事を参照してください。

## <a name="next-steps"></a>次の手順

[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) にフィードバックを提供します。
