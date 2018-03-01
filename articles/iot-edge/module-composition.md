---
title: "Azure IoT Edge モジュールの構成 | Microsoft Docs"
description: "Azure IoT Edge モジュールを構成する内容と、それらをどのように再利用できるかについて説明します。"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5de67b6f1ce79934a3a6aab623d2e77a56a8ce76
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する - プレビュー

Azure IoT Edge では、複数の IoT Edge モジュールを、IoT Edge デバイスにデプロイする前に構成できます。 この記事では、複数の IoT Edge モジュールをデプロイ前に構成することに関する最も重要な概念について説明します。 

## <a name="the-deployment-manifest"></a>デプロイ マニフェスト
*デプロイ マニフェスト*は、次の内容が記述された JSON ドキュメントです。

* どの IoT Edge モジュールをデプロイする必要があるか、およびそれらの作成および管理オプション
* メッセージがモジュール間およびモジュールと IoT Hub の間でどのように流れるかを記述した、Edge ハブの構成
* オプションで、モジュール ツインの必要なプロパティに設定したり、個々のモジュール アプリケーションを構成したりするための値。

Azure IoT Edge チュートリアルでは、Azure IoT Edge ポータルでウィザードを使用することによってデプロイ マニフェストを作成します。 また、REST または IoT Hub サービス SDK を使用して、プログラムでデプロイ マニフェストを適用することもできます。 IoT Edge デプロイの詳細については、「[Deploy and monitor (デプロイおよび監視)][lnk-deploy]」を参照してください。

高いレベルでは、配置マニフェストは、IoT Edge デバイスにデプロイされた IoT Edge モジュールのモジュール ツインの必要なプロパティを構成します。 これらのモジュールのうちの 2 つである Edge エージェントと Edge ハブは常に存在します。

マニフェストは、この構造に従います。

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
                }
            },
            "{module1}": {  // optional
                "properties.desired": {
                    // desired properties of module with id {module1}
                }
            },
            "{module2}": {  // optional
                ...
            },
            ...
        }
    }

デプロイ マニフェストの例が、このセクションの最後に報告されます。

> [!IMPORTANT]
> すべての IoT Edge デバイスをデプロイ マニフェストで構成する必要があります。 新しくインストールされた IoT Edge ランタイムは、有効なマニフェストで構成されるまでエラー コードを報告します。 

### <a name="specify-the-modules"></a>モジュールを指定する
Edge エージェントのモジュール ツインの必要なプロパティには、必要なモジュールとそれらの構成および管理オプションが Edge エージェントの構成パラメーターと共に含まれます。

高いレベルでは、マニフェストのこのセクションには、モジュール イメージへの参照、IoT Edge ランタイム モジュール (Edge エージェントと Edge ハブ) の管理オプション、およびユーザーが指定したモジュールが含まれます。

マニフェストのこのセクションの詳細な説明については、「[desired properties of the Edge agent (Edge エージェントの必要なプロパティ)][lnk-edgeagent-desired]」を参照してください。

> [!NOTE]
> IoT Edge ランタイム (エージェントとハブ) のみが含まれたデプロイ マニフェストは有効です。


### <a name="specify-the-routes"></a>ルートを指定する
Edge ハブは、モジュール間およびモジュールと IoT Hub の間でメッセージを宣言的にルーティングするための方法を提供します。

ルートの構文は次のとおりです。

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

*ソース*は、次のいずれでもかまいません。

| ソース | [説明] |
| ------ | ----------- |
| `/*` | 任意のデバイスまたはモジュールからの、デバイスからクラウドへのすべてのメッセージ |
| `/messages/*` | 何らかの出力と共に、または出力なしでデバイスまたはモジュールによって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/*` | 何らかの出力と共に、または出力なしでモジュールによって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/{moduleId}/*` | 出力なしで {moduleId} によって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/{moduleId}/outputs/*` | 何らかの出力と共に {moduleId} によって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/{moduleId}/outputs/{output}` | {output} を使用して {moduleId} によって送信された、デバイスからクラウドへの任意のメッセージ |

この条件は、IoT Hub ルーティング ルール用に [IoT Hub クエリ言語][lnk-iothub-query]によってサポートされているいずれの条件でもかまいません。

シンクは、次のいずれかです。

| シンク | [説明] |
| ---- | ----------- |
| `$upstream` | IoT Hub にメッセージを送信する |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | モジュール `{moduleId}` の入力 `{input}` にメッセージを送信する |

Edge ハブが少なくとも 1 つの保証を提供することに注意することが重要です。つまり、ルートがそのシンクにメッセージを配信できない場合 (たとえば、Edge ハブが IoT Hub に接続できない場合や、ターゲット モジュールが接続されていない場合)、メッセージはローカルに格納されます。

Edge ハブは、その Edge ハブの必要なプロパティの `storeAndForwardConfiguration.timeToLiveSecs` プロパティで指定された時間までメッセージを格納します。

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>モジュール ツインの必要なプロパティの指定

デプロイ マニフェストは、Edge エージェント セクションで指定された各ユーザー モジュールのモジュール ツインの必要なプロパティを指定できます。

必要なプロパティがデプロイ マニフェストで指定されている場合、それらは現在モジュール ツインにある必要なプロパティをすべて上書きします。

デプロイ マニフェストでモジュール ツインの必要なプロパティを指定しない場合、IoT Hub はモジュール ツインをどのような方法でも変更しないため、ユーザーは必要なプロパティをプログラムで設定できます。

デバイス ツインを変更できるのと同じメカニズムを使用してモジュール ツインを変更できます。 詳しくは、[デバイス ツインの開発者ガイド](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins)に関するページをご覧ください。   

### <a name="deployment-manifest-example"></a>デプロイ マニフェストの例

これは、デプロイ マニフェストの JSON ドキュメントの例です。

    {
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "runtime": {
                    "type": "docker",
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "loggingOptions": ""
                    }
                },
                "systemModules": {
                    "edgeAgent": {
                        "type": "docker",
                        "settings": {
                        "image": "microsoft/azureiotedge-agent:1.0-preview",
                        "createOptions": ""
                        }
                    },
                    "edgeHub": {
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-hub:1.0-preview",
                        "createOptions": ""
                        }
                    }
                },
                "modules": {
                    "tempSensor": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                        "createOptions": "{}"
                        }
                    },
                    "filtermodule": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "myacr.azurecr.io/filtermodule:latest",
                        "createOptions": "{}"
                        }
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "routes": {
                    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
                },
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 10
                }
            }
        }
    }
    }

## <a name="reference-edge-agent-module-twin"></a>参照: Edge エージェントのモジュール ツイン

Edge エージェントのモジュール ツインは `$edgeAgent` と呼ばれ、デバイス上で実行されている Edge エージェントと IoT Hub の間の通信を調整します。
必要なプロパティは、単一デバイスまたは大規模デプロイの一部として特定のデバイスにデプロイ マニフェストを適用するときに設定されます。 IoT Edge デバイスにモジュールをデプロイする方法の詳細については、「[Deployment and monitoring (デプロイおよび監視)][lnk-deploy]」を参照してください。

### <a name="edge-agent-twin-desired-properties"></a>Edge エージェント ツインの必要なプロパティ

| プロパティ | [説明] | 必須 |
| -------- | ----------- | -------- |
| schemaVersion | "1.0" である必要がある | [はい] |
| runtime.type | "docker" である必要がある | [はい] |
| runtime.settings.minDockerVersion | このデプロイ マニフェストに必要な最小の Docker バージョンに設定される | [はい] |
| runtime.settings.loggingOptions | Edge エージェント コンテナーのログ オプションを含む文字列化された JSON。 [Docker のログ オプション][lnk-docker-logging-options] | いいえ  |
| systemModules.edgeAgent.type | "docker" である必要がある | [はい] |
| systemModules.edgeAgent.settings.image | Edge エージェントのイメージの URI。 現在、Edge エージェントは自身を更新できません。 | [はい] |
| systemModules.edgeAgent.settings.createOptions | Edge エージェント コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション][lnk-docker-create-options] | いいえ  |
| systemModules.edgeAgent.configuration.id | このモジュールをデプロイしたデプロイの ID。 | これは、このマニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |
| systemModules.edgeHub.type | "docker" である必要がある | [はい] |
| systemModules.edgeHub.status | "running" である必要がある | [はい] |
| systemModules.edgeHub.restartPolicy | "always" である必要がある | [はい] |
| systemModules.edgeHub.settings.image | Edge ハブのイメージの URI。 | [はい] |
| systemModules.edgeHub.settings.createOptions | Edge ハブ コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション][lnk-docker-create-options] | いいえ  |
| systemModules.edgeHub.configuration.id | このモジュールをデプロイしたデプロイの ID。 | これは、このマニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |
| modules.{moduleId}.version | このモジュールのバージョンを表すユーザー定義の文字列。 | [はい] |
| modules.{moduleId}.type | "docker" である必要がある | [はい] |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | [はい] |
| modules.{moduleId}.settings.image | モジュール イメージへの URI。 | [はい] |
| modules.{moduleId}.settings.createOptions | モジュール コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション][lnk-docker-create-options] | いいえ  |
| modules.{moduleId}.configuration.id | このモジュールをデプロイしたデプロイの ID。 | これは、このマニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |

### <a name="edge-agent-twin-reported-properties"></a>Edge エージェント ツインの報告されるプロパティ

Edge エージェントの報告されるプロパティには、次の 3 つの主な情報が含まれます。

1. 最後に表示された必要なプロパティのアプリケーションの状態
2. Edge エージェントによって報告された、現在デバイス上で実行されているモジュールの状態
3. 現在デバイス上で実行されている必要なプロパティのコピー。

この最後の情報は、最新の必要なプロパティがランタイムによって正常に適用されず、デバイスがまだ以前のデプロイ マニフェストを実行している場合に役立ちます。

> [!NOTE]
> Edge エージェントの報告されるプロパティは、デプロイの状態を大規模に調査するために [IoT Hub クエリ言語][lnk-iothub-query]でクエリできるため有効です。 この機能を使用する方法の詳細については、「[デプロイメント][lnk-deploy]」を参照してください。

次の表には、必要なプロパティからコピーされる情報は含まれません。

| プロパティ | [説明] |
| -------- | ----------- |
| lastDesiredVersion | この int は、Edge エージェントによって処理された必要なプロパティの最後のバージョンを参照します。 |
| lastDesiredStatus.code | これは、Edge エージェントによって表示された最後の必要なプロパティを参照する状態コードです。 許可される値: `200` 成功、`400` 無効な構成、`412` 無効なスキーマ バージョン、`417` 必要なプロパティが空、`500` 失敗 |
| lastDesiredStatus.description | 状態のテキストでの説明 |
| deviceHealth | `healthy` すべてのモジュールのランタイムの状態が `running` または `stopped` のどちらかである場合、`unhealthy` それ以外の場合 |
| configurationHealth.{deploymentId}.health | `healthy` デプロイ {deploymentId} によって設定されたすべてのモジュールのランタイムの状態が `running` または `stopped` のどちらかである場合、`unhealthy` それ以外の場合 |
| runtime.platform.OS | デバイス上で実行されている OS の報告 |
| runtime.platform.architecture | デバイス上の CPU のアーキテクチャの報告 |
| systemModules.edgeAgent.runtimeStatus | Edge エージェントの報告された状態: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Edge エージェントの報告された状態のテキストでの説明。 |
| systemModules.edgeHub.runtimeStatus | Edge ハブの現在の状態: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | 異常な場合の Edge ハブの現在の状態のテキストでの説明。 |
| systemModules.edgeHub.exitCode | 終了した場合、Edge ハブ コンテナーによって報告された終了コード |
| systemModules.edgeHub.startTimeUtc | Edge ハブが最後に起動された時間 |
| systemModules.edgeHub.lastExitTimeUtc | Edge ハブが最後に終了した時間 |
| systemModules.edgeHub.lastRestartTimeUtc | Edge ハブが最後に再起動された時間 |
| systemModules.edgeHub.restartCount | このモジュールが再起動ポリシーの一部として再起動された回数。 |
| modules.{moduleId}.runtimeStatus | モジュールの現在の状態: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | 異常な場合のモジュールの現在の状態のテキストでの説明。 |
| modules.{moduleId}.exitCode | 終了した場合、モジュール コンテナーによって報告された終了コード |
| modules.{moduleId}.startTimeUtc | モジュールが最後に起動された時間 |
| modules.{moduleId}.lastExitTimeUtc | モジュールが最後に終了した時間 |
| modules.{moduleId}.lastRestartTimeUtc | モジュールが最後に再起動された時間 |
| modules.{moduleId}.restartCount | このモジュールが再起動ポリシーの一部として再起動された回数。 |

## <a name="reference-edge-hub-module-twin"></a>参照: Edge ハブのモジュール ツイン

Edge ハブのモジュール ツインは `$edgeHub` と呼ばれ、デバイス上で実行されている Edge ハブと IoT Hub の間の通信を調整します。
必要なプロパティは、単一デバイスまたは大規模デプロイの一部として特定のデバイスにデプロイ マニフェストを適用するときに設定されます。 IoT Edge デバイスにモジュールをデプロイする方法の詳細については、「[デプロイメント][lnk-deploy]」を参照してください。

### <a name="edge-hub-twin-desired-properties"></a>Edge ハブ ツインの必要なプロパティ

| プロパティ | [説明] | デプロイ マニフェストに必要 |
| -------- | ----------- | -------- |
| schemaVersion | "1.0" である必要がある | [はい] |
| routes.{routeName} | Edge ハブのルートを表す文字列。 | `routes` 要素は存在できますが、空です。 |
| storeAndForwardConfiguration.timeToLiveSecs | 切断された (たとえば、IoT Hub またはローカル モジュールから切断された) ルーティング エンドポイントの場合、Edge ハブがメッセージを保持する秒数 | [はい] |

### <a name="edge-hub-twin-reported-properties"></a>Edge ハブ ツインの報告されるプロパティ

| プロパティ | [説明] |
| -------- | ----------- |
| lastDesiredVersion | この int は、Edge ハブによって処理された必要なプロパティの最後のバージョンを参照します。 |
| lastDesiredStatus.code | これは、Edge ハブによって表示された最後の必要なプロパティを参照する状態コードです。 許可される値: `200` 成功、`400` 無効な構成、`500` 失敗 |
| lastDesiredStatus.description | 状態のテキストでの説明 |
| clients.{device or module identity}.status | このデバイスまたはモジュールの接続状態。 可能性のある値 {"connected" \| "disconnected"}。 切断された状態になることができるのはモジュール ID だけです。 Edge ハブに接続されるダウンストリーム デバイスは、接続されている場合にのみ表示されます。 |
| clients.{device or module identity}.lastConnectTime | デバイスまたはモジュールが接続された最後の時間 |
| clients.{device or module identity}.lastDisconnectTime | デバイスまたはモジュールが切断された最後の時間 |

## <a name="next-steps"></a>次の手順

これで IoT Edge モジュールがどのように使用されるかがわかったので、「[Understand the requirements and tools for developing IoT Edge modules (IoT Edge モジュールを開発するための要件およびツールを理解する)][lnk-module-dev]」に進みます。

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
