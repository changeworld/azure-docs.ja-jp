---
title: Azure Active Directory B2C を使用して IDEMIA を構成する (プレビュー)
titleSuffix: Azure AD B2C
description: Azure AD B2C 認証を証明書利用者の IDEMIA と統合して、IDEMIA または米国の発行済みモバイル id を使用する方法について説明します。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 27c83d2ade4552150793aa8fbe777e376a2ae762
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012879"
---
# <a name="tutorial-configure-idemia-with-azure-active-directory-b2c-for-relying-party-to-consume-idemia-or-us-state-issued-mobile-identity-credentials-preview"></a>チュートリアル:  証明書利用者がIDEMIAまたはUS State発行のモバイルID資格情報を消費するようにAzure Active Directory B2Cを使用してIDEMIAを構成する（プレビュー）
[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

この機能は、カスタム ポリシーでのみ使用できます。 セットアップ手順は、前のセレクターで **[カスタム ポリシー]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

このサンプルチュートリアルでは、Azure Active Directory（Azure AD）B2Cを[ IDEMIA ](https://www.idemia.com/)と統合する方法について説明します。 IDEMIA はパスワードレス認証プロバイダーであり、face ID やフィンガープリントなどの生体認証を使用して、不正行為や資格情報の再利用を排除するリアルタイムの同意ベースのサービスを提供します。 IDEMIAのモバイルIDを使用すると、市民は、物理IDを補完するものとして、政府発行の信頼できるデジタルIDを利用できます。 このアプリケーションは、自己選択の PIN またはタッチ ID/face ID を使用して ID を検証するために使用されます。 モバイル ID を使用すると、市民はトランザクションに必要な情報のみを共有し、不正行為の防止を可能にすることで、ID を制御できます。



## <a name="scenario-description"></a>シナリオの説明

IDEMIA 統合には、次のコンポーネントが含まれています。

- **Azure AD B2C** – ユーザーの資格情報の検証を受け持つ承認サーバー。ID プロバイダー(IdP)とも呼ばれます。

- **IDEMIA mID** - OpenID Connect (OIDC) プロバイダーが[Azure AD B2C 外部プロバイダー](add-identity-provider.md) として構成されています。

- **[IDEMIA mID アプリケーション](https://idemia-mobile-id.com/)** - 政府発行の信頼されたデジタル ID です。 モバイル ID は、電話のアプリに存在する運転免許証または状態発行 ID のデジタル版です。 [IDEMIA](https://idemia-mobile-id.com/)

IDEMIA は、自動車車両 (DMVs) の多くの米国の州部門にmIDを提供しています。

mID は、識別ドキュメントを強力なモバイル id トークンにデジタル化しています。これは、検証のために移植性が高く、承認中の **mID サービス** のインデックスとして機能します。 mIDサービスを使用すると、DMVは、発行された運転免許証と生体認証 **セルフィー** から資格情報への顔認識マッチングサービスを使用した資格情報ドキュメントの認証を使用して、個人の身元を証明できます。  

作成されると、mID はエンド ユーザーの携帯電話にデジタル署名された **エッジのIDとして保存されます**。 エンドユーザーは、署名された資格情報を使用して、年齢証明、顧客の財務情報、セキュリティが最優先されるアカウントアクセスなど、他のIDに依存するサービスにアクセスできるようになりました。

Microsoft へのオファーは、州発行のmIDを使用して、mIDの所有者から送信された属性を使用してサービスを提供する証明書利用者（RP）としてのこれらのサービスのサポートです。

次の図は、web またはオンプレミスのシナリオの実装を示しています。

![スクリーンショットは、オンプレミスの検証を示しています。](./media/partner-idemia/idemia-architecture-diagram.png)

| 手順 | 説明 |
|:--------|:--------|
| 1. | ユーザーは、Azure AD B2C ログインページにアクセスします。この場合、デバイスで応答側がトランザクションを実行し、mIDアプリを介してログインします。 |
| 2. | Azure ADB2C では、OIDC 承認コードフローを使用して IDEMIA ルーターにユーザーをリダイレクトする ID チェックとを必要とします。|
| 3. | IDEMIAルーターは、認証および承認リクエストのすべてのコンテキストの詳細を含む生体認証チャレンジをユーザーのモバイルアプリに送信します。|
| 4. | ユーザーは、必要なセキュリティレベルに応じて、追加情報の提供、PIN の入力、live selfie の取得、またはその両方を行うことが必要になる場合があります。|
| 5. | 最終的な認証応答では、所有、存在、同意の証明を提供します。 応答は IDEMIA ルーターに返されます。|
| 6. | IDEMIA ルーター は、ユーザーから提供された情報を検証し、認証結果を使用して Azure AD B2C に返信します。|
|7. | 認証結果に基づいて、ユーザーにはアクセスが許可または拒否されます。 |

## <a name="onboard-with-idemia"></a>IDEMIA を使用してオンボードします。

デモを要求するには、[ IDEMIA ](https://www.idemia.com/get-touch/)に連絡し​​てください。 要求フォームに入力する際に​​、メッセージフィールドで、Azure ADB2Cをオンボードすることを指定します。

## <a name="integrate-idemia-with-azure-ad-b2c"></a>IDEMIAをAzure AD B2C と統合します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- IDEMIA の状態が発行されたモバイル ID 資格情報 (mID) を持つエンドユーザー、またはテストフェーズ中に、 [IDEMIA](https://www.idemia.com/)によって提供される中程度のデモアプリケーションにアクセスできます。

- Azure AD サブスクリプション。 所有していない場合は、 [無料アカウント](https://azure.microsoft.com/free/)を入手してください。

- お使いの Azure サブスクリプションにリンクされている  [Azure AD B2C テナント](tutorial-create-tenant.md) 。

- Azure AD B2C テナントに登録されているビジネス web アプリケーション。 テスト目的では https://jwt.ms 、トークンのデコードされた内容を表示する Microsoft 所有の web アプリケーションを構成できます。

>[!NOTE]
>トークンの内容がブラウザーから離れていることはありません。


### <a name="part-1---submit-a-relying-party-application-on-boarding-for-mid"></a>パート 1-  mIDの証明書利用者アプリケーションを送信します

IDEMIA との統合の一環として、次の情報が提供されます。

| プロパティ | 説明 |
|:---------|:----------|
| アプリケーション名 | Azure AD B2C または希望のアプリケーション名 |
| Client_ID | これは、IdPによって提供される一意の識別子です。 |
| クライアント シークレット | IDEMIA IdP による認証に証明書利用者アプリケーションが使用するパスワード |
| メタデータ エンドポイント | トークン発行者の構成ドキュメントを指す URL。これは、OpenID の既知の構成エンドポイントとも呼ばれます。 |
|リダイレクト URI | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp`<br>(例: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`)<br><br>カスタム ドメインを使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。<br>your-domain-name を実際のカスタム ドメインに、your-tenant-name を実際のテナントの名前に置き換えます。 |
|ログアウト後のリダイレクト URI | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/{policy}/oauth2/v2.0/logout`<br>サインアウト要求を送信します。 |

>[!NOTE]
>Azure AD B2C で IdP を構成するには、後で IDEMIA クライアント ID とクライアントシークレットが必要になります。

### <a name="part-2---create-a-policy-key"></a>パート 2 - ポリシー キーを作成する

Azure AD B2C テナントで前に記録したクライアント シークレットを格納します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。

3. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。

4. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。

5. **[概要]** ページで、 **[Identity Experience Framework]** を選択します。

6. **[ポリシー キー]** を選択し、 **[追加]** を選択します。

7. **[オプション]** では、 **[手動]** を選びます。

8. ポリシー キーの **名前** を入力します。 たとえば、IdemiaAppSecret などです。 プレフィックス "B2C_1A_" がキーの名前に自動的に追加されます。

9. **[シークレット]** に、前に記録したクライアント シークレットを入力します。

10. **キー** 使用法には **[署名]** を選択します。

11. **［作成］** を選択します

### <a name="part-3---configure-idemia-as-an-external-idp"></a>パート 3-IDEMIA を外部 IdP として構成します

ユーザーがIDEMIAモバイルIDパスワードなしIDを使用してサインインできるようにするには、Azure ADB2Cがエンドポイントを介して通信できるクレームプロバイダーとしてIDEMIAを定義する必要があります。 エンドポイントには一連の要求が用意されています。Azure AD B2C では、これによってデバイスで使用可能な指紋や顔スキャンなどの生体測定を使用して特定のユーザーが認証済みであることを検証して、ユーザーの ID を確認します。
ポリシーの拡張ファイル内で **ClaimsProvider** 要素に追加することで、IDEMIA をクレーム プロバイダーとして定義できます。

```PowerShell
     <TechnicalProfile Id="Idemia-Oauth2">
          <DisplayName>IDEMIA</DisplayName>
          <Description>Login with your IDEMIA identity</Description>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="METADATA">https://idp.XXXX.net/oxauth/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid id_basic mt_scope</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
            <Item Key="ClaimsEndpoint">https://idp.XXXX.net/oxauth/restv1/userinfo</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdemiaAppSecret" />
</CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="acr" PartnerClaimType="acr_values" DefaultValue="loa-2" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName1" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="lastName1" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="idemia" />
            <OutputClaim ClaimTypeReferenceId="documentId" />
            <OutputClaim ClaimTypeReferenceId="address1" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
           
```

client_id を、アプリケーションの登録で取得したアプリケーション ID に設定します。

|プロパティ | 説明|
|:------|:-------|
|Scope| OpenID Connect（OIDC）の場合、最小要件は、スコープパラメーターを **openid** に設定することです。 追加のスコープは、スペースで区切られたリストとして追加できます。|
|redirect_uri | これにより、ユーザー エージェントが承認コードを Azure ADB2Cに送り返す場所を定義します。|
|response_type| 認証コードフローの場合、これは **コード** に設定されます|
|acr_values| このパラメーターは、認証プロセス中にユーザーが実行する必要がある認証方法を制御します。 |

次のいずれかの値を設定する必要があります:

|パラメーター値| ユーザー認証プロセスへの影響 |
|:------|:-------|
|`loa-2`| 暗号ベースのAzureAD多要素認証のみ|
|`loa-3`| 暗号ベースのAzureAD多要素認証と1つの追加要素|
|`loa-4`| 暗号ベースのAzureAD多要素認証。ユーザーはピンベースの生体認証も実行する必要があります。 |

**/Userinfo** エンドポイントは、承認要求で要求されたスコープの要求を提供します。 **<mt_scope** の場合、これには、姓、名、ドライバーのライセンス番号などの要求が含まれます。
特定のスコープの要求セットは、discovery API の **scope_to_claims_mapping** セクションで公開されます。
Azure ADB2C は要求エンドポイントから要求を要求し、OutputClaims 要素でそれらの要求を返します。 場合によっては、ポリシーで定義されている要求の名前を IdP で定義されている名前にマップし [ClaimSchema要素](claimsschema.md) で要求の種類を定義する必要があります。

```PowerShell
<ClaimType Id="documentId">
     <DisplayName>documentId</DisplayName>
     <DataType>string</DataType>
</ClaimType>
<ClaimType Id="address1">
     <DisplayName>address</DisplayName>
     <DataType>string</DataType>
</ClaimType>
```

### <a name="part-4---add-a-user-journey"></a>パート 4 - ユーザー体験を追加する

この時点で、IdPは設定されていますが、どのサインインページでもまだ利用できません。 独自のカスタム ユーザー体験がない場合は、既存のテンプレート ユーザー体験の複製を作成してください。そうでない場合は、次の手順に進みます。

1. スターター パックから `TrustFrameworkBase.xml` ファイルを開きます。

2. ID=`ID=SignUpOrSignIn` を含む **UserJourneys** 要素を見つけ、その内容全体をコピーします。

3. `TrustFrameworkExtensions.xml` を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。

4. コピーした **UserJourney** 要素の内容全体を UserJourneys 要素の子として貼り付けます。

5. ユーザー体験の ID の名前を変更します。 たとえば、「 `ID=CustomSignUpSignIn` 」のように入力します。

### <a name="part-5---add-the-idp-to-a-user-journey"></a>パート 5-ユーザー体験に IdP を追加します。

ユーザー体験ができたので、ユーザー体験に新しい ID プロバイダーを追加します。 最初にサインイン ボタンを追加してから、ボタンをアクションにリンクします。 アクションは、前に作成した技術プロファイルです。

1. ユーザー体験内で、Type=`CombinedSignInAndSignUp` または Type=`ClaimsProviderSelection` を含むオーケストレーション ステップ要素を見つけます。 これは通常、最初のオーケストレーション ステップです。 **ClaimsProviderSelections** 要素には、ユーザーがサインインできる IdPsのリストが含まれています。 要素の順序により、ユーザーに表示されるサインイン ボタンの順序が制御されます。 **ClaimsProviderSelection** XML 要素を追加します。 **TargetClaimsExchangeId** の値をフレンドリ名に設定します。

2. 次のオーケストレーション ステップで、**ClaimsExchange** 要素を追加します。 **Id** を、ターゲットの要求交換 ID の値に設定します。 **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。

次の XML は、IdP を使用したユーザー体験の最初の2つのオーケストレーションステップを示しています。

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="IdemiaExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="IdemiaExchange" TechnicalProfileReferenceId="Idemia-Oauth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>パート 6 - 証明書利用者ポリシーを構成する

証明書利用者ポリシー (例 [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml)) は、Azure AD B2C が実行されるユーザー体験を指定します。 証明書利用者内の **DefaultUserJourney** 要素を検索します。 IdP を追加したユーザー体験 ID と一致するように **ReferenceId** を更新します。

次の例では、`CustomSignUpOrSignIn` ユーザー体験について、**ReferenceId** をに設定しています。`CustomSignUpOrSignIn`
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>パート 7 - カスタム ポリシーをアップロードする

1. [Azure portal](https://portal.azure.com/#home) にサインインします。

2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。

3. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。

4. [Azure portal](https://portal.azure.com/#home) で、 **[Azure AD B2C]** を検索して選択します。

5. [ポリシー] で **[Identity Experience Framework]** を選択します。

**[カスタム ポリシーのアップロード]** を選択し、変更した 2 つのポリシー ファイルを拡張ポリシー (`TrustFrameworkExtensions.xml` など)、証明書利用者ポリシー (`SignUpSignIn.xml`など) の順序でアップロードします。

### <a name="part-8---test-your-custom-policy"></a>パート 8 - カスタム ポリシーをテストする

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。

2. **[アプリケーション]** には、[前に登録した](./tutorial-register-applications.md) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。

3. **[今すぐ実行]** ボタンを選択します。

4. サインアップまたはサインインページから、**IDEMIA** を選択して、IDEMIA-米国の州発行のmID（モバイルIDクレデンシャル）でサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。


## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [IDEMIA mID の詳細情報](https://www.idemia.com/mobile-id)

::: zone-end
