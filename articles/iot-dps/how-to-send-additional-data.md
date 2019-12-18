---
title: デバイスと Azure デバイス プロビジョニング サービスの間で追加データを転送する方法
description: このドキュメントでは、デバイスとデバイス プロビジョニング サービス (DPS) の間で追加データを転送する方法について説明します。
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e9482f7069616d61efb98f66590ce33cfe3cf350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974855"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>デバイスと DPS の間で追加データを転送する方法
DPS では、デバイスを正しい IoT Hub に適切にプロビジョニングするために、デバイスの追加データが必要になる場合があり、データはそのデバイスから提供される必要があります。 逆に、DPS では、クライアント側ロジックの処理を進めるためにデバイスにデータを返すことができます。 

## <a name="when-to-use-it"></a>いつ使用するか
この機能は[カスタム割り当て](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)の拡張機能として使用できます。 たとえば、人間が介入することなく、デバイス モデルに基づいてデバイス名を割り当てる必要があります。 この場合は[カスタム割り当て](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)を使用します。 [登録デバイス呼び出し](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)の一部としてモデル情報を報告するようにデバイスを構成します。 デバイスの情報が DPS からカスタム割り当て webhook に渡されます。 また、関数では、モデル情報を受け取ると、このデバイスをプロビジョニングする IoT Hub を判別できます。 同様に、webhook からデバイスに何らかのデータを返す場合は、webhook 応答内の文字列としてそのデータを渡します。  

## <a name="device-sends-data-to-dps"></a>デバイスが DPS にデータを送信する
デバイスが[デバイスの登録呼び出し](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)を DPS に送信しているときに、本文内の他のフィールドを取得するように登録呼び出しを拡張できます。 本文は次のようになります。 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS がデバイスにデータを返す
カスタム割り当てポリシー webhook からデバイスに何らかのデータを返す場合は、webhook 応答内の文字列としてそのデータを渡します。 この変更は、以下の「returnData」セクションにあります。 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK のサポート
この機能は、C、C#、JAVA および Node.js の[クライアント SDK](https://docs.microsoft.com/azure/iot-dps/) で使用できます。  

## <a name="next-steps"></a>次の手順
* Azure IoT Hub および Azure IoT Hub Device Provisioning Service 用に [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) を使用する開発
