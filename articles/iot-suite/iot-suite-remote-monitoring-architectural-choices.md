---
title: リモート監視ソリューションのアーキテクチャの選択 - Azure | Microsoft Docs
description: この記事では、リモート監視に対して行ったアーキテクチャとテクノロジの選択について説明します
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 192a763c01e60d816ae2046587176627fc9d8736
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781551"
---
# <a name="remote-monitoring-architectural-choices"></a>リモート監視のアーキテクチャの選択

Azure IoT リモート監視 (RM) は、MIT ライセンスで提供されるオープン ソースのソリューション アクセラレータで、デバイスの接続、デバイスの管理、ストリーム処理などの一般的な IoT シナリオを実現します。そのため、お客様は開発プロセスを高速化できます。  RM は、[こちら](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)で公開されている推奨の Azure IoT リファレンス アーキテクチャに従っています。  

この記事では、RM の各サブシステムに対して行ったアーキテクチャとテクノロジの選択と、検討した代替手段について説明します。  RM に対して行ったテクノロジの選択がリモート監視 IoT ソリューションを実装する唯一の方法ではないことに注意してください。  技術的な実装は、正常なアプリケーションを構築するためのベースラインであり、お客様のソリューションの実装に必要なスキル、経験、および垂直アプリケーションのニーズに合わせて変更する必要があります。

## <a name="architectural-choices"></a>アーキテクチャの選択

### <a name="microservices-serverless-and-cloud-native"></a>マイクロサービス、サーバーレス、およびクラウド ネイティブ

IoT アプリケーション向けの推奨アーキテクチャは、クラウド ネイティブ、マイクロサービス、およびサーバーレス ベースです。  IoT アプリケーションの個々のサブシステムは、個別にデプロイ可能で、個別に拡張可能な個別のサービスとして構築する必要があります。  そうすることで、個々のサブシステムの更新のスケーラビリティと柔軟性が向上し、サブシステム単位で適切なテクノロジを柔軟に選択できます。  マイクロサービスは、複数のテクノロジを使用して実装できます。 たとえば、Docker などのコンテナー テクノロジと Azure Functions などのサーバーレス テクノロジを使用したり、Azure App Services などの PaaS サービスでマイクロサービスをホストしたりすることができます。

## <a name="core-subsystem-technology-choices"></a>コア サブシステムのテクノロジの選択

このセクションでは、RM の各コア サブシステムに対して行ったテクノロジの選択について詳しく説明します。

![コア ダイアグラム](media/iot-suite-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>クラウド ゲートウェイ
RM クラウド ゲートウェイとして Azure IoT Hub を使用します。  IoT Hub は、デバイスとの安全な双方向通信を提供します。 IoT Hub の詳細については、[こちら](https://azure.microsoft.com/services/iot-hub/)を参照してください。 IoT デバイス接続については、.NET Core と Java IoT Hub SDK を使用します。  この SDK は、IoT Hub REST API のラッパーを提供し、再試行などのシナリオを処理します。 

### <a name="stream-processing"></a>ストリーム処理
ストリーム処理については、RM では複雑なルールの処理に Azure Stream Analytics を使用します。  シンプルなルールを希望するお客様向けに、シンプルなルールの処理をサポートするカスタム マイクロサービスも用意していますが、そのセットアップは既定のデプロイには含まれていません。 リファレンス アーキテクチャでは、単純なルールの処理には Azure Functions を使用し、複雑なルールの処理には Azure Stream Analytics (ASA) を使用することを推奨しています。  

### <a name="storage"></a>Storage
ストレージについては、コールド ストレージ、ウォーム ストレージ、ルール ストレージ、アラームのすべてのストレージ ニーズに対して Cosmos DB を使用します。 リファレンス アーキテクチャの推奨に従って、現在 Azure Blob Storage への移行を進めています。  Azure Time Series Insights や Azure Data Lake などのソリューションは多くの用途に適していますが、IoT アプリケーション向けの推奨の汎用ウォーム ストレージ ソリューションは Cosmos DB です。

### <a name="business-integration"></a>ビジネス統合
RM のビジネス統合は、ウォーム ストレージに配置されるアラームの生成に制限されています。 このソリューションを Azure Logic Apps と統合することで、さらなるビジネス統合を実行できます。

### <a name="user-interface"></a>ユーザー インターフェイス
Web UI は JavaScript React で構築されています。  React は、業界で広く使用されている Web UI フレームワークを提供し、Angular などの他の一般的なフレームワークに似ています。  

### <a name="runtime-and-orchestration"></a>ランタイムとオーケストレーション
RM でサブシステムの実装に選択されたアプリケーション ランタイムは、水平スケールのオーケストレーターとして Kubernetes (K8s) を使用する Docker コンテナーです。  このアーキテクチャでは、サブシステムごとに個別のスケール定義を作成できますが、セキュリティの面から VM とコンテナーを最新の状態に保つために DevOps コストがかかります。  Docker と K8s の代わりに、PaaS サービス (Azure App Service など) でマイクロサービスをホスティングする方法や、Service Fabric、DCOS、Swarm などをオーケストレーターとして使用する方法があります。

## <a name="next-steps"></a>次の手順
* RM ソリューションのデプロイについては、[こちら](https://www.azureiotsuite.com/)をご覧ください。
* GitHub のコードについては、こちら ([C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) および [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)) をご覧ください。  
* IoT リファレンス アーキテクチャの詳細については、[こちら](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)をご覧ください。
