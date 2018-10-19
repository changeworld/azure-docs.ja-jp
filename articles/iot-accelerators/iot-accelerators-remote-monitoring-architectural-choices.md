---
title: リモート監視ソリューションのアーキテクチャの選択 - Azure | Microsoft Docs
description: この記事では、リモート監視に対して行ったアーキテクチャとテクノロジの選択について説明します
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/12/2018
ms.topic: conceptual
ms.openlocfilehash: 09c5981701ffdee5f2e5dba47cc98c91d5df7526
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603908"
---
# <a name="remote-monitoring-architectural-choices"></a>リモート監視のアーキテクチャの選択

Azure IoT リモート監視ソリューション アクセラレータは、MIT ライセンスで提供されるオープン ソースのソリューション アクセラレータで、デバイスの接続、デバイスの管理、ストリーム処理などの一般的な IoT シナリオを実現します。そのため、お客様は開発プロセスを高速化できます。  リモート監視ソリューションは、[こちら](https://aka.ms/iotrefarchitecture)で公開されている推奨の Azure IoT リファレンス アーキテクチャに従っています。  

この記事では、リモート監視ソリューションの各サブシステムに対して行ったアーキテクチャとテクノロジの選択と、検討した代替手段について説明します。  リモート監視ソリューションに対して行ったテクノロジの選択がリモート監視 IoT ソリューションを実装する唯一の方法ではないことに注意してください。  技術的な実装は、正常なアプリケーションを構築するためのベースラインであり、お客様のソリューションの実装に必要なスキル、経験、および垂直アプリケーションのニーズに合わせて変更する必要があります。

## <a name="architectural-choices"></a>アーキテクチャの選択

### <a name="microservices-serverless-and-cloud-native"></a>マイクロサービス、サーバーレス、およびクラウド ネイティブ

IoT アプリケーション向けの推奨アーキテクチャは、クラウド ネイティブ、マイクロサービス、およびサーバーレス ベースです。  IoT アプリケーションの個々のサブシステムは、個別にデプロイ可能で、個別に拡張可能な個別のサービスとして構築する必要があります。  そうすることで、個々のサブシステムの更新のスケーラビリティと柔軟性が向上し、サブシステム単位で適切なテクノロジを柔軟に選択できます。  マイクロサービスは、複数のテクノロジを使用して実装できます。 たとえば、Docker などのコンテナー テクノロジと Azure Functions などのサーバーレス テクノロジを使用したり、Azure App Services などの PaaS サービスでマイクロサービスをホストしたりすることができます。

## <a name="core-subsystem-technology-choices"></a>コア サブシステムのテクノロジの選択

このセクションでは、リモート監視ソリューションの各コア サブシステムに対して行ったテクノロジの選択について詳しく説明します。

![コア ダイアグラム](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>クラウド ゲートウェイ
Azure IoT Hub は、リモート監視ソリューションのクラウド ゲートウェイとして使用されます。  IoT Hub は、デバイスとの安全な双方向通信を提供します。 IoT Hub の詳細については、[こちら](https://azure.microsoft.com/services/iot-hub/)を参照してください。 IoT デバイス接続には、.NET Core と Java IoT Hub SDK を使用します。  この SDK は、IoT Hub REST API のラッパーを提供し、再試行などのシナリオを処理します。

### <a name="stream-processing"></a>ストリーム処理
ストリーム処理については、リモート監視ソリューションでは複雑なルールの処理に Azure Stream Analytics を使用します。  シンプルなルールを希望するお客様向けに、シンプルなルールの処理をサポートするカスタム マイクロサービスも用意していますが、そのセットアップは既定のデプロイには含まれていません。 リファレンス アーキテクチャでは、単純なルールの処理には Azure Functions を使用し、複雑なルールの処理には Azure Stream Analytics (ASA) を使用することを推奨しています。  

### <a name="storage"></a>Storage
ストレージに対しては、リモート監視ソリューション アクセラレータは Azure Time Series Insights と Azure Cosmos DB の両方を使用します。 Azure Time Series Insights は、接続されたデバイスから IoT Hub を介して送信されるメッセージを格納します。 このソリューション アクセラレータでは、コールド ストレージ、ルール定義、アラーム、構成設定など、その他のすべてのストレージに Azure Cosmos DB を使用します。 Azure Time Series Insights や Azure Data Lake などのソリューションは多くの用途に適していますが、IoT アプリケーション向けの推奨の汎用ウォーム ストレージ ソリューションは Azure Cosmos DB です。 Azure Time Series Insights を使用すると、傾向や異常に焦点を当てることで時系列のセンサー データのより深い分析情報を得ることができ、根本原因の分析を行ってコストのかかるダウンタイムを防ぐことができます。 

> [!NOTE]
> Azure China クラウドでは、現在、Time Series Insights はご利用になれません。 Azure China クラウドでの新しいリモート監視ソリューション アクセラレータのデプロイでは、すべてのストレージに Cosmos DB を使用します。

### <a name="business-integration"></a>ビジネス統合
リモート監視ソリューションのビジネス統合は、ウォーム ストレージに配置されるアラームの生成に制限されています。 このソリューションを Azure Logic Apps と統合することで、さらなるビジネス統合を実行できます。

### <a name="user-interface"></a>ユーザー インターフェイス
Web UI は JavaScript React で構築されています。  React は、業界で広く使用されている Web UI フレームワークを提供し、Angular などの他の一般的なフレームワークに似ています。  

### <a name="runtime-and-orchestration"></a>ランタイムとオーケストレーション
リモート監視ソリューションでサブシステムの実装に選択されたアプリケーション ランタイムは、水平スケールのオーケストレーターとして Kubernetes を使用する Docker コンテナーです。  このアーキテクチャでは、サブシステムごとに個別のスケール定義を作成できますが、セキュリティの面から VM とコンテナーを最新の状態に保つために DevOps コストがかかります。  Docker と Kubernetes の代わりに、PaaS サービス (Azure App Service など) でマイクロサービスをホスティングする方法や、Service Fabric、DCOS、Swarm などをオーケストレーターとして使用する方法があります。

## <a name="next-steps"></a>次の手順
* [ここに](https://www.azureiotsolutions.com/)リモート監視ソリューション アクセラレータをデプロイする
* GitHub のコードについては、こちら ([C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) および [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)) をご覧ください。  
* IoT リファレンス アーキテクチャの詳細については、[こちら](https://aka.ms/iotrefarchitecture)をご覧ください。
