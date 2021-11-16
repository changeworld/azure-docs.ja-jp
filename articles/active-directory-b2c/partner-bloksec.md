---
title: Azure Active Directory B2C と BlokSec を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: パスワードレス認証のために Azure AD B2C 認証を BlokSec と統合する方法について説明します
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8e955a50ed85710c0a3a33680b1edc4ef5873da6
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135506"
---
# <a name="tutorial-configure-azure-active-directory-b2c-with-bloksec-for-passwordless-authentication"></a>チュートリアル: パスワードレス認証のために Azure Active Directory B2C と BlokSec を構成する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

::: zone-end

このサンプル チュートリアルでは、Azure Active Directory (AD) B2C 認証を [BlokSec](https://bloksec.com/) と統合する方法について説明します。 BlokSec は、お客様にパスワードレス認証とトークンレス多要素認証 (MFA) を提供することで、エンド ユーザーのログイン エクスペリエンスを簡略化します。 BlokSec は、パスワード スタッフィング、フィッシング、中間者攻撃などの ID 中心のサイバー攻撃から顧客を保護します。

## <a name="scenario-description"></a>シナリオの説明

BlokSec 統合には、次のコンポーネントが含まれています。

- **Azure AD B2C** - 任意の B2C アプリケーションの認可サーバー/ID プロバイダーとして構成されます。

- **BlokSec 分散化 ID ルーター** - BlokSec の DIaaS™ を適用して、エンド ユーザーの個人 ID プロバイダー (PIdP) アプリケーションに認証要求および認可要求をルーティングするサービスのゲートウェイとして機能します。Azure AD B2C の OpenID Connect (OIDC) ID プロバイダーとして構成されます。

- **BlokSec SDK ベースのモバイル アプリ** - 分散型認証シナリオでユーザーの PIdP として機能します。 組織が BlokSec SDK を使用して独自のモバイル アプリケーションを開発しない場合には、自由にダウンロードできる [BlokSec yuID](https://play.google.com/store/apps/details?id=com.bloksec) アプリケーションを使用できます。
次のアーキテクチャの図に、この実装を示します。

![アーキテクチャ図を示す画像](./media/partner-bloksec/partner-bloksec-architecture-diagram.png)

|手順| 説明|
|:---------------|:----------------|
|1.| ユーザーが Azure AD B2C アプリケーションへのログインを試みると、Azure AD B2C のサインインとサインアップの結合ポリシーに転送されます。|
|2.| Azure AD B2C によって、ユーザーは、OIDC 認可コード フローを使用する BlokSec 分散化 ID ルーターにリダイレクトされます。|
|3.| BlokSec 分散化ルーターでは、認証要求と認可要求のすべてのコンテキストの詳細を含むプッシュ通知をユーザーのモバイル アプリに送信します。|
|4.| ユーザーは認証チャレンジを確認します。承認された場合、ユーザーはデバイスで使用可能な指紋や顔スキャンなどの生体測定を求められ、ユーザーの ID を確認します。|
|5.| 応答は、ユーザーの一意のデジタル キーを使用してデジタル署名されます。 最終的な認証応答では、所有、存在、同意の証明を提供します。 応答は、BlokSec 分散化 ID ルーターに返されます。|
|6.| BlokSec 分散化 ID ルーターでは、分散型台帳に格納されているユーザーの変更できない一意の公開キーに対してデジタル署名を検証し、認証結果を使用して Azure AD B2C に応答します。|
|7.| 認証結果に基づいて、ユーザーにはアクセスが許可または拒否されます。|

## <a name="onboard-to-bloksec"></a>BlokSec にオンボードする

[フォーム](https://bloksec.com/)に入力し、BlokSec を使用してデモ テナントを要求します。 メッセージ フィールドでは、Azure AD B2C を使用してオンボードするように指示されます。 アプリ ストアから無料の BlokSec yuID モバイル アプリをダウンロードしてインストールします。 デモ テナントの準備が完了すると、メールが届きます。 BlokSec アプリケーションがインストールされているモバイル デバイスで、yuID アプリを使用して管理者アカウントを登録するリンクを選択します。

::: zone pivot="b2c-user-flow"

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

- BlokSec [試用版アカウント](https://bloksec.com/)。

- Web アプリケーションを[登録](./tutorial-register-applications.md)し、[ID トークンの暗黙的な許可を有効化](./tutorial-register-applications.md#enable-id-token-implicit-grant)します (まだこれらを行っていない場合)。
::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

- BlokSec [試用版アカウント](https://bloksec.com/)。

- Web アプリケーションを[登録](./tutorial-register-applications.md)し、[ID トークンの暗黙的な許可を有効化](./tutorial-register-applications.md#enable-id-token-implicit-grant)します (まだこれらを行っていない場合)。

- 「[**Azure Active Directory B2C でのカスタム ポリシーの概要**](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)」にある手順を完了する。
::: zone-end

### <a name="part-1---create-an-application-registration-in-bloksec"></a>パート 1 - BlokSec でアプリケーションの登録を作成する

1. BlokSec 管理ポータルにサインインします。 リンクは、BlokSec のオンボード時に受信したアカウント登録メールの一部として含まれています。

2. メイン ダッシュボードで、 **[アプリケーションの追加] > [カスタムの作成]** を選択します。

3. アプリケーションの詳細を次のように入力し、送信します。  

   |プロパティ  |値  |
   |---------|---------|
   |  名前         |Azure AD B2C または希望のアプリケーション名|
   |SSO の種類         | OIDC|
   |ロゴ URI     |[https://bloksec.io/assets/AzureB2C.png](https://bloksec.io/assets/AzureB2C.png) 選択した画像へのリンク|
   |リダイレクト URI     | https://**your-B2C-tenant-name**.b2clogin.com/**your-B2C-tenant-name**.onmicrosoft.com/oauth2/authresp<BR>**例**:      'https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp ' <BR><BR>カスタム ドメインを使用する場合は、「 https://**your-domain-name**/**your-tenant-name**.onmicrosoft.com/oauth2/authresp」と入力します。 <BR> your-domain-name を実際のカスタム ドメインに、your-tenant-name を実際のテナントの名前に置き換えます。         |
   |ログアウト後のリダイレクト URI  |https://**your-B2C-tenant-name**.b2clogin.com/**your-B2C-tenant-name**.onmicrosoft.com/ **{policy}** /oauth2/v2.0/logout <BR> [サインアウト要求を送信します](./openid-connect.md#send-a-sign-out-request)。 |

4. 保存したら、新しく作成した Azure AD B2C アプリケーションを選択してアプリケーション構成を開き、 **[アプリ シークレットの生成]** を選択します。

>[!NOTE]
>アプリケーション ID とアプリケーション シークレットは、後から Azure AD B2C で ID プロバイダーを構成するために必要です。

::: zone pivot="b2c-user-flow"

### <a name="part-2---add-a-new-identity-provider-in-azure-ad-b2c"></a>パート 2 - Azure AD B2C で新しい ID プロバイダーを追加する

1. Azure AD B2C テナントのグローバル管理者として [Azure portal](https://portal.azure.com/#home) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ダッシュボード]**  >  **[Azure Active Directory B2C]**  >  **[ID プロバイダー]** の順に移動します。
1. 新しい **[OpenID Connect プロバイダー]** を選択します。
1. **[追加]** を選択します。

### <a name="part-3---configure-an-identity-provider"></a>パート 3 - ID プロバイダーを構成する

1. **[ID プロバイダーの種類] > [OpenID Connect]** の順に選択します。

1. フォームに入力して、ID プロバイダーを設定します。

|プロパティ  |値  |
|:---------|:---------|
|名前     |BlokSec yuID を入力します - パスワードレスまたは選択した名前|
|メタデータ URL| `https://api.bloksec.io/oidc/.well-known/openid-configuration` |
|クライアント ID|**パート 1** でキャプチャされた BlokSec 管理者 UI のアプリケーション ID|
|クライアント シークレット|**パート 1** でキャプチャされた BlokSec 管理者 UI のアプリケーション シークレット|
|Scope|OpenID 電子メール プロファイル|
|応答の種類|コード|
|ドメインのヒント|yuID|

1. **[OK]** を選択します。

1. **[Map this identity provider's claims]** を選択します。

1. フォームに入力して、ID プロバイダーをマップします。

|プロパティ  |値  |
|:---------|:---------|
|User ID|sub|
|表示名|name|
|指定された名前|given_name|
|Surname|family_name|
|Email|email|

1. **[保存]** を選択して、新しい OIDC ID プロバイダーのセットアップを完了します。  

### <a name="part-4---user-registration"></a>パート 4 - ユーザーの登録

1. 先に指定した資格情報を使用して BlokSec 管理コンソールにサインインします。

1. 前に作成した Azure AD B2C アプリケーションに移動します。 右上にある歯車アイコンを選択し、 **[アカウントの作成]** を選択します。  

1. [アカウントの作成] フォームにユーザーの情報を入力し、[アカウント名] をメモし、 **[送信]** を選択します。  

ユーザーは、指定した電子メール アドレスで **アカウント登録メール** を受信します。 ユーザーに、BlokSec yuID アプリがインストールされているモバイル デバイスで登録リンクに従ってもらいます。

### <a name="part-5---create-a-user-flow-policy"></a>パート 5 - ユーザー フロー ポリシーを作成する

これで、B2C ID プロバイダー内に BlokSec が新しい OIDC ID プロバイダーとして表示されるはずです。  

1. Azure AD B2C テナントの **[ポリシー]** で、 **[ユーザー フロー]** を選択します。  

1. **[新しいユーザー フロー]** を選択します。

1. **[サインアップとサインイン]**  >  **[バージョン]**  >  **[作成]** を選択します。

1. ポリシーの **[名前]** を入力します。

1. [ID プロバイダー] セクションで、新しく作成した BlokSec ID プロバイダーを選択します。  

1. [ローカル アカウント] で **[なし]** を選択すると、電子メールとパスワード ベースの認証が無効になります。

1. **[ユーザー フローを実行します]** を選択します。

1. フォームに、`https://jwt.ms` のような 返信 URL を入力します。

1. ブラウザーは、BlokSec のログイン ページにリダイレクトされます。 ユーザーの登録中に登録されたアカウント名を入力します。 ユーザーは、BlokSec yuID アプリケーションがインストールされているモバイル デバイスにプッシュ通知を受け取ります。通知を開くと、ユーザーに認証チャレンジが表示されます。

1. 認証チャレンジが受け入れられると、ユーザーはブラウザーによって応答 URL にリダイレクトされます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end

::: zone pivot="b2c-custom-policy"

>[!NOTE]
>Azure Active Directory B2C で、[**カスタム ポリシー**](./user-flow-overview.md)は、主に、複雑なシナリオに取り組む用途向けに設計されています。 ほとんどのシナリオで、組み込み [**ユーザー フロー**](./user-flow-overview.md)を使用することをお勧めします。

### <a name="part-2---create-a-policy-key"></a>パート 2 - ポリシー キーを作成する

Azure AD B2C テナントで前に記録したクライアント シークレットを格納します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション** については、`Manual`を選択します。
1. ポリシー キーの **名前** を入力します。 たとえば、「 `BlokSecAppSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **［作成］** を選択します

### <a name="part-3---configure-bloksec-as-an-identity-provider"></a>パート 3 - BlokSec を ID プロバイダーとして構成する

ユーザーが BlokSec 分散化 ID を使用してサインインできるようにするには、BlokSec を Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントには一連の要求が用意されています。Azure AD B2C では、これによってデバイスで使用可能な指紋や顔スキャンなどの生体測定を使用して特定のユーザーが認証済みであることを検証して、ユーザーの ID を確認します。

ポリシーの拡張ファイル内で **ClaimsProvider** 要素に追加することで、BlokSec をクレーム プロバイダーとして定義できます。

1. `TrustFrameworkExtensions.xml` を開きます。

2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、ルート要素の下に追加します。

3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>bloksec</Domain>
      <DisplayName>BlokSec</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="BlokSec-OpenIdConnect">
          <DisplayName>BlokSec</DisplayName>
          <Description>Login with your BlokSec decentriled identity</Description>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://api.bloksec.io/oidc/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the BlokSec Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://api.bloksec.io/oidc</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_BlokSecAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

### <a name="part-4---add-a-user-journey"></a>パート 4 - ユーザー体験を追加する

この時点では、ID プロバイダーはセットアップされていますが、サインイン ページではまだ使用できません。 独自のカスタム ユーザー体験がない場合は、既存のテンプレート ユーザー体験の複製を作成してください。そうでない場合は、次の手順に進みます。  

1. スターター パックから `TrustFrameworkBase.xml` ファイルを開きます。

2. ID=`SignUpOrSignIn` を含む **UserJourneys** 要素を見つけ、その内容全体をコピーします。

3. `TrustFrameworkExtensions.xml` を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。

4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。

5. ユーザー体験の ID の名前を変更します。 たとえば、ID=`CustomSignUpSignIn` などです。  

### <a name="part-5---add-the-identity-provider-to-a-user-journey"></a>パート 5 - ユーザー体験に ID プロバイダーを追加する

これでユーザー体験ができたので、ユーザー体験に新しい ID プロバイダーを追加します。 最初にサインイン ボタンを追加してから、ボタンをアクションにリンクします。 アクションは、前に作成した技術プロファイルです。  

1. ユーザー体験内で、Type=`CombinedSignInAndSignUp` または Type=`ClaimsProviderSelection` を含むオーケストレーション ステップ要素を見つけます。 これは通常、最初のオーケストレーション ステップです。 **ClaimsProviderSelections** 要素には、ユーザーがサインインに使用できる ID プロバイダーの一覧が含まれています。 要素の順序により、ユーザーに表示されるサインイン ボタンの順序が制御されます。 **ClaimsProviderSelection** XML 要素を追加します。 **TargetClaimsExchangeId** の値をフレンドリ名に設定します。

2. 次のオーケストレーション ステップで、**ClaimsExchange** 要素を追加します。 **Id** を、ターゲットの要求交換 ID の値に設定します。 **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。

次の XML は、ID プロバイダーを使用したユーザー体験の最初の 2 つのオーケストレーション ステップを示しています。

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="BlokSecExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="BlokSecExchange" TechnicalProfileReferenceId="BlokSec-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>パート 6 - 証明書利用者ポリシーを構成する

証明書利用者ポリシー (例 [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)) は、Azure AD B2C が実行されるユーザー体験を指定します。 証明書利用者内の **DefaultUserJourney** 要素を検索します。 ID プロバイダーを追加したユーザー体験 ID と一致するように **ReferenceId** を更新します。

次の例では、`CustomSignUpOrSignIn` ユーザー体験について、ReferenceId を `CustomSignUpOrSignIn` に設定しています。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>パート 7 - カスタム ポリシーをアップロードする

1. [Azure portal](https://portal.azure.com/#home) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. [Azure portal](https://portal.azure.com/#home) で、 **[Azure AD B2C]** を検索して選択します。
1. [ポリシー] で **[Identity Experience Framework]** を選択します。
**[カスタム ポリシーのアップロード]** を選択し、変更した 2 つのポリシー ファイルを拡張ポリシー (`TrustFrameworkExtensions.xml` など)、証明書利用者ポリシー (`SignUpSignIn.xml`など) の順序でアップロードします。

### <a name="part-8---test-your-custom-policy"></a>パート 8 - カスタム ポリシーをテストする

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](./tutorial-register-applications.md) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで、 **[Google]** を選択して、Google アカウントでサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end
