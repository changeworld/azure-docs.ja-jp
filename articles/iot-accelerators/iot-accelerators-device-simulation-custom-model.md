---
title: カスタム デバイス モデルの構成 - Azure | Microsoft Docs
description: この記事では、デバイス シミュレーション ソリューション アクセラレータでカスタム デバイス モデルを構成する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967493"
---
# <a name="device-models"></a>デバイス モデル

シミュレーションを構成する場合、定義済みの一連のセンサーを搭載した既存のデバイス モデルを使用することも、任意のシミュレーション センサーを搭載したカスタム デバイス モデルを作成することもできます。 カスタム センサーを使用すると、実際のデバイスをより正確に表すモデル化を行うことができます。

## <a name="pre-configured-device-models"></a>構成済みのデバイス モデル

デバイス シミュレーション ソリューション アクセラレータは、冷却装置、エレベーター、およびトラックという 3 つの構成済みのデバイス モデルを提供します。

構成済みデバイス モデルには、高度な動作が JavaScript ファイルで定義された複数のセンサーが含まれます。 Web UI では、これらの動作を構成することはできません。

次の表では、各構成済みデバイス モデルの構成を示します。

| デバイスのモデル  | センサー           | 単位  |
| ------------- | ---------------- | ----- |
| 冷却装置       | 湿度         | %     |
|               | pressure         | psig  |
|               | 温度      | F     |
| エレベーター      | 床            |       |
|               | 振動        | MM    |
|               | 気温      | F     |
| トラック         | Latitude         |       |
|               | Longitude        |       |
|               | 速度            | mph   |
|               | 積み荷の温度 | F     |

## <a name="custom-device-models"></a>カスタム デバイス モデル

カスタム デバイス モデルを使うと、実際のデバイスをより正確に表すセンサーをモデル化できます。 カスタム デバイスでは、最大 10 個のカスタム センサーを使うことができます。

カスタム デバイス モデル タイプを選んだら、**[+Add sensor] (+ センサーの追加)** をクリックして新しいセンサーを追加します。

[![センサーの構成](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

カスタム センサーには次のプロパティがあります。

| フィールド     | 説明 |
| --------- | ----------- |
| Sensor Name (センサー名) | センサーのわかりやすい名前です (例: **温度**、**速度**)。  |
| 動作  | 動作を使うと、メッセージごとにテレメトリ データを変えて、実際のデータをシミュレートできます。 **[Increment]\(増加\)** は、最小値から始めて、送信されるメッセージごとに値を 1 ずつ増やします。 最大値に達したら、再び最小値から開始します。 **[Decrement]\(減少\)** は **[Increment]\(増加\)** と同じ方法で、最大値から値を減らします。 **[Random]\(ランダム\)** 動作は、最小値と最大値の間でランダムな値を生成します。 |
| Min Value (最小値) | 許容範囲を表す下限の値です。 |
| Max Value (最大値) | 許容範囲を表す上限の値です。 |
| 単位      | °F や MPH など、センサーの測定の単位です。 |

## <a name="next-steps"></a>次の手順

このガイドでは、シミュレーション用のカスタム デバイス モデルを構成する方法を学びました。 次に、他のいくつかの [IoT ソリューション アクセラレータ](about-iot-accelerators.md)を試してみてください。