---
title: Azure IoT Hub のエラー 404001 DeviceNotFound のトラブルシューティング
description: エラー 404001 DeviceNotFound を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061283"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

この記事では、**404001 DeviceNotFound** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

C2D メッセージ、ツイン更新、ダイレクト メソッドなどの cloud-to-device (C2D) 通信中に、操作がエラー **404001 DeviceNotFound** で失敗します。

## <a name="cause"></a>原因

IoT Hub によってデバイスが検出されなかったため、操作に失敗しました。 デバイスが登録されていないか、無効になっています。

## <a name="solution"></a>解決策

使用したデバイス ID を登録してから、再試行してください。