---
title: Azure Service Fabric CLI- sfctl イベント | Microsoft Docs
description: Service Fabric CLI sfctl イベントのコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 858fd1971a22b1db2d243838558c3792d3a60cc9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901866"
---
# <a name="sfctl-events"></a>sfctl イベント
イベント ストアからイベントを取得します (EventStore サービスが既にインストールされている場合)。

EventStore システム サービスを追加するには、6.4 以降を実行する SFRP クラスターに構成をアップグレードします。 \:https\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore の URL を参照してください。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| all-applications-list | すべてのアプリケーション関連のイベントを取得します。 |
| all-nodes-list | すべてのノード関連のイベントを取得します。 |
| all-partitions-list | すべてのパーティション関連のイベントを取得します。 |
| all-services-list | すべてのサービス関連のイベントを取得します。 |
| application-list | 1 つのアプリケーション関連のイベントを取得します。 |
| cluster-list | すべてのクラスター関連のイベントを取得します。 |
| node-list | 1 つのノード関連のイベントを取得します。 |
| partition-all-replicas-list | 1 つのパーティションのすべてのレプリカ関連のイベントを取得します。 |
| partition-list | 1 つのパーティション関連のイベントを取得します。 |
| partition-replica-list | 1 つのパーティション レプリカ関連のイベントを取得します。 |
| service-list | 1 つのサービス関連のイベントを取得します。 |

## <a name="sfctl-events-all-applications-list"></a>sfctl events all-applications-list
すべてのアプリケーション関連のイベントを取得します。

応答は ApplicationEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-all-nodes-list"></a>sfctl events all-nodes-list
すべてのノード関連のイベントを取得します。

応答は NodeEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-all-partitions-list"></a>sfctl events all-partitions-list
すべてのパーティション関連のイベントを取得します。

応答は PartitionEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-all-services-list"></a>sfctl events all-services-list
すべてのサービス関連のイベントを取得します。

応答は ServiceEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-application-list"></a>sfctl events application-list
1 つのアプリケーション関連のイベントを取得します。

応答は ApplicationEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-cluster-list"></a>sfctl events cluster-list
すべてのクラスター関連のイベントを取得します。

応答は ClusterEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-node-list"></a>sfctl events node-list
1 つのノード関連のイベントを取得します。

応答は NodeEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --node-name    [必須] | ノード名。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl events partition-all-replicas-list
1 つのパーティションのすべてのレプリカ関連のイベントを取得します。

応答は ReplicaEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --partition-id   [必須] | パーティションの ID。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-partition-list"></a>sfctl events partition-list
1 つのパーティション関連のイベントを取得します。

応答は PartitionEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --partition-id   [必須] | パーティションの ID。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-partition-replica-list"></a>sfctl events partition-replica-list
1 つのパーティション レプリカ関連のイベントを取得します。

応答は ReplicaEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --partition-id   [必須] | パーティションの ID。 |
| --replica-id   [必須] | レプリカの識別子。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-events-service-list"></a>sfctl events service-list
1 つのサービス関連のイベントを取得します。

応答は ServiceEvent オブジェクトの一覧です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --end-time-utc   [必須] | 参照クエリの終了時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --service-id     [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --start-time-utc [必須] | 参照クエリの開始時刻 (ISO UTC yyyy-MM-ddTHH\:mm\:ssZ 形式)。 |
| --events-types-filter | これは、応答にのみ含める FabricEvents の種類を指定するコンマ区切りの文字列です。 |
| --exclude-analysis-events | このパラメーターを指定すると、true が渡された場合に AnalysisEvents の取得が無効になります。 |
| --skip-correlation-lookup | このパラメーターを指定すると、true が渡された場合に CorrelatedEvents 情報の検索が無効になります。 それ以外の場合、CorrelationEvents が処理され、すべての FabricEvent の HasCorrelatedEvents フィールドに値が設定されます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

