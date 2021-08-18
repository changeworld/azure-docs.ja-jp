---
title: Azure IoT Hub のエラー 404001 DeviceNotFound のトラブルシューティング
description: エラー 404001 DeviceNotFound を修正する方法の概要
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: 127f4546d674eb425440a8e0f395ca8ff0a6b6f4
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515531"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

この記事では、**404001 DeviceNotFound** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

C2D メッセージ、ツイン更新、ダイレクト メソッドなどの cloud-to-device (C2D) 通信中に、操作がエラー **404001 DeviceNotFound** で失敗します。

## <a name="cause"></a>原因

IoT Hub によってデバイスが検出されなかったため、操作に失敗しました。 デバイスが登録されていないか、無効になっています。

## <a name="solution"></a>解決策

使用したデバイス ID を登録してから、再試行してください。