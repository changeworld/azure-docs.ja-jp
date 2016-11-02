<properties
 pageTitle="IoT Hub の MQTT サポート | Microsoft Azure"
 description="IoT Hub レベルでの MQTT サポートに関する説明"
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
 ms.date="10/05/2016"
 ms.author="dobett"/>


# <a name="iot-hub-mqtt-support"></a>IoT Hub の MQTT サポート

IoT Hub により、デバイスはポート 8883 で [MQTT v3.1.1][lnk-mqtt-org] プロトコルを使用し、IoT Hub デバイス エンドポイントと通信できます。 IoT Hub では、すべてのデバイス通信を TLS/SSL で保護する必要があります。

## <a name="connecting-to-iot-hub"></a>IoT Hub への接続

MQTT プロトコルを使用してデバイスを IoT Hub に接続するには、[Microsoft Azure IoT SDK][lnk-device-sdks] のライブラリを使用するか、MQTT プロトコルを直接使用します。

## <a name="using-the-device-client-sdks"></a>デバイス クライアント SDK の使用

MQTT プロトコルをサポートする[デバイス クライアント SDK][lnk-device-sdks] は、Java、Node.js、C、C # に対応しています。 デバイス クライアント SDK では、標準的な IoT Hub 接続文字列を使用して、IoT Hub への接続を確立します。 MQTT プロトコルを使用するには、クライアント プロトコル パラメーターを **MQTT**に設定する必要があります。 デバイス クライアント SDK は既定では、**CleanSession** フラグを **0** に設定して IoT Hub に接続し、IoT Hub とのメッセージ交換には **QoS 1** を使用します。

デバイスは、IoT Hub に接続されると、デバイス クライアント SDK によって提供されるメソッドにより、IoT Hub との間でメッセージの送受信ができるようになります。

次の表では、サポートされている各言語のコード サンプルへのリンクを提供すると共に、MQTT プロトコルを使用して IoT Hub への接続を確立するために使用するパラメーターを示します。

| 言語                   | プロトコル パラメーター        |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrate-a-device-app-from-amqp-to-mqtt"></a>デバイス アプリを AMQP から MQTT へ移行する
[デバイス クライアント SDK][lnk-device-sdks] を使用している場合、AMQP から MQTT に切り替えるには前述のようにクライアントの初期化でプロトコル パラメーターを変更する必要があります。

これを行う際は、次の項目をご確認ください。

* AMQP では、MQTT 接続を終了するときに、多くの条件のエラーが返されます。 そのため、例外処理のロジックを一部変更する必要が生じることがあります。
* MQTT は、[C2D メッセージ][lnk-messaging]の受信時の*拒否*操作をサポートしていません。 バックエンドでデバイス アプリからの応答を受信する必要がある場合は、[ダイレクト メソッド][lnk-methods]の使用をご検討ください。
* 現時点では、WebSocket 経由で MQTT は使用できません。

## <a name="using-the-mqtt-protocol-directly"></a>MQTT プロトコルの直接使用

デバイスでデバイス クライアント SDK を使用できない場合でも、MQTT プロトコルを使用してデバイスをパブリックのデバイス エンドポイントに接続できます。 **接続** パケットで、デバイスは次の値を使用する必要があります。

- **ClientId** フィールドには、**deviceId** を使用します。 
- **[Usename]** フィールドには、`{iothubhostname}/{device_id}` を使用します。{iothubhostname} は IoT Hub の完全な CName です。

    たとえば、IoT Hub の名前が **contoso.azure-devices.net** であり、デバイスの名前が **MyDevice01** であるとすると、**Username** フィールドの内容は `contoso.azure-devices.net/MyDevice01` になります。

- **[Password]** フィールドには、SAS トークンを使用します。 SAS トークンの形式は、HTTP プロトコルや AMQP プロトコルの場合と同じです。<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`に設定する必要があります。

    SAS トークンの生成方法の詳細については、「[IoT Hub セキュリティ トークンの使用][lnk-sas-tokens]」のデバイス セクションをご覧ください。
    
    テストするときは、[デバイス エクスプローラー][lnk-device-explorer] ツールを使用して SAS トークンをすばやく生成し、それをコピーして独自のコードに貼り付けることもできます。
    
    1. デバイス エクスプローラーで **[管理]** タブに移動します。
    2. **[SAS トークン]** (右上) をクリックします。
    3. **[SASTokenForm]** の **[DeviceID]** ドロップダウンでデバイスを選択します。 **[TTL]**を設定します。
    4. **[生成]** をクリックしてトークンを作成します。
    
    生成される SAS トークンは、次のような構成になります。  `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

    MQTT を使用して接続するために、このトークンの一部を **[Password]  `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802` フィールドとして使用できます: **

MQTT の接続パケットおよび切断パケットの場合、IoT Hub は **操作の監視** チャネルでイベントを発行します。

### <a name="sending-messages-to-iot-hub"></a>IoT Hub へのメッセージの送信

接続に成功したら、デバイスから IoT Hub に `devices/{device_id}/messages/events/` または `devices/{device_id}/messages/events/{property_bag}` を**トピック名**として使用してメッセージを送信できます。 `{property_bag}` 要素を使用すると、デバイスは追加のプロパティ付きのメッセージを URL エンコード形式で送信できるようになります。 次に例を示します。

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] この `{property_bag}` 要素は、HTTP プロトコルでクエリ文字列に使用したのと同じエンコーディングを使用します。

また、デバイス クライアント アプリケーションは、 `devices/{device_id}/messages/events/{property_bag}` を **Will トピック名** として使用することで、テレメトリ メッセージとして転送される *Will メッセージ* を定義することもできます。

### <a name="receiving-messages"></a>メッセージの受信

IoT Hub からメッセージを受信するには、デバイスで、`devices/{device_id}/messages/devicebound/#”` を**トピック フィルター**として使用してサブスクライブする必要があります。 IoT Hub は、**トピック名** `devices/{device_id}/messages/devicebound/` またはメッセージ プロパティがある場合は `devices/{device_id}/messages/devicebound/{property_bag}` のメッセージを配信します。 `{property_bag}` には、メッセージ プロパティの URL でエンコードされた値/キーのペアが含まれています。 プロパティ バッグに含められるのは、アプリケーション プロパティとユーザーが設定可能なシステム プロパティ (**messageId**、**correlationId** など) のみです。 システム プロパティの名前にはプレフィックス **$** が付きます。アプリケーション プロパティでは、プレフィックスのない元々のプロパティ名が使用されます。

## <a name="next-steps"></a>次のステップ

詳細については、Azure IoT Hub 開発者ガイドの「[MQTT サポートに関する留意事項][lnk-mqtt-devguide]」をご覧ください。

MQTT プロトコルの詳細については、[MQTT のドキュメント][lnk-mqtt-docs]をご覧ください。

IoT Hub のデプロイの計画に関する詳細については、以下をご覧ください。

- [サポートされるデバイス][lnk-devices]
- [その他のプロトコルのサポート][lnk-protocols]
- [Event Hubs との比較][lnk-compare]
- [HA と DR のスケーリング][lnk-scaling]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [開発者ガイド][lnk devguide]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


