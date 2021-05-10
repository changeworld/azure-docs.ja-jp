---
title: Azure IoT Hub のエラー 429001 ThrottlingException のトラブルシューティング
description: エラー 429001 ThrottlingException を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 9619a3d2ba24e806f6c684a5576bce03d7e6b793
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060977"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

この記事では、**429001 ThrottlingException** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub に対する要求がエラー **429001 ThrottlingException** で失敗します。

## <a name="cause"></a>原因

要求された操作で IoT Hub の[調整制限](./iot-hub-devguide-quotas-throttling.md)を超えました。

## <a name="solution"></a>解決策

"*Telemetry message send attempts*" (テレメトリ メッセージ送信試行数) メトリックを前述の指定した制限と比較して、調整制限に達しているかどうかを確認します。 また、"*Number of throttling errors*" (調整エラーの数) メトリックも確認できます。 これらのメトリックの詳細については、「[デバイス テレメトリのメトリック](monitor-iot-hub-reference.md#device-telemetry-metrics)」を参照してください。 メトリックを使用して IoT ハブを監視する方法の詳細については、「[IoT Hub の監視](monitor-iot-hub.md)」を参照してください。

IoT Hub から 429 ThrottlingException が返されるのは、制限に違反した期間が長すぎる場合のみです。 これは、IoT ハブのトラフィックがバーストした場合、メッセージが削除されないようにするためです。 一方、IoT Hub では操作スロットル レートでメッセージが処理されるため、バックログで大量のトラフィックがあると、速度が低下する可能性があります。 詳細については、[IoT Hub のトラフィック シェイプ](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

クォータまたはスロットルの上限に達している場合は、[IoT ハブのスケールアップ](./iot-hub-scaling.md)を検討してください