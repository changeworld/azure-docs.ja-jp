---
title: カスタム ポリシーで RESTful 技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで RESTful 技術プロファイルを定義します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edad748bc2192f98b9674b80dada5b03aa9ee2d1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197988"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで RESTful 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、独自の RESTful サービスのサポートを提供しています。 Azure AD B2C は、入力要求コレクションでデータを RESTful サービスに送信し、出力要求コレクションで返却データを受信します。 RESTful サービスの統合により、次の操作を実行できます。

- **ユーザー入力データの検証** - 不正なデータが Azure AD B2C に残らないようにします。 ユーザーが入力した値が有効でない場合、ユーザーに入力を指示するエラー メッセージがご利用の RESTful サービスによって返されます。 たとえば、ユーザーによって入力されたメール アドレスが顧客データベースに存在するかどうかを確認できます。
- **入力要求の上書き** - 入力要求で値の書式を再設定できるようにします。 たとえば、ユーザーが名をすべて小文字または大文字で入力する場合に、名の最初の文字だけを大文字にするように書式設定できます。
- **ユーザー データの拡充** - 企業の基幹業務アプリケーションとさらに統合できるようにします。 たとえば、RESTful サービスでは、ユーザーのメール アドレスを受け取り、顧客のデータベースを照会し、ユーザーのロイヤルティ番号を Azure AD B2C に返すことができます。 返された要求は、保存するか、次のオーケストレーション手順で評価するか、またはアクセス トークンに追加できます。
- **カスタム ビジネス ロジックの実行** - プッシュ通知を送信する、企業データベースを更新する、ユーザー移行プロセスを実行する、アクセス許可を管理する、データベースを監査するなどの、アクションを実行できるようにします。

お使いのポリシーでは、入力要求を REST API に送信する場合があります。 また、REST API は、お使いのポリシーで後に使用できる出力要求を返すか、エラー メッセージをスローする可能性があります。 次の方法で、RESTful サービスとの統合を設計できます。

- **検証技術プロファイル** - 検証技術プロファイルは、RESTful サービスを呼び出します。 検証技術プロファイルでは、ユーザー体験を続ける前に、ユーザーが入力したデータを検証します。 検証技術プロファイルにより、エラー メッセージが自己宣言されたページに表示され、出力要求で返されます。
- **要求の交換** - RESTful サービスへの呼び出しは、オーケストレーションの手順を経て行われます。 このシナリオでは、エラー メッセージを表示するためのユーザー インターフェイスはありません。 REST API がエラーを返す場合、ユーザーは、エラー メッセージがある証明書利用者アプリケーションにリダイレクトで戻されます。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、Azure AD B2C により使用される、プロトコルハンドラー アセンブリの完全修飾名が存在する必要があります `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`。

RESTful 技術プロファイルの例を次に示します。

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、REST API に送信する要求の一覧が存在します。 REST API で定義される名前に、要求の名前をマップすることもできます。 次の例は、ポリシーと REST API の間のマッピングを示しています。 **givenName** 要求は **firstName** として、**surname** は **lastName** として、REST API に送信されます。 **email** 要求はそのまま送信されます。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** 要素には、REST API に送信する前に、入力要求を修正したり新しい要求を生成するために使用される、**InputClaimsTransformation** 要素のコレクションが存在する場合があります。

## <a name="send-a-json-payload"></a>JSON ペイロードを送信する

REST API 技術プロファイルを使用すると、複雑な JSON ペイロードをエンドポイントに送信できます。

複雑な JSON ペイロードを送信するには、次のようにします。

1. [GenerateJson](json-transformations.md) 要求の変換を使用して JSON ペイロードをビルドします。
1. REST API の技術プロファイルで、次のようにします。
    1. `GenerateJson` 要求の変換への参照を使用して、入力要求の変換を追加します。
    1. `SendClaimsIn` メタデータ オプションを `body` に設定します。
    1. `ClaimUsedForRequestPayload` メタデータ オプションを、JSON ペイロードを含む要求の名前に設定します。
    1. 入力要求に、JSON ペイロードを含む入力要求への参照を追加します。

次の例 `TechnicalProfile` では、サードパーティの電子メール サービス (この場合は SendGrid) を使用して、確認メールを送信します。

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素には、REST API により返される要求の一覧が存在します。 お使いのポリシーで定義される要求の名前を、REST API で定義される名前にマップする必要があるかもしれません。 `DefaultValue` 属性を設定している限り、REST API の ID プロバイダーにより返されない要求を追加することもできます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

次の例は、REST API により返される要求を示しています。

- **loyaltyNumber** 要求名にマップされている **MembershipId** 要求。

また、技術プロファイルは、ID プロバイダーにより返されない要求も返します。

- 既定値が `true` に設定されている **loyaltyNumberIsNew** 要求。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| ServiceUrl | はい | REST API エンドポイントの URL。 |
| AuthenticationType | はい | RESTful 要求プロバイダーにより実行されている認証の種類。 指定できる値: `None`、`Basic`、`Bearer`、または `ClientCertificate`。 `None` の値は、REST API が匿名でないことを示します。 `Basic` の値は、REST API が HTTP 基本認証で保護されていることを示します。 Azure AD B2C などの検証されたユーザーのみが API にアクセスできます。 `ClientCertificate` の (推奨) 値は、REST API がクライアント証明書認証を使用してアクセスを制限していることを示します。 Azure AD B2C などの適切な証明書を持つサービスのみが、ご利用の API にアクセスできます。 `Bearer` 値は、REST API ではクライアント OAuth2 ベアラー トークンを使用してアクセスが制限されることを示します。 |
| SendClaimsIn | いいえ | RESTful クレーム プロバイダーへの入力要求の送信方法を指定します。 可能な値: `Body` (既定)、`Form`、`Header`、または `QueryString`。 `Body` の値は、要求本文で、JSON 形式で送信される入力要求です。 `Form` の値は、要求本文で、キーの値をアンパサンド ' &' で区切った形式で送信される入力要求です。 `Header` の値は、要求本文で送信される入力要求です。 `QueryString` の値は、要求クエリ文字列で送信される入力要求です。 それぞれによって呼び出される HTTP 動詞は次のとおりです。<br /><ul><li>`Body`:POST</li><li>`Form`:POST</li><li>`Header`:GET</li><li>`QueryString`:GET</li></ul> |
| ClaimsFormat | いいえ | 出力要求の形式を指定します。 可能な値: `Body` (既定)、`Form`、`Header`、または `QueryString`。 `Body` の値は、要求本文で、JSON 形式で送信される出力要求です。 `Form` の値は、要求本文で、キーの値をアンパサンド ' &' で区切った形式で送信される出力要求です。 `Header` の値は、要求本文で送信される出力要求です。 `QueryString` の値は、要求クエリ文字列で送信される出力要求です。 |
| ClaimUsedForRequestPayload| いいえ | REST API に送信されるペイロードを含む文字列要求の名前。 |
| DebugMode | いいえ | 技術プロファイルをデバッグ モードで実行します。 指定できる値: `true` または `false` (既定値)。 デバッグ モードでは、REST API はより多くの情報を返すことができます。 [返却エラー メッセージ](#returning-error-message)のセクションを参照してください。 |
| IncludeClaimResolvingInClaimsHandling  | いいえ | 入力要求と出力要求の場合、[要求の解決](claim-resolver-overview.md)が技術プロファイルに含まれるかどうかを指定します。 指定できる値: `true` または `false` (既定値)。 技術プロファイルで要求リゾルバーを使用する場合は、これを `true` に設定します。 |

## <a name="cryptographic-keys"></a>暗号化キー

認証の種類が `None` に設定されている場合、**CryptographicKeys** 要素は使用されません。

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

認証の種類が `Basic` に設定されている場合、**CryptographicKeys** には次の属性が存在します。

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | はい | 認証に使用されるユーザー名。 |
| BasicAuthenticationPassword | はい | 認証に使用されるパスワード。 |

次の例は、基本的な認証を用いた技術プロファイルを示しています。

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

認証の種類が `ClientCertificate` に設定されている場合、**CryptographicKeys** には次の属性が存在します。

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| ClientCertificate | はい | 認証に使用する X509 証明書 (RSA キー セット)。 |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

認証の種類が `Bearer` に設定されている場合、**CryptographicKeys** には次の属性が存在します。

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | いいえ | OAuth 2.0 ベアラー トークン。 |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>返却エラー メッセージ

REST API は、「そのユーザーは CRM システムでは見つかりませんでした」などの、エラー メッセージを返す必要がある場合があります。 エラーが発生した場合、REST API は次の属性を持つ HTTP 409 エラー メッセージ (応答ステータスコードの競合) を返すことになります。

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| version | はい | 1.0.0 |
| status | はい | 409 |
| code | いいえ | `DebugMode` が有効な場合に表示される、RESTful エンドポイント プロバイダーからのエラー コード。 |
| requestId | いいえ | `DebugMode` が有効な場合に表示される、RESTful エンドポイント プロバイダーからの要求識別子。 |
| userMessage | はい | ユーザーに示されるエラー メッセージ。 |
| developerMessage | いいえ | `DebugMode` が有効な場合に表示される、問題の詳細な説明とそれを修正する方法。 |
| moreInfo | いいえ | `DebugMode` が有効な場合に表示される、追加情報をポイントする URI。 |

次の例は、JSON で書式設定されたエラー メッセージを返す REST API を示しています。

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

次の例は、エラー メッセージを返す C# クラスを示しています。

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>次のステップ

RESTful 技術プロファイルの使用例については、次の記事を参照してください。

- [ユーザー入力の検証として REST API 要求交換を Azure AD B2C ユーザー体験に統合する](rest-api-claims-exchange-dotnet.md)
- [HTTP 基本認証を使用して RESTful サービスを保護する](secure-rest-api-dotnet-basic-auth.md)
- [クライアント証明書を使用して RESTful サービスを保護する](secure-rest-api-dotnet-certificate-auth.md)
- [チュートリアル:REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合する](custom-policy-rest-api-claims-validation.md)」をご覧ください
