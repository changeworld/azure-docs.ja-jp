---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579262"
---
IoT プラグ アンド プレイを使用すると、Azure IoT アプリケーションに機能を宣伝するスマート デバイスをビルドできます。 IoT プラグ アンド プレイ デバイスでは、ユーザーが IoT プラグ アンド プレイ対応アプリケーションに接続するときに、手動で構成する必要がありません。

スマート デバイスは、直接実装されているか、[モジュール](../articles/iot-hub/iot-hub-devguide-module-twins.md)を使用しているか、[IoT Edge モジュール](../articles/iot-edge/about-iot-edge.md)を使用している可能性があります。

このガイドでは、[IoT プラグ アンド プレイ規則](../articles/iot-pnp/concepts-convention.md)に従ってデバイス、モジュール、または IoT Edge モジュールを作成するために必要な基本的な手順について説明します。

IoT プラグ アンド プレイ デバイス、モジュール、または IoT Edge モジュールを構築するには、次の手順に従います。

1. デバイスでは、MQTT または MQTT over WebSockets プロトコルを使用して Azure IoT Hub に接続していることを確認します。
1. デバイスを記述する [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) モデルを作成します。 詳細については、[IoT プラグ アンド プレイ モデルのコンポーネントの概要](../articles/iot-pnp/concepts-components.md)に関するページを参照してください。
1. デバイス接続の一部として `model-id` をアナウンスするようにデバイスまたはモジュールを更新します。
1. [IoT プラグ アンド プレイ規則](../articles/iot-pnp/concepts-convention.md)を使用してテレメトリ、プロパティ、コマンドを実装します

デバイスまたはモジュールの実装の準備ができたら、[Azure IoT エクスプローラー](../articles/iot-pnp/howto-use-iot-explorer.md)を使用して、デバイスが IoT プラグ アンド プレイ規則に従っていることを確認します。
