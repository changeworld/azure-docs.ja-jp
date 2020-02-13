---
title: Azure IoT Hub のエラー 404103 DeviceNotOnline のトラブルシューティング
description: エラー 404103 DeviceNotOnline を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960300"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

この記事では、**404103 DeviceNotOnline** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

デバイスがオンラインの場合でも、デバイスに対するダイレクト メソッドがエラー **404103 DeviceNotOnline** で失敗します。 

## <a name="cause"></a>原因

デバイスがオンラインであることがわかっていてもエラーが発生する場合は、ダイレクト メソッド コールバックがデバイスに登録されていないことが原因の可能性があります。

## <a name="solution"></a>解決策

ダイレクト メソッド コールバック用にデバイスを適切に構成する方法については、「[デバイスでダイレクト メソッドを処理する](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)」を参照してください。