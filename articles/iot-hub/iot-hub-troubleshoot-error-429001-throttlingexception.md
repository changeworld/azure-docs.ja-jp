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
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960264"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

この記事では、**429001 ThrottlingException** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub に対する要求がエラー **429001 ThrottlingException** で失敗します。

## <a name="cause"></a>原因

要求された操作で IoT Hub の[調整制限](./iot-hub-devguide-quotas-throttling.md)を超えました。

## <a name="solution"></a>解決策

"*Telemetry message send attempts*" (テレメトリ メッセージ送信試行数) メトリックを前述の指定した制限と比較して、調整制限に達しているかどうかを確認します。 また、"*Number of throttling errors*" (調整エラーの数) メトリックも確認できます。 これらのメトリックと IoT Hub に使用できるその他のメトリックの詳細については、「[IoT Hub メトリックとその使用方法](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)」を参照してください。

IoT Hub から 429 ThrottlingException が返されるのは、制限に違反した期間が長すぎる場合のみです。 これは、IoT ハブのトラフィックがバーストした場合、メッセージが削除されないようにするためです。 一方、IoT Hub では操作スロットル レートでメッセージが処理されるため、バックログで大量のトラフィックがあると、速度が低下する可能性があります。 詳細については、[IoT Hub のトラフィック シェイプ](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

クォータまたはスロットルの上限に達している場合は、[IoT ハブのスケールアップ](./iot-hub-scaling.md)を検討してください