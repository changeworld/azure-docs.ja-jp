---
title: サンプリング オーバーライド (プレビュー) - Azure Monitor Application Insights for Java
description: Azure Monitor Application Insights for Java でサンプリング オーバーライドを構成する方法について説明します。
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 7602392b78f53e5b896e92058836fca60de39d64
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448884"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>サンプリング オーバーライド (プレビュー) - Azure Monitor Application Insights for Java

> [!NOTE]
> サンプリング オーバーライド機能は、3.0.3 からプレビュー段階になっています。

サンプリング オーバーライドを使用すると、[既定のサンプリング率](./java-standalone-config.md#sampling)をオーバーライドできます。次に例を示します。
 * ノイズの多い正常性チェックでは、サンプリング率を 0 (または何らかの小さい値) に設定します。
 * ノイズの多い依存関係呼び出しでは、サンプリング率を 0 (または何らかの小さい値) に設定します。
 * 重要な要求の種類 (`/login` など) では、既定のサンプリングが低く構成されている場合でも、サンプリング率を 100 に設定します。

## <a name="terminology"></a>用語

サンプリング オーバーライドについて学習する前に、"*スパン*" という用語を理解しておく必要があります。 スパンは、次を表す一般的な用語です。

* 着信要求。
* 送信依存関係 (別のサービスへのリモート呼び出しなど)。
* プロセス内の依存関係 (サービスのサブコンポーネントによって実行される操作など)。

サンプリング オーバーライドに関しては、次のスパンのコンポーネントが重要です。

* 属性

スパン属性は、指定された要求または依存関係の標準とカスタムの両方のプロパティを表します。

## <a name="getting-started"></a>作業の開始

開始するには、*applicationinsights.json* という名前の構成ファイルを作成します。 これを、*applicationinsights-agent-\*.jar* と同じディレクトリに保存します。 次に示すテンプレートを使用します。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>しくみ

スパンが開始されると、いずれかのサンプリング オーバーライドが一致するかどうかを確認するために、その時点でスパンに存在する属性が使用されます。

いずれかのサンプリング オーバーライドが一致する場合は、サンプリングの割合を使用してスパンをサンプリングするかどうかを決定します。

一致する最初のサンプリング オーバーライドのみが使用されます。

サンプリング オーバーライドが一致しない場合:

* これがトレースの最初のスパンである場合は、[既定のサンプリング率](./java-standalone-config.md#sampling)が使用されます。
* これがトレースの最初のスパンでない場合は、親のサンプリングの決定が使用されます。

> [!WARNING]
> スパンを収集しないことを決定した場合、ダウンストリームのスパンに一致するサンプリング オーバーライドがある場合でも、ダウンストリームのすべてのスパンも収集されません。
> そうしない場合、ダウンストリームのスパンが収集されるものの、収集されなかったスパンが親になることでトレースが破損するため、この動作が必要になります。

> [!NOTE]
> サンプリングの決定は、traceId (operationId とも呼ばれます) を 0 から 100 までの数値にハッシュすることに基づいており、そのハッシュがサンプリング率と比較されます。
> 特定のトレース内のすべてのスパンは同じ traceId を持つため、同じハッシュになります。したがって、サンプリングの決定はトレース全体で一貫したものになります。

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>例: 正常性チェックのテレメトリの収集の抑制

これにより、`/health-checks` へのすべての要求に対するテレメトリの収集が抑制されます。

これにより、通常は `/health-checks` で収集されるダウンストリームのスパン (依存関係) の収集も抑制されます。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>例: ノイズの多い依存関係呼び出しのテレメトリの収集の抑制

これにより、`GET my-noisy-key` へのすべての redis 呼び出しに対するテレメトリの収集が抑制されます。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>例: 重要な要求の種類のテレメトリを 100% 収集

これにより、`/login` のテレメトリが 100% 収集されます。

ダウンストリームのスパン (依存関係) は親のサンプリング決定を尊重するため (ダウンストリームのスパンのサンプリング オーバーライドは存在しません)、すべての '/login' 要求に対するものも収集されます。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>共通のスパン属性

このセクションでは、サンプリング オーバーライドで使用できるいくつかの共通のスパン属性を一覧表示します。

### <a name="http-spans"></a>HTTP スパン

| 属性  | Type | 説明 | 
|---|---|---|
| `http.method` | string | HTTP 要求メソッド。|
| `http.url` | string | `scheme://host[:port]/path?query[#fragment]` 形式の完全な HTTP 要求 URL。 通常、フラグメントは HTTP 経由で送信されません。 しかし、フラグメントがわかっている場合は、含める必要があります。|
| `http.status_code` | number | [HTTP 応答状態コード](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | HTTP プロトコルの種類。 |
| `http.user_agent` | string | クライアントから送信された [HTTP ユーザー エージェント](https://tools.ietf.org/html/rfc7231#section-5.5.3) ヘッダーの値。 |

### <a name="jdbc-spans"></a>JDBC スパン

| 属性  | Type | 説明  |
|---|---|---|
| `db.system` | string | 使用されているデータベース管理システム (DBMS) 製品の識別子。 |
| `db.connection_string` | string | データベースへの接続に使用された接続文字列。 埋め込みの資格情報は削除することをお勧めします。|
| `db.user` | string | データベースにアクセスするためのユーザー名 |
| `db.name` | string | アクセス対象のデータベースの名前を報告するために使用される文字列。 データベースを切り替えるコマンドの場合、この文字列はターゲット データベースに設定する必要があります (コマンドが失敗する場合でも)。|
| `db.statement` | string | 実行されているデータベース ステートメント。|
