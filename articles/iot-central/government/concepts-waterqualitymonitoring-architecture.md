---
title: Azure IoT Central を使用して構築された水質監視ソリューションの参照アーキテクチャ | Microsoft Docs
description: Azure IoT Central で構築された水質監視ソリューションの概念について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 0c4b13c56a68205195bd5ad4b696d9e01786a8dd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016546"
---
# <a name="water-quality-monitoring-reference-architecture"></a>水質監視の参照アーキテクチャ 



水質監視ソリューションは、**Azure IoT Central アプリ テンプレート**を使って、IoT アプリケーションのキック スターターとして構築できます。 この記事では、エンドツーエンド ソリューションを構築するための高度な参照アーキテクチャのガイダンスを提供します。 


![水質監視アーキテクチャ](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

概念:

1. デバイスと接続  
1. IoT Central 
2. 拡張性と統合
3. ビジネス アプリケーション

水質監視ソリューションで一般的に役立つ主要なコンポーネントについて見ていきましょう。

## <a name="devices-and-connectivity"></a>デバイスと接続 
このセクションでは、水質の監視または水消費量の監視で使用されるデバイスを、総称してスマート ウォーター デバイスと呼びます。 スマート ウォーター デバイスには、流量計、水質モニター、スマート バルブ、漏れ検出器などがあります。

スマート ウォーター ソリューションで使用されるデバイスは、一般的にサードパーティのネットワーク オペレーターを通じて省電力広域ネットワーク (LPWAN) を介して接続されます。 これらの種類のデバイスについては、[Azure IoT Central デバイス ブリッジ](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge)を利用して、Azure IoT Central の IoT アプリケーションにご自分のデバイス データを送信することができます。 または、IP 対応で IoT Central に直接接続することができるデバイス ゲートウェイを使用することもできます。

## <a name="iot-central"></a>IoT Central 
Azure IoT Central は、IoT ソリューションを迅速に起動して実行できる IoT アプリ プラットフォームです。 サードパーティのサービスを使用して、ソリューションをブランド化、カスタマイズ、統合することができます。
スマート ウォーター デバイスを IoT Central に接続すると、デバイス コマンドと制御、監視とアラート、RBAC が組み込まれたユーザー インターフェイス、構成可能な分析情報ダッシュボード、拡張オプションが得られます。 

## <a name="extensibility-and-integrations"></a>拡張性と統合 
IoT Central で IoT アプリケーションを拡張し、必要に応じて次のことを行うことができます。
* 高度な分析 (たとえば、IoT Central アプリケーションからの継続的なデータ エクスポートを使用した機械学習モデルのトレーニングなど) のために IoT データを変換して統合する
* Microsoft Flow または IoT Central アプリケーションの Webhook を使用してアクションをトリガーし、他のシステムのワークフローを自動化する
* IoT Central API を使用して IoT Central で IoT アプリケーションにプログラムでアクセスする

## <a name="business-applications"></a>ビジネス アプリケーション 
IoT データを使用して、水道施設内でさまざまなビジネス アプリケーションを強化することができます。 IoT Central の水質監視アプリケーションをフィールド サービスに接続する方法については、[Dynamics 365 フィールド サービスと統合する方法](./how-to-configure-connected-field-services.md)の記事に従ってください。 


## <a name="next-steps"></a>次のステップ
* [水消質監視の IoT Central アプリケーションを作成する](./tutorial-water-quality-monitoring.md)方法を学習する
* [IoT Central 政府機関テンプレート](./overview-iot-central-government.md)の詳細を学習する
* IoT Central の詳細については、[IoT Central の概要](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)に関する記事を参照してください

