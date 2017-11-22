---
title: "リモート監視ソリューションのアーキテクチャ - Azure | Microsoft Docs"
description: "構成済みリモート監視ソリューションのアーキテクチャについてのチュートリアルです。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: e19ba9c88e4fbe4f065c45ce7029247436f7155c
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
# <a name="remote-monitoring-preconfigured-solution-architecture"></a>構成済みリモート監視ソリューションのアーキテクチャ

IoT Suite のリモート監視用[構成済みソリューション](iot-suite-what-are-preconfigured-solutions.md)は、遠隔地で実行されている複数のコンピューターを対象としたエンド ツー エンドの監視ソリューションを実装します。 このソリューションは、主要な Azure サービスを組み合わせることで、ビジネス シナリオの汎用的な実装を実現したものです。 このソリューションを実装の出発点として使用し、お客様固有のビジネス要件を満たすように[カスタマイズ](iot-suite-remote-monitoring-customize.md)することができます。

この記事では、リモート監視ソリューションのしくみについて理解しやすいように、その主な構成要素をいくつか取り上げて説明します。 この知識は以下の作業に役立ちます。

* ソリューションの問題のトラブルシューティングを行う。
* 独自の要件を満たすためにソリューションをカスタマイズする方法を計画する。
* Azure サービスを利用する独自の IoT ソリューションを設計する。

## <a name="logical-architecture"></a>論理アーキテクチャ

次の図は、[IoT アーキテクチャ](iot-suite-what-is-azure-iot.md)と重ね合わせた構成済みリモート監視ソリューションの論理コンポーネントの概要です。

![論理アーキテクチャ](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>マイクロサービスについて

クラウドのアーキテクチャは、Microsoft がはじめて構成済みソリューションをリリースした当時から進化し続けてきました。 [マイクロサービス](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)は、開発速度を犠牲にすることなく、拡張性と柔軟性を達成できることが実証された方法として登場してきました。 Microsoft のサービスは、このアーキテクチャ パターンを内部的に使用し、優れた信頼性とスケーラビリティの成果を出しています。 最新の構成済みソリューションには、これらの成果が取り込まれているため、その成果を活用できます。

> [!TIP]
> マイクロサービス アーキテクチャの詳細については、「[.NET Application Architecture (.NET アプリケーション アーキテクチャ)](https://www.microsoft.com/net/learn/architecture)」および「[Microservices: An application revolution powered by the cloud. (マイクロサービス: クラウドによって実現されるアプリケーションの革命)](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)」を参照してください。

## <a name="device-connectivity"></a>デバイスの接続性

ソリューションには、論理アーキテクチャのデバイス接続部分に、次のコンポーネントが含まれています。

### <a name="simulated-devices"></a>シミュレートされたデバイス

ソリューションには、ソリューション内のフローのエンドツーエンド試験用にシミュレートされたデバイス プールを管理するためのマイクロサービスが含まれています。 シミュレートされたデバイスは、以下を実行します。

* デバイスとクラウド間の利用統計情報を生成します。
* IoT Hub から送信された、クラウドとデバイス間で通信するためのメソッド呼び出しに応答します。

マイクロサービスは、シミュレーションを作成、開始、および停止するための RESTful エンドポイントを提供します。 各シミュレーションは、利用統計情報を送信したり、メソッドの呼び出しに応答するための、さまざまな種類の仮想デバイスで構成されます。

ソリューション ポータルのダッシュ ボードからシミュレートされたデバイスをプロビジョニングすることができます。

### <a name="physical-devices"></a>物理デバイス

ソリューションには、物理デバイスを接続できます。 Azure IoT device SDK を使用して、シミュレートされたデバイスの動作を実装できます。

ソリューション ポータルのダッシュ ボードから物理デバイスをプロビジョニングできます。

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub と IoT manager マイクロサービス

[IoT Hub ](../iot-hub/index.md)は、デバイスから送信されたデータをクラウドに取り込んで、`telemetry-agent` マイクロサービスに提供します。

ソリューションの IoT ハブは、以下の操作も行います。

* ポータルへの接続を許可されているすべてのデバイスの ID と認証キーを格納する ID レジストリを維持します。 ID レジストリを通じて、デバイスを有効および無効にすることができます。
* ソリューション ポータルに代わって、デバイスのメソッドを呼び出します。
* すべての登録済みデバイスのデバイス ツインを保持します。 デバイス ツインは、デバイスによって報告されたプロパティの値を格納します。 また、デバイスが次回の接続時に取得できるように、ソリューション ポータルで設定された必要なプロパティも格納します。
* 複数のデバイスのプロパティを設定したり、複数のデバイスに対してメソッドを呼び出したりするジョブをスケジュールします。

ソリューションには、IoT hub との次のような交信を処理するための `iot-manager` マイクロサービスが含まれます。

* IoT デバイスの作成と管理
* デバイス ツインの管理
* デバイスでのメソッドの呼び出し
* IoT 資格情報の管理

このサービスは、ユーザー定義のグループに属するデバイスを取得するための IoT Hub クエリも実行します。

マイクロサービスは、デバイスおよびデバイス ツインの管理、メソッドの呼び出しや IoT Hub クエリを実行するための RESTful エンドポイントを提供します。

## <a name="data-processing-and-analytics"></a>データ処理と分析

ソリューションには、論理アーキテクチャのデータ処理および分析部分に、次のコンポーネントが含まれています。

### <a name="device-telemetry"></a>デバイス テレメトリ

ソリューションには、デバイスのテレメトリを処理する 2 つのマイクロサービスが含まれています。

[telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet) マイクロサービス。

* Cosmos DB に利用統計情報を格納します。
* デバイスからの利用統計情報ストリームを分析します。
* 定義された規則に従ってアラームを生成します。

アラームは Cosmos DB に格納されます。

`telemetry-agent`マイクロサービスを使用することにより、ソリューション ポータルは、デバイスから送信された利用統計情報を読み取ることができます。 ソリューション ポータルは、以下の目的でもこのサービスを使用します。

* アラームをトリガーするしきい値などの監視ルールを定義する
* 過去のアラームの一覧を取得する

このマイクロサービスによって提供される RESTful エンドポイントを使用しテレメトリ、ルール、およびアラームを管理できます。

### <a name="storage"></a>ストレージ

[storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) マイクロサービスは、構成済みのソリューションに使用されるメイン ストレージ サービスの前にあるアダプターです。 単純なコレクションおよびキー値のためのストレージを提供します。

構成済みソリューションの標準的な展開では、メイン ストレージ サービスとして Cosmos DB が使用されます。

Cosmos DB データベースでは、構成済みソリューションにデータを格納します。 **storage-adapter** マイクロサービスは、ソリューション内の他のマイクロサービスがストレージ サービスにアクセスするためのアダプターとして機能します。

## <a name="presentation"></a>プレゼンテーション

ソリューションには、論理アーキテクチャのプレゼンテーション部分に、次のコンポーネントが含まれています。

[Web ユーザー インターフェイスは、React Javascript アプリケーション](https://github.com/Azure/pcs-remote-monitoring-webui)です。 アプリケーションでは

* Javascript React 　のみを使用し、すべてブラウザー内で実行されます。
* CSS スタイルを使用します。
* AJAX 呼び出しを通してパブリックのマイクロサービスと交信します。

ユーザー インターフェイスが構成済みのソリューションの機能を表し、次のような他のサービスと交信します。

* [authentication](https://github.com/Azure/pcs-auth-dotnet) マイクロサービスと交信してユーザー データを保護します。
* [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) サービスと交信してサービスを一覧表示し、IoT デバイスを管理します。

[ui-config](https://github.com/Azure/pcs-config-dotnet) マイクロサービスと交信して、構成設定を保存および取得するためのユーザー インターフェイスを実装します。

## <a name="next-steps"></a>次のステップ

ソース コードと開発者のマニュアルを参照する場合は、次の 2 つのメイン GitHub リポジトリのいずれかを参照してください。

* [Azure IoT (.NET) を使用してリモート監視するための構成済みソリューション](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/)。
* [Azure IoT (Java) で使用してリモート監視するための構成済みソリューション](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)。
* [リモート監視アーキテクチャの構成済みソリューション](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)。

リモート監視の構成済みソリューションの概念の詳細については、[構成済みソリューションのカスタマイズ](iot-suite-remote-monitoring-customize.md)に関するページを参照してください。
