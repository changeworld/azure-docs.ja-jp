---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879203"
---
## <a name="iot-plug-and-play-device"></a>IoT プラグ アンド プレイ デバイス

IoT プラグ アンド プレイ デバイスは、データを収集したり他のデバイスを制御したりすることが可能で、[デバイス機能モデル](#device-capability-model)に宣言された機能を実装するソフトウェアまたはファームウェアを実行する、一般的には小規模なスタンドアロン コンピューティング デバイスです。  環境監視デバイス (スマート農業灌漑システムのコントローラー) は、IoT プラグ アンド プレイ デバイスの一例です。 IoT プラグ アンド プレイ デバイスを指示し、制御し、このデバイスからのデータを受信するように、クラウドでホストされる IoT ソリューションを作成できます。 IoT プラグ アンド プレイ デバイスは、[Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)から見つけることができます。 カタログ内の各 IoT プラグ アンド プレイ デバイスは検証済みであり、[デバイス機能モデル](#device-capability-model)を備えています。

## <a name="digital-twin"></a>デジタル ツイン

デジタル ツインは、IoT プラグ アンド プレイ デバイスのモデルです。  デジタル ツインは、[デジタル ツイン定義言語](https://aka.ms/DTDL)を使用してモデル化されています。  Azure IoT API を使用して、デジタル ツインを操作できます。 

## <a name="digital-twin-definition-language"></a>デジタル ツイン定義言語

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)用のモデルとインターフェイスを記述する言語です。  [デジタル ツイン](#digital-twin)の機能を記述したり、IoT プラットフォームと IoT ソリューションでエンティティのセマンティクスを活用できるようにしたりするには、[デジタル ツイン定義言語](https://aka.ms/DTDL)を使用します。

## <a name="device-capability-model"></a>デバイス機能モデル

デバイス機能モデルでは、デバイスを記述し、そのデバイスによって実装される一連のインターフェイスを定義します。 物理デバイス、製品、または SKU に対応するデバイス機能モデルを作成します。

## <a name="interface"></a>インターフェイス

インターフェイスでは、デバイスまたはデジタル　ツインによって実装される関連機能を記述します。 インターフェイスは、さまざまな機能モデルで再利用できます。

## <a name="property"></a>プロパティ

プロパティは、デジタル ツインの何らかの状態を表す[インターフェイス](#interface)で定義されたデータ フィールドです。 プロパティは、読み取り専用または書き込み可能として宣言できます。 読み取り専用プロパティは、IoT プラグ アンド プレイ デバイス自体のコンテキストで実行されるコードによって設定されます (シリアル番号など)。  書き込み可能なプロパティは、外部エンティティによって設定されます (アラームのしきい値など)。

## <a name="telemetry"></a>テレメトリ

[インターフェイス](#interface)で定義されているテレメトリ フィールドは、測定値を表します。 これらの測定値は、通常、センサーの読み取りなどの値です。

## <a name="command"></a>command

[インターフェイス](#interface)で定義されたコマンドは、デジタル ツインで実行できるメソッドを表します。 たとえば、デバイスをリセットするためのリセット コマンドです。
