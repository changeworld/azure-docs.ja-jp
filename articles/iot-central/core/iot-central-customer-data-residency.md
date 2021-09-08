---
title: Azure IoT Central の顧客データ所在地 | Microsoft Docs
description: この記事では、Azure IoT Central アプリケーションの顧客データ所在地について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2086ab1e899b85f52391187425c85eeb86b21c0e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481166"
---
# <a name="azure-iot-central-customer-data-residency"></a>Azure IoT Central の顧客データ所在地

IoT Central は、次の場合を除き、顧客が指定した地理的な場所の外に顧客データを格納しません。

- 既存の IoT Central アプリケーションに新しいユーザーが追加されると、招待されたユーザーが初めてアプリケーションにアクセスするまで、そのユーザーの電子メール ID は地理的な場所の外に格納される可能性があります。

- IoT Central ダッシュボードのマップ タイルは [Azure Maps](../../azure-maps/about-azure-maps.md) を使用します。 既存の IoT Central アプリケーションにマップ タイルを追加すると、Azure Maps サービスの位置情報ルールに従って場所データが処理または保存される場合があります。
