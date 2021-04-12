---
title: Azure IoT Hub のエラー 412002 DeviceMessageLockLost のトラブルシューティング
description: エラー 412002 DeviceMessageLockLost を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: eb3d7f15109d3b217f651a7d927601ef7fae66c2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061028"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

この記事では、**412002 DeviceMessageLockLost** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

cloud-to-device メッセージを送信しようとすると、要求がエラー **412002 DeviceMessageLockLost** で失敗します。

## <a name="cause"></a>原因

デバイスが (たとえば [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) を使用して) cloud-to-device メッセージをキューから受信すると、1 分間のロック タイムアウト期間、IoT Hub によってメッセージはロックされます。 ロック タイムアウトが終了した後にデバイスでメッセージの完了が試行されると、IoT Hub からこの例外がスローされます。

## <a name="solution"></a>解決策

1 分間のロック タイムアウト期間内に IoT Hub が通知を受け取らないと、メッセージは、"*エンキュー済み*" の状態に戻されます。 デバイスは、メッセージの受信を再試行できます。 今後このエラーが発生しないようにするには、メッセージを受け取ってから 1 分以内にメッセージを完了するようにデバイス側のロジックを実装します。 この 1 分間のタイムアウトを変更することはできません。