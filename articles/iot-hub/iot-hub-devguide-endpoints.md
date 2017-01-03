---
title: "開発者ガイド - IoT Hub エンドポイント | Microsoft Docs"
description: "Azure IoT Hub 開発者ガイド - IoT Hub エンドポイントに関する参照情報"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: f2da46ba3fdf9386ad78ade4d2364a4a8990cd6d


---
# <a name="reference---iot-hub-endpoints"></a>リファレンス - IoT Hub エンドポイント
## <a name="list-of-iot-hub-endpoints"></a>IoT Hub エンドポイントの一覧
Azure IoT Hub はさまざまなアクターに機能を公開するマルチテナント サービスです。 次の図は、IoT Hub が公開しているさまざまなエンドポイントを示します。

![IoT Hub エンドポイント][img-endpoints]

エンドポイントの説明を次に示します。

* **リソース プロバイダー**。 IoT Hub のリソース プロバイダーは、[Azure Resource Manager][lnk-arm] のインターフェイスを公開します。このインターフェイスにより、Azure サブスクリプションの所有者は、IoT Hub を作成、削除したり、IoT Hub のプロパティを更新したりできます。 IoT Hub のプロパティでは、[ハブレベルのセキュリティ ポリシー][lnk-accesscontrol] (デバイスレベルのアクセス制御ではありません) と、Cloud-to-device (クラウドからデバイス) と Device-to-cloud (デバイスからクラウド) のメッセージング機能のオプションを管理します。 また、IoT Hub リソース プロバイダーにより、[デバイス ID をエクスポート][lnk-importexport]することもできます。
* **デバイス ID の管理**。 各 IoT Hub は、デバイス ID の管理 (作成、取得、更新、削除) を行うための、一連の HTTP REST エンドポイントを公開します。 [デバイス ID][lnk-device-identities]は、デバイスの認証とアクセス制御に使用されます。
* **デバイス ツインの管理**。 各 IoT Hub は、[デバイス ツイン][lnk-twins]のクエリと更新 (タグとプロパティの更新) を実行するサービス接続 HTTP REST エンドポイントを公開します。
* **ジョブの管理**。 各 IoT Hub は、[ジョブ][lnk-jobs]のクエリと管理を実行するサービス接続 HTTP REST エンドポイントを公開します。
* **デバイスのエンドポイント**。 IoT Hub は、ID レジストリでプロビジョニングされたデバイスごとに、デバイスがメッセージの送受信に使用できるエンドポイントを公開します。
  
  * *D2C メッセージの送信*。 このエンドポイントを使用して、[Device-to-cloud メッセージを送信します][lnk-d2c]。
  * *C2D メッセージの受信*。 デバイスは、このエンドポイントを使用して、そのデバイスが宛先となっている [Cloud-to-device メッセージ][lnk-c2d]を受信します。
  * *ファイルのアップロードの開始*。 デバイスでは、[ファイルをアップロード][lnk-upload]するために、このエンドポイントを使用して、IoT Hub から Azure Storage の SAS URI を受け取ります。
  * *デバイス ツインのプロパティを取得して更新します*。 デバイスは、このエンドポイントを使用して、その[デバイス ツイン][lnk-twins]のプロパティにアクセスします。
  * *ダイレクト メソッド要求の取得*。 デバイスは、このエンドポイントを使用して、[ダイレクト メソッド][lnk-methods]の要求をリッスンします。
    
    これらのエンドポイントは、[MQTT v3.1.1][lnk-mqtt]、HTTP 1.1、および [AMQP 1.0][lnk-amqp] の各プロトコルを使用して公開されます。 なお、AMQP は、ポート 443 で [WebSockets][lnk-websockets] 経由で使用することもできます。
    
    デバイス ツインとメソッドのエンドポイントは、[MQTT v3.1.1][lnk-mqtt]を使用してのみ利用可能です。
* **サービス エンドポイント**。 各 IoT Hub は、アプリケーション バックエンドがデバイスとの通信に使用できる一連のエンドポイントを公開します。 現時点では、これらのエンドポイントは、HTTP 1.1 経由で公開されるメソッド呼び出しエンドポイントを除いて、[AMQP][lnk-amqp] を使用してのみ公開されます。
  
  * *D2C メッセージの受信*。 このエンドポイントには、[Azure Event Hubs][lnk-event-hubs] との互換性があります。 バックエンド サービスはこのエンドポイントを使用して、デバイスから送信されたあらゆる [Device-to-cloud メッセージ][lnk-d2c]を読み取ることができます。
  * *C2D メッセージの送信と、配信の確認メッセージの受信*。 これらのエンドポイントにより、アプリケーション バックエンドは、信頼性の高い [Cloud-to-device メッセージ][lnk-c2d]を送信し、対応する配信または有効期限の確認メッセージを受信できます。
  * *ファイル通知の受信*。 このメッセージング エンドポイントにより、デバイスがファイルを正常にアップロードしたときに通知を受信できます。 
  * *ダイレクト メソッドの呼び出し*。 このエンドポイントにより、バックエンド サービスがデバイスで[ダイレクト メソッド][lnk-methods]を呼び出すことができます。

[Azure IoT SDK][lnk-sdks] に関する記事で、これらのエンドポイントにアクセスするさまざまな方法が説明されています。

最後に注意が必要な重要事項として、すべての IoT Hub エンドポイントは、[TLS][lnk-tls] プロトコルを使用しています。また、暗号化されていない経路やセキュリティで保護されていない経路から公開されているエンドポイントはありません。

## <a name="field-gateways"></a>フィールド ゲートウェイ
IoT ソリューションでは、*フィールド ゲートウェイ*はデバイスと IoT Hub エンドポイントの間に配置され、 通常はデバイスの近くにあります。 デバイスは、そのデバイスでサポートされているプロトコルを使用してフィールド ゲートウェイと直接通信します。 フィールド ゲートウェイは、IoT Hub でサポートされているプロトコルを使用して IoT Hub エンドポイントに接続します。 フィールド ゲートウェイとして使用できるのは、非常に特殊なハードウェアまたは低電力コンピューターであり、そこで実行されているソフトウェアによって、ゲートウェイが対象としているエンド ツー エンド シナリオが実現します。

[Azure IoT Gateway SDK][lnk-gateway-sdk] を使用してフィールド ゲートウェイを実装できます。 この SDK には、同じ接続経由での複数のデバイスから IoT Hub への通信を多重化する機能など、固有の機能が用意されています。

## <a name="next-steps"></a>次のステップ
この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [デバイス ツインとジョブの IoT Hub クエリ言語][lnk-devguide-query]
* [クォータと調整][lnk-devguide-quotas]
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


