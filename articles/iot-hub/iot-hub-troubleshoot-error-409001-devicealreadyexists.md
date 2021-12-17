---
title: Azure IoT Hub のエラー 409001 DeviceAlreadyExists のトラブルシューティング
description: エラー 409001 Deviceexists を修正する方法の概要
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: f6ef32537da22324ac2d4991fac9df785374e16f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829201"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

この記事では、**409001 DeviceAlreadyExists** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub にデバイスを登録しようとすると、要求がエラー **409001 Deviceexists** で失敗します。

## <a name="cause"></a>原因

同じデバイス ID を持つデバイスが IoT ハブに既に存在します。 

## <a name="solution"></a>解決策

別のデバイス ID を使用して、もう一度やり直してください。