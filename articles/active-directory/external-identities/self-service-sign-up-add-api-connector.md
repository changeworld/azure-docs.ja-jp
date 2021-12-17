---
title: セルフサービスのサインアップ フローに API コネクタを追加する - Azure AD
description: ユーザー フローで使用するように Web API を構成します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456e02b5d84dfde9534a62ea909da513ff8f1c81
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746147"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>API コネクタをユーザー フローに追加する

[API コネクタ](api-connectors-overview.md)を使用するには、まず API コネクタを作成してから、ユーザー フローで有効にします。

> [!IMPORTANT]
>
> - **2021 年 7 月 12 日以降**、Azure AD の B2B のお客様が、カスタムまたは基幹業務アプリケーションのセルフサービス サインアップで使用するために新しい Google の統合をセットアップした場合、認証がシステム Web ビューに移動されるまで、Google ID を使用した認証が機能しなくなります。 [詳細については、こちらを参照してください](google-federation.md#deprecation-of-web-view-sign-in-support)。
> - **2021 年の 9 月 30 日より**、Google は[埋め込みの Web ビューのサインイン サポートを廃止](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)します。 自分のアプリで埋め込みの Web ビューを使用してユーザーを認証していて、Google フェデレーションを [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md)、Azure AD B2B [(外部ユーザーの招待用)](google-federation.md)、または[セルフサービス サインアップ](identity-providers.md)で使用している場合、Google Gmail ユーザーが認証されなくなります。 [詳細については、こちらを参照してください](google-federation.md#deprecation-of-web-view-sign-in-support)。

## <a name="create-an-api-connector"></a>API コネクタを作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[All API connectors]\(すべての API コネクタ\)** を選択し、 **[New API connector]\(新しい API コネクタ\)** を選択します。

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-new.png" alt-text="API コネクタの作成時にターゲット URL や表示名などの基本的な構成を指定します。":::

5. 呼び出しの表示名を指定します。 たとえば、「**承認状態の確認**」などです。
6. API 呼び出しの **[エンドポイント URL]** を指定します。
7. **[認証の種類]** を選択し、API を呼び出すための認証情報を構成します。 [API コネクタのセキュリティ保護](self-service-sign-up-secure-api-connector.md)の方法を参照してください。

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connector-config.png" alt-text="API コネクタの作成時に認証の構成を指定します。":::

8. **[保存]** を選択します。

## <a name="the-request-sent-to-your-api"></a>API に送信される要求
API コネクタによってユーザー属性 ("要求") が **HTTP POST** 要求として具体化され、JSON 本文のキーと値のペアとして送信されます。 属性は [Microsoft Graph](/graph/api/resources/user#properties) ユーザー プロパティと同様にシリアル化されます。 

**要求の例**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
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
 "ui_locales":"en-US"
}
```

**[Azure Active Directory]**  >  **[外部 ID]**  >  **[カスタムのユーザー属性]** のエクスペリエンスで一覧表示されるユーザー プロパティとカスタム属性だけが、要求で送信できます。

カスタム属性は、ディレクトリ内に **extension_\<extensions-app-id>_AttributeName** の形式で存在しています。 API では、これと同じシリアル化された形式で要求を受け取ることを想定しています。 カスタム属性の詳細については、[セルフサービス サインアップ フローのカスタム属性の定義](user-flow-add-custom-attributes.md)に関するページを参照してください。

さらに、通常、要求はすべての要求で送信されます。
- **UI ロケール ('ui_locales')** - デバイスに構成されているエンドユーザーのロケール。 これを API で使用して、国際化された応答を返すことができます。
<!-- - **Step ('step')** - The step or point on the user flow that the API connector was invoked for. Values include:
  - `PostFederationSignup` - corresponds to "After federating with an identity provider during sign-up"
  - `PostAttributeCollection` - corresponds to "Before creating the user"
- **Client ID ('client_id')** - The `appId` value of the application that an end-user is authenticating to in a user flow. This is *not* the resource application's `appId` in access tokens. -->
- **メール アドレス ('email')** または [**ID ('identities')** ](/graph/api/resources/objectidentity) - これらの要求は、アプリケーションに対して認証を行っているエンドユーザーを識別するために API で使用することができます。

> [!IMPORTANT]
> API エンドポイントが呼び出されたときに要求に値がない場合、要求は API に送信されません。 API は、要求に要求が含まれていないケースを明示的に確認して処理するように設計する必要があります。

## <a name="enable-the-api-connector-in-a-user-flow"></a>ユーザー フローで API コネクタを有効にする

セルフサービス サインアップ ユーザー フローに API コネクタを追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[ユーザー フロー]** を選択し、API コネクタを追加するユーザー フローを選択します。
5. **[API connectors]\(API コネクタ\)** を選択し、ユーザー フローの次の手順で呼び出す API エンドポイントを選択します。

   - **サインアップ時に ID プロバイダーとのフェデレーションを行った後**
   - **ユーザーを作成する前**

    :::image type="content" source="media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png" alt-text="'ユーザーを作成する前' のようなユーザー フローの手順で使用する API コネクタの選択。":::

6. **[保存]** を選択します。

## <a name="after-federating-with-an-identity-provider-during-sign-up"></a>サインアップ時に ID プロバイダーとのフェデレーションを行った後

サインアップ プロセスのこのステップでの API コネクタは、ID プロバイダー (Google、Facebook、Azure AD など) でユーザーが認証された直後に呼び出されます。 このステップは、***属性コレクション ページ*** (ユーザーに提示される、ユーザー属性を収集するためのフォーム) の前にあります。

### <a name="example-request-sent-to-the-api-at-this-step"></a>この手順で API に送信される要求の例
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
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

サインアップ プロセスのこのステップでの API コネクタは、属性コレクション ページが含まれている場合、その後に呼び出されます。 このステップは、Azure AD でユーザー アカウントが作成される前に必ず呼び出されます。 

### <a name="example-request-sent-to-the-api-at-this-step"></a>この手順で API に送信される要求の例

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
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
 "ui_locales":"en-US"
}
```
API に送信される正確な要求は、ユーザーから収集される情報または ID プロバイダーによって提供される情報によって異なります。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>この手順での Web API からの予期される応答の種類

ユーザー フロー中に Web API で Azure AD から HTTP 要求を受信すると、次の応答が返されることがあります。

- 継続応答
- ブロック応答
- 検証応答

#### <a name="continuation-response"></a>継続応答
継続応答は、ユーザー フローが次の手順 (ディレクトリでのユーザーの作成) に進む必要があることを示します。

継続応答では、API から要求が返されることがあります。 要求が API によって返されると、その要求によって次のことが行われます。

- 属性収集ページから要求に既に割り当てられている値をオーバーライドする。

[継続応答](#example-of-a-continuation-response)の例を参照してください。

#### <a name="blocking-response"></a>ブロック応答
ブロック応答によって、ユーザー フローは終了します。 API を使用してこれを意図的に発行して、ユーザーにブロック ページを表示することにより、ユーザー フローの継続を停止することができます。 ブロック ページには、API によって提供された `userMessage` が表示されます。

[ブロック応答](#example-of-a-blocking-response)の例を参照してください。

### <a name="validation-error-response"></a>検証エラー応答
 API が検証エラー応答で応答すると、ユーザー フローは属性収集ページにとどまり、`userMessage` がユーザーに表示されます。 これで、ユーザーはフォームを編集して再送信することができます。 この種類の応答は、入力の検証に使用できます。

[検証エラー応答](#example-of-a-validation-error-response)の例を参照してください。

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
| version                                            | String            | はい      | API のバージョン。                                                                                                                                                                                                                                                                |
| action                                             | String            | はい      | 値は `Continue` とする必要があります。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | いいえ       | 値は、API コネクタの構成の **[Claim to receive]\(受信する要求\)** とユーザー フローの **[ユーザー属性]** として選択した場合にディレクトリに格納できます。 **[アプリケーション要求]** として選択されている場合、値をトークンで返すことができます。                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | いいえ       | `_<extensions-app-id>_` は "*省略可能*" であり、要求に含める必要はありません。 戻り値を使用すると、ユーザーから収集された値を上書きすることができます。  |

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

:::image type="content" source="media/api-connectors-overview/blocking-page-response.png" alt-text="API からブロック応答が返された後のエンドユーザー エクスペリエンスの画像例。":::

### <a name="example-of-a-validation-error-response"></a>検証エラー応答の例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
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

:::image type="content" source="media/api-connectors-overview/validation-error-postal-code.png" alt-text="API から検証エラーが返された後のエンドユーザー エクスペリエンスの画像例。":::

## <a name="best-practices-and-how-to-troubleshoot"></a>ベスト プラクティスとトラブルシューティングの方法

### <a name="using-serverless-cloud-functions"></a>サーバーレス クラウド機能の使用

[Azure Functions の HTTP トリガー](../../azure-functions/functions-bindings-http-webhook-trigger.md)などのサーバーレス機能を使用すると、API コネクタで使用する API エンドポイントを作成できます。 サーバーレス クラウド機能を使用して、[たとえば](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)、検証ロジックを実行したり、特定の電子メール ドメインへのサインアップを制限したりすることができます。 複雑なシナリオには、サーバーレス クラウド機能を使用して、他の Web API、データ ストア、および他のクラウド サービスを呼び出して起動することもできます。

### <a name="best-practices"></a>ベスト プラクティス
次のことを確認します。
* API は、上で説明した API 要求と応答のコントラクトに従っています。 
* API コネクタの **エンドポイント URL** によって、正確な API エンドポイントが指定されます。
* API によって、それが依存する受信済み要求の null 値が明示的に確認されます。
* API には、[API コネクタのセキュリティ保護](self-service-sign-up-secure-api-connector.md)に関する記事で説明されている認証方法が実装されています。
* API が可能な限り迅速に応答することで、スムーズなユーザー エクスペリエンスが保証されます。
    * Azure AD 側は、応答を受信するために最大 "*20 秒間*" 待機します。 何も受信しない場合は、API の呼び出しが *もう一度実行 (再試行)* されます。
    * サーバーレス機能またはスケーラブルな Web サービスを使用している場合は、API を運用環境で "起動状態" または "ウォーム状態" に保つホスティング プランを使用します。 Azure Functions の場合は、少なくとも [Premium プラン](../../azure-functions/functions-scale.md)を使用することをお勧めします。
* API の高可用性を保証します。
* ダウンストリームの API、データベース、または API のその他の依存関係のパフォーマンスを監視し、最適化します。
* エンドポイントは、Azure AD TLS と暗号のセキュリティ要件に準拠している必要があります。 詳細については、[TLS と暗号スイートの要件](../../active-directory-b2c/https-cipher-tls-requirements.md)に関するページを参照してください。 
 
### <a name="use-logging"></a>ログの使用

一般に、予期しないエラー コード、例外、およびパフォーマンスの低下について API を監視するには、ご使用の Web API サービスによって有効になっているログ ツール ([Application insights](../../azure-functions/functions-monitoring.md) など) を使うと便利です。
* HTTP 200 または 400 以外の HTTP 状態コードを監視します。
* 401 または 403 HTTP 状態コードは、通常、認証に問題があることを示しています。 API コネクタで API の認証レイヤーとそれに対応する構成を再確認します。
* 開発では、必要に応じて、より積極的なログ レベル ("トレース" や "デバッグ" など) を使用します。
* 長い応答時間について API を監視します。 

## <a name="next-steps"></a>次のステップ
- [カスタム承認ワークフローをセルフサービス サインアップに追加する](self-service-sign-up-add-approvals.md)方法を確認する
- [クイックスタートのサンプル](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)からご覧ください。
