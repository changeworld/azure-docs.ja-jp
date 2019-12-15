---
title: IoT ハブの IP アドレスについて | Microsoft Docs
description: IoT ハブの IP アドレスとそのプロパティにクエリを実行する方法について説明します。 IoT ハブの IP アドレスは、ディザスター リカバリーやリージョン間フェールオーバーなど、特定のシナリオを通じて変更される可能性があります。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383915"
---
# <a name="iot-hub-ip-addresses"></a>IoT ハブの IP アドレス

IoT ハブの IP アドレス プレフィックスは、*AzureIoTHub* [サービス タグ](../virtual-network/service-tags-overview.md)で定期的に発行されます。 適切な操作を保証するには、IoT デバイスが *AzureIoTHub* サービス タグに一覧表示されているアドレス プレフィックスに送信接続できることが必要です。 IoT アプリケーション サービスは、さらに *EventHub* サービス タグに一覧表示されているアドレス プレフィックスに送信接続できることが必要です。


## <a name="best-practices"></a>ベスト プラクティス

* IoT ハブの IP アドレス プレフィックスは変更されることがあります。 これらの変更は、有効になる前に、サービス タグ経由で定期的に発行されます。 そのため、最新のサービス タグを定期的に取得して使用するためのプロセスを開発することが重要です。 このプロセスは、[Service Tag Discovery API](../virtual-network/service-tags-overview.md#service-tags-in-on-premises) 経由で自動化できます。
* 特定のリージョン内の IoT Hub エンドポイントによって使用される IP プレフィックスを識別するには、*AzureIoTHub.[リージョン名]* タグを使用します。 データセンターのディザスター リカバリーまたは[リージョン内フェールオーバー](iot-hub-ha-dr.md)に対処するには、IoT Hub の geo ペア リージョンの IP プレフィックスへの接続も有効になっていることを確認してください。


## <a name="support-for-ipv6"></a>IPv6 のサポート 

現在、IPv6 は IoT Hub ではサポートされていません。