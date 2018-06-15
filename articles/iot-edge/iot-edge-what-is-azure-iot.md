---
title: モノのインターネット (IoT Edge) 用の Azure ソリューション | Microsoft Docs
description: サンプル IoT ソリューション アーキテクチャの概要と、このアーキテクチャとデバイス、Azure IoT Hub サービス、Azure IoT device SDK、Azure IoT service SDK、その他の Azure サービスとの関係。
author: dominicbetts
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: bd59e740803f8f0e6f5f542805d615772efba913
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630342"
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>次の手順

Azure IoT Edge は、分析とデータ処理をエッジ側で実行できるようにする Azure サービスです。 IoT Edge を使用すると、既にご利用の Azure サービスから直接呼び出したロジックや、実際のソリューションに特化した独自のコードをコンテナー ベースのコードに含めて、デバイスの能力を高めることができます。 対象となるデバイスでは、次のことが可能となります。

* ゲートウェイ デバイスとして動作しながら、さまざまなリーフ デバイスからのデータを集約して処理する。
* 異常を検出して、クラウドからの命令を待たずに環境の変化に対応する。
* データを事前に処理してその結果を送信することで帯域幅とストレージ コストを最小限に抑える。 

IoT Edge には、デバイスのリモート管理を可能にするクラウド インターフェイスが備わっています。 デバイスに物理的にアクセスすることなく、コードをデプロイしたり、そのステータスを監視したり、それを更新したりすることができます。 1 台のデバイスをリモートから管理できるほか、自分が定義した多数のデバイスに対してまとめて作用するデプロイ環境を作成することもできます。 詳細については、「[Understand IoT Edge deployments for single devices or at scale (1 台のデバイスまたは一群のデバイスを対象とした IoT Edge デプロイについて)][lnk-deployment]」を参照してください。

IoT Edge を成り立たせているコンポーネントについて詳しくは、[Azure IoT Edge のしくみ][lnk-overview]に関するページを参照してください。

これまで Azure IoT Hub を使用したことがない場合は、[Azure IoT Hub サービスの概要][lnk-iot-hub]に関するページからご覧になることをお勧めします。

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
