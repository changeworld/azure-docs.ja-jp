---
title: Azure IoT 用語集 | Microsoft Docs
description: 開発者ガイド - Azure IoT の記事で使用される一般的な用語の一部について説明した用語集です。
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: a45b8db1f17ead8e55bac2b2bf82aa44cb7f080e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304700"
---
# <a name="glossary-of-iot-terms"></a>IoT 用語集

この記事では、IoT の記事で使用される一般的な用語の一部を示します。

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol

[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) は、デバイスと通信するために [IoT Hub](#iot-hub) がサポートするメッセージング プロトコルの 1 つです。 IoT Hub がサポートするメッセージング プロトコルの詳細については、「[IoT Hub でのメッセージの送受信](../iot-hub/iot-hub-devguide-messaging.md)」を参照してください。

### <a name="allocation-policy"></a>割り当てポリシー

[Device Provisioning Service](#device-provisioning-service) では、[リンクされた IoT ハブ](#linked-iot-hub)へのデバイスの割り当て方法が、割り当てポリシーによって決まります。

### <a name="attestation-mechanism"></a>構成証明メカニズム

[Device Provisioning Service](#device-provisioning-service) では、デバイスの ID を確認する手段として構成証明メカニズムが使用されます。 構成証明メカニズムは、[登録](#enrollment)時に構成されます。

構成証明メカニズムには、X.509 証明書、トラステッド プラットフォーム モジュール、対称キーが含まれます。

### <a name="automatic-deployment"></a>自動展開

IoT Edge の自動デプロイでは、一連の IoT Edge モジュールを実行する IoT Edge デバイスの対象のセットを構成します。 各デプロイでは、新しいデバイスが作成されたかターゲット条件と一致するよう変更されたときでも、対象の条件と一致するすべてのデバイスが特定のモジュールのセットを実行することが継続的に保証されます。 各 IoT Edge デバイスは、そのターゲット条件を満たす最優先のデプロイのみを受け取ります。 詳細については、[IoT Edge の自動デプロイ](../iot-edge/module-deployment-monitoring.md)に関するページをご覧ください。

### <a name="automatic-device-configuration"></a>自動デバイス構成

ソリューション バックエンドは、[自動デバイス構成](../iot-hub/iot-hub-automatic-device-management.md)を使用することで、[デバイス ツイン](#device-twin)のセットに必要なプロパティを割り当て、システム メトリックとカスタム メトリックを使って状態を報告することができます。

### <a name="automatic-device-management"></a>自動デバイス管理

Azure IoT Hub の自動デバイス管理では、大規模な数のデバイスをそのライフサイクル全体にわたって管理するための、複雑で反復的なタスクを大幅に自動化できます。 自動デバイス管理を利用すると、プロパティに基づいて対象のデバイス セットを設定し、必要な構成を定義して、デバイスがスコープ内になったときにいつでも IoT Hub がデバイスを更新できるようにすることができます。  [自動デバイス構成](../iot-hub/iot-hub-automatic-device-management.md)と [IoT Edge の自動展開](../iot-edge/how-to-deploy-at-scale.md)で構成されます。

### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins は、実世界の物、場所、ビジネス プロセス、人についてデジタル表現を作成するための PaaS (サービスとしてのプラットフォーム) オファリングです。 環境全体を表すナレッジ グラフを作成し、それらを使用して、製品の質を高め、運用とコストを最適化し、カスタマー エクスペリエンスを通じてブレークスルーをもたらす分析情報を入手します。 詳細については、[Azure Digital Twins](../digital-twins/index.yml) に関するドキュメントを参照してください。

### <a name="azure-digital-twins-instance"></a>Azure Digital Twins インスタンス

お客様のサブスクリプションにおける、Azure Digital Twins サービスの単一のインスタンス。 [Azure Digital Twins](#azure-digital-twins) が Azure サービス全体を指すのに対して、Azure Digital Twins **インスタンス** は、個々の Azure Digital Twins リソースを指します。

### <a name="azure-iot-device-sdks"></a>Azure IoT device SDK

複数の言語で使用可能な _デバイス SDK_ があり、IoT Hub と対話する [デバイス アプリ](#device-app)を作成できます。 IoT Hub のチュートリアルでは、これらのデバイス SDK を使用する方法を説明します。 デバイス SDK のソース コードと詳細については、この GitHub [リポジトリ](https://github.com/Azure/azure-iot-sdks)を参照してください。

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

[Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) を使用して、デバイスから送信されているテレメトリの表示、デバイス プロパティの操作、およびコマンドの呼び出しを行います。 また、エクスプローラーを使用して、デバイスとの対話およびテストを行ったり、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)を管理したりすることもできます。

### <a name="azure-iot-service-sdks"></a>Azure IoT サービス SDK

複数の言語で使用可能な _サービス SDK_ があり、IoT Hub と対話する [バックエンド アプリ](#back-end-app)を作成できます。 IoT Hub のチュートリアルでは、これらのサービス SDK を使用する方法を説明します。 サービス SDK のソース コードと詳細については、この GitHub [リポジトリ](https://github.com/Azure/azure-iot-sdks)を参照してください。

### <a name="azure-iot-tools"></a>Azure IoT Tools

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) は、VS Code で Azure IoT Hub やデバイスを管理するのに役立つ、クロスプラットフォームでかつオープンソースの Visual Studio Code 拡張機能です。 Azure IoT Tools を使用すると、IoT 開発者は VS Code で IoT プロジェクトを容易に開発できます。

## <a name="b"></a>B

### <a name="back-end-app"></a>バックエンド アプリ

[IoT Hub](#iot-hub) においては、バックエンド アプリは、IoT Hub 上のサービス向けエンドポイントのいずれかに接続するアプリです。 たとえば、バックエンド アプリは[デバイスからクラウド](#device-to-cloud)へのメッセージの取得や、[ID レジストリ](#identity-registry)の管理を行います。 通常、バック エンド アプリはクラウドで実行されますが、チュートリアルの多くでは、バックエンド アプリはローカル開発用コンピューターで実行されるコンソール アプリです。

### <a name="built-in-endpoints"></a>組み込みのエンドポイント

IoT Hub に組み込まれている[エンドポイント](#endpoint)のタイプ。 すべての IoT Hub には、Event Hub と互換性がある組み込みの[エンドポイント](../iot-hub/iot-hub-devguide-endpoints.md)が含まれています。 Event Hub で動作する任意のメカニズムを使用して、このエンドポイントからクラウドへのデバイスのメッセージを読み取ることができます。

## <a name="c"></a>C

### <a name="cloud-gateway"></a>クラウド ゲートウェイ

クラウド ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できないデバイスの接続を有効にします。 デバイスでローカルに実行される[フィールド ゲートウェイ](#field-gateway)とは異なり、クラウド ゲートウェイはクラウドでホストされます。 クラウド ゲートウェイの一般的なユース ケースは、デバイスにプロトコル変換を実装することです。

### <a name="cloud-to-device"></a>クラウドからデバイスへ

IoT Hub から接続されたデバイスに送信されるメッセージを指します。 多くの場合、これらのメッセージは、デバイスにアクションを実行することを指示するコマンドです。 詳細については、「[IoT Hub でのメッセージの送受信](../iot-hub/iot-hub-devguide-messaging.md)」を参照してください。

### <a name="commands"></a>コマンド

IoT プラグ アンド プレイにおいて、[インターフェイス](#interface)で定義されたコマンドは、[デジタル ツイン](#digital-twin)で実行できるメソッドを表します。 たとえば、デバイスを再起動するコマンドがあります。

### <a name="component"></a>コンポーネント

IoT プラグ アンド プレイおよび Azure Digital Twins では、コンポーネントを使用すると、他のインターフェイスのアセンブリとしてモデル [インターフェイス](#interface)を作成できます。 [デバイス モデル](#device-model)では、複数のインターフェイスをコンポーネントとして組み合わせることができます。 たとえば、モデルにスイッチ コンポーネントとサーモスタット コンポーネントを含めることができます。 モデル内の複数のコンポーネントで、同じインターフェイスの種類を使用することもできます。 たとえば、モデルに 2 つのサーモスタット コンポーネントを含めることができます。

### <a name="configuration"></a>構成

[自動デバイス構成](../iot-hub/iot-hub-automatic-device-management.md)のコンテキストでは、IoT Hub 内の構成は、デバイス ツインのセットに必要な構成を定義し、状態と進行状況を報告するためのメトリックのセットを提供します。

### <a name="connection-string"></a>接続文字列

アプリのコード内の接続文字列を使用して、エンドポイントへの接続に必要な情報をカプセル化します。 通常、接続文字列には、エンドポイントのアドレスとセキュリティ情報が含まれますが、接続文字列の形式はサービス間で異なります。 IoT Hub サービスに関連付けられた接続文字列には次の 2 種類があります。

- *デバイス接続文字列* を使用すると、IoT Hub 上のデバイス向けエンドポイントにデバイスを接続できます。

- *接続文字列* を使用すると、IoT Hub 上のサービス向けエンドポイントにバックエンド アプリを接続できます。

### <a name="custom-endpoints"></a>カスタム エンドポイント

[ルーティング ルール](#routing-rules)によって送信されたメッセージを配信するカスタム [エンドポイント](../iot-hub/iot-hub-devguide-endpoints.md)を IoT hub に作成できます。 カスタム エンドポイントは、Event hub、Service Bus キュー、または Service Bus トピックに直接接続します。

### <a name="custom-gateway"></a>カスタム ゲートウェイ

ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できないデバイスの接続を有効にします。 Azure IoT Edge を使用して、メッセージを処理するカスタム ロジック、カスタム プロトコルの変換、およびその他の処理を Edge 上で実装するカスタム ゲートウェイを構築できます。

## <a name="d"></a>D

### <a name="data-point-message"></a>データ ポイント メッセージ

データ ポイント メッセージは、風速や温度などの[テレメトリ](#telemetry) データを含む[デバイスからクラウドへの](#device-to-cloud)メッセージです。

### <a name="default-component"></a>既定のコンポーネント

IoT プラグ アンド プレイでは、すべての[デバイス モデル](#device-model)に既定のコンポーネントがあります。 単純なデバイス モデルには、既定のコンポーネントのみがあります。このようなモデルは、コンポーネント デバイスなしとも呼ばれます。 より複雑なモデルでは、既定のコンポーネントの下に複数のコンポーネントが入れ子になっています。

### <a name="deployment-manifest"></a>配置マニフェスト

[IoT Edge](#iot-edge) におけるデプロイ マニフェストとは、一連のモジュール、ルート、関連するモジュールに必要なプロパティをデプロイするために、1 つ以上の IoT Edge デバイスのモジュール ツインにコピーされる情報を含んだ JSON ドキュメントです。

### <a name="desired-configuration"></a>必要な構成

[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)においては、必要な構成とは、デバイスと同期する必要がある、デバイス ツイン内のプロパティとメタデータの完全なセットを指します。

### <a name="desired-properties"></a>必要なプロパティ

[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)においては、必要なプロパティは、デバイスの構成や状態を同期するために[報告されるプロパティ](#reported-properties)とともに使用される、デバイス ツインのサブセクションです。 必要なプロパティは[バックエンド アプリ](#back-end-app)のみで設定でき、[デバイス アプリ](#device-app)によって監視されます。

### <a name="device"></a>Device

IoT においては、デバイスは、通常は、データの収集や他のデバイスの制御を実行できる、小規模なスタンドアロン コンピューティング デバイスです。 環境監視デバイス (温室の散水システムや換気システムのコントローラー) はデバイスの一例です。 [デバイス カタログ](https://catalog.azureiotsolutions.com/)は、[IoT Hub](#iot-hub) で機能することが認定されたハードウェア デバイスの一覧を示します。

### <a name="device-app"></a>デバイス アプリ

デバイス アプリは、[デバイス](#device)で実行され、[IoT Hub](#iot-hub) との通信を処理します。 デバイス アプリを実装する場合、通常 [Azure IoT device SDK](#azure-iot-device-sdks) のいずれかを使用します。 IoT チュートリアルの多くでは、利便性のために[シミュレートされたデバイス](#simulated-device)を使用します。

### <a name="device-builder"></a>デバイス ビルダー

デバイス ビルダーは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)で実行するコードを実装するときに、[デバイス モデル](#device-model)と[インターフェイス](#interface)を使用します。 デバイス ビルダーでは通常、デバイス クライアントを実装するために [Azure IoT device SDK](#azure-iot-device-sdks) のいずれかを使用します。

### <a name="device-certification"></a>デバイス認定

IoT プラグ アンド プレイ デバイス認定プログラムは、IoT プラグ アンド プレイの認定要件をデバイスが満たしていることを確認するものです。 認定されたデバイスは、公開されている [Azure IoT 認定デバイス カタログ](https://aka.ms/devicecatalog)に登録できます。

### <a name="device-condition"></a>デバイスの状態

[デバイス アプリ](#device-app)によって報告されるデバイスの状態情報 (現在使用中の接続方法など) を指します。 [デバイス アプリ](#device-app)は、そのデバイスが備えている機能も報告できます。 デバイス ツインを使用して、状態と機能の情報のクエリを実行できます。

### <a name="device-data"></a>デバイス データ

デバイス データとは、IoT Hub の [ID レジストリ](#identity-registry)に格納されている個々のデバイスのデータを指します。 このデータのインポートおよびエクスポートを行うことができます。

### <a name="device-identity"></a>デバイス ID

デバイス ID (デバイス識別子) は、IoT Hub の [ID レジストリ](#identity-registry)に登録されているすべてのデバイスに割り当てられた一意の識別子です。

### <a name="device-management"></a>デバイス管理

デバイス管理には、計画、プロビジョニング、構成、監視、インベントリからの削除などを含む IoT ソリューション内のデバイスの管理に関連するライフ サイクル全体が含まれます。

### <a name="device-management-patterns"></a>デバイス管理パターン

[IoT Hub](#iot-hub) では、デバイスでの再起動、工場出荷時リセットの実行、ファームウェア更新プログラムの実行など、一般的なデバイス管理のパターンを使用できます。

### <a name="device-model"></a>デバイスのモデル

デバイス モデルは、[Digital Twins Definition Language](#digital-twins-definition-language-dtdl) を使用して IoT プラグ アンド プレイ デバイスの機能を記述する[モデル](#model)の一種です。 単純なデバイス モデルでは、デバイスの機能を記述するために 1 つのインターフェイスが使用されます。 より複雑なデバイス モデルには複数のコンポーネントが含まれており、それぞれに一連の機能が記述されています。 詳細については、[モデル内の IoT プラグ アンド プレイ コンポーネント](../iot-pnp/concepts-components.md)に関する記事を参照してください。

### <a name="device-modeling"></a>デバイス モデリング

[デバイス ビルダー](#device-builder)または[モジュール ビルダー](#module-builder)では、[Digital Twins 定義言語](#digital-twins-definition-language-dtdl)を使用して、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)の機能をモデル化します。 [ソリューション ビルダー](#solution-builder)は、このモデルから IoT ソリューションを構成できます。

### <a name="device-provisioning"></a>デバイス プロビジョニング

デバイス プロビジョニングとは、最初の[デバイス データ](#device-data)をソリューション内のストアに追加するプロセスです。 新しいデバイスをハブに接続できるようにするには、デバイスの ID とキーを IoT Hub [ID レジストリ](#identity-registry)に追加する必要があります。 プロビジョニング プロセスの一環として、他のソリューション ストアにあるデバイス固有データの初期化が必要になる場合があります。

### <a name="device-provisioning-service"></a>デバイス プロビジョニング サービス

IoT Hub Device Provisioning Service (DPS) は [IoT Hub](#iot-hub) のヘルパー サービスです。指定された IoT ハブにプロビジョニングするゼロタッチ デバイスの構成に使用されます。 DPS を使用すると、セキュリティで保護されたスケーラブルな方法で何百万台ものデバイスをプロビジョニングできます。

### <a name="device-rest-api"></a>デバイス REST API

デバイスから [Device REST API](/rest/api/iothub/device) を使用して、デバイスからクラウドへのメッセージを IoT Hub に送信し、[クラウドからデバイスへ](#cloud-to-device)のメッセージを IoT Hub から受信できます。 IoT Hub チュートリアルで示されているように、通常、高レベルの[デバイス SDK](#azure-iot-device-sdks) のいずれかを使用する必要があります。

### <a name="device-twin"></a>デバイス ツイン

デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub でプロビジョニングする各デバイスにデバイス ツインを保持します。 デバイス ツインを使用すると、デバイスとソリューションのバック エンド間でデバイスの状態と構成を同期できます。 デバイス ツインにクエリを実行して、特定のデバイスや、長時間実行されている操作の状態を見つけることができます。

### <a name="device-to-cloud"></a>デバイスからクラウドへ

接続されたデバイスから [IoT Hub](#iot-hub) に送信されるメッセージを指します。 これらのメッセージは、[データ ポイント](#data-point-message)または[対話型](#interactive-message)メッセージの場合があります。 詳細については、「[IoT Hub でのメッセージの送受信](../iot-hub/iot-hub-devguide-messaging.md)」を参照してください。

### <a name="digital-twin"></a>デジタル ツイン

デジタル ツインは、物理オブジェクトを表すデジタル データのコレクションです。 物理オブジェクトでの変更は、デジタル ツインに反映されます。 状況によっては、デジタル ツインを使用して物理オブジェクトを操作できます。 [Azure Digital Twins サービス](../digital-twins/index.yml)では、[Digital Twins Definition Language (DTDL)](#digital-twins-definition-language-dtdl) で記述された[モデル](#model)を使用して、物理デバイスのデジタル ツインまたはそれ以上のレベルの抽象的なビジネス概念を表現することで、クラウドベースの多様なデジタル ツイン ソリューションを実現します。 [IoT プラグ アンド プレイ](../iot-pnp/index.yml) デバイスには、DTDL [デバイス モデル](#device-model)によって記述されたデジタル ツインがあります。

### <a name="digital-twin-change-events"></a>デジタル ツインの変更イベント

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)が IoT ハブに接続されると、このハブはそのルーティング機能を使用して、デジタル ツインの変更に関する通知を送信できます。 たとえば、デバイス上で[プロパティ](#properties)値が変更されるたびに、IoT Hub では通知をイベント ハブなどのエンドポイントに送信できます。

### <a name="digital-twin-route"></a>デジタル ツイン ルート

[デジタル ツインの変更イベント](#digital-twin-change-events)をイベント ハブなどのエンドポイントに配信するために IoT ハブで設定されたルート。

### <a name="digital-twins-definition-language-dtdl"></a>Digital Twins Definition Language (DTDL)

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)と [Azure Digital Twins](../digital-twins/index.yml) エンティティの[モデル](#model)と[インターフェイス](#interface)を記述するための JSON-LD 言語。 [デジタル ツイン](#digital-twin)の機能を記述したり、IoT プラットフォームと IoT ソリューションでエンティティのセマンティクスを使用できるようにしたりするには、[Digital Twins Definition Language バージョン 2](https://github.com/Azure/opendigitaltwins-dtdl) を使用します。 Digital Twins Definition Language は、多くの場合 DTDL と略記されます。

### <a name="direct-method"></a>ダイレクト メソッド

[ダイレクト メソッド](../iot-hub/iot-hub-devguide-direct-methods.md)は、IoT Hub 上で API を呼び出すことによって、デバイス上で実行するメソッドをトリガーするための方法です。

### <a name="downstream-services"></a>ダウンストリーム サービス

現在のコンテキストからデータを取得するサービスを表す相対的な用語。 たとえば、Azure Digital Twins から Time Series Insights へのデータ フローを設定する場合、Azure Digital Twins のコンテキストで考えると、[Time Series Insights](../time-series-insights/index.yml) はダウンストリーム サービスと見なされます。

## <a name="e"></a>E

### <a name="endpoint"></a>エンドポイント

他のサービスからデータを取得できるデータ ルーティング サービスの名前付きの表現。

IoT Hub は、アプリの IoT Hub への接続を有効にする複数の[エンドポイント](../iot-hub/iot-hub-devguide-endpoints.md)を公開します。 [デバイスからクラウドへ](#device-to-cloud)のメッセージの送信や、[クラウドからデバイスへ](#cloud-to-device)のメッセージの受信といった操作をデバイスで実行できるようにする、デバイス向けのエンドポイントがあります。 [デバイス ID](#device-identity) の管理とデバイス ツインの管理といった操作を[バックエンド アプリ](#back-end-app)で実行できるようにする、サービス向けの管理エンドポイントがあります。 デバイスからクラウドへのメッセージを読み取るためのサービス向けの[組み込みエンドポイント](#built-in-endpoints)があります。 [ルーティング ルール](#routing-rules)によって送信されたデバイスからクラウドへのメッセージを受信するカスタム [エンドポイント](#custom-endpoints)を作成できます。

### <a name="enrollment"></a>登録

[Device Provisioning Service](#device-provisioning-service) における登録とは、自動プロビジョニングを通じて[リンクされた IoT ハブ](#linked-iot-hub)に登録できる個々のデバイスや一連のデバイスを記録することです。

### <a name="enrollment-group"></a>加入グループ

[Device Provisioning Service](#device-provisioning-service) では、X.509 または対称キーの[構成証明メカニズム](#attestation-mechanism)を共有する一連のデバイスが、登録グループによって識別されます。

### <a name="event-handlers"></a>イベント ハンドラー

これは、イベントの到着によってトリガーされてなんらかの処理アクションを実行するあらゆるプロセスを指します。 イベント ハンドラーを作成する方法の 1 つは、Azure 関数にイベント処理コードを追加し、[エンドポイント](#endpoint)と[イベント ルーティング](#event-routing)を使用してそれを介してデータを送信することです。

### <a name="event-hub-compatible-endpoint"></a>Event Hub と互換性があるエンドポイント

IoT Hub に送信される[デバイスからクラウドへ](#device-to-cloud)のメッセージを読み取るには、ハブ上のエンドポイントに接続し、Event Hub と互換性があるメソッドを使用してそれらのメッセージを読み取ることができます。 Event Hub と互換性があるメソッドには、[Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) と [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) の使用が含まれます。

### <a name="event-routing"></a>イベント ルーティング

あるデバイスまたはサービスから他のデバイスまたはサービスの[エンドポイント](#endpoint)にイベントとそのデータを送信するプロセス。 

IoT Hub で、メッセージの送信方法を表す[ルーティング規則](#routing-rules)を定義できます。 Azure Digital Twins で、この目的のために作成されるエンティティがイベント ルートです。 Azure Digital Twins のイベント ルートには、各エンドポイントに送信されるイベントの種類を制限するフィルターを含めることができます。

## <a name="f"></a>F

### <a name="field-gateway"></a>フィールド ゲートウェイ

フィールド ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できないデバイスを接続できるようにします。通常はデバイスにローカルにデプロイされます。 詳細については、「[Azure IoT Hub とは](../iot-hub/about-iot-hub.md)」を参照してください。

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できないデバイスの接続を有効にします。 [フィールド ゲートウェイ](#field-gateway)、[クラウド ゲートウェイ](#cloud-gateway)および[カスタム ゲートウェイ](#custom-gateway)もご確認ください。

### <a name="gateway-device"></a>ゲートウェイ デバイス

デバイスは、[フィールド ゲートウェイ](#field-gateway)の一例です。 ゲートウェイ デバイスには、標準の IoT [デバイス](#device)または [IoT Edge デバイス](#iot-edge-device)があります。

[IoT Hub](#iot-hub) に直接接続できないダウンストリーム デバイスの接続が、ゲートウェイ デバイスによって可能になります。

## <a name="h"></a>H

### <a name="hardware-security-module"></a>ハードウェア セキュリティ モジュール

ハードウェア セキュリティ モジュール (HSM) は、デバイス シークレットをハードウェアベースで安全に格納するために使用されます。 これは、デバイスのシークレット ストレージとして最も安全な形態です。 X.509 証明書と対称キーの両方を HSM に格納できます。 [Device Provisioning Service](#device-provisioning-service) では、[構成証明メカニズム](#attestation-mechanism)で HSM を使用できます。

## <a name="i"></a>I

### <a name="id-scope"></a>ID スコープ

ID スコープは、[Device Provisioning Service (DPS)](#device-provisioning-service) インスタンスに、その作成時に割り当てられる一意の値です。

IoT Central アプリケーションでは DPS インスタンスを活用し、IoT Central の UI を通じて ID スコープを公開します。

### <a name="identity-registry"></a>ID レジストリ

[ID レジストリ](../iot-hub/iot-hub-devguide-identity-registry.md)は、IoT Hub に接続することを許可された個々のデバイスに関する情報を保存する IoT Hub の組み込みコンポーネントです。

### <a name="individual-enrollment"></a>個別加入

[Device Provisioning Service](#device-provisioning-service) では、[構成証明メカニズム](#attestation-mechanism)に X.509 リーフ証明書または対称キーを使用する単一のデバイスが個別登録によって識別されます。

### <a name="interactive-message"></a>対話型メッセージ

対話型メッセージは、ソリューション バックエンドの即時動作をトリガーする[クラウドからデバイスへ](#cloud-to-device)のメッセージです。 たとえば、デバイスは、CRM システムに自動的に記録される必要がある障害に関するアラームを送信できます。

### <a name="interface"></a>インターフェイス

IoT プラグ アンド プレイでは、インターフェイスで [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)または[デジタル ツイン](#digital-twin)によって実装される関連機能が記述されます。 インターフェイスは、さまざまな[デバイス モデル](#device-model)で再利用できます。 インターフェイスがデバイス モデルで使用されるときは、デバイスの[コンポーネント](#component)が定義されます。 単純なデバイスには、既定のインターフェイスのみが含まれています。

Azure Digital Twins では、[DTDL](#digital-twins-definition-language-dtdl) モデル定義の最上位のコード アイテムを指す際に "*インターフェイス*" が使用されることがあります。

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge は、Azure サービスのクラウド中心のデプロイと、オンプレミスのデバイスに対するソリューション固有のコードを可能にします。 [IoT Edge デバイス](#iot-edge-device)では、他のデバイスからデータを集計して、データがクラウドに送信される前にコンピューティングと分析を実行できます。 詳細については、[Azure IoT Edge](../iot-edge/index.yml) に関するページを参照してください。

### <a name="iot-edge-agent"></a>IoT Edge エージェント

IoT Edge ランタイムの、モジュールのデプロイと監視を担当する部分です。

### <a name="iot-edge-device"></a>IoT Edge デバイス

IoT Edge デバイスでは、Azure サービス、サードパーティのサービス、または独自のコードを実行するために、コンテナー化された IoT Edge [モジュール](#modules)が使用されます。 IoT Edge デバイスでは、[IoT Edge ランタイム](#iot-edge-runtime) によってモジュールが管理されます。 クラウドから、IoT Edge デバイスをリモートで監視および管理できます。

### <a name="iot-edge-hub"></a>IoT Edge ハブ

IoT Edge ランタイムの、モジュール間通信、(IoT Hub への) アップストリーム通信、(IoT Hub からの) ダウンストリーム通信を担当する部分です。

### <a name="iot-edge-runtime"></a>IoT Edge ランタイム

IoT Edge ランタイムには、Microsoft が配布する、IoT Edge デバイスにインストールされるすべてのものが含まれます。 これには、Edge エージェント、Edge ハブ、IoT Edge セキュリティ デーモンが含まれます。

### <a name="iot-extension-for-azure-cli"></a>Azure CLI 向け IoT 拡張機能

[Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)は、クロスプラットフォームのコマンドライン ツールです。 このツールを使用すると、[ID レジストリ](#identity-registry)でデバイスを管理し、デバイスからメッセージやファイルを送受信し、IoT Hub の操作を監視できます。

### <a name="iot-hub"></a>IoT Hub

IoT Hub は、何百万ものデバイスとソリューションのバックエンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、フル マネージドの Azure サービスです。 詳細については、「[Azure IoT Hub とは](../iot-hub/about-iot-hub.md)」を参照してください。 Azure サブスクリプションを使用して、IoT メッセージングのワークロードを処理する IoT Hub を作成できます。

### <a name="iot-hub-metrics"></a>IoT Hub メトリック

IoT Hub メトリックは、Azure サブスクリプション内の IoT Hub の状態に関するデータを提供します。 IoT Hub メトリックにより、ユーザーはサービスとそれに接続されたデバイスの全体的な正常性を評価することができます。 IoT Hub メトリックは、IoT Hub で起こっていることを確認するため、また、Azure サポートに連絡することなく問題の根本原因を調査するための有用な情報となります。 詳細については、[IoT Hub の監視](../iot-hub/monitor-iot-hub.md)に関する記事を参照してください。

### <a name="iot-hub-query-language"></a>IoT Hub クエリ言語

[IoT Hub クエリ言語](../iot-hub/iot-hub-devguide-query-language.md)は SQL に似た言語であり、[ジョブ](#job)、デジタル ツイン、デバイス ツインのクエリを実行できます。

### <a name="iot-hub-resource-rest-api"></a>IoT Hub リソース REST API

[IoT Hub リソース REST API](/rest/api/iothub/iothubresource) を使用して、ハブの作成、更新、削除などの操作を実行する Azure サブスクリプションの IoT Hub を管理できます。

### <a name="iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジ

IoT プラグ アンド プレイ ブリッジは、Windows または Linux ゲートウェイに接続されている既存のセンサーと周辺機器を [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)として接続できるようにするオープンソース アプリケーションです。

### <a name="iot-plug-and-play-conventions"></a>IoT プラグ アンド プレイ規則

IoT プラグ アンド プレイ [デバイス](#iot-plug-and-play-device)は、データをソリューションとやり取りするときに一連の規則に従うことが想定されています。

### <a name="iot-plug-and-play-device"></a>IoT プラグ アンド プレイ デバイス

IoT プラグ アンド プレイ デバイスは、データの収集や他のデバイスの制御を行い、[デバイス モデル](#device-model)を実装するソフトウェアまたはファームウェアを実行する、一般的には小規模なスタンドアロンのコンピューティング デバイスです。  環境監視デバイス (スマート農業灌漑システムのコントローラー) は、IoT プラグ アンド プレイ デバイスの一例です。 IoT プラグ アンド プレイ デバイスは、直接または IoT Edge モジュールとして実装できます。 IoT プラグ アンド プレイ デバイスに対する命令と制御を行い、そのデバイスからのデータを受信する、クラウドでホストされる IoT ソリューションを作成できます。

### <a name="iot-solution-accelerators"></a>IoT ソリューション アクセラレータ

Azure IoT ソリューション アクセラレータは、複数の Azure サービスをソリューションにパッケージ化します。 これらのソリューションを使用すると、一般的な IoT シナリオをエンド ツー エンドで実装して、すぐに使い始めることができます。 詳しくは、[Azure IoT ソリューション アクセラレータの概要](../iot-accelerators/about-iot-accelerators.md)に関するページをご覧ください。

## <a name="j"></a>J

### <a name="job"></a>ジョブ

ソリューションのバックエンドは、[ジョブ](../iot-hub/iot-hub-devguide-jobs.md)を使用して、IoT Hub に登録されたデバイスでのアクティビティのスケジュール設定と追跡を実行できます。 アクティビティには、デバイス ツインの[必要なプロパティ](#desired-properties)の更新、デバイス ツインの[タグ](#tags)の更新、および[ダイレクト メソッド](#direct-method)の呼び出しが含まれます。 [IoT Hub](#iot-hub) は、ジョブを使用して [ID レジストリ](#identity-registry)との間で[インポートおよびエクスポート](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities)も行います。

## <a name="l"></a>L

### <a name="leaf-device"></a>リーフ デバイス

[IoT Edge](#iot-edge) におけるリーフ デバイスとは、ダウンストリーム デバイスのないデバイスをいいます。

### <a name="lifecycle-event"></a>ライフサイクル イベント

Azure Digital Twins では、データ項目 (デジタル ツイン、リレーションシップ、イベント ハンドラーなど) が Azure Digital Twins インスタンスで作成されたり削除されたりすると、このタイプのイベントが発生します。

### <a name="linked-iot-hub"></a>リンクされた IoT ハブ

[Device Provisioning Service (DPS)](#device-provisioning-service) では、それにリンクされた IoT ハブにデバイスをプロビジョニングすることができます。 DPS インスタンスに IoT ハブをリンクすることで、このサービスがデバイス ID を登録し、デバイス ツインの初期構成を設定できるようになります。

## <a name="m"></a>M

### <a name="model"></a>モデル

モデルは、物理環境におけるエンティティのタイプ (そのプロパティやテレメトリ、コンポーネント、場合によってはその他の情報も含む) を定義します。 モデルを使用して、そのタイプの特定の物理オブジェクトを表す[デジタル ツイン](#digital-twin)が作成されます。 モデルは、[Digital Twins Definition Language](#digital-twins-definition-language-dtdl) で記述されます。

[Azure Digital Twins サービス](../digital-twins/index.yml)では、デバイスまたはそれ以上のレベルの抽象的なビジネス概念をモデルで定義することができます。 [IoT プラグ アンド プレイ](../iot-pnp/index.yml)では、[デバイス モデル](#device-model)を使用して、デバイスが具体的に記述されます。

### <a name="model-id"></a>モデル ID

IoT プラグ アンド プレイ デバイスが IoT Hub に接続されると、それによって実装される [DTDL](#digital-twins-definition-language-dtdl) モデルの **モデル ID** が送信されます。 この ID により、ソリューションではデバイス モデルを見つけることができます。

### <a name="model-repository"></a>モデル リポジトリ

モデル リポジトリには、[デバイス モデル](#device-model)と[インターフェイス](#interface)が格納されます。

### <a name="model-repository-rest-api"></a>モデル リポジトリの REST API

モデル リポジトリの管理と対話のための API です。 たとえば、API を使用して[デバイス モデル](#device-model)を追加および検索できます。

### <a name="module-builder"></a>モジュール ビルダー

モジュール ビルダーでは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)で実行するコードを実装するときに、[デバイス モデル](#device-model)と[インターフェイス](#interface)を使用します。 モジュール ビルダーにより、コードをモジュールまたは IoT Edge モジュールとして実装して、デバイス上の IoT Edge ランタイムにデプロイします。

### <a name="module-identity"></a>モジュール ID

モジュール ID は、デバイスに属しているすべてのモジュールに割り当てられる一意識別子です。 モジュール ID は、[ID レジストリ](#identity-registry)でも登録されます。

モジュール ID は、[IoT ハブ](#iot-hub) (IoT Edge モジュールの場合は [IoT Edge ハブ](#iot-edge-hub)) に対する認証を行うためにモジュールで使用されるセキュリティ資格情報を明示します。

### <a name="module-image"></a>モジュール イメージ

モジュール インスタンスをインスタンス化するために [IoT Edge ランタイム](#iot-edge-runtime)によって使用される Docker イメージ。

### <a name="module-twin"></a>モジュール ツイン

デバイス ツインと同様に、モジュール ツインは、モジュールの状態に関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 IoT Hub は、お使いの IoT ハブ内のデバイス ID でプロビジョニングされたモジュール ID ごとにモジュール ツインを永続化します。 モジュール ツインを使用すると、モジュールとソリューションのバックエンド間でモジュールの状態と構成を同期できます。 モジュール ツインにクエリを実行して、特定のモジュールを見つけ、長時間実行されている操作の状態にクエリを実行できます。

### <a name="modules"></a>モジュール

デバイス側では、IoT Hub デバイス SDK を使用して、それぞれが IoT Hub への独立した接続を確立する[モジュール](../iot-hub/iot-hub-devguide-module-twins.md)を作成できます。 この機能により、デバイスのさまざまなコンポーネントごとに個別の名前空間を使用することができます。

モジュール ID とモジュール ツインは、[デバイス ID](#device-identity) と[デバイス ツイン](#device-twin)と同様の機能を、より細かい粒度で提供します。 このより細かい粒度により、オペレーティング システム ベースのデバイスやファームウェア デバイスなどの複数のコンポーネントで構成され、この機能をサポートするデバイスの各コンポーネントの構成と状態を特定できます。

[IoT Edge](#iot-edge) におけるモジュールは、IoT Edge デバイスにデプロイできる Docker コンテナーです。 デバイスからのメッセージの取り込み、メッセージの変換、IoT Hub へのメッセージの送信など、特定のタスクを実行します。 他のモジュールと通信し、[IoT Edge ランタイム](#iot-edge-runtime)にデータを送信します。

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) は、デバイスと通信するために [IoT Hub](#iot-hub) がサポートするメッセージング プロトコルの 1 つです。 IoT Hub がサポートするメッセージング プロトコルの詳細については、「[IoT Hub でのメッセージの送受信](../iot-hub/iot-hub-devguide-messaging.md)」を参照してください。

## <a name="o"></a>O

### <a name="ontology"></a>オントロジ

不動産、スマート シティ、IoT システム、エネルギー グリッドなど、特定の分野の一連のモデル。 オントロジは、業界標準とベスト プラクティスに基づいた開始点を提供するため、[Azure Digital Twins](#azure-digital-twins) にあるようなナレッジ グラフのスキーマとしてよく使用されます。

オントロジの詳細については、「[オントロジとは](../digital-twins/concepts-ontologies.md)」を参照してください。

### <a name="operations-monitoring"></a>操作の監視

IoT Hub の[操作の監視](../iot-hub/iot-hub-operations-monitoring.md)では、IoT Hub に対する操作の状態をリアルタイムで監視することができます。 [IoT Hub](#iot-hub) は、複数のカテゴリにおよぶ操作のイベントを追跡します。 1 つ以上のカテゴリから IoT Hub のエンドポイントにイベントを送信して処理するように選択できます。 データを監視してエラーがないか確認したり、データ パターンに基づいてより複雑な処理をセットアップしたりできます。

## <a name="p"></a>P

### <a name="physical-device"></a>物理デバイス

物理デバイスは、Raspberry Pi など、IoT Hub に接続する実際のデバイスです。 便宜上、多くの IoT Hub チュートリアルでは、[シミュレートしたデバイス](#simulated-device)を使用して、ローカル コンピューター上でサンプルを実行できるようになっています。

### <a name="primary-and-secondary-keys"></a>主キーおよび 2 次キー

IoT Hub 上のデバイス向け、またはサービス向けエンドポイントに接続する場合、[接続文字列](#connection-string)には、アクセスを許可するためのキーが含まれます。 デバイスを [ID レジストリ](#identity-registry)に追加するか、[共有アクセス ポリシー](#shared-access-policy)をハブに追加すると、サービスによって主キーと 2 次キーが生成されます。 2 つのキーがあることにより、キーの更新時に、IoT Hub へのアクセスを失うことなく、1 つのキーから別のキーにロール オーバーできます。

### <a name="properties"></a>Properties

プロパティは、[デジタル ツイン](#digital-twin)のなんらかの永続的な状態を表す[インターフェイス](#interface)で定義されたデータ フィールドです。 プロパティは、読み取り専用または書き込み可能として宣言できます。 シリアル番号などの読み取り専用プロパティは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)自体で実行されるコードによって設定されます。 アラームのしきい値などの書き込み可能なプロパティは、通常、クラウドベースの IoT ソリューションから設定されます。

### <a name="property-change-event"></a>プロパティ変更イベント

[デジタル ツイン](#digital-twin)におけるプロパティの変化に起因するイベント。

### <a name="protocol-gateway"></a>プロトコル ゲートウェイ

プロトコル ゲートウェイは、通常はクラウドに展開され、[IoT Hub](#iot-hub) に接続するデバイスに対してプロトコル変換サービスを提供します。 詳細については、「[Azure IoT Hub とは](../iot-hub/about-iot-hub.md)」を参照してください。

## <a name="r"></a>R

### <a name="registration"></a>登録

登録は、IoT Hub の [ID レジストリ](#identity-registry)におけるデバイスの記録です。 デバイスは直接登録できるます。また、[Device Provisioning Service](#device-provisioning-service) を使用してデバイス登録を自動化することもできます。

### <a name="registration-id"></a>登録 ID

登録 ID は、[Device Provisioning Service](#device-provisioning-service) のデバイス[登録](#registration)を一意に識別するために使用されます。 登録 ID と[デバイス ID](#device-identity) は同じ値になる場合があります。

### <a name="relationship"></a>リレーションシップ

[Azure Digital Twins](../digital-twins/index.yml) サービスでは、物理環境全体をデジタルで表現するナレッジ グラフに[デジタル ツイン](#digital-twin)を接続するためにリレーションシップが使用されます。 ツインが保持できるリレーションシップの種類は、ツインの[モデル](#model)定義の一部として定義できます。たとえば、特定の種類のツインの [DTDL](#digital-twins-definition-language-dtdl) モデルには、他のツインとの間で保持できるリレーションシップについての情報が含まれます。

### <a name="reported-configuration"></a>報告される構成

[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)においては、報告される構成とは、ソリューションのバック エンドに報告する必要がある、デバイス ツイン内のプロパティとメタデータの完全なセットを指します。

### <a name="reported-properties"></a>報告されるプロパティ

[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)においては、報告されるプロパティとは、デバイスの構成や状態を同期するために[必要なプロパティ](#desired-properties)とともに使用される、デバイス ツインのサブセクションです。 報告されるプロパティは[デバイス アプリ](#device-app)のみで設定でき、[バックエンド アプリ](#back-end-app)で読み取りおよびクエリの実行を行うことができます。

### <a name="retry-policy"></a>再試行ポリシー

クラウド サービスに接続する場合、再試行ポリシーを使用して、[一時的なエラー](/azure/architecture/best-practices/transient-faults)を処理します。

### <a name="routing-rules"></a>ルーティング ルール

デバイスからクラウドへのメッセージを、ソリューションのバックエンドで処理するために[組み込みエンドポイント](#built-in-endpoints)または[カスタム エンドポイント](#custom-endpoints)にルーティングする[ルーティング ルール](../iot-hub/iot-hub-devguide-messages-read-custom.md)を IoT hub に構成します。

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN は、AMQP プロトコルがセキュリティ トークンを転送するために使用するプロトコルです。

### <a name="service-operations-endpoint"></a>サービス操作エンドポイント

サービス管理者によって使用される、サービスの設定を管理するための[エンドポイント](#endpoint)。 たとえば、[Device Provisioning Service](#device-provisioning-service) では、サービス エンドポイントを使用して登録を管理します。

### <a name="service-rest-api"></a>サービス REST API

ソリューションのバックエンドから [Service REST API](/rest/api/iothub/service/configuration) を使用して、デバイスを管理できます。 API を使用して、[デバイス ツイン](#device-twin)のプロパティの取得と更新、[ダイレクト メソッド](#direct-method)の呼び出し、および[ジョブ](#job)のスケジュールを実行できます。 IoT Hub チュートリアルで示されているように、通常、高レベルの[サービス SDK](#azure-iot-service-sdks) のいずれかを使用する必要があります。

### <a name="shared-access-policy"></a>共有アクセス ポリシー

共有アクセス ポリシーは、そのポリシーに関連付けられた、有効な[主キーまたは 2 次キー](#primary-and-secondary-keys)を持つすべてのユーザーに許可する権限を定義します。 ハブの共有アクセス ポリシーとキーは、ポータルで管理できます。

### <a name="shared-access-signature"></a>共有アクセス署名

Shared Access Signature (SAS) は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 SAS 認証には _共有アクセス ポリシー_ と _Shared Access Signature_ (多くの場合トークンと呼ばれます) という 2 つのコンポーネントがあります。 デバイスは、IoT Hub での認証に SAS を使用します。 [バック エンド アプリ](#back-end-app)も IoT Hub 上のサービス向けエンドポイントでの認証に SAS を使用します。 通常、SAS トークンは、IoT Hub への接続を確立するためにアプリが使用する[接続文字列](#connection-string)に含めます。

### <a name="simulated-device"></a>シミュレートされたデバイス

便宜上、IoT Hub チュートリアルの多くは、シミュレートされたデバイスを使用して、ローカル コンピューター上でサンプルを実行できるようにしています。 一方、[物理デバイス](#physical-device)は、Raspberry Pi など、IoT Hub に接続する実際のデバイスです。

### <a name="solution"></a>解決策

_ソリューション_ は、1 つまたは複数のプロジェクトを含む Visual Studio ソリューションを指す場合があります。 _ソリューション_ は、デバイス、[デバイス アプリ](#device-app)、IoT Hub、他の Azure サービス、[バックエンド アプリ](#back-end-app)などの要素を含む IoT ソリューションを指す場合もあります。

### <a name="solution-builder"></a>ソリューション ビルダー

ソリューション ビルダーは、ソリューション バックエンドを作成します。 一般的に、ソリューション ビルダーは、IoT Hub や[モデル リポジトリ](#model-repository)などの Azure リソースを操作します。

### <a name="system-properties"></a>システム プロパティ

[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)においては、システム プロパティは読み取り専用であり、最後のアクティビティの時刻や接続状態などのデバイスの使用状況に関する情報が含まれています。

## <a name="t"></a>T

### <a name="tags"></a>Tags

[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)においては、タグは、ソリューション バックエンドによって JSON ドキュメントの形式で保存および取得されるデバイスのメタデータです。 タグは、デバイス上のアプリが認識することはありません。

### <a name="target-condition"></a>ターゲット条件

IoT Edge のデプロイにおけるターゲット条件では、デプロイの対象となるデバイスを選択します (例: **tag.environment = prod**)。ターゲット条件は、要求を満たす新しいデバイスを含めたり、要求を満たさなくなったデバイスを削除したりするために、継続的に評価されます。

### <a name="telemetry"></a>テレメトリ

デバイスは風速や温度などのテレメトリ データを収集し、データ ポイント メッセージを使用して IoT ハブにテレメトリを送信します。

IoT プラグ アンド プレイおよび Azure Digital Twins において、[インターフェイス](#interface)で定義されているテレメトリ フィールドは、測定値を表します。 これらの測定値は、通常、デバイス ([IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)など) からデータ ストリームとして送信される、センサーの読み取り値などの値です。

[プロパティ](#properties)とは異なり、テレメトリは[デジタル ツイン](#digital-twin)に格納されません。これは、発生時に処理される必要がある期限付きデータ イベントのストリームです。

### <a name="telemetry-event"></a>テレメトリ イベント

テレメトリ データの到着を示すイベント。

### <a name="token-service"></a>トークン サービス

トークン サービスを使用して、デバイスに認証メカニズムを実装できます。 このサービスは、**DeviceConnect** アクセス許可が指定された IoT Hub [共有アクセス ポリシー](#shared-access-policy)を使用して、*デバイスを対象とする* トークンを作成します。 これらのトークンにより、デバイスは IoT Hub に接続できるようになります。 デバイスは、カスタム認証機構を使用して、トークン サービスで認証を受けます。 デバイスが正常に認証された場合、トークン サービスはデバイスに対して IoT Hub へのアクセスに使用する SAS トークンを発行します。

### <a name="twin-graph-or-digital-twin-graph"></a>ツイン グラフ (またはデジタル ツイン グラフ)

[Azure Digital Twins](../digital-twins/index.yml) サービスでは、[リレーションシップ](#relationship)を使用して[デジタル ツイン](#digital-twin)を接続することにより、物理環境全体をデジタルで表現するナレッジ グラフを作成できます。 1 つの [Azure Digital Twins インスタンス](#azure-digital-twins-instance)で、接続されていない多数のグラフ、または相互に接続された単一のグラフをホストできます。

### <a name="twin-queries"></a>ツイン クエリ

[デバイスおよびモジュールのツイン クエリ](../iot-hub/iot-hub-devguide-query-language.md)は、SQL に似た IoT Hub クエリ言語を使用して、デバイス ツインまたはモジュール ツインから情報を取得します。 同じ IoT Hub クエリ言語を使用して、IoT Hub で実行されている[ジョブ](#job)に関する情報を取得できます。

### <a name="twin-synchronization"></a>ツインの同期

ツインの同期では、デバイス ツインまたはモジュール ツインの[必要なプロパティ](#desired-properties)を使用してデバイスまたはモジュールを構成し、それらから[報告されたプロパティ](#reported-properties)を取得して、ツインに格納します。

## <a name="u"></a>U

### <a name="upstream-services"></a>アップストリーム サービス

現在のコンテキストにデータをフィードするサービスを表す相対的な用語。 たとえば、データは IoT Hub から Azure Digital Twins に流れるため、Azure Digital Twins のコンテキストで考えると、IoT Hub はアップストリーム サービスと見なされます。

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X.509 クライアント証明書

デバイスは X.509 証明書を使用して [IoT Hub](#iot-hub) で認証を受けることができます。 [SAS トークン](#shared-access-signature)の代わりに X.509 証明書を使用できます。
