---
title: Azure IoT EdgeAgent および EdgeHub リファレンス | Microsoft Docs
description: edgeAgent および edgeHub モジュール ツインの特定のプロパティとその値を確認します。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2858179d42ebf51cbb24d95d2e0093f8577bacef
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030565"
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Edge エージェントと Edge ハブのモジュール ツインのプロパティ

Edge エージェントと Edge ハブは、IoT Edge ランタイムを構成する 2 つのモジュールです。 各モジュールが実行する役割について詳しくは、「[Azure IoT Edge ランタイムとそのアーキテクチャについて](iot-edge-runtime.md)」をご覧ください。 

この記事では、ランタイム モジュール ツインの必要なプロパティと報告されるプロパティを示します。 IoT Edge デバイスにモジュールをデプロイする方法の詳細については、[デプロイおよび監視][lnk-deploy]に関する記事を参照してください。

## <a name="edgeagent-desired-properties"></a>edgeAgent の必要なプロパティ

Edge エージェントのモジュール ツインは `$edgeAgent` と呼ばれ、デバイス上で実行されている Edge エージェントと IoT Hub の間の通信を調整します。 必要なプロパティは、単一デバイスまたは大規模デプロイの一部として特定のデバイスにデプロイ マニフェストを適用するときに設定されます。 

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| schemaVersion | "1.0" である必要がある | [はい] |
| runtime.type | "docker" である必要がある | [はい] |
| runtime.settings.minDockerVersion | このデプロイ マニフェストに必要な最小の Docker バージョンに設定される | [はい] |
| runtime.settings.loggingOptions | Edge エージェント コンテナーのログ オプションを含む文字列化された JSON。 [Docker のログ オプション][lnk-docker-logging-options] | いいえ  |
| runtime.settings.registryCredentials<br>.{registryId}.username | コンテナー レジストリのユーザー名です。 Azure Container Registry では、ユーザー名は通常、レジストリ名です。<br><br> 公開されていないモジュール イメージでは、レジストリの資格情報が必要です。 | いいえ  |
| runtime.settings.registryCredentials<br>.{registryId}.password | コンテナー レジストリのパスワード。 | いいえ  |
| runtime.settings.registryCredentials<br>.{registryId}.address | コンテナー レジストリのアドレス。 Azure Container Registry の場合、アドレスは通常 *{registryname}.azurecr.io* です。 | いいえ  |  
| systemModules.edgeAgent.type | "docker" である必要がある | [はい] |
| systemModules.edgeAgent.settings.image | Edge エージェントのイメージの URI。 現在、Edge エージェントは自身を更新できません。 | [はい] |
| systemModules.edgeAgent.settings<br>.createOptions | Edge エージェント コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション][lnk-docker-create-options] | いいえ  |
| systemModules.edgeAgent.configuration.id | このモジュールをデプロイしたデプロイの ID。 | このプロパティは、このマニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |
| systemModules.edgeHub.type | "docker" である必要がある | [はい] |
| systemModules.edgeHub.status | "running" である必要がある | [はい] |
| systemModules.edgeHub.restartPolicy | "always" である必要がある | [はい] |
| systemModules.edgeHub.settings.image | Edge ハブのイメージの URI。 | [はい] |
| systemModules.edgeHub.settings<br>.createOptions | Edge ハブ コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション][lnk-docker-create-options] | いいえ  |
| systemModules.edgeHub.configuration.id | このモジュールをデプロイしたデプロイの ID。 | このプロパティは、このマニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |
| modules.{moduleId}.version | このモジュールのバージョンを表すユーザー定義の文字列。 | [はい] |
| modules.{moduleId}.type | "docker" である必要がある | [はい] |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | [はい] |
| modules.{moduleId}.settings.image | モジュール イメージへの URI。 | [はい] |
| modules.{moduleId}.settings.createOptions | モジュール コンテナーの作成のためのオプションを含む文字列化された JSON。 [Docker の作成オプション][lnk-docker-create-options] | いいえ  |
| modules.{moduleId}.configuration.id | このモジュールをデプロイしたデプロイの ID。 | このプロパティは、このマニフェストがデプロイを使用して適用されるときに IoT Hub によって設定されます。 デプロイ マニフェストの一部ではありません。 |

## <a name="edgeagent-reported-properties"></a>edgeAgent の報告されるプロパティ

Edge エージェントの報告されるプロパティには、次の 3 つの主な情報が含まれます。

1. 最後に表示された必要なプロパティのアプリケーションの状態
2. Edge エージェントによって報告された、現在デバイス上で実行されているモジュールの状態
3. 現在デバイス上で実行されている必要なプロパティのコピー。

この最後の情報は、最新の必要なプロパティがランタイムによって正常に適用されず、デバイスがまだ以前のデプロイ マニフェストを実行している場合に役立ちます。

> [!NOTE]
> Edge エージェントの報告されるプロパティは、デプロイの状態を大規模に調査するために [IoT Hub クエリ言語][lnk-iothub-query]でクエリできるため有効です。 状態のために Edge エージェント プロパティを使用する方法の詳細については、[1 台または多数のデバイスを対象とした IoT Edge デプロイ][lnk-deploy]に関する記事を参照してください。

次の表には、必要なプロパティからコピーされる情報は含まれません。

| プロパティ | 説明 |
| -------- | ----------- |
| lastDesiredVersion | この整数は、Edge エージェントによって処理された必要なプロパティの最後のバージョンを参照します。 |
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

## <a name="edgehub-desired-properties"></a>edgeHub の必要なプロパティ

Edge ハブのモジュール ツインは `$edgeHub` と呼ばれ、デバイス上で実行されている Edge ハブと IoT Hub の間の通信を調整します。 必要なプロパティは、単一デバイスまたは大規模デプロイの一部として特定のデバイスにデプロイ マニフェストを適用するときに設定されます。 

| プロパティ | 説明 | デプロイ マニフェストに必要 |
| -------- | ----------- | -------- |
| schemaVersion | "1.0" である必要がある | [はい] |
| routes.{routeName} | Edge ハブのルートを表す文字列。 | `routes` 要素は存在できますが、空です。 |
| storeAndForwardConfiguration.timeToLiveSecs | 切断された (たとえば、IoT Hub またはローカル モジュールから切断された) ルーティング エンドポイントの場合、Edge ハブがメッセージを保持する秒数 | [はい] |

## <a name="edgehub-reported-properties"></a>edgeHub の報告されるプロパティ

| プロパティ | 説明 |
| -------- | ----------- |
| lastDesiredVersion | この整数は、Edge ハブによって処理された必要なプロパティの最後のバージョンを参照します。 |
| lastDesiredStatus.code | これは、Edge ハブによって表示された最後の必要なプロパティを参照する状態コードです。 許可される値: `200` 成功、`400` 無効な構成、`500` 失敗 |
| lastDesiredStatus.description | 状態のテキストでの説明 |
| clients.{device or module identity}.status | このデバイスまたはモジュールの接続状態。 可能性のある値 {"connected" \| "disconnected"}。 切断された状態になることができるのはモジュール ID だけです。 Edge ハブに接続されるダウンストリーム デバイスは、接続されている場合にのみ表示されます。 |
| clients.{device or module identity}.lastConnectTime | デバイスまたはモジュールが接続された最後の時間 |
| clients.{device or module identity}.lastDisconnectTime | デバイスまたはモジュールが切断された最後の時間 |

## <a name="next-steps"></a>次の手順

これらのプロパティを使って配置マニフェストを作成する方法については、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
