---
title: Device Update for IoT Hub ネットワークの要件 |Microsoft Docs
description: Device Update for IoT Hub は、さまざまなネットワーク ポートをそれぞれの目的で使用します。
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558382"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Device Update for IoT Hub が使用するポート
ADU は、さまざまなネットワーク ポートをそれぞれの目的で使用します。

## <a name="default-ports"></a>既定のポート

目的|ポート番号 |
---|---
ネットワーク/CDN からのダウンロード  | 80 (HTTP プロトコル)
MCC/CDN からのダウンロード | 80 (HTTP プロトコル)
Azure IoT Hub への ADU エージェント接続  | 8883 (MQTT プロトコル)

## <a name="use-azure-iot-hub-supported-protocols"></a>IoT Hub でサポートされるプロトコルの使用
ADU エージェントは、サポートされている Azure IoT Hub プロトコルを使用するように変更することができます。

サポートされているプロトコルの現在の一覧については、[こちら](../iot-hub/iot-hub-devguide-protocols.md)を参照してください。
