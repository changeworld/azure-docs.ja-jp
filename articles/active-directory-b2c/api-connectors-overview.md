---
title: Azure AD B2C の API コネクタについて
description: Azure Active Directory (Azure AD) API コネクタを使用して、REST API または外部 ID データ ソースへの送信 Webhook を使用してユーザー フローをカスタマイズおよび拡張します。
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 11/02/2021
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9b5d3c49019d1953dd0deb5d498291d92af4aba1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436937"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows-with-external-identity-data-sources"></a>API コネクタを使用して、外部 ID データ ソースでサインアップ ユーザー フローをカスタマイズおよび拡張する 

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

## <a name="overview"></a>概要 

開発者または IT 管理者は、API コネクタを使用し、サインアップ ユーザー フローと REST API を統合してサインアップ体験をカスタマイズしたり、外部システムと統合したりできます。 たとえば、API コネクタを使用すると、次のことができます。

- **ユーザー入力データの検証**。 不正な、または無効なユーザー データに対する検証を行います。 たとえば、ユーザーが提供したデータを外部データ ストア内の既存のデータまたは許可されている値の一覧と照らし合わせて検証することができます。 無効な場合は、有効なデータを提供するようユーザーに求めることも、ユーザーがサインアップ フローを続行できないようにすることもできます。
- **ユーザー ID を確認します**。 本人確認サービスまたは外部 ID データ ソースを使用して、アカウント作成の決定のセキュリティ レベルを向上させます。
- **カスタム承認ワークフローと統合します**。 アカウントの作成を管理したり、制限したりするには、カスタム承認システムに接続します。
- **外部ソースの属性を使用してトークンを拡張します**。 Azure AD B2C の外部にある、クラウド システム、カスタム ユーザー ストア、カスタム アクセス許可システム、レガシ ID サービスなどを始めとしたソースのユーザーに関する属性を使用して、トークンを強化します。
- **ユーザー属性を上書する** ユーザーから収集された属性を再フォーマットし、それに値を割り当てます。 たとえば、ユーザーが名をすべて小文字または大文字で入力する場合に、名の最初の文字だけを大文字にするように書式設定できます。 
- **カスタム ビジネス ロジックを実行します**。 ご利用のクラウド システム内で下流イベントをトリガーすれば、プッシュ通知の送信、企業データベースの更新、アクセス許可の管理、データベースの監査、およびその他のカスタム アクションの実行を行うことができます。

API コネクタは、API 呼び出しに関する HTTP エンドポイントの URL と認証を定義することで、API エンドポイントを呼び出すために必要な情報を Azure AD B2C に提供します。 API コネクタを構成したら、それをユーザーフローの特定のステップに対して有効にすることができます。 サインアップ フローでユーザーがこのステップに達すると、API コネクタが呼び出され、HTTP POST 要求として具体化されて、ユーザー情報 ("クレーム") を JSON 本文のキーと値のペアとして送信します。 API 応答は、ユーザー フローの実行に影響を与える可能性があります。 たとえば、API 応答によって、ユーザーのサインアップがブロックされたり、ユーザーが情報の再入力を求められたり、ユーザー属性が上書きされたりする可能性があります。

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>ユーザー フロー内で API コネクタを有効にできる場所

ユーザー フロー内には、API コネクタを有効にできる場所が 3 か所あります。

- **サインアップ時に ID プロバイダーとのフェデレーションを行った後** - 当てはまるのはサインアップ エクスペリエンスのみです
- **ユーザーを作成する前** - 当てはまるのはサインアップ エクスペリエンスのみです
- **トークンを送信する前 (プレビュー)** - サインアップとサインインに当てはまります

### <a name="after-federating-with-an-identity-provider-during-sign-up"></a>サインアップ時に ID プロバイダーとのフェデレーションを行った後

サインアップ プロセスのこのステップでの API コネクタは、ID プロバイダー (Google、Facebook、Azure AD など) でユーザーが認証された直後に呼び出されます。 このステップは、***属性コレクション ページ*** (ユーザーに提示される、ユーザー属性を収集するためのフォーム) の前にあります。 ユーザーがローカル アカウントを使用して登録している場合、このステップは呼び出されません。 このステップでお客様が有効する可能性がある API コネクタのシナリオの例を次に示します。

- ユーザーが入力した電子メールまたはフェデレーション ID を使用して、既存のシステム内でクレームを検索します。 既存のシステムからこれらのクレームを返し、属性コレクション ページを事前に入力して、それらをトークンで返すことができるようにします。
- ソーシャル ID に基づいて許可または禁止リストを実装します。

### <a name="before-creating-the-user"></a>ユーザーを作成する前

サインアップ プロセスのこのステップでの API コネクタは、属性コレクション ページが含まれている場合、その後に呼び出されます。 このステップは、ユーザー アカウントが作成される前に必ず呼び出されます。 お客様がサインアップ中にこのポイントで有効にする可能性があるシナリオの例を次に示します。

- ユーザー入力データを検証し、データの再送信をユーザーに求めます。
- ユーザーが入力したデータに基づいてユーザーのサインアップをブロックします。
- ユーザー ID を確認します。
- 外部システムに対して、ユーザーに関する既存のデータをクエリして、それをアプリケーション トークンで返すか、Azure AD に格納します。

### <a name="before-sending-the-token-preview"></a>トークンを送信する前 (プレビュー)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

サインアップまたはサインイン プロセスの、このステップでの API コネクタは、トークンが発行される前に呼び出されます。 以下に、このステップで有効する可能性があるシナリオの例を示します。
- レガシ ID システム、人事システム、外部ユーザー ストアなど、ディレクトリとは異なるソースからのユーザーに関する属性を使用してトークンを強化します。
- 独自のアクセス許可システムに格納して管理するグループまたはロールの属性を使用してトークンを強化します。 
- ディレクトリ内の要求の値に、要求の変換または操作を適用します。

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) の基盤となる Identity Experience Framework は、ユーザー体験における RESTful API と統合することができます。 この記事では、[RESTful 技術プロファイル](restful-technical-profile.md) を使用して RESTful サービスと対話するユーザー体験を作成する方法について説明します。

Azure AD B2C を使用すると、自分の RESTful サービスを呼び出すことで、独自のビジネス ロジックをユーザー体験に追加できます。 Identity Experience Framework は、RESTful サービスからデータを送受信して、要求を交換できます。 たとえば、次のように操作できます。

- **外部 ID データ ソースを使用して、ユーザー入力データを検証します**。 たとえば、ユーザーによって入力されたメール アドレスが顧客データベースに存在するかどうかを確認できます。存在しない場合、エラーが表示されます。 API コネクタは、サインアップなどのイベントが発生したときに呼び出しが行われるため、送信 Webhook をサポートする手段としても考えることができます。
- **要求を処理します**。 ユーザーが名をすべて小文字または大文字で入力する場合に、お使いの REST API は名の最初の文字だけを大文字にするように書式設定でき、それを Azure AD B2C に返すことができます。 ただし、カスタム ポリシーを使用する場合は、RESTful API の呼び出しよりも [ClaimsTransformations](claimstransformations.md) が優先されます。 
- **企業の基幹業務アプリケーションとさらに緊密に統合することでユーザー データを動的に拡充します**。 RESTful サービスでは、ユーザーのメール アドレスを受け取り、顧客のデータベースを照会し、ユーザーのロイヤルティ番号を Azure AD B2C に返すことができます。 返された要求は、ユーザーの Azure AD アカウントに保存するか、次のオーケストレーション手順で評価するか、アクセス トークンに含めることができます。
- **カスタム ビジネス ロジックを実行します**。 プッシュ通知の送信、企業データベースの更新、ユーザーの移行プロセスの実行、アクセス許可の管理、データベースの監査、およびその他のワークフローを実行できます。

![RESTful サービスの要求交換の図](media/api-connectors-overview/restful-service-claims-exchange.png)

> [!NOTE]
> RESTful サービスから Azure AD B2C への応答が遅い場合または応答がない場合、タイムアウトは 30 秒であり、再試行回数は 2 回です (つまり、合計 3 回試行されます)。 タイムアウトと再試行回数の設定は現在構成できません。

## <a name="calling-a-restful-service"></a>RESTful サービスの呼び出し

対話には REST API 要求と Azure AD B2C の間の情報の要求の交換が含まれています。 次の方法で、RESTful サービスとの統合を設計できます。

- **検証技術プロファイル**。 RESTful サービスへの呼び出しは、指定された [セルフアサート技術プロファイル](self-asserted-technical-profile.md) の [検証技術プロファイル](validation-technical-profile.md) 内、または [表示コントロール](display-controls.md) の [確認表示コントロール](display-control-verification.md) 内で行われます。 検証技術プロファイルは、ユーザー体験を進める前に、ユーザーが入力したデータを検証します。 検証技術プロファイルでは、次を行うことができます。

  - REST API に要求を送信します。
  - 要求を検証し、ユーザーに表示されるカスタム エラー メッセージをスローします。
  - REST API から次のオーケストレーション手順に要求を送り返します。

- **要求の交換**。 [ユーザー体験](userjourneys.md)のオーケストレーションの手順から REST API 技術プロファイルを直接呼び出して、ダイレクトな要求交換を構成することができます。 この定義は次に制限されます。

  - REST API に要求を送信します。
  - 要求を検証し、アプリケーションに返されるカスタム エラー メッセージをスローします。
  - REST API から次のオーケストレーション手順に要求を送り返します。

カスタム ポリシーによって定義されているユーザー体験の任意のステップで、REST API 呼び出しを追加できます。 たとえば、次のタイミングで REST API を呼び出すことができます。

- サインイン時の、Azure AD B2C によって資格情報が検証される直前。
- サインインの直後。
- Azure AD B2C によってディレクトリに新しいアカウントが作成される前。
- Azure AD B2C によってディレクトリに新しいアカウントが作成された後。
- Azure AD B2C によってアクセス トークンが発行される前。

![検証技術プロファイル コレクション](media/api-connectors-overview/validation-technical-profile.png)

## <a name="sending-data"></a>データの送信

[RESTful 技術プロファイル](restful-technical-profile.md) では、`InputClaims` 要素には、RESTful サービスに送信する要求のリストが含まれています。 要求の名前を、RESTful サービスで定義されている名前にマップし、既定値を設定して、[要求リゾルバー](claim-resolver-overview.md) を使用できます。

SendClaimsIn 属性を使用して、RESTful 要求プロバイダーに入力要求を送信する方法を構成できます。 指定できる値は、

- **本文**、JSON 形式で HTTP POST 要求本文で送信されます。
- **書式**、 キーの値をアンパサンド「 &」で区切った形式で HTTP POST 要求本文で送信されます。
- **ヘッダー**、HTTP GET 要求ヘッダーで送信されます。
- **QueryString**、HTTP GET 要求クエリ文字列で送信されます。

**本文** のオプションが構成されると、REST API 技術プロファイルで複雑な JSON ペイロードをエンドポイントに送信できます。 詳細については、[JSON ペイロードの送信](restful-technical-profile.md#send-a-json-payload) を参照してください。

## <a name="receiving-data"></a>データの受信

[RESTful 技術プロファイル](restful-technical-profile.md) の `OutputClaims` 要素には、REST API によって返された要求のリストが含まれています。 お使いのポリシーで定義される要求の名前を、REST API で定義される名前にマップする必要があるかもしれません。 また、DefaultValue 属性を設定している限り、REST API ID プロバイダーにより返されない要求を追加することもできます。

RESTful 要求プロバイダーが解析する出力要求は、次のような、常にフラットな JSON 本文の応答を解析することを想定しています。

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

出力要求は次の xml スニペットのようになります。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

### <a name="handling-null-values"></a>null 値の処理 

列の値が不明であるか存在しないときには、データベース内の null 値が使用されます。  `null` 値を持つ JSON キーは含めないでください。 次の例では、メールから `null` 値が返されます。

```json
{
  "name": "Emily Smith",
  "email": null,
  "loyaltyNumber":  1234
}
```

要素が null のときには次のいずれかになります。

- JSON からキーと値のペアが省略されます。
- Azure AD B2C の要求データ型に対応する値が返されます。 たとえば、`string` データ型の場合、空の文字列 `""` が返されます。 `integer` データ型の場合、0 値の `0` が返されます。 `dateTime` データ型の場合、最小値の `1970-00-00T00:00:00.0000000Z` が返されます。

次の例は、null 値の処理方法を示しています。 メールは JSON から省略されています。

```json
{
  "name": "Emily Smith",
  "loyaltyNumber":  1234
}
```

### <a name="parse-a-nested-json-body"></a>入れ子になった JSON 本文を解析する

入れ子になった JSON 本文の応答を解析するには、ResolveJsonPathsInJsonTokens メタデータを true に設定します。 出力要求で、PartnerClaimType を出力する JSON パス要素に設定します。

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


出力要求は次の xml スニペットのようになります。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="localize-the-rest-api"></a>REST API をローカライズする

RESTful 技術プロファイルでは、現在のセッションの言語/ロケールを送信し、必要に応じて、ローカライズされたエラー メッセージを生成することができます。 [要求リゾルバー](claim-resolver-overview.md)を使用すると、ユーザー言語などのコンテキスト要求を送信できます。 このシナリオを示す RESTful 技術プロファイルの例を次に示します。

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>エラー メッセージの処理

REST API は、「そのユーザーは CRM システムでは見つかりませんでした」などの、エラー メッセージを返す必要がある場合があります。 エラーが発生した場合、REST API は HTTP 409 エラー メッセージ (応答状態コードの競合) を返すことになります。 詳細については、「[RESTful 技術プロファイル](restful-technical-profile.md#returning-validation-error-message)」を参照してください。

この動作は、検証技術プロファイルから REST API 技術プロファイルを呼び出すことによってのみ実現できます。 ユーザーはページのデータを修正して、ページの送信時に検証を再度実行します。

ユーザー体験から REST API の技術プロファイルを直接参照した場合、ユーザーは関連するエラー メッセージと共に証明書利用者アプリケーションにリダイレクトされます。

::: zone-end

## <a name="development-of-your-rest-api"></a>REST API の開発 

REST API は、セキュリティで保護され、要求を JSON 形式で送受信できる限り、どのプラットフォームでも開発でき、任意のプログラミング言語で記述できます。

REST API サービスへの要求は Azure AD B2C サーバーから作成されます。 REST API サービスは、パブリックにアクセスできる HTTPS エンドポイントに公開する必要があります。 REST API の呼び出しは、Azure データ センターの IP アドレスから受け取ります。

開発が容易になるよう、[Azure Functions の HTTP トリガー](../azure-functions/functions-bindings-http-webhook-trigger.md)のようなサーバーレス クラウド関数を使用できます。

REST API サービスとその基になるコンポーネント (データベースやファイル システムなど) は、高可用性を備えるように設計する必要があります。

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="next-steps"></a>次のステップ

::: zone pivot="b2c-user-flow"

- [API コネクタを追加してサインアップ エクスペリエンスを変更する](add-api-connector.md)方法を確認します
- [API コネクタを追加し、外部要求によってトークンを強化する](add-api-connector-token-enrichment.md)方法を確認します
- [API コネクタをセキュリティで保護する](secure-rest-api.md)方法を確認します
- [サンプル](api-connector-samples.md#api-connector-rest-api-samples)を使用して作業を開始します

::: zone-end

::: zone pivot="b2c-custom-policy"

RESTful 技術プロファイルの使用例については、次の記事を参照してください。

- [チュートリアル: API コネクタをサインアップ ユーザー フローに追加する](add-api-connector.md)
- [チュートリアル:Azure Active Directory B2C で REST API 要求の交換をカスタム ポリシーに追加する](add-api-connector-token-enrichment.md)
- [REST API サービスをセキュリティで保護する](secure-rest-api.md)
- [リファレンス: RESTful 技術プロファイル](restful-technical-profile.md)

::: zone-end
