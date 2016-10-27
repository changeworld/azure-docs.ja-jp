<properties
 pageTitle="開発者ガイド - メッセージング | Microsoft Azure"
 description="Azure IoT Hub 開発者ガイド - デバイスからクラウドおよびクラウドからデバイスへのメッセージ"
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
 ms.date="09/30/2016" 
 ms.author="dobett"/>


# <a name="send-and-receive-messages-with-iot-hub"></a>IoT Hub でのメッセージの送受信

## <a name="overview"></a>Overview

IoT Hub には、デバイスと通信するための次のメッセージング プリミティブが備わっています。

- [D2C][lnk-d2c]: デバイスからアプリケーション バックエンドへ。
- [C2D][lnk-c2d]: アプリケーション バックエンド (*service* または *cloud*) から。

IoT Hub のメッセージング機能の中心となる特性は、メッセージの信頼性と持続性です。 これらのプロパティにより、デバイス側では断続的な接続に対する復元性を、クラウド側ではイベント処理の負荷の急増に対する復元性を実現できます。 IoT Hub では、D2C および C2D メッセージングの両方について、 *少なくとも 1 回* の配信保証が実装されます。

IoT Hub では、(MQTT、AMQP、HTTP などの) [デバイス用のプロトコル][lnk-protocols]が複数サポートされています。 プロトコル間でのシームレスな相互運用性をサポートするために、IoT Hub では、すべてのデバイス用プロトコルでサポートされる[共通のメッセージ形式][lnk-message-format]が定義されます。

IoT Hub では [Event Hubs と互換性のあるエンドポイント][lnk-compatible-endpoint] が公開されるため、ハブから受け取ったデバイスからクラウドへのメッセージをバックエンド アプリケーションで読み取れるようになります。

### <a name="when-to-use"></a>使用時の注意

メッセージングは、IoT Hub の中核的な機能です。 デバイスからバックエンドにメッセージを送信したり、バックエンドからデバイスにメッセージを送信する場合には、この機能を使用します。

IoT Hub と Event Hubs サービスの比較については、「[IoT Hub と Event Hubs の比較][lnk-compare]」をご覧ください。

## <a name="device-to-cloud-messages"></a>デバイスからクラウドへのメッセージ

デバイスからのクラウドへのメッセージはデバイス向けのエンドポイント (**/devices/{deviceId}/messages/events**) を介して送信されます。 バックエンド サービスはサービス向けのエンドポイント (**/messages/events**) を介してデバイスからクラウドへのメッセージを受け取ります。このエンドポイントは [Event Hubs][lnk-event-hubs] と互換性を持ちます。 そのため、Standard [Event Hubs の統合と SDK][lnk-compatible-endpoint] を使用して D2C メッセージを受信できます。

IoT Hub は、[Event Hubs][lnk-event-hubs] と類似した方法で D2C メッセージングを実装します。 IoT Hub の D2C メッセージは、[Service Bus][lnk-servicebus] の*メッセージ*よりも Event Hubs の*イベント*に類似しています。

この実装には、次のような意味があります。

* Event Hubs のイベントと同様に、D2C メッセージには持続性があり、最長で 7 日間 IoT Hub に保持されます (「[D2C の構成オプション][lnk-d2c-configuration]」をご覧ください)。
* D2C メッセージは、作成時に設定されるパーティションの固定セットにまたがって分割されます (「[D2C の構成オプション][lnk-d2c-configuration]」をご覧してください)。
* Event Hubs と同様に、D2C メッセージを読み取るクライアントでは、パーティションと、チェックポイントを処理する必要があります。 [Event Hubs - イベントの発行][lnk-event-hubs-consuming-events]に関するページをご覧ください。
* Event Hubs のイベントと同様、D2C メッセージは最大 256 KB で、バッチとしてグループ化して送信を最適化できます。 バッチのサイズは最大で 256 KB、メッセージの数は最大 500 個です。

ただし、IoT Hub の D2C のメッセージングと Event Hubs には、いくつかの重要な違いがあります。

* 「[IoT Hub へのアクセスの制御][lnk-devguide-security]」セクションで説明したように、IoT Hub では、デバイスごとの認証とアクセス制御が許可されています。
* IoT Hub では、何百万ものデバイスを同時に接続できますが (「[クォータとスロットル][lnk-quotas]」をご覧ください)、Event Hubs では、名前空間ごとの AMQP 接続が 5000 に制限されています。
* IoT Hub では、 **PartitionKey**を使用した任意のパーティション分割は許可されていません。 D2C メッセージは、発信元の **deviceId**に基づいてパーティション分割されます。
* IoT Hub のスケーリングは Event Hubs と若干異なります。 詳細については、「[IoT Hub のスケーリング][lnk-guidance-scale]」をご覧ください。

> [AZURE.NOTE] すべてのシナリオで、IoT Hub を Event Hubs の代わりに使用することはできません。 たとえば、一部のイベントの計算処理では、データ ストリームを分析する前に、別のプロパティまたはフィールドを基準にイベントを再パーティション分割する必要があります。 このシナリオでは、Event Hub を使用して、ストリーム処理パイプラインの 2 つの部分を分離できます。 詳細については、「[Azure Event Hubs の概要][lnk-eventhub-partitions]」の「*パーティション*」をご覧ください。

D2C メッセージングの使用方法の詳細については、[IoT Hub の API と SDK][lnk-sdks] に関する記事をご覧ください。

> [AZURE.NOTE] HTTP を使用して D2C メッセージを送信する場合、プロパティ名と値に含めることができるのは、ASCII 英数字と ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` のみです。

### <a name="non-telemetry-traffic"></a>非テレメトリ トラフィック

多くの場合、デバイスからは、テレメトリ データ ポイントが送信されるだけでなく、アプリケーションのビジネス ロジック層での実行と処理を必要とするメッセージと要求も送信されます。 たとえば、バックエンドで特定のアクションをトリガーする必要がある重大なアラートや、バックエンドから送信されるコマンドに対するデバイスの応答などです。

この種類のメッセージを処理する最適な方法の詳細については、「[チュートリアル: IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-d2c-tutorial]」をご覧ください。

### <a name="device-to-cloud-configuration-options"></a>D2C の構成オプション

IoT Hub は、D2C メッセージングを制御できる次のプロパティを公開しています。

* **パーティション数**。 このプロパティは作成時に設定し、D2C イベントを取り込む場合のパーティション数を定義します。
* **保存期間**。 このプロパティでは、D2C メッセージのリテンション期間を指定します。 既定は 1 日ですが、7 日間に増やすことができます。

また Event Hubs と同様、IoT Hub でも、D2C の受信エンドポイントでコンシューマー グループを管理できます。

これらのプロパティはすべて、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis] を使用してプログラムにより変更するか、[Azure Portal][lnk-management-portal] を使用して変更できます。

### <a name="anti-spoofing-properties"></a>なりすまし対策のプロパティ

D2C メッセージでのデバイスのなりすましを回避するために、IoT Hub では、すべてのメッセージに次のプロパティを持つスタンプが使用されます。

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

最初の 2 つには、「[デバイス ID プロパティ][lnk-device-properties]」に従い、発信元デバイスの **deviceId** と **generationId** が含まれています。

**ConnectionAuthMethod** プロパティには、次のプロパティを使用してシリアル化された JSON オブジェクトが含まれています。

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>クラウドからデバイスへのメッセージ

C2D メッセージはサービス向けエンドポイント (**/messages/devicebound**) を介して送信できます。 デバイスはそのメッセージをデバイス固有のエンドポイント (**/devices/{deviceId}/messages/devicebound**) を介して受信します。

個々の C2D メッセージの宛先は単一のデバイスであり、**to** プロパティが **/devices/{deviceId}/messages/devicebound** に設定されます。

>[AZURE.IMPORTANT] 各デバイスのキューで保持できる C2D メッセージは最大 50 個です。 同じデバイスにそれ以上のメッセージを送信しようとすると、エラーが発生します。

> [AZURE.NOTE] C2D メッセージを送信するとき、プロパティ名と値に含めることができるのは、ASCII 英数字と ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` のみです。

### <a name="message-lifecycle"></a>メッセージのライフサイクル

少なくとも 1 回のメッセージ配信を保証するために、IoT Hub は C2D デバイス メッセージをデバイスごとのキューに保持します。 IoT Hub がキューからメッセージを削除できるように、デバイスで明示的に *完了* を確認する必要があります。 そうすることで、接続とデバイスの障害に対する復元性が保証されます。

次の図に、C2D メッセージのライフサイクルの状態に関するグラフを示します。

![クラウドとデバイス間のメッセージのライフ サイクル][img-lifecycle]

サービスからメッセージが送信されると、そのメッセージは *エンキューされた*と見なされます。 デバイスでメッセージを*受信*する場合、同じデバイス上の他のスレッドが他のメッセージの受信を開始できるようにするために、IoT Hub によりメッセージが*ロック*されます (状態は**非表示**に設定されます)。 デバイスのスレッドがメッセージの処理を完了すると、メッセージを *完了* して IoT Hub にその旨を通知します。

デバイスは次の動作をすることもあります。

- メッセージの*拒否*。この場合、IoT Hub によってメッセージの状態が**配信不能**に設定されます。 注: MQTT に接続するデバイスは、C2D メッセージを拒否することはできません。
- メッセージの*破棄*。この場合、IoT Hub によってメッセージがキューに戻され、状態が**エンキュー**に設定されます。

スレッドが IoT Hub に通知することなく、メッセージの処理に失敗することもあります。 その場合、*表示 (またはロック) がタイムアウト*になった後で、メッセージの状態が自動的に**非表示**から**エンキュー**に切り替わります。 このタイムアウトの既定値は 1 分です。

メッセージの状態は**エンキュー**と**非表示**の間で切り替わりますが、この回数が IoT Hub の**最大配信数**のプロパティで指定された上限に達すると、 IoT Hub によってメッセージの状態が**配信不能**に設定されます。 同様に、有効期限 ([有効期限][lnk-ttl]に関するセクションを参照) が切れた後も、IoT Hub によってメッセージの状態が**配信不能**に設定されます。

クラウドからデバイスへのメッセージのチュートリアルは、「[チュートリアル: IoT Hub でクラウドからデバイスへのメッセージを送信する方法][lnk-c2d-tutorial]」をご覧ください。 さまざまな API と SDK が C2D 機能を公開する方法に関するリファレンス トピックについては、[IoT Hub API と SDK][lnk-sdks] に関する記事をご覧ください。

> [AZURE.NOTE] 通常、メッセージの損失がアプリケーション ロジックに影響しない場合、C2D メッセージは必ず完了されます。 たとえば、メッセージの内容が正常にローカル ストレージで保持されている場合、操作が正常に実行された場合などがあります。 また、メッセージには、メッセージの損失がアプリケーションの機能に影響しないことを示す一時的な情報が含まれている場合もあります。 実行時間の長いタスクの場合、タスクの説明をローカル ストレージに保持した後で C2D メッセージを完了できます。 その後、タスクの進行状況のさまざまな段階で、1 つ以上の D2C メッセージによりアプリケーション バックエンドに通知できます。

### <a name="message-expiration-(time-to-live)"></a>メッセージの有効期限

すべての C2D メッセージに有効期限があります。 これはサービス (**ExpiryTimeUtc** プロパティ) で設定するか、IoT Hub のプロパティとして指定した既定の *time to live* を使用して IoT Hub で設定できます。 「[C2D の構成オプション][lnk-c2d-configuration]」をご覧ください。

> [AZURE.NOTE] メッセージの有効期限を利用し、接続されていないデバイスにメッセージが送信されないようにするには、一般的には、有効期限に短い値を設定します。 この方法を使用すると、デバイスの接続状態を維持するのと同じ結果が得られますが、より効率化されます。 メッセージの受信確認を要求すると、メッセージを受信できるデバイスや、オンラインではないデバイスまたは障害が発生しているデバイスについて IoT Hub から通知を受け取ります。

### <a name="message-feedback"></a>メッセージのフィードバック

C2D メッセージを送信するときに、サービスは、そのメッセージの最終状態に関するメッセージごとのフィードバックの配信を要求できます。

- **Ack** プロパティを **positive** に設定すると、C2D メッセージの状態が**完了**に達した場合に限り、IoT Hub によりフィードバック メッセージが生成されます。
- **Ack** プロパティを **negative** に設定すると、C2D メッセージの状態が**配信不能**に達した場合に限り、IoT Hub によりフィードバック メッセージが生成されます。
- **Ack** プロパティを **full** に設定すると、上記のどちらの場合にも IoT Hub によりフィードバック メッセージが生成されます。

> [AZURE.NOTE] **Ack** が **full** の場合で、フィードバック メッセージを受信しない場合は、フィードバック メッセージの有効期限が切れたことを意味します。 このとき、元のメッセージがどうなったかをサービスが認識することはできません。 実際には、有効期限切れになる前にフィードバックをサービスが確実に処理できることが必要です。 有効期限は最長 2 日間であるため、障害が発生した場合も、サービスを再び稼働させる時間は十分にあります。

「[エンドポイント][lnk-endpoints]」で説明したように、IoT Hub はサービス向けエンドポイント (**/messages/servicebound/feedback**) 経由で、メッセージとしてフィードバックを配信します。 フィードバックを受信するためのセマンティクスは C2D メッセージの場合と同様であり、[メッセージのライフサイクル][lnk-lifecycle]も同じです。 可能な限り、メッセージのフィードバックは、次の形式で 1 つのメッセージのバッチとして処理します。

| プロパティ | Description |
| -------- | ----------- |
| EnqueuedTime | メッセージが作成された日時を示すタイムスタンプ。 |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

本文はシリアル化された JSON レコードの配列で、それぞれ次のプロパティを持っています。

| プロパティ | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | メッセージの結果が発生した日時を示すタイムスタンプ。 たとえば、デバイスの完了やメッセージの期限切れなどです。 |
| OriginalMessageId | **MessageId** です。 |
| StatusCode | 必須の整数です。 IoT Hub によって生成されたフィードバック メッセージで使用されます。 <br/> 0 = 成功 <br/> 1 = メッセージの有効期限が切れました <br/> 2 = 最大配信数を超えました <br/> 3 = メッセージは拒否されました |
| 説明 | **StatusCode**の文字列値。 |
| deviceId | **DeviceId** 。 |
| DeviceGenerationId | **DeviceGenerationId** 。 |


>[AZURE.IMPORTANT] メッセージのフィードバックを元のメッセージと関連付けることができるように、サービスは C2D メッセージの **MessageId** を指定する必要があります。

次の例は、フィードバック メッセージの本文を示しています。

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
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

### <a name="cloud-to-device-configuration-options"></a>C2D の構成オプション

各 IoT Hub では、C2D メッセージング用に次の構成オプションを公開しています。

| プロパティ | Description | 範囲と既定値 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | C2D メッセージの TTL の既定値。 | 最大 2D の ISO_8601 書式による間隔 (最小 1 分)。 既定値: 1 時間。 |
| maxDeliveryCount | デバイスごとの C2D キューの最大配信数。 | 1 ～ 100。 既定値: 10。 |
| feedback.ttlAsIso8601 | サービス宛てのフィードバックのメッセージの保有期間。 | 最大 2D の ISO_8601 書式による間隔 (最小 1 分)。 既定値: 1 時間。 |
| feedback.maxDeliveryCount | フィードバック キューの最大配信数。 | 1 ～ 100。 既定値: 100。 |

詳細については、「[IoT Hubs の作成][lnk-portal]」をご覧ください。

## <a name="read-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの読み取り

IoT Hub は、バックエンド サービスにエンドポイントを公開して、ハブで受信したデバイスからクラウドへのメッセージを読み取ります。 エンドポイントは Event Hubs との互換性を持ち、Event Hubs がメッセージの読み取りでサポートするすべてのメカニズムを使用できるようになります。

[Azure Service Bus SDK for .NET][lnk-servicebus-sdk] または [Event Hubs - イベント プロセッサ ホスト][lnk-eventprocessorhost]を使用する場合、適切なアクセス許可があれば任意の IoT Hub の接続文字列を使用することができます。 これにより、 **メッセージやイベント** を Event Hub の名前として使用できます。

IoT Hub を認識しない SDK (または製品の統合) を使用する場合は、[Azure Portal][lnk-management-portal]の IoT Hub 設定から、Event Hubs と互換性のあるエンドポイントとイベント ハブの名前を取得する必要があります。

1. [IoT Hub] ブレードで、 **[メッセージング]**をクリックします。
2. **[Device-to-cloud settings (D2C の設定)]** セクションに、**[Event Hub-compatible endpoint (イベント ハブと互換性のあるエンドポイント)]**、**[Event Hub-compatible name (イベント ハブと互換性のある名前)]**、**[パーティション]** の値があります。

    ![Device-to-cloud settings][img-eventhubcompatible]

> [AZURE.NOTE] SDK で **Hostname** または **Namespace** の値が必要な場合は、**[Event Hub-compatible endpoint (イベント ハブと互換性のあるエンドポイント)]** からスキームを削除します。 たとえば、Event Hub 互換のエンドポイントが **sb://iothub-ns-myiothub-1234.servicebus.windows.net/** の場合、**Hostname** は **iothub-ns-myiothub-1234.servicebus.windows.net**、**Namespace** は **iothub-ns-myiothub-1234** です。

この場合、指定したイベント ハブに接続するための **ServiceConnect** のアクセス許可を持つ、共有アクセスのセキュリティ ポリシーを使用できます。

前の情報を使用してイベント ハブの接続文字列を作成する必要がある場合は、次のパターンを使用できます。

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

次に、IoT Hub から公開されている、Event Hub 互換のエンドポイントを使用できる SDK と統合の一覧を示します。

* [Event Hubs の Java クライアント](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm のスパウト](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。 [スパウトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs) は GitHub で確認できます。
* [Apache Spark の統合](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference"></a>リファレンス

### <a name="message-format"></a>メッセージの形式

IoT Hub のメッセージは、次の要素で構成されています。

* 一連の *システム プロパティ*。 IoT Hub が解釈または設定するプロパティです。 この設定は、事前に決定されています。
* 一連の *アプリケーション プロパティ*。 メッセージ本文を逆シリアル化しなくてもアプリケーションが定義してアクセスできる、文字列プロパティのディクショナリです。 IoT Hub によってこれらのプロパティが変更されることはありません。
* 非透過的なバイナリ本文。

さまざまなプロトコルでメッセージをエンコードする方法の詳細については、[IoT Hub の API と SDK][lnk-sdks] に関する記事をご覧ください。

次のテーブルは、IoT Hub メッセージ内の一連のシステム プロパティです。

| プロパティ | Description |
| -------- | ----------- |
| MessageId | 要求/応答パターンに使用する、メッセージのユーザー設定 ID。 形式: ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| Sequence number | IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 |
| To | [C2D][lnk-c2d] メッセージで指定される宛先。 |
| ExpiryTimeUtc | メッセージの有効期限の日時。 |
| EnqueuedTime | IoT Hub でメッセージを受信した日時。 |
| CorrelationId | 通常、要求/応答パターンで要求の MessageId を格納する、応答メッセージの文字列プロパティ。 |
| UserId | メッセージの送信元を指定するために使用される ID。 IoT Hub でメッセージが生成されると、 `{iot hub name}`に設定されます。 |
| Ack | フィードバック メッセージのジェネレーター。 このプロパティは、デバイスがメッセージを使用した結果としてのフィードバック メッセージの生成を IoT Hub に要求するために、C2D メッセージで使用されます。 使用可能な値: **none** (既定値): フィードバック メッセージは生成されません。**positive**: メッセージが完了した場合にフィードバック メッセージを受信します。**negative**: デバイスでメッセージが完了しないまま、メッセージの有効期限が切れた場合 (または最大配信数に達した場合) にフィードバック メッセージを受信します。**full**: positive と negative の両方の値を意味します。 詳細については、「[メッセージのフィードバック][lnk-feedback]」をご覧ください。 |
| ConnectionDeviceId | IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **deviceId** が含まれます。 |
| ConnectionDeviceGenerationId | IoT Hub で D2C メッセージに対して設定される ID。 ([デバイス ID のプロパティ][lnk-device-properties]に従って) メッセージを送信したデバイスの **generationId** が含まれています。 |
| ConnectionAuthMethod | IoT Hub で D2C メッセージに対して設定される認証方法。 このプロパティには、メッセージを送信するデバイスの認証に使用する認証方法に関する情報が含まれます。 詳細については、[D2C のなりすまし対策][lnk-antispoofing]に関する記述をご覧ください。|

### <a name="communication-protocols"></a>通信プロトコル

IoT Hub では、デバイス側の通信に MQTT、[AMQP][lnk-amqp]、AMQP over WebSockets および HTTP/1 の各プロトコルをサポートしています。 プロトコル選択に関する高度な推奨事項には次のようなものがあります。

| プロトコル | そのプロトコルを選択しなければならない場合 |
| -------- | ------------------------------------ |
| MQTT     | WebSocket を使用する必要がないすべてのデバイスで使用します。 |
| AMQPS    | デバイスを多重化する接続を活用するために、フィールド ゲートウェイとクラウド ゲートウェイで使用します。 <br/>  ポート 443 に接続する必要がある場合に使用します。 |
| HTTPS    | その他のプロトコルをサポートできないデバイスに使用します。 |

デバイス側の通信用プロトコルを選択する場合、次の点を考慮してください。

* **C2D のパターン**。 HTTP/1 には、サーバー プッシュを実装する効率的な方法がありません。 そのため、HTTP/1 を使用する場合、デバイスは IoT Hub に対して C2D メッセージのポーリングを行います。 この方法は、デバイスと IoT Hub の両方で非効率的です。 現在の HTTP/1 ガイドラインでは、各デバイスによるメッセージのポーリングの間隔は 25 分以上となっています。 一方、MQTT と AMQP では、C2D メッセージを受信する場合のサーバー プッシュがサポートされています。 IoT Hub からデバイスへのメッセージも即座にプッシュできます。 配信の待機時間が問題となる場合は、AMQP または MQTT が使用に最適なプロトコルです。 頻繁に接続されないデバイスの場合は、HTTP/1 でも対応できます。
* **フィールド ゲートウェイ**。 HTTP/1 と MQTT を使用する場合は、同じ TLS 接続で複数のデバイス (それぞれが、デバイス独自の資格情報を有する) を接続することはできません。 したがって、[フィールド ゲートウェイ シナリオ][lnk-azure-gateway-guidance]の場合は、フィールド ゲートウェイに接続するデバイスごとにフィールド ゲートウェイと IoT Hub の間に TLS 接続が 1 つ必要となるため、これらのプロトコルは次善の策です。
* **リソースの少ないデバイス**。 MQTT および HTTP/1 ライブラリのフットプリントは、AMQP ライブラリの場合より小さくなります。 そのため、リソースが限られたデバイス (RAM が 1 MB 未満など) の場合、MQTT と HTTP/1 が実装可能な唯一のプロトコルとなります。
* **ネットワーク トラバーサル**。 MQTT Standard はポート 8883 でリッスンします。このため、ネットワークが HTTP 以外のプロトコルに限定されている場合、問題が発生する可能性があります。 HTTP と AMQP (over WebSockets) の両方がこのシナリオで使用できます。
* **ペイロードのサイズ**。 AMQP と MQTT は、バイナリ プロトコルであり、HTTP/1 よりペイロードがコンパクトです。

> [AZURE.NOTE] HTTP/1 を使用する場合、各デバイスでは 25 分以上の間隔で C2D メッセージをポーリングする必要があります。 ただし、開発段階では、ポーリングの頻度を 25 分より短く設定することができます。

### <a name="port-numbers"></a>ポート番号

デバイスは、さまざまなプロトコルを使用して Azure の IoT Hub と通信できます。 通常、プロトコルの選択は、ソリューションの特定の要件によって左右されます。 次の表に、デバイスが特定のプロトコルを使用できるようにするために開く必要がある送信ポートを示します。

| プロトコル | ポート |
| -------- | ------- |
| MQTT     | 8883    |
| AMQP     | 5671    |
| AMQP over WebSocket | 443    |
| HTTPS    | 443     |
| LWM2M (デバイス管理) | 5684 |

Azure リージョンに IoT Hub を作成すると、そのハブはハブの存続期間中同じ IP アドレスを保持します。 ただし、サービス品質を維持するため、Microsoft によって IoT Hub が別のスケール ユニットに移動されると、新しい IP アドレスが割り当てられます。

### <a name="notes-on-mqtt-support"></a>MQTT サポートに関する留意事項

IoT Hub では、次の制限事項と特定の動作で MQTT v3.1.1 プロトコルを実装します。

  * **QoS 2 はサポートされていません**。 デバイス クライアントが **QoS 2**によってメッセージを発行すると、IoT Hub はネットワーク接続を閉じます。 デバイス クライアントが **QoS 2** を使用してトピックをサブスクライブしている場合、IoT Hub は **SUBACK** パケットに最大の QoS レベル 1 を許可します。
  * **メッセージの保持は維持されません**。 デバイス クライアントが RETAIN フラグを 1 に設定してメッセージを発行すると、IoT Hub はそのメッセージに **x-opt-retain** アプリケーション プロパティを追加します。 この場合は、IoT Hub は保持メッセージを保持するのでなく、バックエンド アプリケーションに渡します。

詳細については、「[IoT Hub の MQTT サポート][lnk-devguide-mqtt]」をご覧ください。

最後の考慮事項として、[Azure IoT Hub プロトコル ゲートウェイ][lnk-azure-protocol-gateway]に関するページを確認してください。これにより、IoT Hub と直接やり取りする高性能のカスタム プロトコル ゲートウェイをデプロイできます。 Azure IoT プロトコル ゲートウェイでは、ブラウンフィールド MQTT デプロイメントまたは他のカスタム プロトコルに応じてデバイス プロトコルをカスタマイズすることができます。 ただし、このアプローチでは、カスタム プロトコル ゲートウェイを自分でホストし、運用する必要があります。

### <a name="additional-reference-material"></a>参考資料

開発者ガイド内の他の参照トピックは次のとおりです。

- 「[IoT Hub エンドポイント][lnk-endpoints]」では、各 IoT Hub がランタイムと管理の操作のために公開する、さまざまなエンドポイントについて説明します。
- 「[クォータと調整][lnk-quotas]」では、IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
- 「[IoT Hub のデバイス SDK とサービス SDK][lnk sdk]」では、IoT Hub とやりとりするデバイスとサービス アプリケーションの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
- 「[Query language for twins, methods, and jobs (ツイン、メソッド、ジョブのクエリ言語)][lnk-query]」では、IoT Hub からデバイス ツイン、メソッド、ジョブに関する情報を取得する際に使用できるクエリ言語について説明します。
- 「[IoT Hub MQTT サポート][lnk devguide-mqtt]」では、MQTT プロトコルの IoT Hub サポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

IoT Hub でメッセージを送受信する方法を理解できたら、次の開発者ガイドのトピックも参考にしてください。

- [デバイスからのファイルのアップロード][lnk-devguide-upload]
- [IoT Hub でのデバイス ID の管理][lnk-devguide-identities]
- [IoT Hub へのアクセスの制御][lnk-devguide-security]
- [デバイス ツインを使って状態と構成を同期する][lnk-devguide-device-twins]
- [デバイスでダイレクト メソッドを呼び出す][lnk devguide-directmethods]
- [複数デバイスでのジョブのスケジュール][lnk-devguide-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

- [Azure IoT Hub を使ってみる][lnk-getstarted-tutorial]
- [チュートリアル: IoT Hub でクラウドからデバイスへのメッセージを送信する方法][lnk-c2d-tutorial]
- [チュートリアル: IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: https://www.amqp.org/
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md


<!--HONumber=Oct16_HO2-->


