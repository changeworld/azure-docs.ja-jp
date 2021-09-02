---
title: Azure IoT Central を使用して構築された、接続された廃棄物管理ソリューションの参照アーキテクチャ | Microsoft Docs
description: Azure IoT Central を使用して構築された、接続された廃棄物管理ソリューションの概念について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 60fe5b51ffa04de79598f3f4744a8bf2bb57966a
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229901"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>接続された廃棄物監視の参照アーキテクチャ 

接続された廃棄物管理ソリューションは、**Azure IoT Central アプリ テンプレート** を、IoT アプリケーションのキック スターターとして使用して構築できます。 この記事では、エンドツーエンド ソリューションの構築に関する参照アーキテクチャの概要のガイダンスを提供します。

![接続された廃棄物管理アーキテクチャ](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)

概念:

1. デバイスと接続  
1. IoT Central
1. 拡張性と統合
1. ビジネス アプリケーション

この記事では、通常、廃棄物監視ソリューションの一部として機能する主要なコンポーネントについて説明します。

## <a name="devices-and-connectivity"></a>デバイスと接続

オープンな環境で使用されるごみ箱などのデバイスは、省電力広域ネットワーク (LPWAN) またはサードパーティのネットワーク オペレーターを介して接続できます。 これらの種類のデバイスでは、[Azure IoT Central デバイス ブリッジ](../core/howto-build-iotc-device-bridge.md)を使用して、Azure IoT Central の IoT アプリケーションに自分のデバイス データを送信します。 または、IP 対応で IoT Central に直接接続可能なデバイス ゲートウェイを使用することもできます。

## <a name="iot-central"></a>IoT Central

Azure IoT Central は、IoT ソリューションを迅速に構築してデプロイするのに役立つ IoT アプリケーション プラットフォームです。 サードパーティのサービスを使用して、ソリューションをブランド化、カスタマイズ、統合することができます。

スマート廃棄物デバイスを IoT Central に接続することで、デバイス コマンドと制御、監視とアラート、RBAC が組み込まれたユーザー インターフェイス、構成可能な分析情報ダッシュボード、拡張オプションを提供します。

## <a name="extensibility-and-integrations"></a>拡張性と統合

IoT Central で IoT アプリケーションを拡張し、必要に応じて次のことを行うことができます。

* 高度な分析 (たとえば、IoT Central アプリケーションからの継続的なデータ エクスポートを使用した機械学習モデルのトレーニングなど) のために IoT データを変換して統合します。
* Power Automate または IoT Central アプリケーションの Webhook を使用してアクションをトリガーし、他のシステムのワークフローを自動化します。
* IoT Central API を使用して IoT Central で IoT アプリケーションにプログラムでアクセスします。

## <a name="business-applications"></a>ビジネス アプリケーション

IoT データを使用して、廃棄物ユーティリティ内のさまざまなビジネス アプリケーションを強化することができます。 たとえば、接続された廃棄物管理ソリューションで、ごみ回収車の配車を最適化できます。 最適化は、接続されたごみ箱からの IoT センサー データに基づいて行うことができます。[IoT Central に接続された廃棄物管理アプリケーション](./tutorial-connected-waste-management.md)で、ルールとアクションを構成し、[Connected Field Service](/dynamics365/field-service/connected-field-service) でアラートを作成するように、それらを設定できます。 IoT Central のルールで Power Automate を構成し、アプリケーションやサービス間のワークフローを自動化します。 また、Connected Field Service のサービス アクティビティに基づいて、情報を Azure IoT Central に送り返すことができます。

IoT Central と Connected Field Service を使用して、次の統合プロセスを簡単に構成できます。

* Azure IoT Central では、Connected Field Service に、デバイスの異常に関する情報を診断のために送信することができます。
* Connected Field Service では、デバイスの異常によってトリガーされるケースまたは作業指示を作成することができます。
* Connected Field Service では、ダウンタイム インシデントを防止するために、技術者による検査をスケジュールできます。
* Azure IoT Central デバイス ダッシュボードは、関連するサービスおよびスケジュール情報で更新することができます。

## <a name="next-steps"></a>次のステップ

* [接続された廃棄物管理 IoT Central アプリケーションを作成](./tutorial-connected-waste-management.md)する方法の詳細
* [IoT Central 政府機関テンプレート](./overview-iot-central-government.md)の詳細を学習する
* IoT Central の詳細については、[IoT Central の概要](../core/overview-iot-central.md)に関する記事を参照してください
