---
title: Azure IoT Central を使用して構築された、水消費量の監視ソリューションの参照アーキテクチャ | Microsoft Docs
description: Azure IoT Central で構築された、水消費量の監視ソリューションの概念について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: f431e72d7609fcd7469895e461a4be412a5233d6
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229832"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>水消費量監視の参照アーキテクチャ

水消費量の監視ソリューションは、**Azure IoT Central アプリケーション テンプレート** を使用して、IoT アプリのスターターとして構築できます。 この記事では、エンドツーエンド ソリューションを構築するための高度な参照アーキテクチャのガイダンスを提供します。

![水消費量の監視アーキテクチャ](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

概念:

1. デバイスと接続  
1. IoT Central
1. 拡張性と統合
1. ビジネス アプリケーション

この記事では、通常、水消費量の監視ソリューションの一部として機能する主要なコンポーネントについて説明します。

## <a name="devices-and-connectivity"></a>デバイスと接続

水管理ソリューションでは、フロー メーター、水質モニター、スマート バルブ、漏れ検知器などのスマートウォーター デバイスを使用します。

スマート ウォーター ソリューションで使用されるデバイスは、一般的にサードパーティのネットワーク オペレーターを通じて省電力広域ネットワーク (LPWAN) を介して接続されます。 これらの種類のデバイスについては、[Azure IoT Central デバイス ブリッジ](../core/howto-build-iotc-device-bridge.md)を使用して、Azure IoT Central の IoT アプリケーションにご自分のデバイス データを送信します。 または、IP 対応で IoT Central に直接接続可能なデバイス ゲートウェイを使用することもできます。

## <a name="iot-central"></a>IoT Central

Azure IoT Central は、IoT ソリューションを迅速に構築してデプロイするのに役立つ IoT アプリケーション プラットフォームです。 サードパーティのサービスを使用して、ソリューションをブランド化、カスタマイズ、統合することができます。

スマート ウォーター デバイスを IoT Central に接続することで、デバイス コマンドと制御、監視とアラート、RBAC が組み込まれたユーザー インターフェイス、構成可能な分析情報ダッシュボード、拡張オプションを提供します。

## <a name="extensibility-and-integrations"></a>拡張性と統合

IoT Central で IoT アプリケーションを拡張し、必要に応じて次のことを行うことができます。

* 高度な分析 (たとえば、IoT Central アプリケーションからの継続的なデータ エクスポートを使用した機械学習モデルのトレーニングなど) のために IoT データを変換して統合します。
* Power Automate または IoT Central アプリケーションの Webhook を使用してアクションをトリガーし、他のシステムのワークフローを自動化します。
* IoT Central API を使用して IoT Central で IoT アプリケーションにプログラムでアクセスします。

## <a name="business-applications"></a>ビジネス アプリケーション

IoT データを使用して、水ユーティリティ内のさまざまなビジネス アプリケーションに電力を供給できます。 [Azure IoT Central水消費量監視アプリケーション](tutorial-water-consumption-monitoring.md)では、ルールやアクションを設定し、[Connected Field Service](/dynamics365/field-service/connected-field-service)でアラートを作成するように設定できます。 IoT Central のルールで Power Automate を設定し、アプリケーションやサービス間のワークフローを自動化します。 また、接続フィールド サービスのサービス アクティビティに基づいて、情報を Azure IoT Central に送り返すことができます。

## <a name="next-steps"></a>次のステップ

* [水消費量の IoT Central アプリケーションを作成する](./tutorial-water-consumption-monitoring.md)方法を学習する
* [IoT Central 政府機関テンプレート](./overview-iot-central-government.md)の詳細を学習する
* IoT Central の詳細については、[IoT Central の概要](../core/overview-iot-central.md)に関する記事を参照してください
