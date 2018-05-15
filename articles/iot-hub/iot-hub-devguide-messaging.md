---
title: Azure IoT Hub メッセージについて | Microsoft Docs
description: 開発者ガイド - IoT Hub でのデバイスからクラウドおよびクラウドからデバイスへのメッセージ。 メッセージ形式とサポートされる通信プロトコルに関する情報が含まれています。
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 50f95dc1af334468db25bce68f2ca00e0965a28b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>IoT Hub における device-to-cloud と cloud-to-device のメッセージング

IoT Hub メッセージングを使用して、次のようにご利用のデバイスと通信します。

* [device-to-cloud][lnk-d2c] メッセージをデバイスからソリューション バックエンドに送信する。
* [cloud-to-device][lnk-c2d] メッセージをソリューション バックエンドからデバイスに送信する。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub のメッセージング機能の中心となる特性は、メッセージの信頼性と持続性です。 これらのプロパティにより、デバイス側では断続的な接続に対する復元性を、クラウド側ではイベント処理の負荷の急増に対する復元性を実現できます。 IoT Hub では、D2C および C2D メッセージングの両方について、 *少なくとも 1 回* の配信保証が実装されます。

IoT Hub の機能の概要については、[Azure IoT Hub サービスの概要][lnk-iot-hub-overview]に関するページをご覧ください。

## <a name="when-to-use-iot-hub-messaging"></a>IoT Hub メッセージングを使用する場合

時系列のテレメトリとデバイス アプリからのアラートを送信するには device-to-cloud メッセージを使用し、デバイス アプリに一方向の通知を送信するには cloud-to-device メッセージを使用します。

* device-to-cloud メッセージ、報告プロパティ、またはファイルのアップロードのどれを使用するべきか不明な場合は、「[device-to-cloud 通信に関するガイダンス][lnk-d2c-guidance]」をご覧ください。
* cloud-to-device メッセージ、必要とされるプロパティ、またはダイレクト メソッドのどれを使用するべきか不明な場合は、「[cloud-to-device 通信に関するガイダンス][lnk-c2d-guidance]」をご覧ください。

## <a name="next-steps"></a>次の手順

* IoT Hub の [device-to-cloud メッセージング][lnk-d2c]についてご確認ください。
* IoT Hub の [cloud-to-device メッセージング][lnk-c2d]についてご確認ください。

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md