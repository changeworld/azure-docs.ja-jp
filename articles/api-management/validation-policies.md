---
title: Azure API Management の検証ポリシー | Microsoft Docs
description: Azure API Management で、要求と応答を検証するために使用できるポリシーについて説明します。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801895"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>要求と応答を検証するための API Management ポリシー

この記事では、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](./api-management-policies.md)」をご覧ください。

検証ポリシーを使用して、OpenAPI スキーマに対する API 要求と応答を検証し、ヘッダーやペイロードのインジェクションなどの脆弱性から保護します。 検証ポリシーは、Web Application Firewall に代わるものではありませんが、静的な定義済みの規則に依存するセキュリティ製品の対象とならない追加の脅威に対応する柔軟性を備えています。

## <a name="validation-policies"></a>検証ポリシー

- [コンテンツを検証する](#validate-content) - API スキーマに対して要求または応答の本文のサイズまたは JSON スキーマを検証します。 
- [パラメーターを検証する](#validate-parameters) - API スキーマに対して要求ヘッダー、クエリ、またはパス パラメーターを検証します。
- [ヘッダーを検証する](#validate-headers) - API スキーマに対して応答ヘッダーを検証します。
- [状態コードを検証する](#validate-status-code) - API スキーマに対して応答の HTTP 状態コードを検証します。

> [!NOTE]
> 検証ポリシーで使用できる API スキーマの最大サイズは 4 MB です。 スキーマがこの制限を超えた場合、検証ポリシーは実行時にエラーを返します。 これを増やすには、[サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 

## <a name="actions"></a>Actions

各検証ポリシーには、API 要求または応答内のエンティティを API スキーマに対して検証するときに API Management によって行われるアクションを指定する属性が含まれています。 アクションは、API スキーマで表されている要素に対して指定できます。ポリシーによっては、API スキーマで表されていない要素に対しても指定できます。 ポリシーの子要素に指定されたアクションは、その親に対して指定されたアクションをオーバーライドします。

使用可能なアクション:

| アクション         | 説明          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignore | 検証をスキップします。 |
| 回避 (prevent) | 要求または応答の処理をブロックし、詳細な検証エラーをログに記録して、エラーを返します。 最初のエラー セットが検出されると、処理が中断されます。 |
| 検出 (detect) | 要求または応答の処理を中断することなく、検証エラーをログに記録します。 |

## <a name="logs"></a>ログ

ポリシー実行中の検証エラーの詳細は、 ポリシーのルート要素の `errors-variable-name` 属性で指定されている `context.Variables` の変数に記録されます。 `prevent` アクションで構成されている場合、検証エラーによってその後の要求または応答の処理がブロックされ、検証エラーは `context.LastError` プロパティにも反映されます。 

エラーを調査するには、 [トレース](api-management-advanced-policies.md#Trace) ポリシーを使用して、コンテキスト変数から [Application Insights](api-management-howto-app-insights.md) にエラーを記録します。

## <a name="performance-implications"></a>パフォーマンスへの影響

検証ポリシーを追加すると、API のスループットに影響を与える可能性があります。 一般論としては、次のような原則があります。
* API スキーマのサイズが大きいほど、スループットが低下します。 
* 要求または応答のペイロードが大きいほど、スループットが低下します。 
* API スキーマのサイズは、ペイロードのサイズよりもパフォーマンスに大きな影響を与えます。 
* 数メガバイトのサイズの API スキーマに対して検証を行うと、一部の条件下で要求または応答のタイムアウトが発生する可能性があります。 影響は、サービスの **従量課金** レベルと **開発者** レベルでより顕著になります。 

API スループットに対する検証ポリシーの影響を評価するには、想定される運用ワークロードでロード テストを実行することをお勧めします。

## <a name="validate-content"></a>コンテンツの検証

`validate-content` ポリシーは、API スキーマに対して要求または応答の本文のサイズまたは JSON スキーマを検証します。 JSON 以外の形式はサポートされていません。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>例

次の例では、要求と応答の JSON ペイロードが検出モードで検証されます。 ペイロードが 100 KB を超えるメッセージはブロックされます。 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>要素

| 名前         | 説明                                                                                                                                   | 必須 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-content | ルート要素。                                                                                                                               | はい      |
| コンテンツ | これらの要素を 1 つ以上追加して、要求または応答のコンテンツ タイプを検証し、指定されたアクションを実行します。  | いいえ |

### <a name="attributes"></a>属性

| 名前                       | 説明                                                                                                                                                            | 必須 | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| unspecified-content-type-action | API スキーマで指定されていないコンテンツ タイプの要求または応答に対して実行する[アクション](#actions)。 |  はい     | なし   |
| max-size | `Content-Length` ヘッダーに対してチェックされる、要求または応答の本文の最大長 (バイト単位)。 要求本文または応答本文が圧縮されている場合、この値は圧縮解除された長さになります。 許容される最大値: 102,400 バイト (100 KB)。  | はい       | なし   |
| size-exceeded-action | 本文が `max-size` で指定されたサイズを超えている要求または応答に対して実行する[アクション](#actions)。 |  はい     | なし   |
| errors-variable-name | 検証エラーをログに記録する `context.Variables` の変数の名前。  |   はい    | なし   |
| type | `Content-Type` ヘッダーに対してチェックされる、本文の検証を実行するコンテンツ　タイプ。 この値は大文字と小文字が区別されません。 空の場合は、API スキーマで指定されているすべてのコンテンツ タイプに適用されます。 |   いいえ    |  なし  |
| validate-as | コンテンツ タイプが一致する要求または応答の本文の検証に使用する検証エンジン。 現在唯一サポートされている値は "json" です。   |  はい     |  なし  |
| action | 指定されたコンテンツ タイプに一致しない本文を持つ要求または応答に対して実行する[アクション](#actions)。  |  はい      | なし   |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="validate-parameters"></a>パラメーターを検証する:

`validate-parameters` ポリシーは、API スキーマに対して要求のヘッダー、クエリ、またはパス パラメーターを検証します。

> [!IMPORTANT]
> `2021-01-01-preview` より前のバージョンの管理 API を使用して API をインポートした場合、`validate-parameters` ポリシーが機能しない可能性があります。 管理 API バージョン `2021-01-01-preview` 以降を使用して、[API を再インポート](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate)することが必要になる場合があります。


### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>例

この例では、すべてのクエリとパスのパラメーターが防止モードで検証され、ヘッダーが検出モードで検証されます。 いくつかのヘッダー パラメーターについては、検証がオーバーライドされます。

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>要素

| 名前         | 説明                                                                                                                                   | 必須 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-parameters | ルート要素。 要求のすべてのパラメーターに対する既定の検証アクションを指定します。                                                                                                                              | はい      |
| headers | 要求のヘッダー パラメーターの既定の検証アクションをオーバーライドするには、この要素を追加します。   | いいえ |
| query | 要求のクエリ パラメーターの既定の検証アクションをオーバーライドするには、この要素を追加します。  | いいえ |
| path | 要求の URL パス パラメーターの既定の検証アクションをオーバーライドするには、この要素を追加します。  | いいえ |
| パラメーター (parameter) | 名前付きパラメーターに 1 つ以上の要素を追加して、検証アクションの上位レベルの構成をオーバーライドします。 | いいえ |

### <a name="attributes"></a>属性

| 名前                       | 説明                                                                                                                                                            | 必須 | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| specified-parameter-action | API スキーマで指定された要求パラメーターに対して実行する[アクション](#actions)。 <br/><br/> `headers`、`query`、または `path` 要素で指定された場合、 値は `validate-parameters` 要素内の `specified-parameter-action` の値をオーバーライドします。  |  はい     | なし   |
| unspecified-parameter-action | API スキーマで指定されていない要求パラメーターに対して実行する[アクション](#actions)。 <br/><br/>`headers` または `query` 要素で指定された場合、 値は `validate-parameters` 要素内の `unspecified-parameter-action` の値をオーバーライドします。 |  はい     | なし   |
| errors-variable-name | 検証エラーをログに記録する `context.Variables` の変数の名前。  |   はい    | なし   |
| name | 検証アクションをオーバーライドするパラメーターの名前。 この値は大文字と小文字が区別されません。  | はい | なし |
| action | 一致する名前を持つパラメーターに対して実行する[アクション](#actions)。 パラメーターが API スキーマで指定されている場合、この値は上位レベルの `specified-parameter-action` 構成をオーバーライドします。 パラメーターが API スキーマで指定されていない場合、この値は上位レベルの `unspecified-parameter-action` 構成をオーバーライドします。| はい | なし | 

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

## <a name="validate-headers"></a>ヘッダーを検証する

`validate-headers` ポリシーは、API スキーマに対して応答ヘッダーを検証します。

> [!IMPORTANT]
> `2021-01-01-preview` より前のバージョンの管理 API を使用して API をインポートした場合、`validate-headers` ポリシーが機能しない可能性があります。 管理 API バージョン `2021-01-01-preview` 以降を使用して、API を再インポートすることが必要になる場合があります。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>例

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>要素

| 名前         | 説明                                                                                                                                   | 必須 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-headers | ルート要素。 応答のすべてのヘッダーに対する既定の検証アクションを指定します。                                                                                                                              | はい      |
| header | 名前付きヘッダーに 1 つ以上の要素を追加して、応答のヘッダーに対する既定の検証アクションをオーバーライドします。 | いいえ |

### <a name="attributes"></a>属性

| 名前                       | 説明                                                                                                                                                            | 必須 | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| specified-header-action | API スキーマで指定された応答ヘッダーに対して実行する[アクション](#actions)。  |  はい     | なし   |
| unspecified-header-action | API スキーマで指定されていない応答ヘッダーに対して実行する[アクション](#actions)。  |  はい     | なし   |
| errors-variable-name | 検証エラーをログに記録する `context.Variables` の変数の名前。  |   はい    | なし   |
| name | 検証アクションをオーバーライドするヘッダーの名前。 この値は大文字と小文字が区別されません。 | はい | なし |
| action | 一致する名前を持つヘッダーに対して実行する[アクション](#actions)。 API スキーマでヘッダーが指定されている場合、この値は `validate-headers` 要素内の `specified-header-action` の値をオーバーライドします。 それ以外の場合は、validate-headers 要素内の `unspecified-header-action` の値をオーバーライドします。 | はい | なし | 

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** outbound、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="validate-status-code"></a>状態コードを検証する

`validate-status-code` ポリシーは、API スキーマに対して応答の HTTP 状態コードを検証します。 このポリシーは、スタック トレースを含む可能性があるバックエンド エラーの漏えいを防ぐために使用できます。 

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>例

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>要素

| 名前         | 説明                                                                                                                                   | 必須 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-status-code | ルート要素。                                                                                                | はい      |
| status-code | HTTP 状態コードに 1 つ以上の要素を追加して、応答の状態コードに対する既定の検証アクションをオーバーライドします。 | いいえ |

### <a name="attributes"></a>属性

| 名前                       | 説明                                                                                                                                                            | 必須 | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| unspecified-status-code-action | API スキーマで指定されていない応答の HTTP 状態コードに対して実行する[アクション](#actions)。  |  はい     | なし   |
| errors-variable-name | 検証エラーをログに記録する `context.Variables` の変数の名前。  |   はい    | なし   |
| code | 検証アクションをオーバーライドする HTTP 状態コード。 | はい | なし |
| action | API スキーマで指定されていない、一致する状態コードに対して実行する[アクション](#actions)。 状態コードが API スキーマで指定されている場合、このオーバーライドは有効になりません。 | はい | なし | 

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** outbound、on-error

-   **ポリシー スコープ:** すべてのスコープ


## <a name="validation-errors"></a>検証エラー
次の表に、検証ポリシーで発生する可能性があるすべてのエラーを示します。 

* **詳細** - エラーを調査するために使用できます。 一般に共有するためのものではありません。
* **パブリック応答** - クライアントに返されるエラー。 実装の詳細は漏えいしません。

| **名前**                             | **Type**                                                        | **検証規則** | **詳細**                                                                                                                                       | **パブリック応答**                                                                                                                       | **操作**           |
|----|----|---|---|---|----|
| **validate-content** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | 要求の本文の長さは {size} バイトで、構成されている制限の {maxSize} バイトを超えています。                                                       | 要求の本文の長さは {size} バイトで、制限の {maxSize} バイトを超えています。                                                          | 検出 (detect) /回避 (prevent) |
||ResponseBody                                                    | SizeLimit           | 応答の本文の長さは {size} バイトで、構成されている制限の {maxSize} バイトを超えています。                                                      | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {messageContentType}                 | RequestBody                                                     | 指定されていません。         | 指定されていないコンテンツ タイプ {messageContentType} は許可されません。                                                                                     | 指定されていないコンテンツ タイプ {messageContentType} は許可されません。                                                                             | 検出 (detect) /回避 (prevent) |
| {messageContentType}                 | ResponseBody                                                    | 指定されていません。         | 指定されていないコンテンツ タイプ {messageContentType} は許可されません。                                                                                     | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| | ApiSchema                                                       |                     | API のスキーマが存在しないか、解決できませんでした。                                                                                          | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
|                                      | ApiSchema                                                       |                     | API のスキーマで定義が指定されていません。                                                                                                        | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {messageContentType}                 | RequestBody / ResponseBody                                      | MissingDefinition   | API のスキーマに、コンテンツ タイプ {messageContentType} に関連付けられている定義 {definitionName} が含まれていません。                        | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | 要求の本文が、コンテンツ タイプ {messageContentType} に関連付けられている定義 {definitionName} に準拠していません。<br/><br/>{valError.Message} 行: {valError.LineNumber}、位置: {valError.LinePosition}                  | 要求の本文が、コンテンツ タイプ {messageContentType} に関連付けられている定義 {definitionName} に準拠していません。<br/><br/>{valError.Message} 行: {valError.LineNumber}、位置: {valError.LinePosition}            | 検出 (detect) /回避 (prevent) |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | 応答の本文が、コンテンツ タイプ {messageContentType} に関連付けられている定義 {definitionName} に準拠していません。<br/><br/>{valError.Message} 行: {valError.LineNumber}、位置: {valError.LinePosition}                                       | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
|                                      | RequestBody                                                     | ValidationException | コンテンツ タイプ {messageContentType} に対して、要求の本文を検証できません。<br/><br/>{exception details}                                                                | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
|                                      | ResponseBody                                                    | ValidationException | コンテンツ タイプ {messageContentType} に対して、応答の本文を検証できません。<br/><br/>{exception details}                                                                | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| **validate-parameter / validate-headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName} / {headerName}           | QueryParameter / PathParameter / RequestHeader                  | 指定されていません。         | 指定されていない {path parameter / query parameter / header} {paramName} は許可されません。                                                               | 指定されていない {path parameter / query parameter / header} {paramName} は許可されません。                                                       | 検出 (detect) /回避 (prevent) |
| {headerName}                         | ResponseHeader                                                  | 指定されていません。         | 指定されていないヘッダー {headerName} は許可されません。                                                                                                   | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
|                                      |ApiSchema                                                       |                     | API のスキーマが存在しないか、解決できませんでした。                                                                                            | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
|                                       | ApiSchema                                                       |                     | API スキーマで定義が指定されていません。                                                                                                          | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | API のスキーマに、{query parameter / path parameter / header} {paramName} に関連付けられている定義 {definitionName} が含まれていません。  | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | 要求では、{query parameter / path parameter / header} {paramName} に対して複数の値を含めることはできません。                                           | 要求では、{query parameter / path parameter / header} {paramName} に対して複数の値を含めることはできません。                                   | 検出 (detect) /回避 (prevent) |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | 応答では、ヘッダー {headerName} に対して複数の値を含めることはできません。                                                                              | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | {query parameter / path parameter / header} {paramName} の値が定義に準拠していません。<br/><br/>{valError.Message} 行: {valError.LineNumber}、位置: {valError.LinePosition}                                          | {query parameter / path parameter / header} {paramName} の値が定義に準拠していません。<br/><br/>{valError.Message} 行: {valError.LineNumber}、位置: {valError.LinePosition}                              | 検出 (detect) /回避 (prevent) |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | ヘッダー {headerName} の値が定義に準拠していません。<br/><br/>{valError.Message} 行: {valError.LineNumber}、位置: {valError.LinePosition}                                                                              | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | {query parameter / path parameter / header} {paramName} の値を、定義に従って解析できません。 <br/><br/>{ex.Message}                                | {query parameter / path parameter / header} {paramName} の値を、定義に従って解析できませんでした。 <br/><br/>{ex.Message}                      | 検出 (detect) /回避 (prevent) |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | ヘッダー {headerName} の値を、定義に従って解析できませんでした。                                                                  | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {paramName}                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | {Query parameter / Path parameter / Header} {paramName} を検証できません。<br/><br/>{exception details}                                                                      | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| {headerName}                         | ResponseHeader                                                  | ValidationError     | ヘッダー {headerName} を検証できません。<br/><br/>{exception details}                                                                                                          | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |
| **validate-status-code**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-code}                        | StatusCode                                                      | 指定されていません。         | 応答状態コード {status-code} は許可されません。                                                                                                | 内部エラーにより、要求を処理できませんでした。 API の所有者に問い合わせてください。                                                       | 検出 (detect) /回避 (prevent) |


次の表に、検証エラーで考えられるすべての原因の値と、考えられるメッセージの値を示します。

|  **理由**         |    **メッセージ** |
|---|---|  
| 正しくない要求       |     {詳細} (コンテキスト変数用)、{パブリック応答} (クライアント用)|
| 応答が許可されない  | {詳細} (コンテキスト変数用)、{パブリック応答} (クライアント用) |






## <a name="next-steps"></a>次のステップ

ポリシーに対する処理の詳細については、次のトピックを参照してください。

-   [API Management のポリシー](api-management-howto-policies.md)
-   [API を変換する](transform-api.md)
-   ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
-   [ポリシーのサンプル](./policy-reference.md)
-   [エラー処理](./api-management-error-handling-policies.md)
