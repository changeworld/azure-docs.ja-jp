---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 374fcb6470ab22bfd3531d9ef543bf3cc06e17ac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131576790"
---
## <a name="generate-device-identity-certificates"></a>デバイス ID 証明書を生成する

X.509 証明書を使用した手動プロビジョニングには、バージョン 1.0.10 以降の IoT Edge が必要です。

X.509 証明書を使用して IoT Edge デバイスをプロビジョニングする場合は、"*デバイス ID 証明書*" と呼ばれるものを使用します。 この証明書は、IoT Edge デバイスのプロビジョニングと、Azure IoT Hub でのデバイス認証を行うためにのみ使用されます。 これは、他の証明書には署名しないリーフ証明書です。 デバイス ID 証明書は、IoT Edge デバイスによって検証のためにモジュールやダウンストリーム デバイスに提示される証明機関 (CA) 証明書とは別のものです。

X.509 証明書認証の場合、各デバイスの認証情報は、デバイス ID 証明書から取得した "*拇印*" の形式で提供されます。 これらの拇印は、サービスが接続時にデバイスを認識できるように、デバイスの登録時に IoT Hub に付与されます。

IoT Edge デバイスでの CA 証明書の使用方法については、「[Azure IoT Edge での証明書の使用方法について理解する](../articles/iot-edge/iot-edge-certs.md)」を参照してください。

X.509 を使用して手動でプロビジョニングするには、次のファイルが必要です。

* 2 つのデバイス ID 証明書と、それに対応する .cer または pem 形式の秘密キー証明書。

  IoT Edge ランタイムには、証明書/キー ファイルのセットが 1 つ用意されています。 デバイス ID 証明書を作成するとき、IoT ハブ内のデバイスで使用するデバイス ID を使って証明書の共通名 (CN) を設定します。

* 両方のデバイス ID 証明書から取得された拇印。

  拇印の値は SHA-1 ハッシュの場合は 40 桁の 16 進数、SHA-256 ハッシュの場合は 64 桁の 16 進数です。 両方の拇印は、デバイスの登録時に IoT Hub に提供されます。

使用できる証明書がない場合は、[デモ証明書を作成して IoT Edge デバイスの機能をテスト](../articles/iot-edge/how-to-create-test-certificates.md)できます。 この記事に記載されている手順に従って、証明書作成スクリプトを設定し、ルート CA 証明書を作成してから、2 つの IoT Edge デバイス ID 証明書を作成します。

証明書から拇印を取得する方法の 1 つは、次の openssl コマンドを使用することです。

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```
