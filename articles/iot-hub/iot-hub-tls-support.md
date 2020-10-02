---
title: Azure IoT Hub の TLS サポート
description: IoT Hub と通信するデバイスとサービスにセキュリティで保護された TLS 接続を使用する際のベスト プラクティス
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jlian
ms.openlocfilehash: 08ecb766a1a9bd7ff75bf97647be811577212eb5
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006042"
---
# <a name="tls-support-in-iot-hub"></a>IoT Hub の TLS サポート

IoT Hub は、トランスポート層セキュリティ (TLS) を使用して、IoT デバイスとサービスからの接続をセキュリティで保護します。 現時点では 3 つのバージョンの TLS プロトコル (つまり、バージョン 1.0、1.1、および 1.2) がサポートされています。

TLS 1.0 と 1.1 はレガシと見なされており、非推奨となる予定です。 詳細については、[IoT Hub の TLS 1.0 および 1.1 の廃止](iot-hub-tls-deprecating-1-0-and-1-1.md)に関する記事を参照してください。 IoT Hub に接続するときは、TLS 1.2 を優先 TLS バージョンとして使用することを強くお勧めします。

## <a name="tls-12-enforcement-available-in-select-regions"></a>一部のリージョンで利用できる TLS 1.2 の強制

セキュリティを強化するために、TLS バージョン 1.2 を使用するクライアント接続 "*のみ*" を許可し、[暗号スイート](#cipher-suites)の使用を強制するように、IoT Hub を構成します。 この機能は、次のリージョンでのみサポートされています。

* 米国東部
* 米国中南部
* 米国西部 2
* US Gov アリゾナ
* US Gov バージニア州

このためには、サポートされているリージョンのいずれかに新しい IoT Hub をプロビジョニングし、Azure Resource Manager テンプレートの IoT ハブ リソース仕様で `minTlsVersion` プロパティを `1.2` に設定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

この構成を使用して作成された IoT Hub リソースは、TLS バージョン 1.0 および 1.1 を使用して接続を試みるデバイスとサービス クライアントを拒否します。 同様に、`ClientHello` メッセージに[推奨される暗号](#cipher-suites)が示されていない場合、TLS ハンドシェイクは拒否されます。

> [!NOTE]
> `minTlsVersion` プロパティは読み取り専用であり、IoT Hub リソースの作成後は変更できません。 したがって、*すべての* IoT デバイスとサービスが TLS 1.2 および[推奨される暗号](#cipher-suites)と互換性があることを、事前に適切にテストし、検証する必要があります。
> 
> フェールオーバー時、IoT Hub の `minTlsVersion` プロパティは、フェールオーバー後に geo ペア リージョンで有効なままになります。

## <a name="cipher-suites"></a>暗号スイート

TLS 1.2 のみを受け入れるように構成されている IoT Hub では、次の推奨される暗号スイートの使用も強制されます。

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

TLS 1.2 の強制が構成されていない IoT Hub では、TLS 1.2 は次の暗号スイートで引き続き動作します。

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

クライアントからは、`ClientHello` の間、上位の暗号スイートの一覧を提案できます。 ただし、IoT Hub ではサポートされないものもあります (`ECDHE-ECDSA-AES256-GCM-SHA384` など)。 その場合、IoT Hub はクライアントの設定に従おうとしますが、最終的には `ServerHello` と暗号化スイートをネゴシエートします。

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>IoT Hub SDK で TLS 1.2 を使用する

次のリンクを使用して、IoT Hub クライアント SDK で TLS 1.2 および許可されている暗号を構成します。

| Language | TLS 1.2 をサポートするバージョン | ドキュメント |
|----------|------------------------------------|---------------|
| C        | タグ 2019-12-11 以降            | [リンク](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | バージョン 2.0.0 以降             | [リンク](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | バージョン 1.21.4 以降            | [リンク](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | バージョン 1.19.0 以降            | [リンク](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | バージョン 1.12.2 以降            | [リンク](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>IoT Edge のセットアップで TLS 1.2 を使用する

IoT Edge デバイスは、IoT Hub との通信時に TLS 1.2 を使用するように構成できます。 このためには、[IoT Edge ドキュメント ページ](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)を使用してください。

## <a name="device-authentication"></a>デバイスの認証

TLS ハンドシェイクに成功すると、IoT Hub では、対称キーか X.509 証明書を利用してデバイスを認証できます。 証明書ベースの認証の場合、ECC など、あらゆる X.509 証明書を利用できます。 IoT Hub では、指定した拇印または認証機関 (CA) を利用し、証明書の有効性を検証します。 IoT Hub では X.509 ベースの相互認証 (mTLS) がまだサポートされていません。 詳細については、「[サポートされている X.509 証明書](iot-hub-devguide-security.md#supported-x509-certificates)」をご覧ください。
