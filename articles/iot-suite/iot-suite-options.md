---
title: "Microsoft Azure IoT オプション | Microsoft Docs"
description: "Azure IoT Suite、Microsoft IoT Central、または Azure IoT Hub を使用して IoT ソリューションを実装する方法を選択します。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1a23835f341bf02f8e662782984d95bfe194030b
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="compare-azure-iot-options"></a>Azure IoT オプションの比較

「[Azure とモノのインターネット](iot-suite-what-is-azure-iot.md)」の記事では、次のレイヤーごとに一般的な IoT ソリューション アーキテクチャについて説明しています。

* デバイスの接続と管理
* データ処理と分析
* プレゼンテーションとビジネスの統合

このアーキテクチャを実装するために、Azure IoT には複数のオプションが用意されており、さまざまな顧客要件のセットに対応しています。

* [Azure IoT Suite](index.md) は、Azure の提供する "サービスとしてのプラットフォーム" 上に構築された、エンタープライズ グレードの[構成済みソリューション](iot-suite-what-are-preconfigured-solutions.md) コレクションであり、これを使用するとカスタム IoT ソリューションの開発を加速できます。

* [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) は、モデル ベースのアプローチを使用する SaaS ソリューションです。これを使用すると、クラウド ソリューション開発に関する専門知識がなくても、エンタープライズ グレードの IoT ソリューションを構築できます。

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub は、Azure の提供する中核的な "サービスとしてのプラットフォーム" であり、Microsoft IoT Central と Azure IoT Suite の両方によって使用されます。 IoT Hub によって、何百万もの IoT デバイスとクラウド ソリューションの間で、安全かつ信頼性の高い双方向通信が実現します。 IoT Hub は、以下の IoT 実装の課題に対応するうえで役立ちます。

* 大量のデバイスの接続と管理。
* 大量のテレメトリ インジェスト。
* コマンドとデバイスの制御。
* デバイスに対するセキュリティの強制。

## <a name="compare-azure-iot-suite-and-microsoft-iot-central"></a>Azure IoT Suite と Microsoft IoT Central の比較

IoT ソリューションを計画するうえで、Azure IoT 製品の選択は重要です。 IoT Hub は、単独ではエンド ツー エンドの IoT ソリューションを提供することができない単一の Azure サービスです。 IoT Hub は、任意の IoT ソリューションの開始点として使用でき、これを使用するために Azure IoT Suite や Microsoft IoT Central を使用する必要はありません。 Azure IoT Suite と Microsoft IoT Central はどちらも、他の Azure サービスと共に IoT Hub を使用します。 要件に合わせていずれかを適切に選択できるように、Azure IoT Suite と Microsoft IoT Central の主な違いを次の表にまとめます。

|                        | Azure IoT Suite | Microsoft IoT Central |
| ---------------------- | --------- | ----------- |
| 主な用途 | 最大限の柔軟性が求められるカスタム IoT ソリューションの開発を加速します。 | サービスの細かなカスタマイズを必要としないシンプルな IoT ソリューションの市場投入時間を短縮します。 |
| 基になる PaaS サービスへのアクセス          | 基になる Azure サービスにアクセスして管理します。または、必要に応じてサービスを置き換えます。 | SaaS。 完全に管理されたソリューション。基になるサービスは公開されません。 |
| 柔軟性            | 高。 マイクロサービスのコードはオープン ソースであるため、必要に応じて、どのようにも変更できます。 また、デプロイ インフラストラクチャをカスタマイズすることもできます。| 中。 組み込みのブラウザー ベースのユーザー エクスペリエンスを使用して、ソリューション モデルやさまざまな UI 要素をカスタマイズできます。 各種コンポーネントが公開されないため、インフラストラクチャをカスタマイズすることはできません。|
| 難易度                 | 中/高。 ソリューション バックエンドをカスタマイズするには、Java または .NET のスキルが必要です。 視覚化をカスタマイズするには、JavaScript のスキルが必要です。 | 低。 ソリューションをカスタマイズするには、モデリングのスキルが必要です。 コーディングのスキルは必要ありません。 |
| 開始 | 構成済みソリューションによって一般的な IoT シナリオが実装されます。 数分でデプロイできます。 | アプリケーション テンプレートとデバイス テンプレートには、既製のモデルが用意されています。 数分でデプロイできます。 |
| 価格                | サービスを細かく調整してコストを制御できます。 | シンプルで予測可能な料金体系です。 |

最終的には以下を検討して、どの製品を使って IoT ソリューションを構築するかを決定します。

* ビジネス要件。
* 構築するソリューションの種類。
* ソリューションを構築し長期にわたって保守管理するための組織のスキル セット。

## <a name="next-steps"></a>次のステップ

選択した製品とアプローチに基づいて、以下の手順を推奨します。

* **Azure IoT Suite**: [Azure IoT 構成済みソリューションとは](iot-suite-what-are-preconfigured-solutions.md)。
* **Microsoft IoT Central**: [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions)。
* **IoT Hub**: [Azure IoT Hub サービスの概要](../iot-hub/iot-hub-what-is-iot-hub.md)。
