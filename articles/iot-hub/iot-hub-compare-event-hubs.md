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
ms.openlocfilehash: b86132b42aef981e6218b27e271e6db645d14071
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT デバイスを Azure に接続する: IoT Hub と Event Hubs

Azure では、データとクラウドの機能をつなぐ助けとなるように、さまざまな種類の接続と通信のために特に開発されたサービスを提供しています。 Azure IoT Hub と Azure Event Hubs はどちらも、大量のデータを取り込み、ビジネス情報分析のためにそのデータを処理または保存できるクラウド サービスです。 2 つのサービスは、どちらも少ない待ち時間と高い信頼性でデータの取り込みをサポートするという点で類似していますが、異なる目的のために設計されています。 IoT Hub は、とりわけ、IoT デバイスを大規模に Azure クラウドに接続することに特有の要件に対処するために開発されたのに対して、Event Hubs は、ビッグ データのストリーミング用に設計されました。 IoT デバイスを Azure に接続するために、Azure IoT Hub を使用することをお勧めするのは、これが理由です

Azure IoT Hub は、IoT デバイスを接続し、ビジネスの分析情報と自動化を推進するためにデータを収集するクラウド ゲートウェイです。 さらに、IoT Hub には、デバイスとバックエンド システム間のリレーションシップを強化する機能が含まれています。 双方向通信機能は、デバイスからデータを受信している間でも、デバイスにコマンドやポリシーを返送し、たとえばプロパティを更新したり、デバイス管理のアクションを呼び出したりできることを意味します。  クラウドとデバイスのこうした接続により、Azure IoT Edge を使用してクラウド インテリジェンスをエッジ デバイスに提供する重要な機能も実現されます。 IoT Hub によって提供されるデバイス レベルの一意の ID は、潜在的な攻撃からの IoT ソリューションのセキュリティ保護を強化する助けになります。 

[Azure Event Hubs][Azure Event Hubs] は、Azure のビッグ データ ストリーミング サービスです。 これは、ユーザーが 1 日あたり何十億もの要求を送信する可能性がある高スループットのデータ ストリーミング シナリオに向けて設計されています。 Event Hubs は、パーティション分割されたコンシューマー モデルを使用してストリームをスケールアウトし、ビッグ データと、Databricks、Stream Analytics、ADLS、HDInsight を含む Azure の分析サービスに統合されます。 Event Hubs Capture や自動インフレのような機能を備えたこのサービスは、ビッグ データのアプリとソリューションをサポートするために設計されています。 さらに、IoT Hub は、テレメトリ フロー パスのために Event Hubs を活用するので、IoT ソリューションでも、Event Hubs に備わる強力な処理機能の恩恵を受けることができます。

要約すると、どちらのソリューションも大規模なデータ インジェストを想定して設計されていますが、IoT Hub のみが、IoT デバイスを Azure クラウドに接続することのビジネス価値を最大化するように設計された、IoT 特有の豊富な機能を備えています。  IoT への取り組みを開始したばかりの場合は、データ インジェストのシナリオをサポートするために IoT Hub から着手することで、ビジネス面や技術面のニーズによって必要になり次第、確実に、多彩な IoT 機能をすばやく利用できるようになります。

次の表では、IoT 機能を評価するときのために、IoT Hub の 2 つの階層と Event Hubs の詳細な比較を示します。 IoT Hub の Standard レベルと Basic レベルについて詳しくは、[適切な IoT Hub レベルの選び方][lnk-scaling]に関するページをご覧ください。

| IoT の機能 | IoT Hub Standard レベル | IoT Hub Basic レベル | Event Hubs |
| --- | --- | --- | --- |
| デバイスからクラウドへのメッセージ | ![○][1] | ![○][1] | ![○][1] |
| プロトコル: HTTPS、AMQP、AMQP over webSockets | ![○][1] | ![○][1] | ![○][1] |
| プロトコル: MQTT、MQTT over webSockets | ![○][1] | ![○][1] |  |
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
