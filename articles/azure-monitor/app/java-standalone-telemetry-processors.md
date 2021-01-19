---
title: テレメトリ プロセッサ (プレビュー) - Azure Monitor Application Insights for Java
description: Azure Monitor Application Insights for Java でテレメトリ プロセッサを構成する方法
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133176"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>テレメトリ プロセッサ (プレビュー) - Azure Monitor Application Insights for Java

> [!NOTE]
> この機能はまだプレビュー段階です。

Application Insights 用の Java 3.0 エージェントには現在、データがエクスポートされる前に利用統計情報を処理する機能があります。

テレメトリ プロセッサのユース ケースを次に示します。
 * 機微なデータをマスクする
 * 条件付きでカスタム ディメンションを追加する
 * 集計や表示に使用されるテレメトリ名を更新する
 * インジェスト コストを制御するためにスパン属性を削除またはフィルター処理する

## <a name="terminology"></a>用語

テレメトリ プロセッサに取り掛かる前に、トレースとスパンについて理解することが重要です。

### <a name="traces"></a>トレース

トレースは、アプリケーションを構成するサービスによって処理される `trace` と呼ばれる単一の要求の進行状況を追跡します。 要求は、ユーザーまたはアプリケーションによって開始されることがあります。 `trace` 内の各作業単位は `span` と呼ばれます。`trace` は、スパンのツリーです。 `trace` は、1 つのルート スパンと、任意の数の子スパンで構成されます。

### <a name="span"></a>スパン

スパンは、要求がシステムを通過するときに、それに関連する個々のサービスまたはコンポーネントによって実行される作業を表すオブジェクトです。 `span` には `span context` が含まれます。これは、各スパンが属する一意の要求を表す一連のグローバル一意識別子です。 

スパンでは、以下がカプセル化されます。

* スパン名
* スパンを一意に識別する変更不可の `SpanContext`
* `Span`、`SpanContext`、または null の形式の親スパン
* `SpanKind`。
* 開始タイムスタンプ
* 終了タイムスタンプ
* [`Attributes`](#attributes)
* タイムスタンプ付きイベントの一覧
* `Status`。

一般に、スパンのライフサイクルは次のようになります。

* 要求がサービスによって受信されます。 スパン コンテキストが存在する場合は、要求ヘッダーから抽出されます。
* 新しいスパンが、抽出されたスパン コンテキストの子として作成されます。存在しない場合は、新しいルート スパンが作成されます。
* サービスが要求を処理します。 要求を処理するマシンのホスト名、顧客 ID など、追加の属性とイベントがスパンに追加されます。これらは、要求のコンテキストを理解するのに役立ちます。
* サービスのサブコンポーネントによって実行される作業を表すために、新しいスパンが作成される場合があります。
* サービスが別のサービスに対してリモート呼び出しを行うと、ヘッダーまたはメッセージ エンベロープにスパン コンテキストを挿入することによって、現在のスパン コンテキストがシリアル化され、次のサービスに転送されます。
* サービスによって実行されている作業は、成功したかどうかにかかわらず完了します。 スパンの状態が適切に設定され、スパンは完了としてマークされます。

### <a name="attributes"></a>属性

`Attributes` は、`span` にカプセル化される 0 個以上のキーと値のペアのリストです。 属性には、次のプロパティが必要です。

属性キー。null 以外で、空でない文字列である必要があります。
属性値。次のいずれかになります。
* プリミティブ型: 文字列、ブール値、倍精度浮動小数点数 (IEEE 754-1985)、または符号付き 64 ビット整数。
* プリミティブ型の値の配列。 配列は同種である必要があります。つまり、異なる型の値を含めることはできません。 配列値がネイティブにサポートされていないプロトコルの場合、そのような値は JSON 文字列として表す必要があります。

## <a name="supported-processors"></a>サポートされているプロセッサ:
 * 属性プロセッサ
 * スパン プロセッサ

## <a name="to-get-started"></a>開始するには

次のテンプレートを使用して、`applicationinsights.json` という名前の構成ファイルを作成し、`applicationinsights-agent-***.jar` と同じディレクトリに配置します。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>スパンを包含または除外する

属性プロセッサとスパン プロセッサでは、照合対象のスパンの一連のプロパティを指定し、テレメトリ プロセッサにスパンを包含するか除外するかを決定するためのオプションが公開されています。 このオプションを構成するには、`include` と `exclude` またはそのいずれかで、少なくとも 1 つの `matchType` と、`spanNames` または `attributes` のいずれかが必須となります。 包含/除外構成では、複数の条件を指定できます。 一致と見なされるようにするには、指定されたすべての条件が true と評価される必要があります。 

**必須フィールド**: 
* `matchType` では、`spanNames` および `attributes` 配列内の項目をどのように解釈するかを制御します。 指定できる値は `regexp` または `strict` です。 

**省略可能なフィールド**: 
* `spanNames` は少なくとも 1 つの項目に一致する必要があります。 
* `attributes` では、照合対象の属性の一覧を指定します。 一致と見なされるようにするには、これらすべての属性が完全に一致している必要があります。

> [!NOTE]
> `include` と `exclude` の両方が指定されている場合は、`exclude` プロパティの前に `include` プロパティがチェックされます。

#### <a name="sample-usage"></a>使用例

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
詳しく理解するには、[テレメトリ プロセッサの例](./java-standalone-telemetry-processors-examples.md)に関するドキュメントを参照してください。

## <a name="attribute-processor"></a>属性プロセッサ 

属性プロセッサによりスパンの属性が変更されます。 必要に応じて、スパンを含有/除外する機能がサポートされます。 構成ファイルで指定された順序で実行されるアクションの一覧が取得されます。 サポートされているアクションは次のとおりです。

### `insert`

キーがまだ存在しないスパンに新しい属性を挿入します。   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      },
    ]
  }
]
```
`insert` アクションでは、次のものが必要です
  * `key`
  * `value` または `fromAttribute` のいずれか
  * `action`:`insert`

### `update`

キーが存在するスパンの属性を更新します

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      },
    ]
  }
]
```
`update` アクションでは、次のものが必要です
  * `key`
  * `value` または `fromAttribute` のいずれか
  * `action`:`update`


### `delete` 

スパンから属性を削除します

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      },
    ]
  }
]
```
`delete` アクションでは、次のものが必要です
  * `key`
  * `action`: `delete`

### `hash`

既存の属性値をハッシュ (SHA1) します

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      },
    ]
  }
]
```
`hash` アクションでは、次のものが必要です
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> この機能は、3.0.1 以降にのみ存在します

正規表現規則を使用して、入力キーから規則で指定されたターゲット キーに値を抽出します。 ターゲット キーが既に存在する場合は、上書きされます。 これは、既存の属性がソースとして設定されている[スパン プロセッサ](#extract-attributes-from-span-name) `toAttributes` 設定と同様に動作します。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      },
    ]
  }
]
```
`extract` アクションでは、次のものが必要です
* `key`
* `pattern`
* `action` : `extract`

詳しく理解するには、[テレメトリ プロセッサの例](./java-standalone-telemetry-processors-examples.md)に関するドキュメントを参照してください。

## <a name="span-processors"></a>スパン プロセッサ

スパン プロセッサにより、スパン名、またはそのスパン名に基づくスパンの属性が変更されます。 必要に応じて、スパンを含有/除外する機能がサポートされます。

### <a name="name-a-span"></a>スパンに名前を付ける

name セクションの一部として、次の設定が必須となります。

* `fromAttributes`:キーの属性値は、構成で指定された順序で新しい名前を作成するために使用されます。 名前を変更するには、すべての属性キーをプロセッサのスパンに指定する必要があります。

必要に応じて、次の設定を構成できます。

* `separator`:指定された文字列は値を分割するために使用されます
> [!NOTE]
> 名前の変更が、属性プロセッサによって変更されている属性に依存している場合は、確実にパイプライン仕様の属性プロセッサの後にスパン プロセッサが指定されるようにします。

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>スパン名から属性を抽出する

スパン名を照合し、部分式に基づいてそこから属性を抽出する正規表現の一覧を取得します。 `toAttributes` セクションで指定する必要があります。

次の設定は必須です。

`rules` : スパン名から属性値を抽出する規則の一覧。 スパン名の値は、抽出された属性名で置き換えられます。 一覧内の各規則は正規表現パターンの文字列です。 スパン名は正規表現に対してチェックされます。 正規表現に一致する場合、正規表現のすべての名前付き部分式が属性として抽出され、スパンに追加されます。 各部分式の名前が属性名になり、部分式の一致部分がその属性値になります。 スパン名の一致する部分は、抽出された属性名で置き換えられます。 属性がスパン内に既に存在する場合、それらは上書きされます。 すべての規則について、指定された順序でプロセスが繰り返されます。 後続の各規則は、前の規則を処理した後の出力であるスパン名で動作します。

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>属性の一覧

テレメトリ プロセッサで使用できる一般的なスパン属性の一覧を次に示します。

### <a name="http-spans"></a>HTTP スパン

| 属性  | Type | 説明 | 
|---|---|---|
| `http.method` | string | HTTP 要求メソッド。|
| `http.url` | string | `scheme://host[:port]/path?query[#fragment]` 形式の完全な HTTP 要求 URL。 通常、フラグメントは HTTP を介して送信されませんが、それが既知のものであれば、含める必要があります。|
| `http.status_code` | number | [HTTP 応答状態コード](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | 使用される HTTP プロトコルの種類 |
| `http.user_agent` | string | クライアントから送信された [HTTP ユーザー エージェント](https://tools.ietf.org/html/rfc7231#section-5.5.3) ヘッダーの値。 |

### <a name="jdbc-spans"></a>JDBC スパン

| 属性  | Type | 説明  |
|---|---|---|
| `db.system` | string | 使用されているデータベース管理システム (DBMS) 製品の識別子。 |
| `db.connection_string` | string | データベースへの接続に使用する接続文字列。 埋め込みの資格情報は削除することをお勧めします。|
| `db.user` | string | データベースにアクセスするためのユーザー名 |
| `db.name` | string | この属性は、アクセス対象のデータベースの名前を報告するために使用されます。 データベースを切り替えるコマンドの場合は、これはターゲット データベースに設定する必要があります (コマンドが失敗する場合でも)。|
| `db.statement` | string | 実行対象のデータベース ステートメント。|