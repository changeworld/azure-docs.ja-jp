---
title: Azure IoT Central を使用して構築された水質監視ソリューションの参照アーキテクチャ | Microsoft Docs
description: Azure IoT Central で構築された水質監視ソリューションの概念について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 83906682a188a0d6abf859589a38311a0d726a05
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229814"
---
# <a name="water-quality-monitoring-reference-architecture"></a>水質監視の参照アーキテクチャ 

水質監視ソリューションは、**Azure IoT Central アプリ テンプレート** を使って、IoT アプリケーションのキック スターターとして構築できます。 この記事では、エンドツーエンド ソリューションを構築するための高度な参照アーキテクチャのガイダンスを提供します。 

![水質監視アーキテクチャ](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

概念:

1. デバイスと接続  
1. IoT Central
1. 拡張性と統合
1. ビジネス アプリケーション

この記事では、通常、水質の監視ソリューションの一部として機能する主要なコンポーネントについて説明します。

## <a name="devices-and-connectivity"></a>デバイスと接続

水管理ソリューションでは、フロー メーター、水質モニター、スマート バルブ、漏れ検知器などのスマートウォーター デバイスを使用します。

スマート ウォーター ソリューションのデバイスは、省電力広域ネットワーク (LPWAN) またはサードパーティのネットワーク オペレーターを介して接続できます。 これらの種類のデバイスでは、[Azure IoT Central デバイス ブリッジ](../core/howto-build-iotc-device-bridge.md)を使用して、Azure IoT Central の IoT アプリケーションに自分のデバイス データを送信します。 または、IP 対応で IoT Central に直接接続可能なデバイス ゲートウェイを使用することもできます。

## <a name="iot-central"></a>IoT Central

Azure IoT Central は、IoT ソリューションを迅速に構築してデプロイするのに役立つ IoT アプリケーション プラットフォームです。 サードパーティのサービスを使用して、ソリューションをブランド化、カスタマイズ、統合することができます。

スマート ウォーター デバイスを IoT Central に接続すると、アプリケーションによって、デバイス コマンドと制御、監視とアラート、RBAC が組み込まれたユーザー インターフェイス、構成可能な分析情報ダッシュボード、拡張オプションが提供されます。

## <a name="extensibility-and-integrations"></a>拡張性と統合

IoT Central で IoT アプリケーションを拡張し、必要に応じて次のことを行うことができます。

* 高度な分析 (たとえば、IoT Central アプリケーションからの継続的なデータ エクスポートを使用した機械学習モデルのトレーニングなど) のために IoT データを変換して統合します。
* Power Automate または IoT Central アプリケーションの Webhook を使用してアクションをトリガーし、他のシステムのワークフローを自動化します。
* IoT Central API を使用して IoT Central で IoT アプリケーションにプログラムでアクセスします。

## <a name="business-applications"></a>ビジネス アプリケーション

IoT データを使用して、水道施設内でさまざまなビジネス アプリケーションを強化することができます。 [IoT Central 水消費量監視アプリケーション](tutorial-water-consumption-monitoring.md)では、ルールやアクションを設定し、[Connected Field Service](/dynamics365/field-service/connected-field-service) でアラートを作成するように設定できます。 IoT Central のルールで Power Automate を構成し、アプリケーションやサービス間のワークフローを自動化します。 また、Connected Field Service のサービス アクティビティに基づいて、情報を Azure IoT Central に送り返すことができます。

## <a name="next-steps"></a>次のステップ

* [水消質監視の IoT Central アプリケーションを作成する](./tutorial-water-quality-monitoring.md)方法を学習する
* [IoT Central 政府機関テンプレート](./overview-iot-central-government.md)の詳細を学習する
* IoT Central の詳細については、[IoT Central の概要](../core/overview-iot-central.md)に関する記事を参照してください
