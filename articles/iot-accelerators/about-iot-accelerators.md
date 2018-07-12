---
title: Azure IoT ソリューション アクセラレータの概要 | Microsoft Docs
description: Azure IoT ソリューション アクセラレータについて説明します。 IoT ソリューション アクセラレータは、すぐにデプロイできるエンドツーエンドの完全な IoT ソリューションです。
author: dominicbetts
ms.author: dobett
ms.date: 06/07/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 2a4f0b035ce80809a678731a50921791fc0db928
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097990"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Azure IoT ソリューション アクセラレータとは

クラウドベースの IoT ソリューションは通常、カスタム コードと複数のクラウド サービスを使用してデバイスの接続性、データ処理と分析、プレゼンテーションを管理します。

IoT ソリューション アクセラレータは、リモート監視、接続済みファクトリ、予測メンテナンスなどの一般的な IoT シナリオを実装する、すぐにデプロイできる、完全な IoT ソリューションのコレクションです。 ソリューション アクセラレータをデプロイすると、そのデプロイには、必要なクラウド ベースのサービスと、必要なアプリケーション コードがすべて含まれています。

ソリューション アクセラレータは、IoT ソリューションの出発点です。 すべてのソリューション アクセラレータのソース コードはオープン ソースで、GitHub で入手できます。 ソリューション アクセラレータをダウンロードし、要件に合わせて[カスタマイズ](iot-accelerators-remote-monitoring-customize.md)することをお勧めします。

カスタムの IoT ソリューションをゼロからビルドする前の学習ツールとして、ソリューション アクセラレータを使用することもできます。 ソリューション アクセラレータには、クラウド ベースの IoT ソリューションの実証済みプラクティスが実装されており、それに従って実行できます。

各ソリューション アクセラレータのアプリケーション コードには、ソリューション アクセラレータを管理するためのダッシュ ボードが含まれています。 たとえば、ダッシュボードを使用して、接続されたデバイスからのテレメトリの表示、新しいデバイスのプロビジョニング、接続されたデバイスのファームウェアのアップグレードなどを行うことができます。

[![ソリューションのダッシュボード](./media/about-iot-accelerators/dashboard-inline.png)](./media/about-iot-accelerators/dashboard-expanded.png#lightbox)

## <a name="supported-iot-scenarios"></a>サポートされる IoT のシナリオ

現時点では、次の 4 つのソリューション アクセラレータをデプロイできます。

### <a name="remote-monitoring"></a>リモート監視

このソリューション アクセラレータは、複数のリモート デバイスからテレメトリを収集し、それらを制御するために使用します。 デバイスの例としては、オンプレミスに設置されている冷却装置や、リモートのポンプ設備に設置されているバルブなどが挙げられます。

### <a name="connected-factory"></a>接続済みファクトリ

このソリューション アクセラレータは、[OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) インターフェイスを使用して工業資産からテレメトリを収集し、それらを制御するために使用します。 工業資産には、工場の生産ラインで組み立てやテストを行う作業場が含まれる場合があります。

### <a name="predictive-maintenance"></a>予測メンテナンス

このソリューション アクセラレータは、リモート デバイスのエラーを予測し、そのエラーが発生する前にメンテナンスを行うために使用します。 このソリューション アクセラレータは機械学習アルゴリズムを使用して、デバイスのテレメトリからエラーを予測します。 デバイスの例として、航空機のエンジンやエレベーターが挙げられる場合があります。

### <a name="device-simulation"></a>デバイスのシミュレーション

このソリューション アクセラレータは、現実のテレメトリを生成する、シミュレートされたデバイスを複数実行するために使用します。 このソリューション アクセラレータを使用して、他のソリューション アクセラレータの動作をテストしたり、独自のカスタム IoT ソリューションをテストしたりできます。

## <a name="design-principles"></a>設計原則

すべてのソリューション アクセラレータは同じ設計原則とゴールに従います。 次を満たすように設計されています。

* **スケーラブル**。何百万ものデバイスを接続して管理できます。
* **拡張可能**。要件に合わせてカスタマイズできるようにします。
* **わかりやすい**。そのしくみや実装方法が理解しやすくなっています。
* **モジュール式**。代替のサービスにスワップアウトできます。
* **セキュリティで保護されている**。組み込みの接続性とデバイスのセキュリティ機能を Azure のセキュリティと組み合わせています。

## <a name="architectures-and-languages"></a>アーキテクチャと言語

元のソリューション アクセラレータは、モデル ビュー コントローラー (MVC) アーキテクチャを使用して .NET で作成されています。 マイクロソフトでは、ソリューション アクセラレータを、新しいマイクロサービス アーキテクチャに更新しています。 各マイクロサービスの [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) バージョンと [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) バージョンは両方とも GitHub で入手できます。 次の表は、ソリューション アクセラレータの現在の状態を示しています。

| ソリューション アクセラレータ   | アーキテクチャ  | 言語     |
| ---------------------- | ------------- | ------------- |
| リモート監視      | マイクロサービス | Java と .NET |
| 予測メンテナンス | MVC           | .NET          |
| 接続済みファクトリ      | MVC           | .NET          |

マイクロサービス アーキテクチャの詳細については、「[.NET Application Architecture (.NET アプリケーション アーキテクチャ)](https://www.microsoft.com/net/learn/architecture)」および「[Microservices: An application revolution powered by the cloud. (マイクロサービス: クラウドによって実現されるアプリケーションの革命)](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)」を参照してください。

## <a name="deployment-options"></a>デプロイ オプション

マイクロサービスベースのソリューション アクセラレータは、次の構成でデプロイできます。

* **Standard:** 運用環境のデプロイを開発するための拡張インフラストラクチャ デプロイ。 Azure Container Service により、マイクロサービスが複数の Azure 仮想マシンにデプロイされます。 個々のマイクロサービスをホストする Docker コンテナーは、Kubernetes によって調整されます。
* **Basic:** デモまたはデプロイ テストのための低コスト バージョン。 すべてのマイクロサービスが 1 つの Azure 仮想マシンにデプロイされます。
* **Local:** テストおよび開発用のローカル コンピューターのデプロイ。 このアプローチでは、マイクロサービスをローカル Docker コンテナーにデプロイし、クラウド内で IoT Hub、Azure Cosmos DB、および Azure Storage サービスに接続します。

ソリューション アクセラレータを実行するコストは、[基礎となる Azure サービスのコスト](https://azure.microsoft.com/pricing)の総計です。 デプロイのオプションを選択するときに、使用する Azure サービスの詳細が表示されます。

## <a name="next-steps"></a>次の手順

IoT ソリューション アクセラレータを試すには、[クラウドベースのリモート監視ソリューションのデプロイ](quickstart-remote-monitoring-deploy.md)に関するクイックスタートを参考にしてください。
