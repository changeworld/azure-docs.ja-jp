---
title: セルフサービスのサインアップ フローに API コネクタを追加する - Azure AD
description: ユーザー フローで使用するように Web API を構成します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0498a2015b75221763ab5fdd4f6e94428922bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386744"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>API コネクタをユーザー フローに追加する

[API コネクタ](api-connectors-overview.md)を使用するには、まず API コネクタを作成してから、ユーザー フローで有効にします。

## <a name="create-an-api-connector"></a>API コネクタを作成する

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[All API connectors (Preview)]\(すべての API コネクタ (プレビュー)\)** を選択し、 **[New API connector]\(新しい API コネクタ\)** を選択します。

   ![新しい API コネクタを追加する](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. 呼び出しの表示名を指定します。 たとえば、「**承認状態の確認**」などです。
6. API 呼び出しの **[エンドポイント URL]** を指定します。
7. API の認証情報を指定します。

   - 現在サポートされているのは [基本認証] のみです。 開発目的で基本認証を使用せずに API を使用する場合は、API で無視できるダミーの **[ユーザー名]** と **[パスワード]** を入力するだけです。 Azure Functions で API キーを使用するには、コードをクエリ パラメーターとして **[エンドポイント URL]** に含めることができます (例: https[]()://contoso.azurewebsites.net/api/endpoint<b>?code=0123456789</b>)。

   ![新しい API コネクタを追加する](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. API に送信する要求を選択します。
9. API から返信される予定の要求を選択します。

   ![API コネクタ要求を設定する](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. **[保存]** を選択します。

## <a name="enable-the-api-connector-in-a-user-flow"></a>ユーザー フローで API コネクタを有効にする

セルフサービス サインアップ ユーザー フローに API コネクタを追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[User flows (Preview)]\(ユーザー フロー (プレビュー)\)** を選択し、API コネクタを追加するユーザー フローを選択します。
5. **[API connectors]\(API コネクタ\)** を選択し、ユーザー フローの次の手順で呼び出す API エンドポイントを選択します。

   - **ID プロバイダーを使用してサインインした後**
   - **ユーザーを作成する前**

   ![API をユーザー フローに追加する](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. **[保存]** を選択します。

「[ユーザー フロー内で API コネクタを有効にできる場所](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow)」を参照してください。

## <a name="request-sent-to-the-api"></a>API に送信される要求

API コネクタによって、選択した要求が HTTP POST 要求として具体化され、JSON 本文のキーと値のペアとして送信されます。 応答には HTTP ヘッダー `Content-Type: application/json` も含まれます。 属性は Microsoft Graph ユーザー属性と同様にシリアル化されます。 <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>要求の例

```http
POST <API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

**UI ロケール ('ui_locales')** 要求は、すべての要求で既定で送信されます。 これによってユーザーのロケールがわかります。また、国際化応答を返すために API で使用することができます。 これは API の構成ペインには表示されません。

API エンドポイントが呼び出されたときに送信する要求に値がない場合、要求は API に送信されません。

カスタム属性は、**extension_\<extensions-app-id>_AttributeName** 形式を使用してユーザー向けに作成できます。 API では、これと同じシリアル化された形式で要求を受け取ることを想定しています。 API からは、`<extensions-app-id>` を含む、または含まない要求が返されることがあります。 カスタム属性の詳細については、[セルフサービス サインアップ フローのカスタム属性の定義](user-flow-add-custom-attributes.md)に関するページを参照してください。

> [!TIP] 
> [**identities ('identities')** ](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) および **Email Address ('email_address')** の各要求は、テナントにアカウントを作成する前にユーザーを識別するために使用できます。 'identities' 要求は、ユーザーが Google または Facebook で認証されるときに送信され、'email_address' は常に送信されます。

## <a name="expected-response-types-from-the-web-api"></a>Web API からの予期される応答の種類

ユーザー フロー中に Web API で Azure AD から HTTP 要求を受信すると、次の応答が返されることがあります。

- [継続応答](#continuation-response)
- [ブロック応答](#blocking-response)
- [検証エラー応答](#validation-error-response)

### <a name="continuation-response"></a>継続応答

継続応答は、ユーザー フローが次の手順に進む必要があることを示します。 継続応答では、API から要求が返されることがあります。

要求が API から返され、 **[Claim to receive]\(受信する要求\)** として選択された場合、要求によって以下が実行されます。

- ページが表示される前に API コネクタが呼び出された場合、属性収集ページの入力フィールドに事前に入力する。
- 要求に既に割り当てられている値をオーバーライドする。
- 以前は null だった場合に、値を要求に割り当てる。

#### <a name="example-of-a-continuation-response"></a>継続応答の例

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
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | いいえ       | 返される要求には、必要に応じて `_<extensions-app-id>_` を含めることはできません。 値は、API コネクタの構成の **[Claim to receive]\(受信する要求\)** とユーザー フローの **[ユーザー属性]** として選択した場合にディレクトリに格納されます。 トークンではカスタム属性を返信できません。 |

### <a name="blocking-response"></a>ブロック応答

ブロック応答によって、ユーザー フローは終了します。 API を使用してこれを意図的に発行して、ユーザーにブロック ページを表示することにより、ユーザー フローの継続を停止することができます。 ブロック ページには、API によって提供された `userMessage` が表示されます。

ブロック応答の例を次に示します。

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| パラメーター   | Type   | 必須 | 説明                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | はい      | API のバージョン。                                                    |
| action      | String | はい      | 値は `ShowBlockPage` とする必要があります                                              |
| userMessage | String | はい      | ユーザーに表示するメッセージ。                                            |
| code        | String | いいえ       | エラー コード。 デバッグの目的で使用できます。 ユーザーには表示されません。 |

#### <a name="end-user-experience-with-a-blocking-response"></a>ブロック応答を使用したエンド ユーザー エクスペリエンス

![ブロック ページの例](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>検証エラー応答

属性収集ページの後に呼び出された API 呼び出しから、検証エラー応答が返される場合があります。 その場合、ユーザー フローは属性収集ページにとどまり、`userMessage` がユーザーに表示されます。 これで、ユーザーはフォームを編集して再送信することができます。 この種類の応答は、入力の検証に使用できます。

#### <a name="example-of-a-validation-error-response"></a>検証エラー応答の例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| パラメーター   | Type    | 必須 | 説明                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | はい      | API のバージョン。                                                    |
| action      | String  | はい      | 値は `ValidationError` とする必要があります。                                           |
| status      | Integer | はい      | ValidationError 応答の値 `400` である必要があります。                        |
| userMessage | String  | はい      | ユーザーに表示するメッセージ。                                            |
| code        | String  | いいえ       | エラー コード。 デバッグの目的で使用できます。 ユーザーには表示されません。 |

#### <a name="end-user-experience-with-a-validation-error-response"></a>検証エラー応答を使用したエンド ユーザー エクスペリエンス

![検証ページの例](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Azure Functions との統合
API コネクタで使用する API を作成する簡単な方法として、Azure Functions で HTTP トリガーを使用することがあります。 Azure Functions を使用して、[たとえば](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)、検証ロジックを実行し、サインアップを特定のドメインに制限します。 他の Web API、ユーザー ストア、および他のクラウド サービスを呼び出して起動することもできます。

## <a name="next-steps"></a>次のステップ

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- [カスタム承認ワークフローをセルフサービス サインアップに追加する](self-service-sign-up-add-approvals.md)方法を確認する
- [Azure Function クイックスタート サンプル](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)を使用する。
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
