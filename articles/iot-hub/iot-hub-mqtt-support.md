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
 ms.date="07/19/2016"
 ms.author="dobett"/>

# IoT Hub の MQTT サポート

IoT Hub により、デバイスはポート 8883 で [MQTT v3.1.1][lnk-mqtt-org] プロトコルを使用し、IoT Hub デバイス エンドポイントと通信することができます。IoT Hub では、すべてのデバイス通信を TLS/SSL で保護する必要があります。

## IoT Hub への接続

MQTT プロトコルを使用してデバイスを IoT Hub に接続するには、[Microsoft Azure IoT SDK][lnk-device-sdks] のライブラリを使用するか、または MQTT プロトコルを直接使用します。

## デバイス クライアント SDK の使用

MQTT プロトコルをサポートする[デバイス クライアント SDK][lnk-device-sdks] は、Java、Node.js、C、および C # に対応しています。デバイス クライアント SDK では、標準的な IoT Hub 接続文字列を使用して、IoT Hub への接続を確立します。MQTT プロトコルを使用するには、クライアント プロトコル パラメーターを **MQTT** に設定する必要があります。デバイス クライアント SDK は既定では、**CleanSession** フラグを **0** に設定して IoT Hub に接続し、IoT Hub とのメッセージ交換には **QoS 1** を使用します。

デバイスは、IoT Hub に接続されると、デバイス クライアント SDK によって提供されるメソッドにより、IoT Hub との間でメッセージの送受信ができるようになります。

次の表では、サポートされている各言語のコード サンプルへのリンクを提供すると共に、MQTT プロトコルを使用して IoT Hub への接続を確立するために使用するパラメーターを示します。

| 言語 | プロトコル パラメーター |
| -------------------------- | ------------------------- |
| [Node.JS][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

## MQTT プロトコルの直接使用

デバイスでデバイス クライアント SDK を使用できない場合でも、MQTT プロトコルを使用してデバイスをパブリックのデバイス エンドポイントに接続できます。**接続**パケットで、デバイスは次の値を使用する必要があります。

- **ClientId** フィールドには、**deviceId** を使用します。
- **Username** フィールドには、`{iothubhostname}/{device_id}` を使用します。{iothubhostname} は IoT Hub の完全な CName です。

    たとえば、IoT Hub の名前が **contoso.azure-devices.net** であり、デバイスの名前が **MyDevice01** であるとすると、**Username** フィールドの内容は `contoso.azure-devices.net/MyDevice01` になります。

- **Password** フィールドには、SAS トークンを使用します。SAS トークンの形式は、HTTP および AMQP プロトコルの場合と同じです。<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

    SAS トークンの生成方法の詳細については、「[IoT Hub セキュリティ トークンの使用][lnk-sas-tokens]」のデバイス セクションをご覧ください。
    
    テストをするときは、[デバイス エクスプローラー][lnk-device-explorer] ツールを使用して SAS トークンを簡単に生成し、それをコピーして独自のコードに貼り付けることもできます。
    
    1. デバイス エクスプローラーで **[管理]** タブに移動します。
    2. **[SAS トークン]** (右上) をクリックします。
    3. **[SASTokenForm]** の **[DeviceID]** ドロップダウンでデバイスを選択します。**[TTL]** を設定します。
    4. **[生成]** をクリックしてトークンを作成します。
    
    次のような SAS トークンが生成されます: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

    MQTT を使用して接続するために、この SAS トークンの一部を **Password** フィールドとして使用できます: `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`

MQTT の接続パケットおよび切断パケットの場合、IoT Hub は**操作の監視**チャネルでイベントを発行します。

### IoT Hub へのメッセージの送信

接続に成功したら、デバイスは `devices/{device_id}/messages/events/` または `devices/{device_id}/messages/events/{property_bag}` を**トピック名**として使用して IoT Hub にメッセージを送信できます。`{property_bag}` 要素を使用すると、デバイスは追加のプロパティ付きのメッセージを URL エンコード形式で送信できるようになります。次に例を示します。

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] これは、HTTP プロトコルでクエリ文字列に使用したのと同じエンコーディングです。

また、デバイス クライアント アプリケーションは、`devices/{device_id}/messages/events/{property_bag}` を **Will トピック名**として使用することで、テレメトリ メッセージとして転送される *Will メッセージ*を定義することもできます。

### メッセージの受信

IoT Hub からメッセージを受信するには、デバイスが `devices/{device_id}/messages/devicebound/#”` を**トピック フィルター**として使用してサブスクライブする必要があります。IoT Hub はメッセージを**トピック名** `devices/{device_id}/messages/devicebound/` または `devices/{device_id}/messages/devicebound/{property_bag}` (メッセージ プロパティがある場合) で送信します。`{property_bag}` には、メッセージ プロパティの URL エンコードされたキーと値のペアが含まれます。プロパティ バッグに含められるのは、アプリケーション プロパティとユーザーが設定可能なシステム プロパティ (**messageId**、**correlationId** など) のみです。システム プロパティの名前にはプレフィックス **$** が付きます。アプリケーション プロパティでは、プレフィックスのない元々のプロパティ名が使用されます。

## 次のステップ

IoT デバイス SDK を使用した MQTT サポートの詳細については、「Azure IoT Hub 開発者ガイド」の「[MQTT サポートに関する留意事項][lnk-mqtt-devguide]」をご覧ください。

MQTT プロトコルの詳細については、[MQTT のドキュメント][lnk-mqtt-docs]を参照してください。

IoT Hub のデプロイの計画に関する詳細については、以下をご覧ください。

- [サポートされているデバイス][lnk-devices]
- [その他のプロトコルのサポート][lnk-protocols]
- [Event Hubs との比較][lnk-compare]
- [HA と DR のスケーリング][lnk-scaling]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [開発者ガイド][lnk-devguide]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0720_2016-->