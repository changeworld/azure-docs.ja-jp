<properties
 pageTitle="開発者ガイド - デバイス ツインについて | Microsoft Azure"
 description="Azure IoT Hub 開発者ガイド - デバイス ツインを使用して、IoT Hub とデバイス間で状態と構成を同期する"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>


# <a name="understand-device-twins---preview"></a>デバイス ツインについて - プレビュー

## <a name="overview"></a>Overview

*デバイス ツイン*は、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 この記事では、次のような内容について説明します。

* デバイス ツインの構造: *タグ*、*必要なプロパティ*、*報告されるプロパティ*、
* デバイス ツインでデバイス アプリとバックエンドが実行できる操作。

> [AZURE.NOTE] 現在のところ、デバイス ツインには MQTT プロトコルを使用して IoT Hub に接続するデバイスからのみアクセスできます。 既存のデバイス アプリを変換して MQTT を使用する方法については、[MQTT サポート](lnk-devguide-mqtt)の記事をご覧ください。

### <a name="when-to-use"></a>使用時の注意

次の場合にデバイス ツインを使用します。

* 自動販売機の配置場所など、デバイス固有のメタデータをクラウドに格納する。
* デバイス アプリで利用可能な機能や状態 (携帯電話接続や Wi-Fi 経由で接続するデバイスなど) などの現在の状態に関する情報を報告する。
* デバイス アプリとバックエンド間の実行時間の長いワークフロー (インストールする新しいファームウェアのバージョンを指定するバックエンドや、更新プロセス デバイスのさまざまな段階を報告するアプリケーションなど) の状態を同期する。
* デバイス メタデータ、構成、状態などのクエリを実行する。

時系列のセンサー データやアラームなどの、タイムスタンプの付いた連続するイベントには、[デバイスからクラウドへのメッセージ](lnk-d2c)を使用します。 ファンの電源を入れるといった、デバイスの対話型のコントロールには、[クラウドからデバイスへのメソッド][lnk-methods]を使用します。

## <a name="device-twins"></a>デバイス ツイン

デバイス ツインには、デバイスに関連する次のような情報が格納されます。

- デバイスの状態と構成を同期するために使用するデバイスとバックエンド。
- 実行時間の長い操作にクエリを行い、ターゲットを設定するために使用するアプリケーション バックエンド。

デバイス ツインのライフサイクルは、対応する[デバイス ID][lnk-identity] にリンクされています。 デバイス ツインは、新しいデバイス ID を IoT Hub で作成または削除したときに、暗黙的に作成または削除されます。

デバイス ツインは次の情報を含む JSON ドキュメントです。

* **タグ**。 バックエンドによって読み取りおよび書き込みされる JSON ドキュメントです。 タグは、デバイス アプリには表示されません。
* **必要なプロパティ**。 デバイスの構成や状態を同期するために、報告されるプロパティとともに使用します。 必要なプロパティはアプリケーションのバックエンドのみで設定でき、デバイス アプリで読み取ることができます。 デバイス アプリには、必要なプロパティの変化をリアルタイムで通知することもできます。
* **報告されるプロパティ**。 デバイスの構成や状態を同期するために、必要なプロパティとともに使用します。 報告されるプロパティはデバイス アプリのみで設定でき、アプリケーションのバックエンドで読み取ることができます。

さらに、デバイス ツインのルートには、対応する ID の読み取り専用プロパティが含まれます。このプロパティは[デバイス ID レジストリ][lnk-identity]に含まれます。

![][img-twin]

デバイス ツインの JSON ドキュメントの例を次に示します。

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

ルート オブジェクトには、システム プロパティと、`tags` のコンテナー オブジェクトと `reported` と `desired properties` の両方があります。 `properties` コンテナーには、いくつか読み取り専用の要素 (`$metadata`、`$etag`、`$version`) が含まれています。各要素については、それぞれ「[ツイン メタデータ][lnk-twin-metadata]」と「[オプティミスティック同時実行制御][lnk-concurrency]」のセクションに説明があります。

### <a name="reported-property-example"></a>報告されたプロパティの例

上の例では、デバイス アプリによって報告される `batteryLevel` プロパティがデバイス ツインに含まれています。 このプロパティでは、最後に報告されたバッテリ レベルに基づいて、デバイス上でクエリや操作を実行できます。 別の例では、デバイス アプリにデバイスの報告機能や接続オプションが備わっています。

バックエンドがプロパティの最後の既知の値を対象とする場合に、報告されたプロパティを使用するとシナリオがどのように簡略化されるのかをご確認ください。 バックエンドでタイムスタンプが付けられた連続するイベントの形式で (時系列など) デバイス テレメトリを処理する必要がある場合、[デバイスからクラウドへのメッセージ][lnk-d2c]を使用します。

### <a name="desired-configuration-example"></a>必要な構成の例

上記の例では、このデバイスのテレメトリ構成を同期するために、`telemetryConfig` の必要なプロパティと報告されたプロパティがバックエンドとデバイス アプリで使用されています。 次に例を示します。

1. アプリ バックエンドでは、必要なプロパティが必要な構成値で設定されます。 以下は、ドキュメント内の必要なプロパティを使用した部分です。

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. 接続されている場合や、最初に再接続されたときに、変更があるとデバイス アプリに直ちに通知されます。 その後、デバイス アプリが更新された構成 (またはエラー条件。`status` プロパティを使用) を報告します。 報告されたプロパティの部分を次に示します。

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. アプリのバックエンドは、デバイス ツインに[クエリを実行][lnk-query]することによって、多数のデバイス間で行われる構成操作の結果を追跡できます。

> [AZURE.NOTE] 上記のスニペットは、デバイス構成とその状態をエンコードするための例の 1 つであり、読みやすいように最適化されています。 IoT Hub は、デバイス ツインの必要なプロパティや報告されたプロパティに対して特定のスキーマを強制しません。

多くの場合、デバイス ツインは、ファームウェアの更新などの実行時間の長い操作の同期に使用されます。 デバイス間の実行時間の長い操作を同期、追跡するためのプロパティの使用方法については、「[Use desired properties to configure devices (必要なプロパティを使用してデバイスを構成する)][lnk-twin-properties]」をご覧ください。

## <a name="back-end-operations"></a>バックエンドの操作

バックエンドは、HTTP を介して公開される次の分割不可能な操作を使用して、デバイス ツイン上で動作します。

1. **ID を使用したデバイス ツインの取得** この操作は、タグ、必要なプロパティ、報告されたプロパティ、システム プロパティを含む、デバイス ツインのドキュメントの内容を返します。
2. **デバイス ツインの部分的な更新** この操作では、バックエンドによってツインのタグまたは必要なプロパティを部分的に更新できます。 部分的な更新は JSON ドキュメントとして表され、指定したプロパティが追加、更新されます。 `null` に設定されたプロパティが削除されます。 たとえば、次の場合、`{"newProperty": "newValue"}` の値を持つ新しい必要なプロパティが作成され、`existingProperty` の既存の値が `"otherNewValue"` で上書きされ、`otherOldProperty` が完全に削除されます。 その他の既存の必要なプロパティやタグは変更されません。

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **必要なプロパティの置換** この操作では、バックエンドによって既存の必要なプロパティをすべて完全に上書きし、`properties/desired` の新しい JSON ドキュメントに置き換えられます。
4. **タグの置換** 必要なプロパティの置換と同様に、この操作では、バックエンドによって既存のタグがすべて完全に上書きされ、`tags` の新しい JSON ドキュメントに置き換えられます。

上述の操作はすべて[オプティミスティック同時実行制御][lnk-concurrency]をサポートしており、「[セキュリティ][lnk-security]」記事で定義されているとおり、**ServiceConnect** アクセス許可を必要とします。

これらの操作以外に、バックエンドでは SQL に似た[クエリ言語][lnk-query]を使用してデバイス ツインにクエリを実行したり、[ジョブ][lnk-jobs]を使用して多数のデバイス ツインに操作を実行したりできます。

## <a name="device-operations"></a>デバイスの操作

デバイス アプリは、次のアトミック操作を使用して、デバイス ツイン上で動作します。

1. **デバイス ツインの取得** この操作は、タグ、必要なプロパティ、報告されたプロパティ、システム プロパティを含む、現在接続されているデバイスのデバイス ツインのドキュメントの内容を返します。
2. **報告されたプロパティの部分的な更新** この操作では、現在接続されているデバイスの報告されたプロパティを部分的に更新できます。 これは、必要なプロパティの部分的な更新を行うバックエンドと同じ JSON 更新フォーマットを使用します。
3. **必要なプロパティの監視** 現在接続されているデバイスでは、必要なプロパティの更新が行われた場合に直ちに通知をするように設定できます。 デバイスは、バックエンドによって実行される更新 (部分的または完全な置換) と同じフォームを受け取ります。

「[セキュリティ][lnk-security]」の記事に定義されているように、上述の操作にはすべて **DeviceConnect** のアクセス許可が必要です。

[Azure IoT device SDK][lnk-sdks] を使用すると、多数の言語やプラットフォームでの上述の操作を簡単に使用できます。 必要なプロパティの同期の IoT Hub プリミティブの詳細については、「[デバイスの再接続フロー][lnk-reconnection]」をご覧ください。

> [AZURE.NOTE] 現在のところ、デバイス ツインには MQTT プロトコルを使用して IoT Hub に接続するデバイスからのみアクセスできます。

## <a name="reference"></a>リファレンス

### <a name="tags-and-properties-format"></a>タグやプロパティの形式

タグ、必要なプロパティ、報告されたプロパティは JSON オブジェクトであり、次のような制限があります。

* JSON オブジェクトのキーはすべて 128 文字の UNICODE 文字列であり、大文字小文字が区別されます。 UNICODE 制御文字列 (セグメント C0 と C1)、`'.'`、`' '`、`'$'` は使用できません。
* JSON オブジェクトのすべての値には、ブール値、数値、文字列、オブジェクトの JSON 型を使用できます。 配列は使用できません。

### <a name="twin-size"></a>ツインのサイズ

読み取り専用の要素を除き、IoT Hub の `tags`、`properties/desired`、`properties/reported` の値には 8 KB のサイズ制限が強制的に適用されます。
UNICODE 制御文字 (セグメント C0 と C1) を除くすべての文字とスペース `' '` (文字列定数以外で使用されるもの) をカウントして、サイズが算出されます。
ドキュメントのサイズが上述の制限を超えると、IoT Hub はすべての操作を拒否して、エラーを返します。

### <a name="twin-metadata"></a>ツインのメタデータ

IoT Hub は、必要なプロパティと報告されたプロパティの各 JSON オブジェクトが最後に更新されたときのタイムスタンプを保持します。 タイムスタンプは UTC であり、[ISO8601] 形式の `YYYY-MM-DDTHH:MM:SS.mmmZ` でエンコードされます。
次に例を示します。

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

この情報は、オブジェクトのキーによって削除される更新を保持するために、JSON の構造のリーフだけでなくすべてのレベルで保持されます。

### <a name="optimistic-concurrency"></a>オプティミスティック同時実行制御

タグ、必要なプロパティ、報告されたプロパティはすべて、オプティミスティック同時実行制御をサポートします。
タグは、[RFC7232] に従って etag を持ちます。これは、タグの JSON 表現を表します。 これを使用すると、条件付き更新操作をバックエンドから実行し、一貫性を保持できます。

必要なプロパティと報告されたプロパティには etag はありませんが、`$version` の値によって確実に増分されます。 etag と同様に、更新側 (報告されたプロパティの場合はデバイス アプリ、必要なプロパティの場合はバックエンド) でバージョンを使用することで、整合性を保つために更新を強制的に適用できます。

バージョンは、監視エージェント (必要なプロパティを監視するデバイス アプリなど) が取得操作の結果と更新の通知の間の競合を調整する場合に便利です。 詳細は、「[デバイスの再接続フロー][lnk-reconnection]」をご覧ください。

### <a name="device-reconnection-flow"></a>デバイスの再接続フロー

IoT Hub では、必要なプロパティの更新通知は接続されていないデバイス用に保持されません。 つまり、接続しているデバイスは必要なプロパティの完全なドキュメントを取得し、さらに更新通知にサブスクライブする必要があります。 更新通知と完全取得で競合が発生する場合、次のフローを実行する必要があります。

1. デバイス アプリを IoT hub に接続する。
2. デバイス アプリを、必要なプロパティの更新通知にサブスクライブする。
3. デバイス アプリで、必要なプロパティの完全なドキュメントを取得する。

`$version` が取得された完全なドキュメントのバージョン以下の場合、デバイス アプリではすべての通知を無視できます。 IoT Hub ではバージョンが常に増分されるため、このような処理が可能になります。

> [AZURE.NOTE] このロジックは [Azure IoT device SDK][lnk-sdks] に既に実装されています。 上述の説明は、デバイス アプリが Azure IoT device SDK を使用できず、MQTT インターフェイスを直接プログラミングしなければならない場合にのみ適用されます。

### <a name="additional-reference-material"></a>参考資料

開発者ガイド内の参照トピックは次のとおりです。

- 「[IoT Hub エンドポイント](lnk-endpoints)」では、各 IoT Hub がランタイムと管理の操作のために公開する、さまざまなエンドポイントについて説明します。
- 「[クォータと調整](lnk-quotas)」では、IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
- 「[IoT Hub のデバイス SDK とサービス SDK](lnk sdk)」では、IoT Hub とやりとりするデバイスとサービス アプリケーションの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
- 「[Query language for twins, methods, and jobs (ツイン、メソッド、ジョブのクエリ言語)](lnk-query)」では、IoT Hub からデバイス ツイン、メソッド、ジョブに関する情報を取得する際に使用できるクエリ言語について説明します。
- 「[IoT Hub MQTT サポート](lnk devguide-mqtt)」では、MQTT プロトコルの IoT Hub サポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

デバイス ツインの詳細を理解できたら、次の開発者ガイド トピックも参考にしてください。

- [デバイスでダイレクト メソッドを呼び出す][lnk-methods]
- [複数デバイスでのジョブのスケジュール][lnk-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

- [デバイス ツインの使用方法][lnk-twin-tutorial]
- [ツインのプロパティの使用方法][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png


<!--HONumber=Oct16_HO2-->


