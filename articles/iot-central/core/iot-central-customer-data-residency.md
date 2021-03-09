---
title: Azure IoT Central の顧客データ所在地 | Microsoft Docs
description: この記事では、Azure IoT Central アプリケーションの顧客データ所在地について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280659"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central の顧客データ所在地

IoT Central は、次の場合を除き、顧客が指定した地理的な場所の外に顧客データを格納しません。

- 既存の IoT Central アプリケーションに新しいユーザーが追加されると、招待されたユーザーが初めてアプリケーションにアクセスするまで、そのユーザーの電子メール ID は地理的な場所の外に格納される可能性があります。

- IoT Central ダッシュボードのマップ タイルは [Azure Maps](../../azure-maps/about-azure-maps.md) を使用します。 既存の IoT Central アプリケーションにマップ タイルを追加すると、Azure Maps サービスの位置情報ルールに従って場所データが処理または保存される場合があります。
