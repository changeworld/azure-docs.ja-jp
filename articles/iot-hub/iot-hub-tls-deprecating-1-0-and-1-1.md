---
title: IoT Hub と Device Provisioning Service (DPS) での TLS 1.0 および1.1 の非推奨 | Microsoft Docs
description: TLS 1.0 および1.1 の非推奨に関するガイドラインと IoT Hub と DPS でサポートされている暗号。
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912156"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>IoT Hub および Device Provisioning Service での TLS 1.0 および 1.1 の非推奨

クラス最高レベルの暗号化を提供するために、IoT Hub と Device Provisioning Service (DPS) は、IoT デバイスとサービス用に選択された暗号化メカニズムとして Transport Layer Security (TLS) 1.2 に移行します。 そのため、**2020 年 7 月 1 日**には TLS 1.0 と TLS 1.1 の従来のサポートに加えて、いくつかの非推奨のレガシ暗号が非推奨とされます。


## <a name="impact"></a>影響
お客様固有の状況と構成に基づいて、TLS 1.0 と 1.1 の非推奨と、推奨されていない従来の暗号は、IoT Hub または DPS と通信する IoT デバイスとサービスにとって影響が大きい変更になる可能性があります。 場合によっては、これらの変更と互換性のないデバイスおよびサービスが、上記のカットオフ日以降 IoT Hub または DPS に接続できないことがあります。


## <a name="supported-ciphers"></a>サポートされる暗号

TLS ハンドシェイクで使用されるさまざまな暗号が使用可能になるタイムラインを次に示します。

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (現在サポートされています)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (2020 年の後半にサポートされる予定です)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (2020 年の後半にサポートされる予定です)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (2020 年の後半にサポートされる予定です)


## <a name="customer-feedback"></a>カスタマー フィードバック

TLS 1.2 強制は業界全体で最高クラスの暗号化の選択肢であり、計画どおりに有効になりますが、特定のデプロイおよび TLS 1.2 の導入に関する問題についてのご意見をお待ちしております。 このため、[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) にコメントを送信できます。
