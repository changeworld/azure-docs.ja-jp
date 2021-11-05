---
title: Azure Active Directory B2C と BioCatch を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: リスクのある不正なユーザーを特定するために、BioCatch と共に Azure Active Directory B2C を構成するためのチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 173ef82e77e44f3b7e2a664d613b1810995fee21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044800"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C と共に BioCatch を構成する

このサンプル チュートリアルでは、Azure Active Directory (AD) B2C 認証を [BioCatch](https://www.biocatch.com/) に統合して、顧客 ID およびアクセス管理 (CIAM) のセキュリティ体制をさらに強化する方法について説明します。 BioCatch では、ユーザーの物理的およびコグニティブ デジタル動作が分析され、正当な顧客とサイバー犯罪者を見分ける分析情報が生成されます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。

- [BioCatch](https://www.biocatch.com/contact-us) アカウント。

## <a name="scenario-description"></a>シナリオの説明

BioCatch 統合には、次のコンポーネントが含まれています。

- **1 つの Web アプリまたは Web サービス** - ユーザーは最初にこの Web サービスを参照します。 この Web サービスでは、BioCatch に送信される一意のクライアント セッション ID のインスタンスが作成されます。 その後すぐに、クライアント セッション ID によって、ユーザーの動作特性の BioCatch への送信が開始されます。

- **1 つのメソッド** - Azure AD B2C に一意のクライアント セッション ID を送信します。 示されている例では、JavaScript を使用して、非表示の HTML フィールドに値を入力します。

- **1 つの Azure AD B2C カスタマイズ済み UI** - 上記のメソッドを使用している場合は、JavaScript からのクライアント セッション ID 入力用の HTML フィールドを非表示にします

- **Azure AD B2C カスタム ポリシー**

  - UI からカスタム クライアント セッション ID をクレームの形式で取得します。 これは、セルフアサート技術プロファイルを使用して実現されます

  - REST API クレーム プロバイダー経由で BioCatch と統合し、BioCatch プラットフォームにクライアント セッション ID を渡します

  - 複数のカスタム クレームが BioCatch から返され、その後、カスタム ポリシー ロジックによって操作されます

  - 1 つの userjourney。これにより、返されたクレームが評価され (たとえば、セッションのリスク)、多要素認証 (MFA) の呼び出しなどのアクションが条件付きで実行されます。

![BioCatch のアーキテクチャの図。](media/partner-biocatch/biocatch-architecture-diagram.png)

| 手順  | 説明 |
|:---|:-----------------------|
|1a     | ユーザーが Web サービスを参照します。 すると、Web サービスによって HTML、CSS、または JavaScript の値が返され、BioCatch JavaScript SDK を読み込むように構成されます。 クライアント側 JavaScript により、BioCatch SDK のクライアント セッション ID が構成 (設定) されます。 Web サービスで、クライアント セッション ID を事前に構成し、クライアントに送信することもできます。        |
|1b    |  BioCatch プラットフォームに対して、インスタンス化された BioCatch JavaScript SDK を構成します。 手順 1a のクライアント セッション ID を使用して、クライアント デバイスから BioCatch へのユーザーの動作特性の送信を直ちに開始します。    |
|2     |  ユーザーはサインアップまたはサインインを試行し、Azure AD B2C にリダイレクトされます。      |
|3a    | userjourney の一部は、クライアントセッション ID を入力として受け取るセルフアサート claimsprovider です。 このフィールドは画面に表示されません。 JavaScript を使用して、フィールドにセッション ID を入力できます。 サインアップまたはサインイン プロセスを続行するには、 *[次へ]* ボタンを選択します。|
|3b     | リスク スコアを判定するために、クライアント セッション ID が BioCatch プラットフォームに送信されます。 |
|3c     | BioCatch により、リスク スコアなどのセッションに関する情報と、対処 (許可またはブロック) に関する推奨事項が返されます |
|3d    |userjourney には条件付きチェック手順があり、これは返されたクレームに対して作用します|
| 4   | 条件付きチェックの結果に基づいて、"*ステップアップ MFA*" などのアクションが呼び出されます|
|5    | ユーザーが最初に Web サービス ページに到達したときはいつでも、Web サービスはクライアント セッション ID を使用して BioCatch API のクエリを実行し、リアルタイムでリスク スコアとセッション情報を確認できます。 |

## <a name="onboard-with-biocatch"></a>BioCatch を使用してオンボードする

[BioCatch](https://www.biocatch.com/contact-us) に連絡し、アカウントを作成します。

## <a name="configure-the-custom-ui"></a>カスタム UI を構成する

クライアントセッション ID フィールドを非表示にすることをお勧めします。 このフィールドを非表示にするには、CSS、JavaScript、またはその他のメソッドを使用します。 テスト目的の場合は、フィールドの非表示を解除できます。 たとえば、JavaScript を使用して入力フィールドを非表示にするには、次のようにします。

```JavaScript
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Azure AD B2C Identity Experience Framework のポリシーを構成する

1. 最初の[カスタム ポリシー構成](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)を設定します。

2. 拡張機能ファイルを継承する新しいファイルを作成します。

    ```xml
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. BuildingBlocks リソースの下に、入力ボックスを非表示にするカスタム UI への参照を作成します。

    ```xml
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. BuildingBlocks リソースの下に次のクレームを追加します。

    ```xml
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    </ClaimsSchema> 
    ```

5. クライアント セッション ID フィールドに対して、セルフアサート クレーム プロバイダーを構成します。

    ```xml
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. BioCatch に対して REST API クレーム プロバイダーを構成します。 

    ```xml
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item>

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!NOTE]
    > BioCatch により、構成する URL、顧客 ID、一意のユーザー ID (uuID) が提供されます。 顧客の セッションID クレームは、BioCatch に クエリ文字列パラメーターとして渡されます。 アクティビティの種類 (*MAKE_PAYMENT* など) を選択できます。

7. userjourney を構成します。例に従ってください

   1. clientSessionID をクレームとして取得します

   1. BioCatch API を呼び出してセッション情報を取得します

   1. 返されたクレームの "*リスク*" が "*低い*" 場合は、MFA の手順をスキップします。それ以外の場合は、MFA をユーザーに強制します

    ```xml
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

          </ClaimsExchanges> 

        </OrchestrationStep> 

        <OrchestrationStep Order="9" Type="ClaimsExchange"> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

          </ClaimsExchanges> 

        </OrchestrationStep> 

        <OrchestrationStep Order="10" Type="ClaimsExchange"> 

          <Preconditions> 

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

              <Value>riskLevel</Value> 

              <Value>LOW</Value> 

              <Action>SkipThisOrchestrationStep</Action> 

            </Precondition> 

          </Preconditions> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

          </ClaimsExchanges>
    ```

8. 証明書利用者の構成を設定します (省略可能)

    BioCatch から返された情報 (特に *risklevel* と *score*) をトークン内のクレームとしてアプリケーションに渡すと役立ちます。

    ```xml
    <RelyingParty> 

    <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

    <UserJourneyBehaviors> 

      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

      <SessionExpiryType>Absolute</SessionExpiryType> 

      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

      <ScriptExecution>Allow</ScriptExecution> 

    </UserJourneyBehaviors> 

    <TechnicalProfile Id="PolicyProfile"> 

      <DisplayName>PolicyProfile</DisplayName> 

      <Protocol Name="OpenIdConnect" /> 

      <OutputClaims> 

        <OutputClaim ClaimTypeReferenceId="displayName" /> 

        <OutputClaim ClaimTypeReferenceId="givenName" /> 

        <OutputClaim ClaimTypeReferenceId="surname" /> 

        <OutputClaim ClaimTypeReferenceId="email" /> 

        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

        <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

        <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

        <OutputClaim ClaimTypeReferenceId="score" /> 

        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

      </OutputClaims> 

      <SubjectNamingInfo ClaimType="sub" /> 

    </TechnicalProfile> 

  </RelyingParty>
    ```

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

ポリシー ファイルを Azure AD B2C に追加するには、こちらの手順に従います

1. Azure AD B2C テナントの全体管理者として  [**Azure portal**](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅の  **[すべてのサービス]**   を選択し、「Azure AD B2C」を検索して選択します。
1.  **[Azure AD B2C]**  > **[Identity Experience Framework]** に移動します
1. すべてのポリシー ファイルをテナントにアップロードします。

## <a name="test-the-solution"></a>ソリューションをテストする

1. [JWT.MS にリダイレクトするダミー アプリケーションを登録します](./tutorial-register-applications.md?tabs=app-reg-ga)  
1. **[Identity Experience Framework]** で、作成したポリシーを選択します
1. そのポリシーのウィンドウで、ダミーの JWT.MS アプリケーションを選択し、 **[今すぐ実行]** を選択します
1. サインアップ フローを実行し、アカウントを作成します。 JWT.MS に返されるトークンには、riskLevel と score に対する 2 倍のクレームが含まれているはずです。 例に従ってください。  

    ```JavaScript
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>その他のリソース

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
