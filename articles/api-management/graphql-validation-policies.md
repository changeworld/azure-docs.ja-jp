---
title: GraphQL 要求のための Azure API Management 検証ポリシー | Microsoft Docs
description: Azure API Management で GraphQL 要求を検証および承認するために使用できる新しいポリシーについて説明します。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 10/21/2021
ms.author: danlep
ms.custom: ignite-fall-2021
ms.openlocfilehash: 02225e0a07ef6567b8b190d9d31a957a69adbc4f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091726"
---
# <a name="api-management-policy-to-validate-and-authorize-graphql-requests-preview"></a>GraphQL 要求を検証および承認するための API Management ポリシー (プレビュー)

この記事では、API Management にインポートされた [GraphQL API](graphql-api.md) への要求を検証して承認する新しい API Management ポリシーのリファレンスを示します。

ポリシーを追加および構成する方法については、[Azure API Management のポリシー](./api-management-policies.md)に関するページを参照してください。

## <a name="validation-policy"></a>検証ポリシー

| ポリシー | [説明] |
| ------ | ----------- |
| [GraphQL 要求の検証](#validate-graphql-request) | GraphQL API への要求を検証して承認します。 |


## <a name="validate-graphql-request"></a>GraphQL 要求の検証

`validate-graphql-request` ポリシーでは、GraphQL 要求を検証し、特定のクエリ パスへのアクセスを承認します。 無効なクエリは "要求エラー" です。 承認は、有効な要求に対してのみ実行されます。 

**アクセス許可**  
GraphQL クエリはフラット化スキーマを使用するため、次のようになります。
* アクセス許可は、以下の出力の種類の、任意のリーフ ノードで適用できます。 
    * mutation、query、または subscription
    * 型宣言内の個々のフィールド。 
* アクセス許可は、以下には適用されません。 
    * 入力の種類
    * fragments
    * Unions
    * インターフェイス
    * スキーマ要素   

**承認の要素**  
複数の承認要素を使用できます。 最も具体的なパスが、クエリ内の各リーフ ノードに適切な承認規則を選ぶために使用されます。 
* 各承認では、必要に応じて別のアクションを指定できます。
* `if` 句を使用すると、管理者は条件付きアクションを指定できます。 

**イントロスペクション システム**   
path=`/__*` のポリシーは、[イントロスペクション](https://graphql.org/learn/introspection/)システムです。 これを使用して、イントロスペクション要求 (`__schema`、`__type` など) を拒否できます。   

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<validate-graphql-request error-variable-name="variable name" max-size="size in bytes" max-depth="query depth">
    <authorize-path="query path, for example: /Query/list Users or /__*" action="allow|remove|reject" />
        <if condition="policy expression" action="allow|remove|reject" />
</validate-graphql-request>
```

### <a name="example"></a>例

次の例では、GraphQL クエリを検証して拒否します。
* 100 kb を超える要求、またはクエリの深さが 4 を超える要求。 
* イントロスペクション システムおよび `list Users` クエリへのアクセス。 

```xml
<validate-graphql-request error-variable-name="name" max-size="102400" max-depth="4"> 
    <authorize path="/" action="allow" /> 
    <authorize path="/__*" action="reject" /> 
    <authorize path="/Query/list Users" action="reject" /> 
</validate-graphql-request> 
```

### <a name="elements"></a>要素

| 名前         | 説明                                                                                                                                   | 必須 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-graphql-request` | ルート要素。                                                                                                                               | はい      |
| `authorize` | これらの要素を 1 つ以上追加して、要求レベルとフィールドレベルのエラーの両方を含むフィールドレベルの承認を提供します。   | はい |
| `if` | フィールドレベルの承認へのアクションに対する条件付きの変更のために、これらの要素を 1 つ以上追加します。 | いいえ |

### <a name="attributes"></a>属性

| 名前                       | 説明                                                                                                                                                            | 必須 | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `error-variable-name` | 検証エラーをログに記録する `context.Variables` の変数の名前。  |   いいえ    | 該当なし   |
| `max-size` | 要求ペイロードの最大サイズ (バイト単位)。 許容される最大値: 102,400 バイト (100 KB)。 (この制限を引き上げる必要がある場合は、[サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。) | はい       | 該当なし   |
| `max-depth` | 整数。 クエリの深さの最大値。 | いいえ | 6 |
| `path` | 承認検証を実行するためのクエリ パス。 | はい | 該当なし |
| `action` | 一致するフィールドに対して実行する[アクション](#request-actions)。 一致条件が指定されている場合は、変更できます。 |  はい     | 該当なし   |
| `condition` | [ポリシー式](api-management-policy-expressions.md)が一致するかどうかを判別するブール値。 最初の一致条件が使用されます。 | いいえ | 該当なし |

### <a name="request-actions"></a>要求アクション

利用可能なアクションを次の表で説明します。

|アクション |説明  |
|---------|---------|
|`reject`     | 要求エラーが発生し、要求はバックエンドに送信されません。     |
|`remove`     | フィールドエラーが発生し、フィールドが要求から削除されます。         |
|`allow`     | フィールドがバックエンドに渡されます。        |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

## <a name="error-handling"></a>エラー処理

GraphQL スキーマに対する検証に失敗した場合、または要求のサイズまたは深さのエラーが発生した場合は、要求エラーが発生し、エラーブロック (データブロックなし) で要求が失敗します。 

[`Context.LastError`](api-management-error-handling-policies.md#lasterror) プロパティと同様に、すべての GraphQL 検証エラーは自動的に `GraphQLErrors` 変数に反映されます。 エラーを個別に反映する必要がある場合は、エラー変数名を指定できます。 `error` 変数と `GraphQLErrors` 変数にエラーがプッシュされます。 

## <a name="next-steps"></a>次のステップ

ポリシーに対する処理の詳細については、次のトピックを参照してください。

-   [API Management のポリシー](api-management-howto-policies.md)
-   [API を変換する](transform-api.md)
-   ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
-   [ポリシーのサンプル](./policy-reference.md)
-   [エラー処理](./api-management-error-handling-policies.md)
