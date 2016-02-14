<properties
 pageTitle="IoT Hub の開発者ガイド トピック | Microsoft Azure"
 description="IoT Hub のエンドポイント、セキュリティ、デバイス ID レジストリ、メッセージングなどについて説明する、Azure IoT Hub の開発者ガイドです。"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Azure IoT Hub 開発者ガイド

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。

Azure IoT Hub により、次のことを実現できます。

* デバイスごとのセキュリティ資格情報とアクセス制御を使用した、通信のセキュリティ保護。
* 非常にスケール性が高く信頼性に優れた、デバイスとクラウド間の双方向メッセージング。
* 最も一般的な言語とプラットフォームのデバイスのライブラリを使用した、簡単なデバイスの接続。

この記事では、次のトピックについて説明します。

- [エンドポイント](#endpoints)。このセクションでは、各 IoT Hub がランタイムと管理の操作のために公開する、さまざまなエンドポイントについて説明します。
- [デバイス ID レジストリ](#device-identity-registry)。このセクションでは、各 IoT ハブのデバイス ID レジストリに格納されている情報と、レジストリにアクセスして変更する方法について説明します。
- [セキュリティ](#security)。このセクションでは、IoT Hub の機能へのアクセスを許可するために使用する、デバイスとクラウド コンポーネントの両方のセキュリティ モデルについて説明します。
- [メッセージング](#messaging)。このセクションでは、IoT Hub が公開している (デバイスとクラウド間の双方向の) メッセージング機能について説明します。
- [クォータとスロットル](#throttling)。このセクションでは、IoT Hub の使用に適用されるクォータの概要ついて説明します。

## エンドポイント<a id="endpoints"></a>

Azure IoT Hub はマルチテナント サービスで、さまざまなアクターの機能を公開します。次の図は、IoT Hub が公開しているさまざまなエンドポイントを示します。

![IoT Hub エンドポイント][img-endpoints]

エンドポイントの説明を次に示します。

* **リソース プロバイダー**: IoT Hub のリソース プロバイダーから、[Azure リソース マネージャー][lnk-arm]のインターフェイスが公開されています。このインターフェイスにより、Azure サブスクリプションの所有者は、IoT Hub の作成、IoT Hub のプロパティの更新、IoT Hub の削除などを行うことができます。IoT Hub のプロパティでは、デバイスレベル (「[アクセス制御](#accesscontrol)」を参照してください) のアクセス制御ではなく、ハブレベルのセキュリティ ポリシーと、C2D (クラウドからデバイス) と D2C (デバイスからクラウド) のメッセージング機能のオプションを制御します。またリソース プロバイダーにより、[デバイス ID をエクスポート](#importexport)することもできます。
* **デバイス ID の管理**: 各 IoT Hub により、デバイス ID の管理 (作成、取得、更新、削除) を行うための、一連の HTTP REST エンドポイントが公開されています。デバイス ID は、デバイスの認証とアクセス制御に使用されます。詳細については、「[デバイス ID レジストリ](#device-identity-registry)」を参照してください。
* **デバイスのエンドポイント**: デバイス ID レジストリでプロビジョニングされたデバイスごとに、デバイスがメッセージの送受信に使用できる一連のエンドポイントが IoT Hub から公開されています。
    - *D2C メッセージの送信*。このエンドポイントを使用して、D2C メッセージを送信します。詳細については、「[D2C メッセージング](#d2c)」を参照してください。
    - *C2D メッセージの受信*。デバイスは、このエンドポイントを使用して、そのデバイスが宛先となっている C2D メッセージを受信します。詳細については、「[C2D メッセージング](#c2d)」を参照してください。

    これらのエンドポイントは、HTTP、[MQTT][lnk-mqtt]、および [AMQP][lnk-amqp] プロトコルを使用して公開されます。なお、AMQP は、ポート 443 で [WebSocket][lnk-websockets] 経由で使用することもできます。
* **サービス エンドポイント**: 各 IoT Hub は、アプリケーション バックエンドがデバイスとの通信に使用できる一連のエンドポイントを公開します。これらのエンドポイントは、現在 [AMQP][lnk-amqp] プロトコルのみを使用して公開されています。
    - *D2C メッセージの受信*。このエンドポイントは [Azure Event Hubs][lnk-event-hubs] と互換性があるため、バックエンド サービスはこのエンドポイントを使用して、デバイスから送信された D2C メッセージをすべて読み取ることができます。詳細については、「[D2C メッセージング](#d2c)」を参照してください。
    - *C2D メッセージの送信と、配信の確認メッセージの受信*。これらのエンドポイントにより、アプリケーション バックエンドは、信頼性の高い C2D メッセージを送信し、対応する配信または有効期限の確認メッセージを受信できます。詳細については、「[C2D メッセージング](#c2d)」を参照してください。

[IoT Hub の API と SDK][lnk-apis-sdks] に関する記事で、これらのエンドポイントにアクセスするさまざまな方法が説明されています。

最後に注意が必要な重要事項として、すべての IoT Hub エンドポイントは、[TLS][lnk-tls] プロトコルを使用しています。また、暗号化されていない経路やセキュリティで保護されていない経路から公開されているエンドポイントはありません。

### Event Hubs と互換性のあるエンドポイントから読み取る方法<a id="eventhubcompatible"></a>

[Azure Service Bus SDK for .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) または [Event Hubs - イベント プロセッサ ホスト][]を使用する場合、適切なアクセス許可を持つ IoT Hub の接続文字列を使用することで、**messages/events** をイベント ハブの名前として使用できます。

IoT Hub を認識しない SDK (または製品の統合) を使用する場合は、[Azure ポータル][]の IoT Hub 設定から、Event Hubs と互換性のあるエンドポイントとイベント ハブの名前を取得する必要があります。

1. [IoT Hub] ブレードで、**[設定]**、**[メッセージング]** の順にクリックします。
2. **[D2C の設定]** セクションに、**[イベント ハブと互換性のあるエンドポイント]**、**[イベント ハブと互換性のある名前]**、**[パーティション]** の値があります。

    ![][img-eventhubcompatible]

> [AZURE.NOTE] SDK は、**ホスト名**または**名前空間**の値を必要とする場合があります。この場合、**[イベント ハブと互換性のあるエンドポイント]** からスキームを削除します。たとえば、Event Hub 互換のエンドポイントが ****sb://iothub-ns-myiothub-1234.servicebus.windows.net/** の場合、**Hostname** は **iothub-ns-myiothub-1234.servicebus.windows.net**、**Namespace** は **iothub-ns-myiothub-1234** です。

この場合、指定したイベント ハブに接続するための **ServiceConnect** のアクセス許可を持つ、共有アクセスのセキュリティ ポリシーを使用できます。

前の情報を使用してイベント ハブの接続文字列を作成する必要がある場合は、次のパターンを使用できます。

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

次に、IoT Hub から公開されている、Event Hub 互換のエンドポイントを使用できる SDK と統合の一覧を示します。

* [Event Hubs の Java クライアント](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm のスパウト](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。[スパウトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs)は GitHub で確認できます。
* [Apache Spark の統合](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## デバイス ID レジストリ

各 IoT Hub には、転送中の C2D メッセージを含むキューなど、サービスでデバイスごとのリソースを作成するのに使用し、「[アクセス制御](#accesscontrol)」セクションで説明するように、デバイス向けのエンドポイントにアクセスできるデバイス ID レジストリがあります。

大まかに言うと、デバイス ID レジストリは、デバイス ID リソースの REST 対応のコレクションです。次のセクションでは、デバイス ID リソースのプロパティと、レジストリで ID に対して許可されている操作について詳しく説明します。

> [AZURE.NOTE] デバイス ID レジストリの操作に使用できる HTTP プロトコルと SDK の詳細については、[IoT Hub の API と SDK][lnk-apis-sdks] に関する記事を参照してください。

### デバイス ID プロパティ<a id="deviceproperties"></a>

デバイス ID は、次のプロパティを持つ JSON ドキュメントとして表されます。

| プロパティ | オプション | 説明 |
| -------- | ------- | ----------- |
| deviceId | 必須、読み取り専用 (更新時) | ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}`。 |
| generationId | 必須、読み取り専用 | ハブによって生成された大文字と小文字が区別される文字列。最大 128 文字。これは、削除され再作成された場合に、同じ **deviceId** を持つデバイスと区別するために使用します。 |
| etag | 必須、読み取り専用 | [RFC7232][lnk-rfc7232] に従い、デバイス ID の弱い ETag を表す文字列。|
| auth | 省略可能 | 認証情報とセキュリティのマテリアルを含む複合オブジェクト。 |
| auth.symkey | 省略可能 | base64 形式でプライマリ キーとセカンダリ キーを格納する複合オブジェクト。 |
| status | 必須 | **[有効]** か **[無効]** のいずれか。**[有効]** の場合、デバイスからの接続が許可されます。**[無効]** の場合、このデバイスからデバイス向けのエンドポイントにアクセスできません。 |
| statusReason | 省略可能 | デバイス ID の状態の理由を格納する、長さが 128 文字の文字列。すべての UTF-8 文字を使用できます。 |
| statusUpdateTime | 読み取り専用 | 状態が最後に更新された日付と時刻。 |
| connectionState | 読み取り専用 | **[接続]** または **[切断]**。デバイスの接続状態に関する IoT Hub ビューを表します。**重要**: このフィールドは、開発およびデバッグ専用として使用してください。接続状態は、AMQP または MQTT を使用するデバイスについてのみ更新されます。また、この更新はプロトコル レベルの ping (MQTT ping または AMQP ping) に基づいており、最大で 5 分の遅延が生じる場合があります。このため、接続状態にあると報告されているものの実際には切断状態にあるデバイスのように、偽陽性を示す可能性があります。 |
| connectionStateUpdatedTime | 読み取り専用 | 前回接続状態が更新された日付と時刻。 |
| lastActivityTime | 読み取り専用 | 前回デバイスが接続された、またはメッセージを送受信した日付と時刻。 |

> [AZURE.NOTE] 接続状態が表すのは、その接続状態を示す IoT Hub ビューのみです。ネットワークの状態と構成によっては、この状態の更新が遅延することがあります。

### デバイス ID の操作

IoT Hub のデバイス ID レジストリでは、次の操作が公開されています。

* デバイス ID の作成
* デバイス ID の更新
* ID ごとのデバイス ID の取得
* デバイス ID の削除
* 最大 1000 個の ID の一覧表示
* BLOB ストレージへのすべての ID のエクスポート
* BLOB ストレージからの ID のインポート

これらすべての操作で、[RFC7232][lnk-rfc7232] で指定されているオプティミスティック同時実行制御を使用できます。

> [AZURE.IMPORTANT] ハブの ID レジストリにあるすべての ID を取得する唯一の方法は、[エクスポート](#importexport)機能の使用です。

IoT Hub デバイス ID レジストリ:

- アプリケーションのメタデータは含まれていません。
- **deviceId** をキーとして使用するディクショナリと同様にアクセスできます。
- 詳細なクエリはサポートされていません。

通常、IoT ソリューションにはソリューション固有のストアがあり、各ストアにはアプリケーション固有のメタデータが含まれています。たとえば、スマート ビルディング ソリューションのソリューション固有のストアでは、気温センサーをデプロイする部屋を記録します。

> [AZURE.IMPORTANT] デバイス ID レジストリは、デバイスの管理およびプロビジョニング操作でのみ使用します。実行時の高スループット操作が、デバイス ID レジストリの操作の影響を受けないようにする必要があります。たとえば、コマンドを送信する前に、デバイスの接続状態を確認するやり方は、サポートされていないパターンです。デバイス ID レジストリの[スロットル率](#throttling)と[デバイス ハートビート][lnk-guidance-heartbeat] パターンを必ず確認してください。

### デバイスの無効化

レジストリで ID の **status** プロパティを更新することにより、デバイスを無効にすることができます。通常、次の 2 つのシナリオで使用します。

- オーケストレーション プロセスのプロビジョニング中。詳細については、「[ソリューションの設計 - デバイス プロビジョニング][lnk-guidance-provisioning]」を参照してください。
- 何らかの理由でデバイスが侵害された、または許可されていないと考えられる場合。

### デバイス ID のエクスポート<a id="importexport"></a>

エクスポートは、顧客が指定した BLOB コンテナーを使用して、デバイス ID レジスタから読み取ったデバイス ID データを保存する、長時間実行されるジョブです。

[IoT Hub のリソース プロバイダーのエンドポイント](#endpoints)での非同期操作を使用して、IoT Hub の ID レジストリから一括でデバイス ID をエクスポートすることができます。

エクスポート ジョブで使用できる操作を次に示します。

* エクスポート ジョブの作成
* 実行中のジョブの状態の取得
* 実行中のジョブのキャンセル

> [AZURE.NOTE] 各ハブで一度に実行できるジョブは 1 つのみです。

インポート API とエクスポート API の詳細については、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis]に関する記事を参照してください。

インポートおよびエクスポート ジョブの実行方法の詳細については、「[Bulk management of IoT Hub device identities (IoT Hub デバイス ID の一括管理)][lnk-bulk-identity]」を参照してください。

### エクスポート ジョブ

エクスポート ジョブには次のプロパティがあります。

| プロパティ | オプション | 説明 |
| -------- | ------- | ----------- |
| jobId | システムにより生成、作成時は無視 | |
| creationTime | システムにより生成、作成時は無視 | |
| endOfProcessingTime | システムにより生成、作成時は無視 | |
| type | 読み取り専用 | **ExportDevices** |
| status | システムにより生成、作成時は無視 | **[エンキュー]**、**[開始]**、**[完了]**、**[失敗]** |
| 進捗状況 | システムにより生成、作成時は無視 | 完了の割合を示す整数値。 |
| outputBlobContainerURI | すべてのジョブで必須 | BLOB コンテナーへの書き込みアクセス権を持つ、BLOB の Shared Access Signature URI (「[BLOB サービスによる SAS の作成および使用][lnk-createuse-sas]」を参照してください)。これは、ジョブの状態と結果の出力に使用されます。 |
| excludeKeysInExport | 省略可能 | **false** の場合、キーがエクスポートの出力に含まれます。true の場合、キーは **null** としてエクスポートされます。既定値は **false** です。 |
| failureReason | システムにより生成、作成時は無視 | 状態が **[失敗]** の場合、理由を含む文字列。 |

エクスポート ジョブは、BLOB の Shared Access Signature URI をパラメーターとして取得します。これによって BLOB コンテナーに書き込みアクセス権を許可し、ジョブが結果を出力できるようにします。

このジョブで、**devices.txt** というファイルの指定した BLOB コンテナーに出力結果を書き込みます。このファイルには、「[デバイス ID プロパティ](#deviceproperties)」に示すように、JSON としてシリアル化されたデバイス ID が含まれています。**excludeKeysInExport** パラメーターを **true** に設定した場合、**devices.txt** ファイル内の各デバイスの認証値は **null** に設定されます。

**例**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
```

### デバイス ID のエクスポート

インポートは、顧客が指定した BLOB コンテナー内のデータを使用してデバイス ID データをデバイス ID レジスタに書き込む、長時間実行されるジョブです。

[IoT Hub のリソース プロバイダーのエンドポイント](#endpoints)での非同期操作を使用して、IoT Hub の ID レジストリに一括でデバイス ID をインポートすることができます。

インポート ジョブで使用できる操作を次に示します。

* インポート ジョブの作成
* 実行中のジョブの状態の取得
* 実行中のジョブのキャンセル

> [AZURE.NOTE] 各ハブで一度に実行できるジョブは 1 つのみです。

インポート API とエクスポート API の詳細については、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis]に関する記事を参照してください。

インポートおよびエクスポート ジョブの実行方法の詳細については、「[Bulk management of IoT Hub device identities (IoT Hub デバイス ID の一括管理)][lnk-bulk-identity]」を参照してください。

### インポート ジョブ

インポート ジョブには次のプロパティがあります。

| プロパティ | オプション | 説明 |
| -------- | ------- | ----------- |
| jobId | システムにより生成、作成時は無視 | |
| creationTime | システムにより生成、作成時は無視 | |
| endOfProcessingTime | システムにより生成、作成時は無視 | |
| type | 読み取り専用 | **ImportDevices** |
| status | システムにより生成、作成時は無視 | **[エンキュー]**、**[開始]**、**[完了]**、**[失敗]** |
| 進捗状況 | システムにより生成、作成時は無視 | 完了の割合を示す整数値。 |
| outputBlobContainerURI | すべてのジョブで必須 | BLOB コンテナーへの書き込みアクセス権を持つ、BLOB の Shared Access Signature URI (「[BLOB サービスによる SAS の作成および使用][lnk-createuse-sas]」を参照してください)。これは、ジョブの状態を出力するために使用されます。 |
| inputBlobContainerURI | 必須 | BLOB コンテナーへの読み込みアクセス権を持つ、BLOB の Shared Access Signature URI (「[BLOB サービスによる SAS の作成および使用][lnk-createuse-sas]」を参照してください)。ジョブは、インポートするデバイス情報をこの BLOB から読み取ります。 |
| failureReason | システムにより生成、作成時は無視 | 状態が **[失敗]** の場合、理由を含む文字列。 |

インポート ジョブは、BLOB の 2 つの Shared Access Signature URI をパラメーターとして取得します。その 1 つは、ジョブがその状態を出力できるように BLOB コンテナーに対する書き込みアクセスを許可し、もう 1 つは、ジョブがその入力データを読み取ることができるように BLOB コンテナーに対する読み取りアクセスを許可します。

インポート ジョブは、**devices.txt** というファイルの指定した BLOB コンテナーから入力データを読み取ります。このファイルには、「[デバイス ID プロパティ](#deviceproperties)」に示すように、JSON としてシリアル化されたデバイス ID が含まれています。各デバイスの既定のインポート動作は、**importMode** プロパティを追加することで、置き換えることができます。このプロパティには、次のいずれかの値を使用できます。

| importMode | 説明 |
| -------- | ----------- |
| **createOrUpdate** | 指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。<br/>該当するデバイスが既に存在する場合、既存の情報は、**ETag** 値に関係なく、指定した入力データで上書きされます。 |
| **create** | 指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。<br/>該当するデバイスが既に存在する場合は、エラーがログ ファイルに書き込まれます。 |
| **update** | 指定した **ID** を持つデバイスが既に存在する場合、**ETag** 値に関係なく、既存の情報は指定した入力データで上書きされます。<br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。 |
| **updateIfMatchETag** | 指定した **ID** を持つデバイスが既に存在する場合、**ETag** 値が一致した場合に限り、既存の情報は指定した入力データで上書きされます。<br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。<br/>**ETag** 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 |
| **createOrUpdateIfMatchETag** | 指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。<br/>該当するデバイスが既に存在する場合、**ETag** 値が一致した場合に限り、既存の情報は指定した入力データで上書きされます。<br/>**ETag** 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 |
| **delete** | 指定した **ID** を持つデバイスが既に存在する場合、そのデバイスは **ETag** 値に関係なく削除されます。<br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。 |
| **deleteIfMatchETag** | 指定した **ID** を持つデバイスが既に存在する場合、そのデバイスは **ETag** 値が一致した場合に限り削除されます。該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。<br/>ETag 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 |

**例**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"delete"}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"createOrUpdate"}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"create"}
```

## セキュリティ<a id="security"></a>

このセクションでは、Azure IoT Hub をセキュリティで保護するためのオプションについて説明します。

### アクセス制御<a id="accesscontrol"></a>

IoT Hub では、次の一連の*アクセス許可*を使用して、各 IoT Hub のエンドポイントへのアクセスを許可します。次のアクセス許可により、機能に応じて IoT Hub へのアクセスを制限します。

* **RegistryRead**。デバイス ID レジストリへの読み取りアクセス権を許可します。詳細については、「[デバイス ID レジストリ](#device-identity-registry)」を参照してください。
* **RegistryReadWrite**。デバイス ID レジストリへの読み取りと書き込みのアクセスを許可します。詳細については、「[デバイス ID レジストリ](#device-identity-registry)」を参照してください。
* **ServiceConnect**。クラウド サービス向けの通信エンドポイントと監視エンドポイントへのアクセスを許可します。たとえば、D2C メッセージの受信、C2D メッセージの送信、対応する配信確認メッセージの取得のアクセス許可をバックエンド クラウド サービスに付与します。
* **DeviceConnect**。デバイス向けの通信エンドポイントへのアクセスを許可します。たとえば、D2C メッセージの送信と、C2D メッセージの受信のアクセス許可を付与します。このアクセス許可はデバイスによって使用されます。

次の方法でアクセス許可を付与することができます。

* **ハブレベルの共有アクセス ポリシー**。共有アクセス ポリシーにより、前のセクションの一覧にあるアクセス許可を自由に組み合わせて付与できます。ポリシーは、[Azure ポータル][lnk-management-portal]で定義することも、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis] を使用してプログラムによって定義することもできます。新しく作成された IoT Hub には、次の既定のポリシーがあります。

    - *iothubowner*: すべてのアクセス許可を持つポリシー
    - *service*: **ServiceConnect** アクセス許可を持つポリシー
    - *device*: **DeviceConnect** アクセス許可を持つポリシー
    - *registryRead*: **RegistryRead** アクセス許可を持つポリシー
    - *registryReadWrite*: **RegistryRead** アクセス許可と **RegistryWrite** アクセス許可を持つポリシー

* **デバイスごとのセキュリティ資格情報**。各 IoT Hub には、[デバイス ID レジストリ](#device-identity-registry)が含まれています。このレジストリ内の各デバイスでは、対応するデバイスのエンドポイントを対象として **DeviceConnect** アクセス許可を付与する、セキュリティ資格情報を構成できます。

**例**。一般的な IoT ソリューションの場合: - デバイス管理コンポーネントは *registryReadWrite* ポリシーを使用します。 - イベント プロセッサ コンポーネントは *service* ポリシーを使用します。 - 実行時デバイス ビジネス ロジック コンポーネントは *service* ポリシーを使用します。個々のデバイスは、IoT ハブの ID レジストリに格納されている資格情報を使用して接続します。

IoT Hub のセキュリティに関するトピックのガイダンスについては、「[ソリューションの設計][lnk-guidance-security]」のセキュリティに関するセクションを参照してください。

### 認証

Azure IoT Hub では、共有アクセス ポリシーとデバイス ID レジストリのセキュリティ資格情報に対してトークンを確認することにより、エンドポイントへのアクセスを許可します。

対称キーなどのセキュリティの資格情報がネットワーク上で送信されることはありません。

> [AZURE.NOTE] Azure IoT Hub のリソース プロバイダーは、[Azure リソース マネージャー][lnk-azure-resource-manager]のすべてのプロバイダーと同様、Azure のサブスクリプションによりセキュリティで保護されています。

#### セキュリティ トークンの形式<a id="tokenformat"></a>

セキュリティ トークンには、次の形式があります。

	SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

考えられる値を次に示します。

| 値 | 説明 |
| ----- | ----------- |
| {signature} | HMAC-SHA256 署名文字列 (形式: `{URL-encoded-resourceURI} + "\n" + expiry`)。**重要**: キーは base64 からデコードされ、HMAC-SHA256 計算を実行するためのキーとして使用されます。 |
| {resourceURI} | このトークンを使用してアクセスできるエンドポイントの (セグメント単位の) URI プレフィックス。たとえば、`/events` のように指定します。 |
| {expiry} | 1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。 |
| {URL-encoded-resourceURI} | 小文字のリソース URI の小文字の URL エンコード |
| {policyName} | このトークンの参照先となる共有アクセス ポリシーの名前。デバイスとレジストリの資格情報を参照するトークンの場合は存在しません。 |

**プレフィックスに関する注意事項**: URI プレフィックスは、文字単位ではなくセグメント単位で計算されます。たとえば、`/a/b` は `/a/b/c` のプレフィックスであり、`/a/bc` のプレフィックスではありません。

署名アルゴリズムの実装は、IoT デバイス SDK およびサービス SDK にあります。

* [Java 用 IoT サービス SDK](https://github.com/Azure/azure-iot-sdks/tree/master/java/service/iothub-service-sdk/src/main/java/com/microsoft/azure/iot/service/auth)
* [Java 用 IoT デバイス SDK](https://github.com/Azure/azure-iot-sdks/tree/master/java/device/iothub-java-client/src/main/java/com/microsoft/azure/iothub/auth)
* [Node.js 用 IoT デバイス SDK およびサービス SDK](https://github.com/Azure/azure-iot-sdks/blob/master/node/common/core/lib/shared_access_signature.js)

#### プロトコルの詳細

AMQP、MQTT、HTTP など、サポートされているプロトコルごとに、さまざまな方法でトークンが転送されます。

HTTP 認証は、**Authorization** 要求ヘッダーに有効なトークンを含めることによって実装されます。**Authorization** というクエリ パラメーターでも、トークンを転送できます。

[AMQP][lnk-amqp] を使用する場合、[SASL PLAIN][lnk-sasl-plain] と [AMQP Claims-Based-Security][lnk-cbs] が IoT Hub でサポートされます。

AMQP Claims-Based-Security の場合、標準でこれらのトークンの送信方法が指定されます。

SASL PLAIN では、**ユーザー名**を以下のように指定できます。

* ハブレベルのトークンの場合、`{policyName}@sas.root.{iothubName}`
* デバイスを対象とするトークンの場合、`{deviceId}`

どちらの場合も、[トークン形式](#tokenformat)に関するセクションで説明するように、パスワード フィールドにトークンが含まれています。

MQTT を使用する場合、CONNECT パケットでは、ClientId、ユーザー名フィールドの {iothubhostname}/{deviceId}、およびパスワード フィールドの SAS トークンとして、deviceId が使用されます。{iothubhostname} は IoT の完全な CName とする必要があります (たとえば、contoso.azure-devices.net とします)。

> [AZURE.NOTE] [Azure IoT Hub の SDK][lnk-apis-sdks] を使用すると、サービスに接続した時点で自動的にトークンが生成されます。場合によっては、SDK でサポートされないプロトコルや認証方法もあります。

#### SASL PLAIN と CBS の比較

SASL PLAIN を使用する場合、IoT Hub に接続するクライアントは、TCP 接続ごとにトークンを 1 つ使用できます。トークンの有効期限が切れると、サービスから TCP 接続が切断され、再接続がトリガーされます。この動作は、アプリケーション バックエンド コンポーネントの場合は問題ありませんが、デバイス側アプリケーションの場合は、次の理由から大きな損害を及ぼす可能性があります。

*  ゲートウェイは、通常多くのデバイスの代理として接続しています。SASL PLAIN を使用する場合、IoT Hub に接続するデバイスごとに、個別の TCP 接続を作成する必要があります。これにより、電源とネットワーク リソースの消費量が大幅に増大し、各デバイスの接続の待機時間が増加します。
* 各トークンの有効期限が切れた後に、再接続に使用するリソースの量が増加すると、リソースが限られたデバイスには悪影響が出ます。

### ハブレベルの資格情報のスコープ

制限付きのリソース URI を持つトークンを作成することにより、ハブレベルのセキュリティ ポリシーのスコープを指定できます。たとえば、デバイスからの D2C メッセージを送信するエンドポイントは **/devices/{deviceId}/events** になります。また、**DeviceConnect** アクセス許可を持つハブレベルの共有アクセス ポリシーを使用して、resourceURI が **/devices/{deviceId}** であるトークンに署名し、デバイスの **deviceId** の代わりにデバイスを送信する場合にのみ使用可能なトークンを作成できます。

これは、[Event Hubs の発行元ポリシー][lnk-event-hubs-publisher-policy]に似たメカニズムで、「[ソリューションの設計][lnk-guidance-security]」のセキュリティに関するセクションの説明に従って、カスタムの認証方法を実装できます。

## メッセージング

IoT Hub には、通信を行うためのメッセージング プリミティブが用意されています。[C2D](#c2d) はアプリケーション バックエンド (*service* または *cloud*) から、[D2C](#d2c) はデバイスからアプリケーション バックエンドへの配信です。

IoT Hub のメッセージング機能の中心となる特性は、メッセージの信頼性と持続性です。これにより、デバイス側では断続的な接続に対する復元性を、クラウド側ではイベント処理の負荷の急増に対する復元性を実現できます。IoT Hub は、D2C および C2D メッセージングの両方について、*少なくとも 1 回*の配信保証を実装しています。

IoT Hub では、(AMQP や HTTP/1 などの) デバイス用のプロトコルが複数サポートされています。プロトコル間でのシームレスな相互運用性をサポートするために、IoT Hub では、すべてのデバイス用プロトコルでサポートされる共通のメッセージ形式が定義されます。

### メッセージの形式<a id="messageformat"></a>

IoT Hub のメッセージは、次の要素で構成されています。

* 一連の*システム プロパティ*。IoT Hub が解釈または設定するプロパティです。この設定は、事前に決定されています。
* 一連の*アプリケーション プロパティ*。これは、メッセージ本文を逆シリアル化しなくてもアプリケーションが定義してアクセスできる、文字列プロパティのディクショナリです。IoT Hub によってこれらのプロパティが変更されることはありません。
* 非透過的なバイナリ本文。

さまざまなプロトコルでメッセージをエンコードする方法の詳細については、[IoT Hub の API と SDK][lnk-apis-sdks] に関する記事を参照してください。

これは、IoT Hub メッセージ内の一連のシステム プロパティです。

| プロパティ | 説明 |
| -------- | ----------- |
| MessageId | 通常、要求/応答パターンに使用する、メッセージのユーザー設定 ID。形式: ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| Sequence number | IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 |
| To | 宛先を指定するために、[C2D](#c2d) メッセージで使用します。|
| ExpiryTimeUtc | メッセージの有効期限の日時。 |
| EnqueuedTime | IoT Hub でメッセージを受信した時刻。 |
| CorrelationId | 通常、要求/応答パターンで要求のメッセージ ID を格納する文字列プロパティ。 |
| UserId | メッセージの送信元の指定に使用します。IoT Hub でメッセージが生成されると、`{iot hub name}` に設定されます。 |
| Ack | デバイスがメッセージを使用した結果としてのフィードバック メッセージの生成を IoT Hub に要求するために、C2D メッセージで使用します。使用可能な値: **none** (既定値): フィードバック メッセージは生成されません。**positive**: メッセージが完了した場合にフィードバック メッセージを受信します。**negative**: デバイスでメッセージが完了しないまま、メッセージの有効期限が切れた場合 (または最大配信数に達した場合) にフィードバック メッセージを受信します。**full**: positive と negative の両方の値。詳細については、「[メッセージのフィードバック](#feedback)」を参照してください。 |
| ConnectionDeviceId | IoT Hub で D2C メッセージに設定します。メッセージを送信したデバイスの **deviceId** が含まれます。 |
| ConnectionDeviceGenerationId | IoT Hub で D2C メッセージに設定します。([デバイス ID のプロパティ](#deviceproperties)に従って) メッセージを送信したデバイスの **generationId** が含まれています。 |
| ConnectionAuthMethod | IoT Hub で D2C メッセージに設定します。メッセージを送信するデバイスの認証に使用する認証方法に関する情報。詳細については、「[なりすまし対策のプロパティ](#antispoofing)」を参照してください。|

### 通信プロトコルの選択<a id="amqpvshttp"></a>

IoT Hub では、デバイス側の通信に [AMQP][lnk-amqp]、AMQP over WebSockets、MQTT、および HTTP/1 のプロトコルをサポートしています。その使用方法に関する考慮事項の一覧を次に示します。

* **C2D のパターン**。HTTP/1 には、サーバー プッシュを実装する効率的な方法がありません。そのため、HTTP/1 を使用する場合、デバイスは、C2D メッセージの IoT Hub をポーリングします。これは、デバイスと IoT Hub の両方できわめて非効率的です。現在のガイドラインでは、HTTP/1 を使用する場合、各デバイスのポーリング間隔は 25 分以上となっています。一方、AMQP と MQTT では、C2D メッセージを受信する場合のサーバー プッシュがサポートされていて、IoT Hub からデバイスへのメッセージも即座にプッシュできます。配信の待機時間が問題となる場合は、AMQP または MQTT が使用に最適なプロトコルです。一方、頻繁に接続されないデバイスの場合は、HTTP/1 でも機能します。
* **フィールド ゲートウェイ**。HTTP/1 と MQTT を使用する場合は、同じ TLS 接続で複数のデバイス (それぞれが、デバイス独自の資格情報を有する) を接続することはできません。結果として、これらのプロトコルの場合は、フィールド ゲートウェイに接続するデバイスごとに、フィールド ゲートウェイと IoT Hub との間に TLS 接続が 1 つ必要となるため、[フィールド ゲートウェイ シナリオ][lnk-azure-gateway-guidance] を実装する際に最適なプロトコルであるとは言えません。
* **リソースの少ないデバイス**。MQTT および HTTP/1 ライブラリのフットプリントは、AMQP ライブラリの場合より小さくなります。そのため、リソースが少ないデバイス (RAM が 1 MB 未満など) の場合、MQTT と HTTP/1 が実装可能な唯一のプロトコルとなります。
* **ネットワーク トラバーサル**。MQTT Standard は、ポート 8883 でリッスンします。これにより、HTTP 以外のプロトコルに限定されているネットワークの場合は、問題が発生する可能性があります。HTTP と AMQP (over WebSockets) の両方がこのシナリオで使用できます。
* **ペイロードのサイズ**。AMQP と MQTT は、バイナリ プロトコルであり、HTTP/1 よりはるかにコンパクトです。

大まかに言えば、可能な限り AMQP (または AMQP over WebSocket) を使用するようにし、MQTT は、リソースに制約があるために AMQP を使用できない場合にのみ使用します。HTTP/1 は、ネットワーク トラバースとネットワーク構成の両方によって MQTT と AMQP の使用が妨げられている場合にのみ使用する必要があります。さらに、HTTP/1 を使用する場合、各デバイスでは 25 分以上の間隔で C2D メッセージをポーリングする必要があります。

> [AZURE.NOTE] 当然のことですが、開発段階では、ポーリングの頻度を 25 分より短く設定することができます。

#### MQTT サポートに関する留意事項
IoT Hub では、次の制限事項と特定の動作で MQTT v3.1.1 プロトコルを実装します。

  * **QoS 2 はサポートされていません**: デバイス クライアントが **QoS 2** によってメッセージを発行すると、IoT Hub はネットワーク接続を閉じます。デバイス クライアントが **QoS 2** を使用してトピックをサブスクライブしている場合、IoT Hub は **SUBACK** パケットに最大の QoS レベル 1 を許可します。
  * **保持**: デバイス クライアントが RETAIN フラグを 1 に設定してメッセージを発行すると、IoT Hub はそのメッセージに **x-opt-retain** アプリケーション プロパティを追加します。すなわち、IoT Hub は保持メッセージを保持するのでなく、バックエンド アプリケーションに渡します。

最後の考慮事項として、[Azure IoT のプロトコル ゲートウェイ][lnk-azure-protocol-gateway]に関するページを参照してください。これにより、IoT Hub と直接やり取りする高性能のカスタム プロトコル ゲートウェイをデプロイできます。Azure IoT プロトコル ゲートウェイでは、ブラウンフィールド MQTT デプロイメントまたは他のカスタム プロトコルに応じてデバイス プロトコルをカスタマイズすることができます。このアプローチの短所は、カスタム プロトコル ゲートウェイを自前でホストし運用する必要がある点です。

### D2C (デバイスからクラウド)<a id="d2c"></a>

「[エンドポイント](#endpoints)」セクションで説明するように、D2C メッセージは、デバイス向けエンドポイント (**/devices/{deviceId}/messages/events**) を介して送信され、サービス向けエンドポイント (**/messages/events**) を介して受信されます。エンドポイントは [Event Hubs][lnk-event-hubs] と互換性があるため、Standard Event Hubs の統合と SDK を使用して D2C メッセージを受信できます。

IoT Hub では、[Event Hubs][lnk-event-hubs] と類似した方法で D2C メッセージングを実装します。また IoT Hub の D2C メッセージは、[Service Bus][lnk-servicebus] の*メッセージ*よりも Event Hubs の*イベント*に類似しています。

この実装には、次のような意味があります。

* Event Hubs の*イベント*と同様に、D2C メッセージには持続性があり、最大 7 日間 IoT Hub に保持されます (「[D2C の構成オプション](#d2cconfiguration)」を参照してください)。
* D2C メッセージは、作成時に設定されるパーティションの固定セットで分割されます (「[D2C の構成オプション](#d2cconfiguration)」を参照してください)。
* Event Hubs と同様に、D2C メッセージを読み取るクライアントでは、パーティションと、チェックポイントを処理する必要があります。[Event Hubs - イベントの発行][lnk-event-hubs-consuming-events]に関するページを参照してください。
* Event Hubs のイベントと同様、D2C メッセージのサイズは最大 256 KB で、バッチとしてグループ化して送信を最適化できます。バッチでも最大 256 KB、メッセージ数にして最大 500 個です。

ただし、IoT Hub の D2C と Event Hubs には、いくつかの重要な違いがあります。

* 「[セキュリティ](#security)」セクションで説明したように、IoT Hub では、デバイスごとの認証とアクセス制御が許可されています。
* IoT Hub では、何百万ものデバイスを同時に接続できますが (「[クォータとスロットル](#throttling)」を参照してください)、Event Hubs では、名前空間ごとの AMQP 接続が 5000 に制限されています。
* IoT Hub では、**PartitionKey** を使用した任意のパーティション分割は許可されていません。D2C メッセージは、元の **deviceId** に基づいてパーティション分割されます。
* IoT Hub のスケーリングは Event Hubs と若干異なります。詳細については、「[IoT Hub のスケーリング][lnk-guidance-scale]」を参照してください。

上記の説明は、すべてのシナリオで IoT Hub が Event Hubs に代わるという意味ではないので、ご注意ください。たとえば、一部のイベントの計算処理では、データ ストリームを分析する前に、別のプロパティまたはフィールドを基準にイベントを再パーティション分割する必要があります。このシナリオでは、Event Hub を使用して、ストリーム処理パイプラインの 2 つの部分を分離できます。

D2C メッセージングの使用方法の詳細については、[IoT Hub の API と SDK][lnk-apis-sdks] に関する記事を参照してください。

> [AZURE.NOTE] HTTP を使用して D2C メッセージを送信する場合、システム プロパティの値、およびアプリケーション プロパティの名前と値の文字列には ASCII 文字のみを含めることができます。

#### 非テレメトリ トラフィック

多くの場合、デバイスからは、テレメトリ データ ポイントが送信されるだけでなく、アプリケーションのビジネス ロジック層での実行と処理を必要とするメッセージと要求も送信されます。たとえば、バックエンドで特定のアクションをトリガーする必要がある重大なアラートや、バックエンドから送信されるコマンドに対するデバイスの応答などです。

このようなメッセージを処理する最適な方法について詳しくは、「[デバイスからクラウドへのメッセージの処理][lnk-guidance-d2c-processing]」を参照してください。

#### D2C の構成オプション<a id="d2cconfiguration"></a>

IoT Hub は、D2C メッセージングを制御できる次のプロパティを公開しています。

* **パーティション数**。このプロパティは作成時に設定し、D2C イベントを取り込む場合のパーティション数を定義します。
* **保存期間**。このプロパティでは、D2C メッセージのリテンション期間を指定します。既定は 1 日ですが、7 日間に増やすことができます。

また Event Hubs と同様、IoT Hub でも、D2C の受信エンドポイントでコンシューマー グループを管理できます。

これらのプロパティはすべて、[Azure ポータル][lnk-management-portal]から変更するか、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis] からプログラムにより変更できます。

#### なりすまし対策のプロパティ<a id="antispoofing"></a>

D2C メッセージでのデバイスのなりすましを回避するために、IoT Hub では、すべてのメッセージに次のプロパティを持つスタンプが使用されます。

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

最初の 2 つには、「[デバイス ID プロパティ](#deviceproperties)」に従い、発信元デバイスの **deviceId** と **generationId** が含まれています。

**ConnectionAuthMethod** プロパティには、次のプロパティを使用してシリアル化された JSON オブジェクトが含まれています。

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### C2D (クラウドからデバイス) <a id="c2d"></a>

「[エンドポイント](#endpoints)」セクションで詳しく説明されているように、サービス向けのエンドポイント (**/messages/devicebound**) で C2D メッセージを送信し、デバイスはデバイス固有のエンドポイント (**/devices/{deviceId}/messages/devicebound**) 経由でそのメッセージを受信できます。

各 C2D メッセージで 1 つのデバイスを対象にし、**to** プロパティを **/devices/{deviceId}/messages/devicebound** に設定します。

**重要**: 各デバイスのキューで保持できる C2D メッセージは最大 50 個です。同じデバイスにそれ以上のメッセージを送信しようとすると、エラーが発生します。

> [AZURE.NOTE] C2D メッセージを送信する場合、システム プロパティの値、およびアプリケーション プロパティの名前と値の文字列には ASCII 文字のみを含めることができます。

#### メッセージのライフサイクル<a id="message lifecycle"></a>

*少なくとも 1 回*の配信の保証を実装するために、C2D メッセージはデバイスごとのキューに保持されます。そのため、IoT Hub がキューからメッセージを削除できるように、デバイスで明示的に*完了*を確認する必要があります。そうすることで、接続とデバイスの障害に対する復元性が保証されます。

次の図に、C2D メッセージのライフサイクルの状態に関するグラフを示します。

![クラウドとデバイス間のメッセージのライフ サイクル][img-lifecycle]

サービスからメッセージが送信されると、そのメッセージは*エンキュー*されたと見なされます。デバイスでメッセージを*受信*する場合、同じデバイス上の他のスレッドが他のメッセージの受信を開始できるようにするために、IoT Hub によりメッセージが*ロック*されます (状態は **[非表示]** に設定されます)。デバイスのスレッドがデバイスの処理を完了したら、メッセージを*完了*して IoT Hub にその旨を通知します。

デバイスがメッセージを*拒否*または*破棄*することもあります。拒否した場合は IoT Hub によりメッセージの状態が **[配信不能]** に設定されます。破棄した場合は IoT Hub によりメッセージがキューに戻され、状態が **[エンキュー]** に設定されます。

スレッドが IoT Hub に通知することなく、メッセージの処理に失敗することもあります。その場合、既定値の 1 分で*表示 (またはロック) がタイムアウト*し、メッセージの状態が自動的に **[非表示]** から **[エンキュー]** に戻ります。メッセージの状態は **[エンキュー]** と **[非表示]** の間で遷移することがありますが、この回数が IoT Hub の*最大配信数*のプロパティで指定された上限に達すると、IoT Hub によってメッセージの状態が **[配信不能]** に設定されます。同様に、有効期限 (「[有効期限](#ttl)」を参照) が切れた後も、IoT Hub によってメッセージの状態が **[配信不能]** に設定されます。

C2D メッセージのチュートリアルについては、[Azure IoT Hub C2D メッセージの使用][lnk-getstarted-c2d-tutorial]に関する記事を参照してください。さまざまな API と SDK が C2D 機能を公開する方法に関するリファレンス トピックについては、[IoT Hub API と SDK][lnk-apis-sdks] に関する記事を参照してください。

> [AZURE.NOTE] 通常、メッセージの損失がアプリケーション ロジックに影響しない場合、C2D メッセージは必ず完了されます。これに該当するシナリオはさまざまです。たとえば、メッセージの内容が正常にローカル ストレージに格納された場合、操作が正常に実行された場合、メッセージの損失がアプリケーションの機能に影響しないことを示す一時的な情報がメッセージに含まれている場合などがあります。長時間実行されるタスクの場合、タスクの説明をローカル ストレージ上に保持した後で C2D メッセージを完了し、その後、タスクの進行状況のさまざまな段階で、1 つ以上の D2C メッセージによってアプリケーション バックエンドに通知することができます。

#### 有効期限<a id="ttl"></a>

すべての C2D メッセージに有効期限があります。これはサービス (**ExpiryTimeUtc** プロパティ) で明示的に設定するか、IoT Hub のプロパティとして指定した既定の*有効期限*を使用して IoT Hub で設定できます。「[C2D の構成オプション](#c2dconfiguration)」を参照してください。

#### メッセージのフィードバック<a id="feedback"></a>

C2D メッセージを送信するときに、サービスは、そのメッセージの最終状態に関するメッセージごとのフィードバックの配信を要求できます。

- **Ack** プロパティを **positive** に設定すると、C2D メッセージの状態が **[完了]** に達した場合に限り、IoT Hub によりフィードバックのメッセージが生成されます。
- **Ack** プロパティを **negative** に設定すると、C2D メッセージの状態が **[配信不能]** に達した場合に限り、IoT Hub によりフィードバックのメッセージが生成されます。
- **Ack** プロパティを **full** に設定すると、上記のどちらの場合にも IoT Hub によりフィードバック メッセージが生成されます。

「[エンドポイント](#endpoints)」で説明したように、IoT Hub はメッセージとしてサービス向けエンドポイント (**/messages/servicebound/feedback**) 経由でフィードバックを配信します。フィードバックの受信セマンティクスは、同じ[メッセージのライフサイクル](#message lifecycle)を持つ C2D メッセージの場合と同様です。可能な限り、メッセージのフィードバックは、次の形式で 1 つのメッセージのバッチとして処理します。

フィードバックのエンドポイントから取得された各メッセージには、次のプロパティがあります。

| プロパティ | 説明 |
| -------- | ----------- |
| EnqueuedTime | バッチが作成された日時を示すタイムスタンプ。 |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

本文はシリアル化された JSON レコードの配列で、それぞれ次のプロパティを持っています。

| プロパティ | 説明 |
| -------- | ----------- |
| EnqueuedTimeUtc | メッセージの結果が発生した日時を示すタイムスタンプ。たとえば、デバイスの完了やメッセージの期限切れなどです。 |
| OriginalMessageId | このフィードバック情報が関連付けられている C2D メッセージの **MessageId** です。 |
| 説明 | 前の結果の文字列の値。 |
| DeviceId | フィードバックのこの部分が関連付けられている C2D メッセージの宛先デバイスの **DeviceId**。 |
| DeviceGenerationId | フィードバックのこの部分が関連付けられている C2D メッセージの宛先デバイスの **DeviceGenerationId**。 |

**重要**。メッセージのフィードバックを元のメッセージと関連付けることができるように、サービスは C2D メッセージの **MessageId** を指定する必要があります。

**例**。フィードバック メッセージの本文の例を次に示します。

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

#### C2D の構成オプション<a id="c2dconfiguration"></a>

各 IoT Hub では、C2D メッセージング用に次の構成オプションを公開しています。

| プロパティ | 説明 | 範囲と既定値 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | C2D メッセージの TTL の既定値。 | 最大 2D の ISO\_8601 書式による間隔 (最小 1 分)。既定値: 1 時間。 |
| maxDeliveryCount | デバイスごとの C2D キューの最大配信数。 | 1 ～ 100。既定値: 10。 |
| feedback.ttlAsIso8601 | サービス宛てのフィードバックのメッセージの保有期間。 | 最大 2D の ISO\_8601 書式による間隔 (最小 1 分)。既定値: 1 時間。 |
| feedback.maxDeliveryCount | フィードバック キューの最大配信数。 | 1 ～ 100。既定値: 100。 |

## クォータとスロットル<a id="throttling"></a>

各 Azure サブスクリプションに最大 10 個の IoT Hub を割り当てることができます。

各 IoT Hub は、特定の SKU のユニット数でプロビジョニングされます (詳細については、[Azure IoT Hub の価格][lnk-pricing]に関するページを参照してください)。SKU とユニット数により、送信できるメッセージの1 日あたりの最大クォータが決定されます。

また、IoT Hub がすべての操作に適用するスロットル制限は、SKU によっても決まります。

### 操作のスロットル

操作のスロットルは、ごく限られた範囲に適用される、不正使用を回避するためのレート制限です。IoT Hub は、可能な限りエラーを返さないようにしようとしますが、長時間にわたりスロットル違反が続く場合は、例外が返され始めます。

適用されるスロットルの一覧を次に示します。値は個々のハブのものです。

| スロットル | ハブごとの値 |
| -------- | ------------- |
| ID レジストリの操作 (作成、取得、一覧表示、更新、削除) | 100/分/ユニット、最大 5000/分 |
| デバイスの接続 | 120/秒/ユニット (S2 の場合)、12/秒/ユニット (S1 の場合)。最小 100/秒。 |
| デバイスからクラウドへの送信 | 120/秒/ユニット (S2 の場合)、12/秒/ユニット (S1 の場合)。最小 100/秒。 |
| クラウドからデバイスへの送信 | 100/分/ユニット |
| クラウドからデバイスへの受信 | 1000/分/ユニット |

**注**:任意の時点で、IoT Hub にプロビジョニングされたユニットを増やすことで、クォータやスロットルの制限値を増やすことができます。

**重要**: Identity レジストリの操作は、デバイスの管理とプロビジョニングのシナリオでの実行時の使用を目的としています。多数のデバイス ID の読み取りまたは更新は、[インポート/エクスポート ジョブ](#importexport)でサポートされています。

## 次のステップ

IoT Hub の開発の概要については以上です。詳細については、以下のリンク先にアクセスしてください。

- [IoT Hubs の使用 (チュートリアル)][lnk-get-started]
- [OS プラットフォームとハードウェアの互換性][lnk-compatibility]
- [Azure IoT デベロッパー センター][lnk-iotdev]
- [ソリューションの設計][lnk-guidance]

[Event Hubs - イベント プロセッサ ホスト]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Azure ポータル]: https://portal.azure.com

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md

<!---HONumber=AcomDC_0204_2016-->