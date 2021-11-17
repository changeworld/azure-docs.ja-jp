---
title: トークン エンリッチメント - Azure Active Directory B2C
description: API または送信 Webhook を使用して、外部 ID データ ソースからの要求を使用してトークンを強化します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/09/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a6f91fd8de1f208bcbe57a9e541ad1e73340e995
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179491"
---
# <a name="enrich-tokens-with-claims-from-external-sources-using-api-connectors"></a>API コネクタを使用して外部ソースからの要求でトークンを強化する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) では、ID 開発者が [API コネクタ](api-connectors-overview.md)を使用して、RESTful API との対話をユーザー フローに統合することができます。 これにより、開発者は外部 ID ソースからデータを動的に取得できます。 このチュートリアルを終了すると、API と対話して外部ソースからの情報でトークンを強化する Azure AD B2C ユーザー フローを作成できるようになります。

::: zone pivot="b2c-user-flow"

「**トークンを送信する前 (プレビュー)** 」の手順に応用した API コネクタを使用して、外部ソースからの情報でアプリケーションのトークンを強化することができます。 ユーザーがサインインまたはサインアップすると、Azure AD B2C では、API コネクタで構成された API エンドポイントが呼び出されます。これによって、クラウド サービス、カスタム ユーザー ストア、カスタムのアクセス許可システム、レガシ ID システムなどのダウンストリーム サービスのユーザーに関する情報をクエリできます。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

API エンドポイントは、[サンプル](api-connector-samples.md#api-connector-rest-api-samples)の 1 つを使用して作成できます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-api-connector"></a>API コネクタを作成する

[API コネクタ](api-connectors-overview.md)を使用するには、まず API コネクタを作成してから、ユーザー フローで有効にします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。
4. **[API コネクタ]** を選択し、 **[新しい API コネクタ]** を選択します。

   ![基本的な API コネクタ構成のスクリーンショット](media/add-api-connector-token-enrichment/api-connector-new.png)

5. 呼び出しの表示名を指定します。 たとえば、「**Enrich token from external source**」(外部ソースからトークンを強化) のようにします。
6. API 呼び出しの **[エンドポイント URL]** を指定します。
7. **[認証の種類]** を選択し、API を呼び出すための認証情報を構成します。 [API コネクタのセキュリティ保護](secure-rest-api.md)の方法を参照してください。

   ![API コネクタの認証構成のスクリーンショット](media/add-api-connector-token-enrichment/api-connector-config.png)

8. **[保存]** を選択します。

## <a name="enable-the-api-connector-in-a-user-flow"></a>ユーザー フローで API コネクタを有効にする

サインアップ ユーザー フローに API コネクタを追加するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。
4. **[ユーザー フロー]** を選択し、API コネクタを追加するユーザー フローを選択します。
5. **[API コネクタ]** を選択し、ユーザー フローの「**トークンを送信する前 (プレビュー)** 」の手順で呼び出す API エンドポイントを選択します。

   ![ユーザー フローの手順で API コネクタを選択するスクリーンショット](media/add-api-connector-token-enrichment/api-connectors-user-flow-select.png)

6. **[保存]** を選択します。

この手順は、「**サインアップとサインイン (推奨)** 」、「**サインアップ (推奨)** 」、「**サインイン (推奨)** 」の各ユーザー フローにのみ存在します。

## <a name="example-request-sent-to-the-api-at-this-step"></a>この手順で API に送信される要求の例

この手順の API コネクタは、サインインとサインアップ中にトークンを発行しようとすると呼び出されます。 

API コネクタによってユーザー属性 ("要求") が **HTTP POST** 要求として具体化され、JSON 本文のキーと値のペアとして送信されます。 属性は [Microsoft Graph](/graph/api/resources/user#properties) ユーザー プロパティと同様にシリアル化されます。 

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "objectId": "ab3ec3b2-a435-45e4-b93a-56a005e88bb7",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "client_id": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenIssuance",
 "ui_locales":"en-US"
}
```

API に送信される要求は、ユーザーに定義した情報によって異なります。

**[Azure AD B2C]** の **[ユーザー属性]** のエクスペリエンスで一覧表示されるユーザー プロパティとカスタム属性だけが、要求で送信できます。

カスタム属性は、ディレクトリ内に **extension_\<extensions-app-id>_CustomAttribute** の形式で存在しています。 API では、これと同じシリアル化された形式で要求を受け取ることを想定しています。 カスタム属性の詳細については、[Azure AD B2C でカスタム属性を定義する方法](user-flow-custom-attributes.md)に関するページを参照してください。

また、これらの要求は通常、この手順のすべての要求で送信されます。
- **UI ロケール ('ui_locales')** - デバイスで構成されているエンド ユーザーのロケール。 これを API で使用して、国際化された応答を返すことができます。
- **ステップ ('step')** - API コネクタが呼び出されたユーザー フロー上のステップまたはポイント。 この手順の値は ` です。
- **クライアント ID ('client_id')** - エンド ユーザーがユーザー フローで認証しているアプリケーションの `appId` 値。 これは、アクセス トークンのリソース アプリケーションの `appId` では *ありません*。
- **objectId** - ユーザーの識別子。 これを使用して、ダウンストリーム サービスに対して、ユーザーに関する情報のクエリを実行できます。
  
> [!IMPORTANT]
> API エンドポイントが呼び出されたときに要求に値がない場合、要求は API に送信されません。 API は、要求に要求が含まれていないケースを明示的に確認して処理するように設計する必要があります。

## <a name="expected-response-types-from-the-web-api-at-this-step"></a>この手順での Web API からの予期される応答の種類

ユーザー フロー中に Web API で Azure AD から HTTP 要求を受信すると、"継続応答" が返されることがあります。

### <a name="continuation-response"></a>継続応答

継続応答は、ユーザー フローが次の手順 (トークンの発行) に進む必要があることを示します。

継続応答では、API から追加の要求が返されることがあります。 API から返される要求のうち、トークンで返すものは、組み込みの要求であるか、[カスタム属性として定義](user-flow-custom-attributes.md)する必要があり、ユーザー フローの **アプリケーション要求** の構成で選択する必要があります。 

トークン内の要求の値は、ディレクトリ内の値ではなく、API によって返される値になります。 一部の要求値は、API 応答によって上書きできません。 API によって返すことができる要求は、`email` を除き、 **[ユーザー属性]** にあるセットに対応します。

> [!NOTE]
> API は、最初の認証中にのみ呼び出されます。 更新トークンを使用して新しいアクセス トークンまたは ID トークンを自動的に取得する場合、トークンには、最初の認証中に評価された値が含まれます。 

## <a name="example-response"></a>応答の例

### <a name="example-of-a-continuation-response"></a>継続応答の例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| パラメーター                                          | Type              | 必須 | 説明                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version     | String | はい      | API のバージョン。                                                    |
| action                                             | String            | はい      | 値は `Continue` とする必要があります。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | いいえ       | **アプリケーション要求** として選択されている場合は、トークンで返すことができます。                                        |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | いいえ       | `_<extensions-app-id>_` は *省略可能* であり、要求に含める必要はありません。 **アプリケーション要求** として選択されている場合は、トークンで返すことができます。  |

::: zone-end

::: zone pivot="b2c-custom-policy"

このシナリオでは、企業の基幹業務ワークフローと統合し、ユーザーのトークン データを強化します。 Azure AD B2C は、ローカルまたはフェデレーション アカウントを使用したサインアップまたはサインイン時に REST API を呼び出し、リモート データ ソースからユーザーの拡張プロファイル データを取得します。 このサンプルでは、Azure AD B2C は、ユーザーの一意の識別子である objectId を送信します。 これにより REST API は、ユーザーのアカウント残高 (ランダムな数値) を返します。 このサンプルは、お使いのご自分の CRM システム、マーケティング データベース、または任意の基幹業務ワークフローと統合して、出発点として使用することができます。

また、対話を検証技術プロファイルとして設計することもできます。 これは、REST API が画面上のデータを検証して要求を返す場合に適しています。 詳細については、「[チュートリアル: API コネクタをサインアップ ユーザー フローに追加する](add-api-connector.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- [カスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)に関するページの手順を完了します。 ローカル アカウントでのサインアップとサインインのために作業用カスタム ポリシーを持つ必要があります。
- [REST API 要求交換のお使いの Azure Active Directory B2C カスタム ポリシーへの統合](api-connectors-overview.md)に関する方法を参照してください。

## <a name="prepare-a-rest-api-endpoint"></a>REST API エンドポイントを準備する

このチュートリアルでは、ユーザーの Azure AD B2C の objectId がご自分のバックエンド システムに登録されているかどうかを検証する REST API が必要です。 登録されている場合、REST API によってユーザー アカウントの残高が返されます。 それ以外の場合は、REST API によってディレクトリに新しいアカウントが登録され、`50.00` の開始残高が返されます。

次の JSON コードでは、お使いの REST API エンドポイントに送信される Azure AD B2C のデータを示しています。 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

データがお使いの REST API によって検証されると、次の JSON データと共に HTTP 200 (Ok) が返されます。

```json
{
    "balance": "760.50"
}
```

REST API エンドポイントの設定は、この記事では扱っていません。 [Azure Functions](../azure-functions/functions-reference.md) のサンプルを作成しました。 Azure 関数の完全なコードは、[GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function) から入手できます。

## <a name="define-claims"></a>要求を定義する

要求は、Azure AD B2C ポリシーの実行時に、データの一時的なストレージとなります。 要求は、[claims schema](claimsschema.md) セクションで宣言できます。 

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. 次の要求を **ClaimsSchema** 要素に追加します。  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>RESTful API 技術プロファイルを追加する 

[Restful 技術プロファイル](restful-technical-profile.md)では、お使いの独自の RESTful サービスとのインターフェイスをサポートしています。 Azure AD B2C は、`InputClaims` コレクションでデータを RESTful サービスに送信し、`OutputClaims` コレクションで返却データを受信します。 お使いの <em> **`TrustFrameworkExtensions.xml`**</em> ファイルで **ClaimsProviders** を見つけ、次のように新しい要求プロバイダーを追加します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

この例では、`userLanguage` が JSON ペイロード内の `lang` として REST サービスに送信されます。 `userLanguage` 要求の値には、現在のユーザーの言語 ID が含まれます。 詳細については、[要求リゾルバー](claim-resolver-overview.md)に関するページを参照してください。

### <a name="configure-the-restful-api-technical-profile"></a>RESTful API 技術プロファイルを構成する 

REST API をデプロイした後、`REST-GetProfile` 技術プロファイルのメタデータを、次のような独自の REST API を反映させるように設定します。

- **ServiceUrl**: REST API エンドポイントの URL を設定します。
- **SendClaimsIn**: RESTful クレーム プロバイダーへの入力要求の送信方法を指定します。
- **AuthenticationType**: `Basic` または `ClientCertificate` などの RESTful クレーム プロバイダーにより実行されている認証の種類を設定します。 
- **AllowInsecureAuthInProduction**: 運用環境では、このメタデータを必ず `false` に設定してください。
    
詳細な構成については、[RESTful 技術プロファイルのメタデータ](restful-technical-profile.md#metadata)に関する記事を参照してください。

`AuthenticationType` と `AllowInsecureAuthInProduction` の上のコメントに、運用環境に移行するときに行う必要がある変更が指定されています。 お使いの RESTful API を実稼働用にセキュリティで保護する方法については、[RESTful API のセキュリティ保護](secure-rest-api.md)に関するページを参照してください。

## <a name="add-an-orchestration-step"></a>オーケストレーション手順を追加する

[ユーザー体験](userjourneys.md)では、証明書利用者アプリケーションがユーザーの任意の要求を取得できるようにする、ポリシーの明示的なパスを指定します。 ユーザー体験は、トランザクションを成功させるために従う必要のあるオーケストレーション シーケンスとして表されます。 オーケストレーションのステップは追加または削除できます。 この場合は、ユーザーのサインアップまたはサインイン後に、REST API の呼び出しでアプリケーションに提供する、情報を拡張する新しいオーケストレーション ステップを追加します。

1. ポリシーの基本ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>です。
1. `<UserJourneys>` 要素を検索します。 要素全体をコピーしてから、削除します。
1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. `<ClaimsProviders>` 要素を閉じた後、拡張ファイルに `<UserJourneys>` を貼り付けます。
1. `<UserJourney Id="SignUpOrSignIn">` を見つけて、最後のオーケストレーション ステップの前に次のオーケストレーション ステップを追加します。

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. `Order` を `8` に変更して、最後のオーケストレーション ステップをリファクターします。 お使いの最後の 2 つのオーケストレーション ステップは次のようになります。

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. **ProfileEdit** と **PasswordReset** のユーザー体験で、最後の 2 つのステップを繰り返します。


## <a name="include-a-claim-in-the-token"></a>トークンに要求を含める 

`balance` 要求を証明書利用者アプリケーションに返すには、出力要求を <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ファイルに追加します。 出力要求を追加すると、ユーザー体験が成功した後、その要求がトークンに追加され、アプリケーションに送信されます。 証明書利用者セクション内の技術プロファイル要素を変更して、`balance` を出力要求として追加します。
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

**ProfileEdit.xml** および **PasswordReset.xml** のユーザー体験にこの手順を繰り返します。

変更した次のファイルを保存します。*TrustFrameworkBase.xml* および *TrustFrameworkExtensions.xml*、*SignUpOrSignin.xml*、*ProfileEdit.xml*、および *PasswordReset.xml*。 

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルのツールバーにある **[ディレクトリ + サブスクリプション]** アイコンを選択して、Azure AD テナントを含むディレクトリを使っていることを確認します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[スイッチ]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択し、変更した次のポリシー ファイルをアップロードします。*TrustFrameworkBase.xml* および *TrustFrameworkExtensions.xml*、*SignUpOrSignin.xml*、*ProfileEdit.xml*、および *PasswordReset.xml*。 
1. アップロードしたサインアップまたはサインイン ポリシーを選択し、 **[今すぐ実行]** ボタンをクリックします。
1. メール アドレスまたは Facebook のアカウントを使用してサインアップできるはずです。
1. アプリケーションに返送されるトークンには、`balance` 要求が含まれています。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="best-practices-and-how-to-troubleshoot"></a>ベスト プラクティスとトラブルシューティングの方法

### <a name="using-serverless-cloud-functions"></a>サーバーレス クラウド機能の使用

[Azure Functions の HTTP トリガー](../azure-functions/functions-bindings-http-webhook-trigger.md)などのサーバーレス機能を使用すると、API コネクタで使用する API エンドポイントを作成できます。 複雑なシナリオには、サーバーレス クラウド機能を使用して、他の Web API、データ ストア、および他のクラウド サービスを呼び出して起動することもできます。

### <a name="best-practices"></a>ベスト プラクティス

次のことを確認します。
* API は、上で説明した API 要求と応答のコントラクトに従っています。 
* API コネクタの **エンドポイント URL** によって、正確な API エンドポイントが指定されます。
* API によって、それが依存する受信済み要求の null 値が明示的に確認されます。
* API には、[API コネクタのセキュリティ保護](secure-rest-api.md)に関する記事で説明されている認証方法が実装されています。
* API が可能な限り迅速に応答することで、スムーズなユーザー エクスペリエンスが保証されます。
    * Azure AD B2C 側は、応答を受信するために最大 *20 秒間* 待機します。 何も受信しない場合は、API の呼び出しが *もう一度実行 (再試行)* されます。
    * サーバーレス機能またはスケーラブルな Web サービスを使用している場合は、API を運用環境で "起動状態" または "ウォーム状態" に保つホスティング プランを使用します。 Azure Functions の場合、運用環境では少なくとも [Premium プラン](../azure-functions/functions-scale.md)を使用することをお勧めします。
* API の高可用性を保証します。
* ダウンストリームの API、データベース、または API のその他の依存関係のパフォーマンスを監視し、最適化します。
  
[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

### <a name="use-logging"></a>ログの使用

一般に、予期しないエラー コード、例外、およびパフォーマンスの低下について API を監視するには、ご使用の Web API サービスによって有効になっているログ ツール ([Application insights](../azure-functions/functions-monitoring.md) など) を使うと便利です。
* HTTP 200 または 400 以外の HTTP 状態コードを監視します。
* 401 または 403 HTTP 状態コードは、通常、認証に問題があることを示しています。 API コネクタで API の認証レイヤーとそれに対応する構成を再確認します。
* 開発では、必要に応じて、より積極的なログ レベル ("トレース" や "デバッグ" など) を使用します。
* 長い応答時間について API を監視します。 

さらに、Azure AD B2C では、ユーザー フローを介してユーザー認証中に発生した API トランザクションに関するメタデータをログに記録します。 次のようにして探します。
1. **Azure AD B2C** にアクセスします。
2. **[活動]** で、 **[監査ログ]** を選択します。
3. リスト ビューをフィルター処理する: **[日付]** で、目的の期間を選択し、 **[アクティビティ]** で、 **[ユーザー フローの一部として呼び出された API]** を選択します。
4. 個々のログを検査します。 各行は、ユーザー フロー中に呼び出しを試行する API コネクタを表します。 API 呼び出しが失敗し、再試行が発生した場合でも、1 つの行として表されます。 `numberOfAttempts` は、API が呼び出された回数を示します。 この値は `1` または `2` です。 API 呼び出しに関するその他の情報については、ログに詳しく説明されています。

   ![API コネクタ トランザクションを使用した監査ログの例のスクリーンショット](media/add-api-connector-token-enrichment/example-anonymized-audit-log.png)

::: zone-end

## <a name="next-steps"></a>次のステップ

::: zone pivot="b2c-user-flow"

- [サンプル](api-connector-samples.md#api-connector-rest-api-samples)を使用して作業を開始します。
- [API コネクタをセキュリティで保護する](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

API をセキュリティで保護する方法については、次の記事を参照してください。

- [チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](add-api-connector-token-enrichment.md)
- [お使いの RESTful API を保護する](secure-rest-api.md)
- [リファレンス: RESTful 技術プロファイル](restful-technical-profile.md)

::: zone-end
