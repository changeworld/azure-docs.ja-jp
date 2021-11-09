---
title: API コネクタをサインアップ ユーザー フローに追加する
description: サインアップ ユーザー フローで使用するように API コネクタを構成します。
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 11/09/2021
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 665f914d19f0ab73c8ed6ab6af75ba485c1d35bf
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053372"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow"></a>API コネクタをサインアップ ユーザー フローに追加する

開発者または IT 管理者は、API コネクタを使用し、サインアップ ユーザー フローと REST API を統合してサインアップ体験をカスタマイズしたり、外部システムと統合したりできます。 このチュートリアルの最後では、[REST API サービス](api-connectors-overview.md)と対話する Azure AD B2C ユーザー フローを作成して、サンアップ エクスペリエンスを変更できるようになります。 

::: zone pivot="b2c-user-flow"
API エンドポイントは、[サンプル](api-connector-samples.md#api-connector-rest-api-samples)の 1 つを使用して作成できます。
::: zone-end

::: zone pivot="b2c-custom-policy"

このシナリオでは、ユーザーが Azure AD B2C サインアップ ページにロイヤルティ番号を入力する機能を追加します。 REST API によって、メール アドレスとロイヤルティ番号の組み合わせがプロモーション コードにマッピングされているかどうかが検証されます。 REST API がこのユーザーのプロモーション コードを見つけると、Azure AD B2C に返されます。 最後に、アプリケーションが使用するトークン要求にプロモーション コードが挿入されます。

対話は、オーケストレーション手順として設計することもできます。 これは、REST API が画面上のデータを検証せず、常に要求を返す場合に適しています。 詳細については、「[チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](add-api-connector-token-enrichment.md)を参照してください。

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="create-an-api-connector"></a>API コネクタを作成する

[API コネクタ](api-connectors-overview.md)を使用するには、まず API コネクタを作成してから、ユーザー フローで有効にします。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。
1. **[API コネクタ]** を選択し、 **[新しい API コネクタ]** を選択します。

   ![API コネクタの基本構成のスクリーンショット](media/add-api-connector/api-connector-new.png)

1. 呼び出しの表示名を指定します。 例: **ユーザー情報の検証**。
1. API 呼び出しの **[エンドポイント URL]** を指定します。
1. **[認証の種類]** を選択し、API を呼び出すための認証情報を構成します。 [API コネクタのセキュリティ保護](secure-rest-api.md)の方法を参照してください。

   ![API コネクタの認証構成のスクリーンショット](media/add-api-connector/api-connector-config.png)

1. **[保存]** を選択します。

## <a name="the-request-sent-to-your-api"></a>API に送信される要求
API コネクタによってユーザー属性 ("要求") が **HTTP POST** 要求として具体化され、JSON 本文のキーと値のペアとして送信されます。 属性は [Microsoft Graph](/graph/api/resources/user#properties) ユーザー プロパティと同様にシリアル化されます。 

**要求の例**
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
 "objectId": "11111111-0000-0000-0000-000000000000"
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "step": "<step-name>",
 "client_id":"93fd07aa-333c-409d-955d-96008fd08dd9",
 "ui_locales":"en-US"
}
```

**[Azure AD B2C]** の **[ユーザー属性]** のエクスペリエンスで一覧表示されるユーザー プロパティとカスタム属性だけが、要求で送信できます。

カスタム属性は、ディレクトリ内に **extension_\<extensions-app-id>_CustomAttribute** の形式で存在しています。 API では、これと同じシリアル化された形式で要求を受け取ることを想定しています。 カスタム属性の詳細については、[Azure AD B2C でカスタム属性を定義する方法](user-flow-custom-attributes.md)に関するページを参照してください。

また、これらの要求は、通常、すべての要求で送信されます。
- **UI ロケール ('ui_locales')** - デバイスで構成されているエンド ユーザーのロケール。 これを API で使用して、国際化された応答を返すことができます。
- **ステップ ('step')** - API コネクタが呼び出されたユーザー フロー上のステップまたはポイント。 次の値が含まれます。
  - `PostFederationSignup` - "サインアップ中に ID プロバイダーとのフェデレーションを行った後" に対応します。
  - `PostAttributeCollection` - "ユーザーを作成する前" に対応します。
  - `PreTokenIssuance` - "トークン (プレビュー) を送信する前に" に対応します。 [このステップについて説明します。](add-api-connector-token-enrichment.md)
- **クライアント ID ('client_id')** - エンド ユーザーがユーザー フローで認証しているアプリケーションの `appId` 値。 これは、アクセス トークンのリソース アプリケーションの `appId` では *ありません*。
- **メールアドレス ('email')** または [**ID ('identities')**](/graph/api/resources/objectidentity) - これらの要求は、アプリケーションに対して認証を行っているエンド ユーザーを識別するために API で使用できます。
  
> [!IMPORTANT]
> API エンドポイントが呼び出されたときに要求に値がない場合、要求は API に送信されません。 API は、要求に要求が含まれていないケースを明示的に確認して処理するように設計する必要があります。

## <a name="enable-the-api-connector-in-a-user-flow"></a>ユーザー フローで API コネクタを有効にする

サインアップ ユーザー フローに API コネクタを追加するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。
1. **[ユーザー フロー]** を選択し、API コネクタを追加するユーザー フローを選択します。
1. **[API connectors]\(API コネクタ\)** を選択し、ユーザー フローの次の手順で呼び出す API エンドポイントを選択します。

   - **サインアップ時に ID プロバイダーとのフェデレーションを行った後**
   - **ユーザーを作成する前**
   - **トークンを送信する前 (プレビュー)**

   ![ユーザー フローのステップに対して API コネクタを選択する](media/add-api-connector/api-connectors-user-flow-select.png)

1. **[保存]** を選択します。

これらの手順は、**サインアップとサインイン (推奨)** と **サインアップ (推奨)** 用ですが、エクスペリエンスのサインアップ部分のみに適用されます。

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>サインアップ時に ID プロバイダーとのフェデレーションを行った後

サインアップ プロセスのこのステップでの API コネクタは、ID プロバイダー (Google、Facebook、Azure AD など) でユーザーが認証された直後に呼び出されます。 このステップは、***属性コレクション ページ*** (ユーザーに提示される、ユーザー属性を収集するためのフォーム) の前にあります。 ユーザーがローカル アカウントを使用して登録している場合、このステップは呼び出されません。

### <a name="example-request-sent-to-the-api-at-this-step"></a>この手順で API に送信される要求の例
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
 "objectId": "11111111-0000-0000-0000-000000000000",
 "givenName":"John",
 "lastName":"Smith",
 "step": "PostFederationSignup",
 "client_id":"<guid>",
 "ui_locales":"en-US"
}
```

API に送信される正確な要求は、ID プロバイダーによって提供される情報によって異なります。 "email" は常に送信されます。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>この手順での Web API からの予期される応答の種類

ユーザー フロー中に Web API で Azure AD から HTTP 要求を受信すると、次の応答が返されることがあります。

- 継続応答
- ブロック応答

#### <a name="continuation-response"></a>継続応答

継続応答は、ユーザー フローが次の手順 (属性収集ページ) に進む必要があることを示します。

継続応答では、API から要求が返されることがあります。 要求が API によって返されると、その要求によって次のことが行われます。

- 属性収集ページの入力フィールドに事前入力する。

[継続応答](#example-of-a-continuation-response)の例を参照してください。

#### <a name="blocking-response"></a>ブロック応答

ブロック応答によって、ユーザー フローは終了します。 API を使用してこれを意図的に発行して、ユーザーにブロック ページを表示することにより、ユーザー フローの継続を停止することができます。 ブロック ページには、API によって提供された `userMessage` が表示されます。

[ブロック応答](#example-of-a-blocking-response)の例を参照してください。

## <a name="before-creating-the-user"></a>ユーザーを作成する前

サインアップ プロセスのこのステップでの API コネクタは、属性コレクション ページが含まれている場合、その後に呼び出されます。 このステップは、ユーザー アカウントが作成される前に必ず呼び出されます。

### <a name="example-request-sent-to-the-api-at-this-step"></a>この手順で API に送信される要求の例

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
 "objectId": "11111111-0000-0000-0000-000000000000",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "step": "PostAttributeCollection",
 "client_id":"93fd07aa-333c-409d-955d-96008fd08dd9",
 "ui_locales":"en-US"
}
```

API に送信される要求は、ユーザーから収集される情報または ID プロバイダーによって提供される情報によって異なります。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>この手順での Web API からの予期される応答の種類

ユーザー フロー中に Web API で Azure AD から HTTP 要求を受信すると、次の応答が返されることがあります。

- 継続応答
- ブロック応答
- 検証応答

#### <a name="continuation-response"></a>継続応答

継続応答は、ユーザー フローが次の手順 (ディレクトリでのユーザーの作成) に進む必要があることを示します。

継続応答では、API から要求が返されることがあります。 要求が API によって返されると、その要求によって次のことが行われます。

- [属性コレクション] ページでユーザーが既に指定したすべての値を上書きします。

ユーザーから収集しない要求をサインアップ時にディレクトリに書き込むには、ユーザー フローの **[ユーザー属性]** で要求を選択する必要があります。既定では、ユーザーに値の入力が求められますが、[カスタム JavaScript または CSS](customize-ui-with-html.md) を使用して、エンド ユーザーの入力フィールドを非表示にすることができます。

[継続応答](#example-of-a-continuation-response)の例を参照してください。

#### <a name="blocking-response"></a>ブロック応答
ブロック応答によって、ユーザー フローは終了します。 API を使用してこれを意図的に発行して、ユーザーにブロック ページを表示することにより、ユーザー フローの継続を停止することができます。 ブロック ページには、API によって提供された `userMessage` が表示されます。

[ブロック応答](#example-of-a-blocking-response)の例を参照してください。

### <a name="validation-error-response"></a>検証エラー応答
 API が検証エラー応答で応答すると、ユーザー フローは属性収集ページにとどまり、`userMessage` がユーザーに表示されます。 これで、ユーザーはフォームを編集して再送信することができます。 この種類の応答は、入力の検証に使用できます。

[検証エラー応答](#example-of-a-validation-error-response)の例を参照してください。

## <a name="before-sending-the-token-preview"></a>トークンを送信する前 (プレビュー)

> [!IMPORTANT]
> この手順で使用されている API コネクタはプレビュー段階です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この手順の API コネクタは、サインインとサインアップ中にトークンを発行しようとすると呼び出されます。 この手順の API コネクタを使用すると、外部ソースからの要求値でトークンを強化できます。

### <a name="example-request-sent-to-the-api-at-this-step"></a>この手順で API に送信される要求の例

```http
POST <API-endpoint>
Content-type: application/json

{
 "clientId": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenApplicationClaims",
 "ui_locales":"en-US"
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
}
```

API に送信される要求は、ユーザーに定義した情報によって異なります。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>この手順での Web API からの予期される応答の種類

ユーザー フロー中に Web API で Azure AD から HTTP 要求を受信すると、次の応答が返されることがあります。

- 継続応答

#### <a name="continuation-response"></a>継続応答

継続応答は、ユーザー フローが次の手順 (トークンの発行) に進む必要があることを示します。

継続応答では、API から追加の要求が返されることがあります。 トークンで返す API から返される要求は、組み込みの要求であるか、[カスタム属性として定義する必要があり](user-flow-custom-attributes.md)、ユーザー フローの **アプリケーション要求** 構成で選択する必要があります。 

トークンの要求値は、ディレクトリの値ではなく、API によって返される値になります。 一部の要求値は、API 応答によって上書きできません。 API によって返すことができる要求は、`email` を除き、 **[ユーザー属性]** にあるセットに対応します。

[継続応答](#example-of-a-continuation-response)の例を参照してください。

> [!NOTE]
> API は、最初の認証中にのみ呼び出されます。 更新トークンを使用して新しいアクセス トークンまたは ID トークンを自動的に取得する場合、トークンには、最初の認証中に評価された値が含まれます。 

## <a name="example-responses"></a>応答の例

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
| \<builtInUserAttribute>                            | \<attribute-type> | いいえ       | 戻り値を使用すると、ユーザーから収集された値を上書きすることができます。                    |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | いいえ       | `_<extensions-app-id>_` は *省略可能* であり、要求に含める必要はありません。 戻り値を使用すると、ユーザーから収集された値を上書きすることができます。 |

### <a name="example-of-a-blocking-response"></a>ブロック応答の例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| パラメーター   | Type   | 必須 | 説明                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | はい      | API のバージョン。                                                    |
| action      | String | はい      | 値は `ShowBlockPage` とする必要があります                                              |
| userMessage | String | はい      | ユーザーに表示するメッセージ。                                            |

**ブロック応答を使用したエンド ユーザー エクスペリエンス**

![ブロック応答の例](media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>検証エラー応答の例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| パラメーター   | Type    | 必須 | 説明                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | はい      | API のバージョン。                                                    |
| action      | String  | はい      | 値は `ValidationError` とする必要があります。                                           |
| status      | Integer/String | はい      | ValidationError 応答の値は、`400` または `"400"` である必要があります。  |
| userMessage | String  | はい      | ユーザーに表示するメッセージ。                                            |

> [!NOTE]
> HTTP 状態コードは、応答の本文で、"status" 値であることに加え、"400" である必要があります。

**検証エラー応答を使用したエンド ユーザー エクスペリエンス**

![検証エラー応答の例](media/add-api-connector/validation-error-postal-code.png)

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prepare-a-rest-api-endpoint"></a>REST API エンドポイントを準備する

このチュートリアルでは、メール アドレスがバックエンド システムに登録されているかどうかを、ロイヤルティ ID を使用して検証する REST API が必要です。 登録されている場合、REST API は、顧客がアプリケーション内で商品を購入するために使用できる登録プロモーション コードを返す必要があります。 それ以外の場合、REST API は次の HTTP 409 エラー メッセージを返します: "Loyalty ID '{loyalty ID}' is not associated with '{email}' email address." (ロイヤルティ ID '{loyalty ID}' は '{email}' 電子メール アドレスに関連付けられていません。)

次の JSON コードでは、お使いの REST API エンドポイントに送信される Azure AD B2C のデータを示しています。 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

データがお使いの REST API によって検証されると、次の JSON データと共に HTTP 200 (Ok) が返されます。

```json
{
    "promoCode": "24534"
}
```

検証が失敗した場合、REST API は `userMessage` JSON 要素と共に HTTP 409 (Conflict) を返す必要があります。 IEF は、REST API が返す `userMessage` 要求を予期しています。 検証が失敗した場合、この要求はユーザーに文字列として表示されます。

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>RESTful API 技術プロファイルを追加する 

[Restful 技術プロファイル](restful-technical-profile.md)では、お使いの独自の RESTful サービスへのインターフェイスをサポートしています。 Azure AD B2C は、`InputClaims` コレクションでデータを RESTful サービスに送信し、`OutputClaims` コレクションで返却データを受信します。 **ClaimsProviders** 要素を見つけて、次のように新しい要求プロバイダーを追加します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

この例では、`userLanguage` が JSON ペイロード内の `lang` として REST サービスに送信されます。 `userLanguage` 要求の値には、現在のユーザーの言語 ID が含まれます。 詳細については、[要求リゾルバー](claim-resolver-overview.md)に関するページを参照してください。

### <a name="configure-the-restful-api-technical-profile"></a>RESTful API 技術プロファイルを構成する 

REST API をデプロイした後、`REST-ValidateProfile` 技術プロファイルのメタデータを、次のような独自の REST API を反映させるように設定します。

- **ServiceUrl**: REST API エンドポイントの URL を設定します。
- **SendClaimsIn**: RESTful クレーム プロバイダーへの入力要求の送信方法を指定します。
- **AuthenticationType**: RESTful 要求プロバイダーにより実行されている認証の種類を設定します。 
- **AllowInsecureAuthInProduction**: 運用環境では、このメタデータを必ず `true` に設定してください。
    
詳細な構成については、[RESTful 技術プロファイルのメタデータ](restful-technical-profile.md#metadata)に関する記事を参照してください。

`AuthenticationType` と `AllowInsecureAuthInProduction` の上のコメントに、運用環境に移行するときに行う必要がある変更が指定されています。 お使いの RESTful API を実稼働用にセキュリティで保護する方法については、[RESTful API のセキュリティ保護](secure-rest-api.md)に関するページを参照してください。

## <a name="validate-the-user-input"></a>ユーザー入力の検証

サインアップ時にユーザーのロイヤルティ番号を取得するには、ユーザーが画面上でこのデータを入力できるようにする必要があります。 **loyaltyId** 出力要求を、既存のサインアップ技術プロファイル セクションの `OutputClaims` 要素に追加することによって、サインアップページに追加します。 要求が画面に表示される順序を制御するには、出力要求の一覧全体を指定します。  

検証技術プロファイルの参照を、`REST-ValidateProfile` を呼び出すサインアップ技術プロファイルに追加します。 新しい検証技術プロファイルが、基本ポリシーで定義されている `<ValidationTechnicalProfiles>` コレクションの先頭に追加されます。 この動作は、検証が成功した後にのみ、Azure AD B2C がディレクトリにアカウントを作成することを意味します。   

1. **ClaimsProviders** 要素を見つけます。 新しい要求プロバイダーを次のように追加します。

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>トークンに要求を含める 

プロモーション コード要求を証明書利用者アプリケーションに返すには、出力要求を <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ファイルに追加します。 ユーザー体験が成功した後、出力要求によって要求がトークンに追加され、アプリケーションに送信されます。 証明書利用者セクション内の技術プロファイル要素を変更して、`promoCode` を出力要求として追加します。
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルのツールバーにある **[ディレクトリ + サブスクリプション]** アイコンを選択して、Azure AD テナントを含むディレクトリを使っていることを確認します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[スイッチ]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択し、変更した次のポリシー ファイルをアップロードします。*TrustFrameworkExtensions.xml*、および *SignUpOrSignin.xml*。 
1. アップロードしたサインアップまたはサインイン ポリシーを選択し、 **[今すぐ実行]** ボタンをクリックします。
1. メール アドレスを使用してサインアップできることを確認します。
1. **[Sign-up now]\(今すぐサインアップ\)** リンクをクリックします。
1. **[Your loyalty ID]\(ロイヤルティ ID\)** に「1234」と入力して **[続行]** をクリックします。 この時点で、検証エラー メッセージが表示されるはずです。
1. 別の値に変更して **[続行]** をクリックします。
1. アプリケーションに返送されるトークンには、`promoCode` 要求が含まれています。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

::: zone-end

## <a name="best-practices-and-how-to-troubleshoot"></a>ベスト プラクティスとトラブルシューティングの方法

::: zone pivot="b2c-user-flow"

### <a name="using-serverless-cloud-functions"></a>サーバーレス クラウド機能の使用

[Azure Functions の HTTP トリガー](../azure-functions/functions-bindings-http-webhook-trigger.md)などのサーバーレス機能を使用すると、API コネクタで使用する API エンドポイントを作成できます。 サーバーレス クラウド機能を使用して、[たとえば](api-connector-samples.md#api-connector-rest-api-samples)、検証ロジックを実行したり、特定の電子メール ドメインへのサインアップを制限したりすることができます。 複雑なシナリオには、サーバーレス クラウド機能を使用して、他の Web API、データ ストア、および他のクラウド サービスを呼び出して起動することもできます。

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

![ユーザー認証中の API コネクタ トランザクションの例](media/add-api-connector/example-anonymized-audit-log.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="using-serverless-cloud-functions"></a>サーバーレス クラウド機能の使用

[Azure Functions の HTTP トリガー](../azure-functions/functions-bindings-http-webhook-trigger.md)などのサーバーレス クラウド機能では、API コネクタとして使用する API エンドポイントを作成するためのシンプルで高可用性、高パフォーマンスな方法を提供します。

### <a name="best-practices"></a>ベスト プラクティス
次のことを確認します。
* API によって、それが依存する受信済み要求の null 値が明示的に確認されます。
* API には、[API コネクタのセキュリティ保護](secure-rest-api.md)に関する記事で説明されている認証方法が実装されています。
* API が可能な限り迅速に応答することで、スムーズなユーザー エクスペリエンスが保証されます。 
    * サーバーレス機能またはスケーラブルな Web サービスを使用している場合は、API を運用環境で "起動状態" または "ウォーム状態" に保つホスティング プランを使用します。 Azure Functions の場合は、少なくとも [Premium プラン](../azure-functions/functions-scale.md)を使用することをお勧めします。
* API の高可用性を保証します。
* ダウンストリームの API、データベース、または API のその他の依存関係のパフォーマンスを監視し、最適化します。

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]
 
### <a name="use-logging"></a>ログの使用

一般に、予期しないエラー コード、例外、およびパフォーマンスの低下について API を監視するには、ご使用の Web API サービスによって有効になっているログ ツール ([Application insights](../azure-functions/functions-monitoring.md) など) を使うと便利です。
* HTTP 200 または 400 以外の HTTP 状態コードを監視します。
* 401 または 403 HTTP 状態コードは、通常、認証に問題があることを示しています。 API コネクタで API の認証レイヤーとそれに対応する構成を再確認します。
* 開発では、必要に応じて、より積極的なログ レベル ("トレース" や "デバッグ" など) を使用します。
* 長い応答時間について API を監視します。

::: zone-end

## <a name="next-steps"></a>次の手順

::: zone pivot="b2c-user-flow"

- [サンプル](api-connector-samples.md#api-connector-rest-api-samples)を使用して作業を開始します。
- [API コネクタをセキュリティで保護する](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

- [チュートリアル:REST API 要求交換をオーケストレーション手順として Azure AD B2C ユーザー体験に統合する](add-api-connector-token-enrichment.md)
- [API コネクタをセキュリティで保護する](secure-rest-api.md)
- [リファレンス: RESTful 技術プロファイル](restful-technical-profile.md)

::: zone-end
