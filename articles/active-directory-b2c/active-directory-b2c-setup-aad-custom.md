---
title: "Azure Active Directory B2C: カスタム ポリシーを使用した Azure AD プロバイダーの追加 | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーに関するトピック"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 652a2eb0db0e41c4706e06cd0cb2e97ce1eb6ab2
ms.contentlocale: ja-jp
ms.lasthandoff: 05/12/2017


---
# <a name="azure-active-directory-b2c-log-in-using-azure-ad-accounts"></a>Azure Active Directory B2C: Azure AD アカウントを使用したログイン

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して特定の Azure AD 組織のユーザーのログインを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーを使ってみる](active-directory-b2c-get-started-custom.md)方法について説明した手順をすべて完了していることを確認します。

次のトピックがあります。

1. Azure AD B2C テナントの作成。
1. Azure AD B2C アプリケーションの作成。
1. 2 つのポリシー エンジン アプリケーションの登録。
1. キーのセットアップ。
1. スターター パックのセットアップ。

## <a name="create-an-azure-ad-app"></a>Azure AD アプリの作成

特定の Azure AD 組織のユーザーのログインを有効にするには、組織の Azure AD テナント内でアプリケーションを登録する必要があります。

>[!NOTE]
> 組織の Azure AD テナントを `contoso.com`、Azure AD B2C テナントを `fabrikamb2c.onmicrosoft.com` とします。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
1. 上部のバーにある自分のアカウントをクリックし、**[ディレクトリ]** の一覧から、アプリケーションを登録する組織の Azure AD テナント (つまり contoso.com) を選択します。
1. 左側のナビゲーションで **[More Services]**(その他のサービス) をクリックし、**[App registrations]**(アプリの登録) を検索します。
1. **[New application registration]**(新規アプリケーションの登録) を選択します。
1. **[Name]**(名前) にアプリケーションの名前を入力します (例: Azure AD B2Cアプリ)。
1. アプリケーション タイプとして **[Web app / API]**(Web アプリ/API) を選択します。
1. "サインオン URL" として、次の URL を入力します。`yourtenant` は、使用している Azure AD B2C テナントの名前 (つまり fabrikamb2c.onmicrosoft.com) に置き換えてください。

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. **アプリケーション ID** を保存します。
1. 新しく作成されたアプリをクリックします。
1. [Settings](設定) ブレードで、**[Keys]**(キー) をクリックします。
1. 新しいキーを作成し、次のセクションのために保存します。

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Azure AD キーの Azure AD B2C への追加

`contoso.com` のアプリケーション キーを Azure AD B2C テナントに格納する必要があります。 これを行うには、次の手順を実行します。

1. Azure AD B2C テナントに移動し、[B2C Settings]\(B2C 設定\)、[Identity Experience Framework]、[Policy Keys]\(ポリシー キー\) の順に開きます。
1. [+ 追加] をクリックします。
1. オプション:
 * [オプション]、[`Manual`] の順に選択します。
 * [名前]、[`ContosoAppSecret`] の順に選択します。Azure AD テナント名に一致する名前を選択します。  プレフィックス B2C_1A_ がキーの名前に自動的に追加されます。
 * アプリケーション キーを [`Secret`] ボックスに貼り付けます。
 * [署名] を選択します。
1. [`Create`] をクリックします。
1. 次のキーが作成されたことを確認します: `B2C_1A_ContosoAppSecret`


## <a name="add-a-claims-provider-in-your-base-policy"></a>基本ポリシーでの要求プロバイダーの追加

ユーザーが Azure AD を使用してログインできるようにするためには、Azure AD を要求プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用する一連の*要求*を*提供*します。 これを行うには、ポリシーの拡張ファイルで Azure AD を `<ClaimsProvider>` として追加します。

1. 作業ディレクトリ (TrustFrameworkExtensions.xml) から拡張ファイルを開きます。
1. セクション `<ClaimsProviders>` を探します。 存在しない場合は、ルート ノードの下に追加します。
1. 次のように新しい `<ClaimsProvider>` を追加します。

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. `<ClaimsProvider>` ノードの下で、`<Domain>` の値を、他の ID プロバイダーと区別するために使用できる一意の値に更新します。
1. `<ClaimsProvider>` ノードの下で、`<DisplayName>` の値を、要求プロバイダーを表すわかりやすい名前に更新します。 この値は現在使用されていません。

### <a name="update-the-technical-profile"></a>技術プロファイルの更新

Azure AD エンドポイントからトークンを取得するためには、Azure AD B2C で Azure AD との通信に使用するプロトコルを定義する必要があります。 これは、`<ClaimsProvider>` の `<TechnicalProfile>` 要素内で行います。

1. `<TechnicalProfile>` ノードの ID を更新します。 この ID は、ポリシーの他の部分からこの技術プロファイルを参照するために使用します。
1. `<DisplayName>` の値を更新します。 この値は、ログイン画面のログイン ボタンに表示されます。
1. `<Description>` の値を更新します。
1. Azure AD では OpenID Connect プロトコルを使用するので、`<Protocol>` が "OpenIDConnect" になっていることを確認してください。

特定の Azure AD テナントの設定を反映するように、上の XML の `<Metadata>` セクションを更新する必要があります。 XML で、次のようにメタデータ値を更新します。

1. `<Item Key="METADATA">` を `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration` に設定します。ここで、`yourAzureADtenant` は Azure AD テナント名です (例: contoso.com)。
1. ブラウザーを開き、更新した `Metadata` の URL に移動します。
1. ブラウザーで '発行者' オブジェクトを検索し、その値をコピーします。 値は `https://sts.windows.net/{tenantId}/` のようになります。
1. 値を XML の `<Item Key="ProviderName">` に貼り付けます。
1. `<Item Key="client_id">` をアプリ登録の `Application ID` に設定します。
1. `<Item Key="IdTokenAudience">` をアプリ登録の `Application ID` に設定します。
1. `<Item Key="response_types">` が `id_token` に設定されていることを確認します。
1. `<Item Key="UsePolicyInRedirectUri">` が `false` に設定されていることを確認します。

また、Azure AD B2C テナントに登録した Azure AD シークレットを Azure AD の `<ClaimsProvider>` にリンクする必要もあります。

* 上の XML の `<CryptographicKeys>` セクションで、`StorageReferenceId` の値を、定義したシークレットの参照 ID に更新します (例: ContosoAppSecret)。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C が Azure AD ディレクトリと通信する方法を認識するようにポリシーを設定しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。 そのためには、次の手順を実行します。

1. Azure AD B2C テナントの **[All Policies]**(すべてのポリシー) ブレードに移動します。
1. **[Overwrite the policy if it exists]**(ポリシーが存在する場合は上書き) チェック ボックスをオンにします。
1. 拡張ファイル (TrustFrameworkExtensions.xml) をアップロードし、検証に失敗しないことを確認します。

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Azure AD 要求プロバイダーのユーザー ジャーニーへの登録

ここで、Azure AD の ID プロバイダーをいずれかのユーザー ジャーニーに追加する必要があります。 この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 これを行うには、既存のテンプレート ユーザー ジャーニーの複製を作成してから変更し、Azure AD の ID プロバイダーも含めるようにします。

1. ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
1. `<UserJourneys>` 要素を見つけて、Id ="SignUpOrSignIn" を含む `<UserJourney>` 全体をコピーします。
1. 拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
1. コピーした `<UserJourney>` 全体を `<UserJourneys>` 要素の子として貼り付けます。
1. 新しい `<UserJourney>` (つまり SignUpOrSignUsingContoso) の ID の名前を変更します。

### <a name="display-the-button"></a>"ボタン" の表示

`<ClaimsProviderSelection>` 要素は、サインアップ/サインイン画面の ID プロバイダーのボタンに類似しています。 Azure AD の `<ClaimsProviderSelection>` 要素を追加することで、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 これを行うには、次の手順を実行します。

1. 作成した `<UserJourney>` で `Order="1"` になっている `<OrchestrationStep>` を見つけます。
1. 以下を追加します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. `TargetClaimsExchangeId` を適切な値に設定します。 他の場合と同じ規則に従うことをお勧めします - *\[ClaimProviderName\]Exchange*。

### <a name="link-the-button-to-an-action"></a>"ボタン" のアクションへのリンク

"ボタン" が所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Azure AD と通信してトークンを受信します。 これを行うには、Azure AD 要求プロバイダーの技術プロファイルをリンクします。

1. `<UserJourney>` ノードで `Order="2"` になっている `<OrchestrationStep>` を見つけます。
1. 以下を追加します。

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. `Id` を上の `TargetClaimsExchangeId` の値と同じ値に更新します。
1. `TechnicalProfileReferenceId` を、前に作成した技術プロファイルの `Id` に更新します (例: ContosoProfile)。

### <a name="upload-the-updated-extension-file"></a>更新済み拡張ファイルのアップロード

拡張ファイルの変更が終了しました。 このファイルを保存してアップロードし、すべての検証が正常に行われることを確認します。

### <a name="update-the-rp-file"></a>RP ファイルの更新

ここで、作成したユーザー ジャーニーを開始する RP ファイルを更新する必要があります。

1. 作業ディレクトリに SignUpOrSignIn.xml のコピーを作成してその名前を変更します (例: SignUpOrSignInWithAAD.xml)。
1. 新しいファイルを開き、`<TrustFrameworkPolicy>` の `PolicyId` 属性を一意な値 (例: SignUpOrSignInWithAAD) で更新します。 これがポリシーの名前になります (例: B2C\_1A\_SignUpOrSignInWithAAD)。
1. `<DefaultUserJourney>` の `ReferenceId` 属性を変更して、作成した新しいユーザー ジャーニーの ID と一致するようにします (例: SignUpOrSignUsingContoso)。
1. 変更内容を保存し、ファイルをアップロードします。

## <a name="troubleshooting"></a>トラブルシューティング

アップロードしたカスタム ポリシーのブレードを開いて [Run now](今すぐ実行) をクリックし、カスタム ポリシーをテストします。 エラーが発生する場合は、[トラブルシューティング](active-directory-b2c-troubleshoot-custom.md)の方法を参照してください。

## <a name="next-steps"></a>次のステップ

AADB2CPreview@microsoft.com にフィードバックを提供します。

