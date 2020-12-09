---
title: テレメトリ プロセッサ (プレビュー) - Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java 用テレメトリ プロセッサ
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 316243e130345ce1b009e065cf9d8250c501ee84
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601035"
---
# <a name="telemetry-processors-preview-for-azure-monitor-application-insights-java"></a>Azure Monitor Application Insights Java 用テレメトリ プロセッサ (プレビュー)

> [!NOTE]
> この機能はまだプレビュー段階です。

Application Insights 用の Java 3.0 エージェントには現在、データがエクスポートされる前に利用統計情報を処理する機能があります。

### <a name="some-use-cases"></a>いくつかのユースケース:
 * 機微なデータをマスクする
 * 条件付きでカスタム ディメンションを追加する
 * 集計や表示に使用されるテレメトリ名を更新する

### <a name="supported-processors"></a>サポートされているプロセッサ:
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

## <a name="includeexclude-spans"></a>スパンを包含/除外する

属性プロセッサとスパン プロセッサにより、照合対象のスパンの一連のプロパティを指定し、プロセッサのスパンを包含するか除外するかを決定するためのオプションが公開されます。 このオプションを構成するには、`include` と `exclude` またはそのいずれかで、少なくとも 1 つの `matchType` と、`spanNames` または `attributes` のいずれかが必須となります。 包含/除外構成では、複数の条件を指定できます。 一致と見なされるようにするには、指定されたすべての条件が true と評価される必要があります。 

**必須フィールド**: 
* `matchType` では、`spanNames` および `attributes` 配列内の項目をどのように解釈するかを制御します。 指定できる値は `regexp` または `strict` です。 

**省略可能なフィールド**: 
* `spanNames` は少なくとも 1 つの項目に一致する必要があります。 
* `attributes` では、照合対象の属性の一覧を指定します。 一致と見なされるようにするには、これらすべての属性が完全に一致している必要があります。

> [!NOTE]
> `include` と `exclude` の両方が指定されている場合は、`exclude` プロパティの前に `include` プロパティがチェックされます。

#### <a name="sample-usage"></a>使用例

次の例では、一連のスパン プロパティを指定して、このプロセッサを適用する必要があるスパンを示します。 プロパティの `include` は含める必要があるものを示し、`exclude` プロパティでは処理すべきでないスパンをさらにフィルターで除外します。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
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
  }
}
```

上記の構成の場合、次のスパンがプロパティと一致し、プロセッサ アクションが適用されます。

* Span1 名前: 'svcB' 属性: {env: production, test_request:123, credit_card:1234, redact_trace: "false"}

* Span2 名前: 'svcA' 属性: {env: staging, test_request: false, redact_trace: true}

次のスパンは include プロパティと一致しておらず、プロセッサ アクションは適用されません。

* Span3 名前: 'svcB' 属性: {env: production, test_request: true, credit_card:1234, redact_trace: false}

* Span4 名前: ' svcC ' 属性: {env: dev, test_request: false}

## <a name="attribute-processor"></a>属性プロセッサ 

属性プロセッサによりスパンの属性が変更されます。 必要に応じて、スパンを含有/除外する機能がサポートされます。
構成ファイルで指定された順序で実行されるアクションの一覧が取得されます。 サポートされているアクションは次のとおりです。

* `insert` :キーがまだ存在しないスパンに新しい属性を挿入します
* `update` :キーが存在するスパンの属性を更新します
* `delete` :スパンから属性を削除します
* `hash`   :既存の属性値をハッシュ (SHA1) します

アクション `insert` と `update` の場合
* `key` は必須です
* `value` または `fromAttribute` のいずれかが必須です
* `action` は必須です。

`delete` アクションの場合、
* `key` は必須です
* `action`: `delete` は必須です。

`hash` アクションの場合、
* `key` は必須です
* `action` : `hash` は必須です。

アクションの一覧を構成することで、属性のバックフィル、新しいキーへの値のコピー、機密情報の編集などの豊富なシナリオを作成できます。

#### <a name="sample-usage"></a>使用例

次の例では、キーと値をスパンに挿入する方法を示します。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

次の例は、属性の既存のキーのみを更新するようにプロセッサを構成する方法を示しています。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

次の例では、正規表現パターンに一致するスパン名を持つスパンを処理する方法を示します。
このプロセッサにより "token" 属性が削除され、スパン名が "auth.\*" に一致し、スパン名が "login.\*" に一致しないスパンの "password" 属性が難読化されます。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>スパン プロセッサ

スパン プロセッサにより、スパン名、またはそのスパン名に基づくスパンの属性が変更されます。 必要に応じて、スパンを含有/除外する機能がサポートされます。

### <a name="name-a-span"></a>スパンに名前を付ける

name セクションの一部として、次の設定が必須となります。

* `fromAttributes`:キーの属性値は、構成で指定された順序で新しい名前を作成するために使用されます。 名前を変更するには、すべての属性キーをプロセッサのスパンに指定する必要があります。

必要に応じて、次の設定を構成できます。

* `separator`:指定された文字列は値を分割するために使用されます
> [!NOTE]
> 名前の変更が、属性プロセッサによって変更されている属性に依存している場合は、確実にパイプライン仕様の属性プロセッサの後にスパン プロセッサが指定されるようにします。

#### <a name="sample-usage"></a>使用例

次の例は、属性 "db. svc"、"operation"、および "id" の値により、スパンの新しい名前がその順序で、値 "::" で区切られて形成されることを示しています。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>スパン名から属性を抽出する

スパン名を照合し、部分式に基づいてそこから属性を抽出する正規表現の一覧を取得します。 `toAttributes` セクションで指定する必要があります。

次の設定は必須です。

`rules` : スパン名から属性値を抽出する規則の一覧。 スパン名の値は、抽出された属性名で置き換えられます。 一覧内の各規則は正規表現パターンの文字列です。 スパン名は正規表現に対してチェックされます。 正規表現に一致する場合、正規表現のすべての名前付き部分式が属性として抽出され、スパンに追加されます。 各部分式の名前が属性名になり、部分式の一致部分がその属性値になります。 スパン名の一致する部分は、抽出された属性名で置き換えられます。 属性がスパン内に既に存在する場合、それらは上書きされます。 すべての規則について、指定された順序でプロセスが繰り返されます。 後続の各規則は、前の規則を処理した後の出力であるスパン名で動作します。

#### <a name="sample-usage"></a>使用例

入力スパン名が /api/v1/document/12345678/update であるとします。 出力スパン名 /api/v1/document/{documentId}/update に次の結果を適用すると、スパンに新しい属性 "documentId"="12345678" が追加されます。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

スパン名を "{operation_website}" に変更し、スパンに次のプロパティがある場合の属性 {Key: operation_website, Value: oldSpanName} を追加する方法を次に示します。
- スパン名の文字列の任意の場所に '/' が含まれています。
- スパン名は 'donot/change' ではありません。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```