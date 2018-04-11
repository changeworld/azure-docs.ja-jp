---
title: Azure IoT Hub と Azure Event Hubs の比較 | Microsoft Docs
description: 機能の相違点とユース ケースを中心とした、Azure の IoT Hub サービスと Event Hubs サービスの比較です。 比較には、サポートされているプロトコル、デバイス管理、監視、およびファイルのアップロードが含まれます。
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Azure IoT Hub と Azure Event Hubs の比較

Azure IoT Hub と Azure Event Hubs はどちらも、大量のデータを取り込み、ビジネス情報分析のためにそのデータを処理または保存できるクラウド サービスです。 短い待機時間と高い信頼性でプロセス イベントと利用統計情報をサポートする点では、2 つのサービスは似ています。 ただし、大規模なモノのインターネットのシナリオをサポートするために必要な特定の機能を備えて開発されたのは IoT Hub だけです。 

Azure IoT Hub は、デバイスを接続し、ビジネスの分析情報と自動化のためのデータを収集するクラウド ゲートウェイです。 Azure IoT Hub は、クラウドへのデータのストリーミングと、大規模なデバイスの管理を容易にします。 IoT Hub と他のデータ インジェスト サービスの間の重要な相違は、IoT Hub にはデバイスとバックエンド システム間のリレーションシップを強化する機能が含まれていることです。 双方向通信機能は、デバイスからデータを受信している間でも、デバイスにメッセージを返送してプロパティを更新したり、アクションを呼び出したりできることを意味します。 デバイス レベルの ID は、システムをセキュリティで保護するのに役立ちます。 分散コンピューティングは、クラウド サービスのロジックをエッジ デバイス上に移動します。

[Azure Event Hubs][Azure Event Hubs] は、大量のデータと利用統計情報を処理して格納できるイベント取り込みサービスです。 Event Hubs は、データセンター間シナリオとデータセンター内シナリオの両方のコンテキストでの大規模なイベント取り込み用に設計されていますが、IoT Hub で可能な IoT 固有の豊富な機能は提供しません。 そのため、IoT ソリューションには Event Hubs はお勧めしません。 

次の表では、IoT 機能を評価するときのために、IoT Hub の 2 つの階層と Event Hubs の詳細な比較を示します。 IoT Hub の Standard レベルと Basic レベルについて詳しくは、[適切な IoT Hub レベルの選び方][lnk-scaling]に関するページをご覧ください。

| IoT の機能 | IoT Hub Standard レベル | IoT Hub Basic レベル | Event Hubs |
| --- | --- | --- | --- |
| デバイスからクラウドへのメッセージ | ![○][1] | ![○][1] | ![○][1] |
| プロトコル: HTTPS、AMQP、AMQP over websockets | ![○][1] | ![○][1] | ![○][1] |
| プロトコル: MQTT、MQTT over websockets | ![○][1] | ![○][1] |  |
| デバイスごとの ID | ![○][1] | ![○][1] |  |
| 複数デバイスからのファイルのアップロード | ![○][1] | ![○][1] |  |
| Device Provisioning Service | ![○][1] | ![○][1] |  |
| クラウドからデバイスへのメッセージ | ![○][1] |  |  |
| デバイス ツインとデバイス管理 | ![○][1] |  |  |
| IoT Edge | ![○][1] |  |  |

唯一のユース ケースがデバイスからクラウドへのデータ インジェストである場合でも、IoT Hub は IoT デバイス接続用に設計されたサービスを提供するので、IoT Hub を使うことを強くお勧めします。 

### <a name="next-steps"></a>次の手順

IoT Hub の機能を詳しく調べるには、「[Azure IoT Hub 開発者ガイド][lnk-devguide]」をご覧ください


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png