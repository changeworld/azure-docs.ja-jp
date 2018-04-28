---
title: Azure IoT Suite 構成済みソリューションの概要 | Microsoft Docs
description: Azure IoT Suite 構成済みソリューションとそのアーキテクチャ (追加リソースのリンクを含む) の説明。
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: b7b9f61a8fb46d5d591b317049cfd60b723a7e77
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-iot-suite"></a>Azure IoT Suite とは

Azure IoT Suite は一連の "*構成済みソリューション*" で、次の特長があります。

* 数分でデプロイ
* すぐに開始できるようにサポート
* 特定の要件に合わせてカスタマイズ可能

*IoT Suite* 構成済みソリューションは、すべて同じ原則と目標に基づいて設計されています。

次のビデオでは、リモート監視構成済みソリューションの概要を説明しています。

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="preconfigured-solutions-overview"></a>構成済みソリューションの概要

構成済みソリューションとは、サブスクリプションを使用して Azure にデプロイできる、一般的な IoT ソリューション パターンのオープン ソース実装です。 構成済みソリューションごとに、カスタム コードと Azure サービスを組み合わせて、特定の IoT シナリオを実装します。 こうしたシナリオはどれも、特定の要件に合わせてカスタマイズできます。 これらのシナリオは、次のとおりです。

* 充実したダッシュボードでデータを視覚化し、深い洞察とソリューションの状態を確認する。
* ライブ IoT デバイス テレメトリに対してルールとアラームを構成する。
* ソフトウェアと構成の更新など、デバイス管理ジョブをスケジュール設定する。
* 独自のカスタム物理デバイスまたはシミュレートされたデバイスをプロビジョニングする。
* トラブルシューティングを行い、IoT デバイス グループ内の問題を修復する。

構成済みソリューションはそれぞれ、シミュレートされたデバイスまたは物理デバイスを使用してテレメトリを生成できる、完全なエンド ツー エンドの実装です。 構成済みソリューションは、ソリューション アクセラレータとして、次の用途でご利用いただけます。

* 独自の IoT ソリューションの開始点を提供する。
* IoT ソリューションの設計と開発における一般的なパターンについて学習する。

次の 3 つの構成済みソリューションを今すぐ利用できます。

* [リモート監視](iot-suite-remote-monitoring-explore.md)
* [予測的なメンテナンス](iot-suite-predictive-overview.md)
* [コネクテッド ファクトリ](iot-suite-connected-factory-overview.md)

次の表は、ソリューションが特定の IoT 機能にどのようにマップされるかを示しています。

| 解決策 | データの取り込み | デバイス ID | デバイス管理 | エッジ処理 | コマンドと制御 | ルールとアクション | 予測分析 |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [リモート監視](iot-suite-remote-monitoring-explore.md)  |[はい] |はい |はい |-   |はい |[はい] |-   |
| [予測的なメンテナンス](iot-suite-predictive-overview.md)   |[はい] |はい |-   |-   |はい |はい |[はい] |
| [コネクテッド ファクトリ](iot-suite-connected-factory-overview.md) |[はい] |- |- |はい |はい |[はい] |-   |

* *データの取り込み*: クラウドへの大規模なデータの取り込み。
* "*デバイス ID*": 一意のデバイス ID を管理し、ソリューションへのデバイス アクセスを制御します。
* "*デバイス管理*": デバイス メタデータの管理し、デバイスの再起動、ファームウェアのアップグレードなどの操作を実行します。
* "*コマンドと制御*": デバイスでアクションを実行するには、クラウドからデバイスにメッセージを送信します。
* "*ルールとアクション*": 特定のデバイスからクラウドへのデータを操作するには、ソリューション バックエンドでルールを使用します。
* "*予測分析*": ソリューション バックエンドはデバイスからクラウドへのデータを分析して、特定のアクションを実行するタイミングを予測します。 たとえば、航空機エンジンのテレメトリを分析して、エンジンのメンテナンス時期を判断できます。

> [!NOTE]
> 構成済みソリューションをデプロイしたり、カスタマイズ方法の詳細を確認したりするには、[Microsoft Azure IoT Suite](https://www.azureiotsuite.com/) に関するページをご覧ください。

## <a name="azure-services"></a>Azure サービス

構成済みソリューションをデプロイすると、プロビジョニング プロセスによりいくつかの Azure サービスが構成されます。 次の表は、構成済みソリューションで使用されるサービスを示しています。

|                      | リモート監視  | 予測的なメンテナンス | コネクテッド ファクトリ |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | [はい]                | はい                    | [はい]               |
| Event Hubs           |                    | [はい]                    |                   |
| Time Series Insights |                    |                        | [はい]               |
| コンテナー サービス   | [はい]                |                        |                   |
| Stream Analytics     |                    | [はい]                    |                   |
| Web Apps             | [はい]                | はい                    | [はい]               |
| Cosmos DB            | [はい]                | [はい]                    |                    |
| Azure Storage         |                    | [はい]                    | [はい]               |

> [!NOTE]
> リモート監視構成済みソリューションにデプロイされたリソースの詳細については、こちらの GitHub [記事](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments)を参照してください。

* [Azure IoT Hub](../iot-hub/index.md)。 このサービスは、デバイスからクラウドへ、およびクライアントからデバイスへのメッセージング機能を提供し、クラウドや他の主要な IoT Suite サービスに対するゲートウェイとして機能します。 このサービスを使用すると、デバイスから大規模にメッセージを受信し、デバイスにコマンドを送信することができます。 また、サービスを使用すると[デバイスを管理](../iot-hub/iot-hub-device-management-overview.md)することもできます。 たとえば、ハブに接続されている 1 台以上のデバイスで、構成、再起動、または出荷時の設定へのリセットを実行できます。
* [Azure Event Hubs](../event-hubs/index.md)。 このサービスは、クラウドに大量のイベント インジェストを提供します。 「[Azure IoT Hub と Azure Event Hubs の比較](../iot-hub/iot-hub-compare-event-hubs.md)」を参照してください。
* [Azure Time Series Insights](../time-series-insights/index.yml)。 構成済みソリューションは、このサービスを使用して、デバイスの利用統計情報を分析および表示します。
* [Azure Container Service](../container-service/index.yml)。 このサービスは、構成済みソリューションにおいてマイクロサービスをホストおよび管理します。
* データ ストレージ用の [Azure Cosmos DB](../cosmos-db/index.yml) および [Azure Storage](../storage/index.yml)。
* [Azure Stream Analytics](../stream-analytics/index.yml)。 予測メンテナンス構成済みソリューションは、このサービスを利用して、受信テレメトリを処理し、集計を実行してイベントを検出します。 また、この構成済みソリューションは、ストリーム分析を使用して、メタデータやデバイスからのコマンド応答などのデータを含む情報メッセージも処理します。
* [Azure Web Apps](../app-service/index.yml)。構成済みソリューションにおいてカスタム アプリケーション コードをホストします。

一般的な IoT ソリューションのアーキテクチャの概要については、[Microsoft Azure とモノのインターネット (IoT)](iot-suite-what-is-azure-iot.md) に関するページをご覧ください。

## <a name="whats-new-in-preconfigured-solutions"></a>構成済みソリューションの新機能

マイクロソフトでは、構成済みソリューションを、マイクロサービス ベースの新しいアーキテクチャに更新しています。 次の表は、構成済みソリューションの現在の状態を示しています。

| 構成済みのソリューション | アーキテクチャ  | 言語     |
| ---------------------- | ------------- | ------------- |
| リモート監視      | マイクロサービス | Java と .NET |
| 予測的なメンテナンス | MVC           | .NET          |
| コネクテッド ファクトリ      | MVC           | .NET          |

次のセクションでは、マイクロサービス ベースの構成済みソリューションの新機能について説明します。

### <a name="microservices"></a>マイクロサービス

新しいバージョンのリモート監視構成済みソリューションでは、マイクロサービス アーキテクチャが採用されています。 この構成済みソリューションは、"*IoT Hub マネージャー*" や "*Storage マネージャー*" など、複数のマイクロサービスで構成されます。 各マイクロサービスの Java バージョンと .NET バージョンの両方を、関連する開発者向けドキュメントと共にダウンロードすることができます。 マイクロサービスの詳細については、[リモート管理アーキテクチャ](iot-suite-remote-monitoring-sample-walkthrough.md)に関するページをご覧ください。

このマイクロサービス アーキテクチャは、クラウド ソリューションの実証済みのパターンで、次の特長があります。

* スケーラブル。
* 拡張性を実現。
* わかりやすい。
* 個々のサービスをスワップ アウトして代替サービスを使用可能。

> [!TIP]
> マイクロサービス アーキテクチャの詳細については、「[.NET Application Architecture (.NET アプリケーション アーキテクチャ)](https://www.microsoft.com/net/learn/architecture)」および「[Microservices: An application revolution powered by the cloud. (マイクロサービス: クラウドによって実現されるアプリケーションの革命)](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)」を参照してください。

リモート監視の新しいバージョンをデプロイする場合は、次のデプロイ オプションのいずれかを選択する必要があります。

* **Basic:** デモまたはデプロイ テストのための低コスト バージョン。 すべてのマイクロサービスが 1 つの Azure 仮想マシンにデプロイされます。
* **Standard:** 運用環境のデプロイを開発するための拡張インフラストラクチャ デプロイ。 Azure Container Service により、マイクロサービスが複数の Azure 仮想マシンにデプロイされます。 個々のマイクロサービスをホストする Docker コンテナーは、Kubernetes によって調整されます。

### <a name="language-choices-java-and-net"></a>言語の選択肢: Java と .NET

Java と .NET の両方で、各マイクロサービスの実装を使用できます。 .NET コードと同様に、Java ソース コードはオープン ソースであり、開発者固有の要件に合わせてカスタマイズできます。

* [.NET GitHub リポジトリのリモート監視](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java GitHub リポジトリのリモート監視](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

その他の言語の実装を確認する場合は、[Azure IoT ユーザーの声](https://feedback.azure.com/forums/321918-azure-iot)にリクエストを追加してください。

### <a name="react-user-interface-framework"></a>React のユーザー インターフェイス フレームワーク

UI は、[React](https://facebook.github.io/react/) JavaScript ライブラリを使用して作成されます。 ソース コードはオープン ソースであり、ダウンロードしてカスタマイズできます。

## <a name="next-steps"></a>次の手順

これで IoT Suite 構成済みソリューションの概要を確認できました。各構成済みソリューションで推奨される次の手順は以下のとおりです。

* [Azure IoT Suite リモート監視ソリューションの Resource Manager デプロイメント モデルを確認する](iot-suite-remote-monitoring-explore.md)。
* [予測メンテナンス構成済みソリューションの概要](iot-suite-predictive-overview.md)。
* [コネクテッド ファクトリ構成済みソリューションの概要](iot-suite-connected-factory-overview.md)。

IoT ソリューション アーキテクチャの詳細については、「[Microsoft Azure IoT Reference Architecture (Microsoft Azure IoT リファレンス アーキテクチャ)](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf)」を参照してください。
