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
 ms.date="02/03/2016"
 ms.author="dobett"/>

# IoT Hub の MQTT サポート

IoT Hub により、デバイスはポート 8883 で [MQTT v3.1.1][lnk-mqtt-org] プロトコルを使用し、IoT Hub デバイス エンドポイントと通信することができます。IoT Hub では、すべてのデバイス通信を TLS/SSL で保護する必要があります。

詳細については、Azure IoT Hub 開発者ガイドの「[MQTT サポートに関する留意事項][lnk-mqtt-devguide]」をご覧ください。

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

## MQTT プロトコルの直接使用

デバイスでデバイス クライアント SDK を使用できない場合でも、MQTT プロトコルを使用してデバイスをパブリックのデバイス エンドポイントに接続できます。**接続**パケットで、デバイスは次の値を使用する必要があります。

- **ClientId** フィールドには、**deviceId** を使用します。 
- **Username** フィールドには、`{iothubhostname}/{device_id}` を使用します。{iothubhostname} は IoT Hub の完全な CName です。

    たとえば、IoT Hub の名前が **contoso.azure-devices.net** であり、デバイスの名前が **MyDevice01** であるとすると、**Username** フィールドの内容は `contoso.azure-devices.net/MyDevice01` になります。

- **Password** フィールドには、SAS トークンを使用します。[SAS トークンの形式][lnk-iothub-security]は、HTTP および AMQP プロトコルの場合と同じで、次のようになります。<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

    SAS トークンの生成方法の詳細については、「[IoT Hub セキュリティ トークンの使用][lnk-sas-tokens]」を参照してください。
    
    テストをするときは、[デバイス エクスプローラー][lnk-device-explorer] ツールを使用して SAS トークンを簡単に生成し、それをコピーして独自のコードに貼り付けることもできます。
    
    1. デバイス エクスプローラーで **[管理]** タブに移動します。
    2. **[SAS トークン]** (右上) をクリックします。
    3. **[SASTokenForm]** の **[DeviceID]** ドロップダウンでデバイスを選択します。**[TTL]** を設定します。
    4. **[生成]** をクリックしてトークンを作成します。
    
    次のような SAS トークンが生成されます: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

    MQTT を使用して接続するために、この SAS トークンの一部を **Password** フィールドとして使用できます: `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`

MQTT の接続パケットおよび切断パケットの場合、IoT Hub は**操作の監視**チャネルでイベントを発行します。

### IoT Hub へのメッセージの送信

接続に成功すると、デバイスは `devices/{did}/messages/events/` または `devices/{did}/messages/events/{property_bag}` を**トピック名**として使用して IoT Hub にメッセージを送信できるようになります。`{property_bag}` 要素を使用すると、デバイスは追加のプロパティ付きのメッセージを URL エンコード形式で送信できるようになります。次に例を示します。

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] これは、HTTP プロトコルでクエリ文字列に使用したのと同じエンコーディングです。

また、デバイス クライアント アプリケーションは、`devices/{did}/messages/events/{property_bag}` を **Will トピック名**として使用することで、*Will メッセージ*をテレメトリ メッセージとして転送されるように定義することもできます。

### メッセージの受信

IoT Hub からメッセージを受信するには、デバイスで、`devices/{did}/messages/devicebound/#”` を**トピック フィルター**として使用してサブスクライブする必要があります。IoT Hub はメッセージを**トピック名** `devices/{did}/messages/devicebound/` または `devices/{did}/messages/devicebound/{property_bag}` (プロパティがある場合) で送信します。`{property_bag}` には、メッセージ プロパティの URL エンコードされたキー/値ペアが入ります。プロパティ バッグに含められるのは、アプリケーション プロパティとユーザーが設定可能なシステム プロパティ (**messageId**、**correlationId** など) のみです。システム プロパティの名前にはプレフィックス **$** が付きます。アプリケーション プロパティでは、プレフィックスのない元々のプロパティ名が使用されます。

## 次のステップ

デバイス クライアント SDK を使用して IoT Hub と通信する方法の詳細については、[Azure IoT Hub の使用][lnk-iot-get-stated]に関する記事をご覧ください。

MQTT プロトコルの詳細については、[MQTT のドキュメント][lnk-mqtt-docs]を参照してください。

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

<!---HONumber=AcomDC_0406_2016-->