---
title: 2\.x からのアップグレード - Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java 2.x からのアップグレード
ms.topic: conceptual
ms.date: 11/25/2020
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 4de1f6788b5df94bc2c35516983cf67748e66ee0
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131203"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Application Insights Java 2.x SDK からのアップグレード

アプリケーションで Application Insights Java 2.x SDK を既に使用している場合は、使用し続けてかまいません。
Application Insights Java 3.x エージェントによって、2.x SDK を介して送信しているカスタム テレメトリの検出、キャプチャ、関連付けが行われます。一方で、テレメトリの重複を防止するために、2.x SDK によって実行される自動収集が抑制されます。

Application Insights 2.x エージェントを使用していた場合は、2.x エージェントを指す `-javaagent:` JVM 引数を削除する必要があります。

このドキュメントの残りの部分では、2.x から 3.x にアップグレードするときに発生する可能性がある制限事項および変更点と、役に立つ回避策について説明します。

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializer と TelemetryProcessor

3\.x エージェントを使用している場合、2.x SDK TelemetryInitializers と TelemetryProcessors は実行されません。
以前にこれらを必要としていたユース ケースの多くは、Application Insights Java 3.x で[カスタム ディメンション](./java-standalone-config.md#custom-dimensions)を構成するか、[テレメトリ プロセッサ](./java-standalone-telemetry-processors.md)を構成することによって解決できます。

## <a name="multiple-applications-in-a-single-jvm"></a>1 つの JVM 内の複数のアプリケーション

現在、Application Insights Java 3.x では、実行中のプロセスあたり 1 つの[接続文字列とロール名](./java-standalone-config.md#connection-string-and-role-name)のみがサポートされます。 特に、異なる接続文字列または異なるロール名を使用して、同じ Tomcat デプロイ内に複数の Tomcat Web アプリを作成することはできません。

## <a name="operation-names"></a>操作名

また、Application Insights Java 2.x SDK では、操作名に完全なパスが含まれている場合がありました。

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="完全なパスを含む操作名を示すスクリーンショット":::

Application Insights Java 3.x では操作名が変更され、Application Insights ポータル U/X で全般的により適切な集計ビューを提供するようになりました。

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-parameterized.png" alt-text="パラメーター化された操作名を示すスクリーンショット":::

ただし、アプリケーションによっては、以前の操作名によって提供されていた U/X の集計ビューを使用することをお勧めします。その場合は、3.x の[テレメトリ プロセッサ](./java-standalone-telemetry-processors.md) (プレビュー) 機能を使用して、以前の動作をレプリケートできます。

次のスニペットでは、以前の動作をレプリケートするために組み合わせる 3 つのテレメトリ プロセッサを構成します。
テレメトリ プロセッサにより、次の操作が順番に実行されます。

1. 最初のテレメトリ プロセッサは、属性プロセッサ (種類は `attribute`) であり、属性 (現在は `requests`、`dependencies`、近い将来に `traces`) を持つすべてのテレメトリに適用されます。

   これは、`http.method` および `http.url` という名前の属性を持つすべてのテレメトリと一致します。

   次に、`http.url` 属性のパス部分が、`tempName` という名前の新しい属性に抽出されます。

2. 2 番目のテレメトリ プロセッサは、スパン プロセッサ (種類は `span`) であり、`requests` と `dependencies` に適用されます。

   これは、`tempPath` という名前の属性を持つすべてのスパンと一致します。

   次に、`tempPath` 属性からスパン名が更新されます。

3. 最後のテレメトリ プロセッサは、最初のテレメトリ プロセッサと同じ種類の属性プロセッサです。

   これは、`tempPath` という名前の属性を持つすべてのテレメトリと一致します。

   次に、カスタム ディメンションとして報告されないように、`tempPath` という名前の属性が削除されます。

```
{
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "actions": [
          {
            "key": "http.url",
            "pattern": "https?://[^/]+(?<tempPath>/[^?]*)",
            "action": "extract"
          }
        ]
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempPath" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="2x-sdk-logging-appenders"></a>2.x SDK ロギング アペンダー

Application Insights Java 3.x では、ロギング アペンダーを構成する必要なく、[ログが自動収集](./java-standalone-config.md#auto-collected-logging)されます。
2\.x SDK ロギング アペンダーを使用している場合は、Application Insights Java 3.x によって抑制されるため、これらを削除できます。

## <a name="2x-sdk-spring-boot-starter"></a>2.x SDK Spring Boot スターター

Application Insights Java 3.x Spring Boot スターターはありません。
3.x の設定と構成は、Spring Boot を使用しているかどうかにかかわらず、同じ[簡単な手順](./java-in-process-agent.md#get-started)に従います。

Application Insights Java 2.x SDK Spring Boot スターターからアップグレードする場合、クラウド ロールの既定名は `spring.application.name` にならないことに注意してください。
Json 構成または環境変数を使用して 3.x でクラウド ロール名を設定する方法については、[3.x 構成ドキュメント](./java-standalone-config.md#cloud-role-name)を参照してください。
