---
title: IoT Hub と Device Provisioning Service (DPS) での TLS 1.0 および1.1 の非推奨 | Microsoft Docs
description: TLS 1.0 および1.1 の非推奨に関するガイドラインと IoT Hub と DPS でサポートされている暗号。
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402794"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>IoT Hub および Device Provisioning Service での TLS 1.0 および 1.1 の非推奨

クラス最高レベルの暗号化を提供するために、IoT Hub と Device Provisioning Service (DPS) は、IoT デバイスとサービス用に選択された暗号化メカニズムとして Transport Layer Security (TLS) 1.2 に移行します。 

## <a name="supported-ciphers"></a>サポートされる暗号

TLS ハンドシェイクで使用されるさまざまな暗号が使用可能になるタイムラインを次に示します。

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (現在サポートされています)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (2020 年の後半にサポートされる予定です)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (2020 年の後半にサポートされる予定です)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (2020 年の後半にサポートされる予定です)


## <a name="customer-feedback"></a>カスタマー フィードバック

TLS 1.2 強制は業界全体で最高クラスの暗号化の選択肢であり、計画どおりに有効になりますが、特定のデプロイおよび TLS 1.2 の導入に関する問題についてのご意見をお待ちしております。 このため、[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) にコメントを送信できます。
