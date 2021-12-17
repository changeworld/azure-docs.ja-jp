---
title: IoT Hub アウトバウンド ネットワーク アクセスとデータ損失防止を制限する
description: 開発者ガイド - 信頼できる場所にのみ送信されるように IoT Hub を構成する方法。
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: f4f8044237c82212723a54b677d77bc5aecb95a5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868740"
---
# <a name="restrict-outbound-network-access-for-azure-iot-hub"></a>Azure IoT Hub のアウトバウンド ネットワーク アクセスの制限

IoT Hub では、[カスタム エンドポイントへのルーティング](iot-hub-devguide-messages-d2c.md)、[ファイル アップロード](iot-hub-devguide-file-upload.md)、[デバイス ID エクスポート](iot-hub-bulk-identity-mgmt.md)を介して他のサービスにデータを送信できます。 エンタープライズ環境でセキュリティを強化するには、`restrictOutboundNetworkAccess` API を使用し、IoT ハブのエグレスを明示的に承認された宛先にのみ制限します。 現在のところ、この機能は Azure portal では使用できません。

## <a name="enabling-the-restriction"></a>制限の有効化

この機能を有効にするには、IoT Hub リソース プロパティを更新するメソッドを利用し (`PUT`)、`restrictOutboundNetworkAccess` を `true` に設定し、同時に完全修飾ドメイン名 (FQDN) が含まれる `allowedFqdnList` を配列として追加します。 

[create または update メソッド](/rest/api/iothub/iothubresource/createorupdate)で使用する JSON 表現を示す例:

```json
{
...
            "properties": {
              ...
                "restrictOutboundNetworkAccess": true,
                "allowedFqdnList": [
                    "my-eventhub.servicebus.windows.net",
                    "iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net"
                ]
              ...
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            }
        }
    }
}
```
Azure CLI を使用して同じ更新を行うには次を実行します。

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --set properties.restrictOutboundNetworkAccess=true properties.allowedFqdnList="['my-eventhub.servicebus.windows.net','iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net']"
```

## <a name="restricting-outbound-network-access-with-existing-routes"></a>既存のルートを使用したアウトバウンド ネットワーク アクセスの制限

`restrictOutboundNetworkAccess` が `true` に設定された場合、許可されている FQDN 以外の宛先にデータを送信しようとすると失敗します。 許可されている FQDN リストにカスタム エンドポイントが含まれていない場合、既存の構成済みルートも動作を停止します。

## <a name="built-in-endpoint"></a>組み込みのエンドポイント

`restrictOutboundNetworkAccess` が `true` に設定されている場合、組み込みのイベント ハブ対応エンドポイントは制限から除外されません。 言い換えると、引き続き動作させるには、許可されている FQDN リストに組み込みエンドポイント FQDN を含める必要があります。

## <a name="next-steps"></a>次のステップ

- データ送信にマネージド ID を使用する方法については、「[IoT Hub でのマネージド ID のサポート](iot-hub-managed-identity.md)」を参照してください。
- インバウンド ネットワーク アクセスを制限するには、[IoT ハブのパブリック ネットワーク アクセスの管理](iot-hub-public-network-access.md)と[プライベート リンクによる仮想ネットワークの IoT Hub サポート](virtual-network-support.md)に関するページを参照してください。