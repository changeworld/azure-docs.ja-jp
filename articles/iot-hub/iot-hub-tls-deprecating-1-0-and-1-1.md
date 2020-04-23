---
title: IoT Hub での TLS 1.0 と 1.1 の非推奨化 | Microsoft Docs
description: TLS 1.0 および1.1 の非推奨に関するガイドラインと IoT Hub でサポートされている暗号。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381292"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>IoT Hub での TLS 1.0 と 1.1 の非推奨化

クラス最高レベルの暗号化を提供するために、IoT Hub は、IoT デバイスとサービス用に選択された暗号化メカニズムとして Transport Layer Security (TLS) 1.2 に移行します。 

## <a name="timeline"></a>タイムライン

IoT Hub は、将来通知があるまで TLS 1.0/1.1 を引き続きサポートします。 ただし、すべてのお客様にできるだけ早く TLS 1.2 に移行することをお勧めします。

## <a name="supported-ciphers"></a>サポートされる暗号

TLS ハンドシェイクで使用されるさまざまな暗号が使用可能になるタイムラインを次に示します。

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (現在サポートされています)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (2020 年の後半にサポートされる予定です)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (2020 年の後半にサポートされる予定です)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (2020 年の後半にサポートされる予定です)

## <a name="customer-feedback"></a>カスタマー フィードバック

TLS 1.2 強制は業界全体で最高クラスの暗号化の選択肢であり、計画どおりに有効になりますが、特定のデプロイおよび TLS 1.2 の導入に関する問題についてのご意見をお待ちしております。 このため、[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) にコメントを送信できます。
