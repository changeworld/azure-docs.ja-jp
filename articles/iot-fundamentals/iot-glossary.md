---
title: Azure IoT 用語集 | Microsoft Docs
description: 開発者ガイド - Azure IoT の記事で使用される一般的な用語の一部について説明した用語集です。
author: dominicbetts
ms.author: dobett
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 631b1d9e81b50b42da5ca01b17be2a8b89c830bc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704578"
---
# <a name="glossary-of-iot-terms"></a>IoT 用語集

この記事では、IoT の記事で使用される一般的な用語の一部を示します。

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol

[IoT Hub](#iot-hub) および IoT Central が[デバイス](#device)と通信するためにサポートしているメッセージング プロトコルの 1 つです。

[詳細情報](../iot-hub/iot-hub-devguide-protocols.md)

大文字と小文字の規則: 常に *Advanced Message Queueing Protocol*。

初回およびそれ以降の言及: コンテキストに応じて完全にスペルアウトします。 あるいは、省略形である AMQP を使用します。

省略形: AMQP

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="allocation-policy"></a>割り当てポリシー

[Device Provisioning Service ](#device-provisioning-service)では、[リンクされた IoT ハブ](#linked-iot-hub)への[デバイス](#device)の割り当て方法が、割り当てポリシーによって決まります。

大文字と小文字の規則: 常に小文字にします。

適用先: Device Provisioning Service

### <a name="attestation-mechanism"></a>構成証明メカニズム

[Device Provisioning Service ](#device-provisioning-service)では、[デバイス](#device)の ID を確認する手段として構成証明メカニズムが使用されます。 構成証明メカニズムは、[登録](#enrollment)時に構成されます。

構成証明メカニズムには、X.509 証明書、トラステッド プラットフォーム [モジュール](#module)、対称キーが含まれます。

大文字と小文字の規則: 常に小文字にします。

適用先: Device Provisioning Service

### <a name="automatic-deployment"></a>自動展開

一連の IoT Edge [モジュール](#module)を実行する [IoT Edge デバイス](#iot-edge-device)の対象のセットを構成する [IoT Edge](#iot-edge) の機能。 各デプロイでは、新しいデバイスが作成されたかターゲット条件と一致するよう変更されたときでも、[対象の条件](#target-condition)と一致する[すべてのデバイス](#device)が特定のモジュールのセットを実行することが継続的に保証されます。 各 IoT Edge デバイスは、そのターゲット条件を満たす最優先のデプロイのみを受け取ります。

[詳細情報](../iot-edge/module-deployment-monitoring.md)

大文字と小文字の規則: 常に小文字にします。

適用対象: IoT Edge

### <a name="automatic-device-configuration"></a>自動デバイス構成

[ソリューション](#solution) バックエンドが、[デバイス ツイン](#device-twin)のセットに[必要なプロパティ](#desired-properties)を割り当て、システム メトリックとカスタム メトリックを使って[デバイス](#device)の状態を報告することができるようにする [IoT Hub](#iot-hub) の機能

[詳細情報](../iot-hub/iot-hub-automatic-device-management.md)

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="automatic-device-management"></a>自動デバイス管理

大規模な数の[デバイス](#device)をそのライフサイクル全体にわたって管理するための、複雑で反復的なタスクを大幅に自動化する [IoT Hub](#iot-hub) の機能。 この機能を使用して、対象となるデバイスを[プロパティ](#properties)に基づいて設定し、[必要な構成](#desired-configuration)を定義しておくと、デバイスがスコープに適合したときに、IoT Hub によってデバイスが更新されます。

[自動デバイス構成](../iot-hub/iot-hub-automatic-device-management.md)と [IoT Edge の自動展開](../iot-edge/how-to-deploy-at-scale.md)で構成されます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="azure-certified-device-program"></a>Azure Certified Device プログラム

Azure Certified [Device](#device) は、Azure 上で実行するように構築された IoT デバイスの差別化、認定、および販売促進を可能にする無料のプログラムです。

[詳細情報](../certification/overview.md)

大文字と小文字の規則: *Azure Certified Device* として常に各単語の先頭文字を大文字にします。

適用対象: Iot Hub、IoT Central

### <a name="azure-digital-twins"></a>Azure Digital Twins

実世界の物、場所、ビジネス プロセス、人についてデジタル表現を作成するための PaaS (サービスとしてのプラットフォーム) オファリング。 環境全体を表すツイン グラフを作成し、それらを使用して、製品の質を高め、運用とコストを最適化し、カスタマー エクスペリエンスを通じてブレークスルーをもたらす分析情報を入手します。

[詳細情報](../digital-twins/overview.md)

大文字と小文字の規則: サービスに言及している場合は、常に各単語の先頭文字を大文字にします。

初回およびそれ以降の言及: サービスに言及している場合は、常に *Azure Digital Twins* として完全にスペルアウトします。

使用例: *Azure Digital Twins* モデルのデータは、追加の分析またはストレージのために下流の Azure サービスにルーティングできます。

適用対象: デジタル ツイン

### <a name="azure-digital-twins-instance"></a>Azure Digital Twins インスタンス

お客様のサブスクリプションにおける、[Azure Digital Twins](#azure-digital-twins) サービスの単一のインスタンス。 Azure [Digital Twins](#digital-twin) が Azure サービス全体を指すのに対して、Azure Digital Twins *インスタンス* は、個々の Azure Digital Twins リソースを指します。

大文字と小文字の規則: サービス名は常に各単語の先頭文字を大文字にします。

初回およびそれ以降の言及: 常に "Azure Digital Twins インスタンス" として完全にスペルアウトします。

適用対象: デジタル ツイン

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

[デバイス](#device)が送信している[テレメトリ](#telemetry)の表示、デバイスの[プロパティ](#properties)の操作、 [コマンド](#command) の呼び出しを行うために使用できるツール。 また、エクスプローラーを使用して、デバイスとの対話およびテストを行ったり、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)を管理したりすることもできます。

[詳細情報](https://github.com/Azure/azure-iot-explorer)

大文字と小文字の規則: 常に *Azure IoT Explorer* として各単語の先頭文字を大文字にします。

適用対象: Iot Hub、デバイス開発者

### <a name="azure-iot-tools"></a>Azure IoT Tools

VS Code での Azure [IoT Hub](#iot-hub) と[デバイス](#device)の管理に役立つクロス プラットフォームでオープンソースの Visual Studio Code 拡張機能。 Azure IoT Tools を使用すると、IoT 開発者は VS Code で IoT プロジェクトを容易に開発できます。

大文字と小文字の規則: 常に *Azure IoT Tools* として各単語の先頭文字を大文字にします。

適用対象: Iot Hub、IoT Edge、IoT Central、デバイス開発者

### <a name="azure-iot-device-sdks"></a>Azure IoT device SDK

複数の言語で使用可能なこれらの SDKS を使用すると、[IoT Hub](#iot-hub) または IoT Central アプリケーションと対話する[デバイス アプリ](#device-app)を作成できます。

[詳細情報](../iot-develop/about-iot-sdks.md)

大文字と小文字の規則: 常に *Azure IoT device SDK* と呼びます。

初回およびそれ以降の言及: 初回の言及では、常に *Azure IoT device SDK* を使用します。 それ以降の言及では、*device SDK* に省略します。

使用例: *Azure IoT device SDK* には、デバイスのクライアント ライブラリ、開発者ガイド、サンプル、ドキュメントが集約されています。 プログラムから *device SDK* を使用すると、Azure IoT サービスにデバイスを接続するのに役立ちます。

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="azure-iot-service-sdks"></a>Azure IoT サービス SDK

複数の言語で使用可能なこれらの SDK を使用すると、[IoT Hub](#iot-hub) と対話する[バックエンド アプリ](#back-end-app)を作成できます。

[詳細情報](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

大文字と小文字の規則: 常に *Azure IoT service SDK* と呼びます。

初回およびそれ以降の言及: 初回の言及では、常に *Azure IoT service SDK* を使用します。 それ以降の言及では、*service SDK* に省略します。

適用対象: Iot Hub

## <a name="b"></a>B

### <a name="back-end-app"></a>バックエンド アプリ

[IoT Hub](#iot-hub) において、IoT Hub 上のサービス向け[エンドポイント](#endpoint)のいずれかに接続するアプリ。 たとえば、バックエンド アプリは[デバイスからクラウド](#device-to-cloud)へのメッセージの取得や、[ID レジストリ](#identity-registry)の管理を行います。 通常、バック エンド アプリはクラウドで実行されますが、チュートリアルの多くでは、シンプルにするためにバックエンド アプリはローカル開発用コンピューターで実行されるコンソール アプリです。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="built-in-endpoints"></a>組み込みのエンドポイント

[IoT Hub](#iot-hub) に組み込まれた[エンドポイント](#endpoint)。 たとえば、すべての IoT Hub には、Event Hub と互換性がある組み込みのエンドポイントが含まれています。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

## <a name="c"></a>C

### <a name="cloud-gateway"></a>クラウド ゲートウェイ

クラウド ゲートウェイは、[IoT Hub](#iot-hub) または IoT Central に直接接続できない[デバイス](#device)の接続を有効にするクラウドでホストされたアプリ。 デバイスでローカルに実行される[フィールド ゲートウェイ](#field-gateway)とは異なり、クラウド [ゲートウェイ](#gateway)はクラウドでホストされます。 クラウド ゲートウェイの一般的なユース ケースは、デバイスにプロトコル変換を実装することです。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central

### <a name="cloud-property"></a>クラウド プロパティ

IoT Central アプリケーションに[デバイス](#device)のメタデータを格納できるようにする IoT Central の機能。 クラウド [プロパティ](#properties)は[デバイス テンプレート](#device-template)で定義されますが、[デバイス モデル](#device-model)の一部ではありません。 クラウド プロパティはデバイスと同期されません。

大文字と小文字の規則: 常に小文字にします。

適用対象: IoT Central

### <a name="cloud-to-device"></a>クラウドからデバイスへ

[IoT Hub](#iot-hub) から接続された[デバイス](#device)に送信されるメッセージ。 多くの場合、これらのメッセージは、デバイスにアクションを実行することを指示する[コマンド](#command)です。

大文字と小文字の規則: 常に小文字にします。

省略形: *C2D* は使用しないでください。

適用対象: Iot Hub

### <a name="command"></a>command

コマンドは、IoT プラグ アンド プレイ [インターフェイス](#interface)で定義され、[デジタル ツイン](#digital-twin)で呼び出すことができるメソッドを表します。 たとえば、[デバイス](#device)を再起動するコマンドがあります。 IoT Central では、コマンドは[デバイス テンプレート](#device-template)で定義されます。

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="component"></a>コンポーネント

IoT プラグ アンド プレイおよび [Azure Digital Twins](#azure-digital-twins) では、コンポーネントを使用すると、他のインターフェイスのアセンブリとして[モデル](#model) [インターフェイス](#interface)を作成できます。 [デバイス モデル](#device-model)では、複数のインターフェイスをコンポーネントとして組み合わせることができます。 たとえば、モデルにスイッチ コンポーネントとサーモスタット コンポーネントを含めることができます。 モデル内の複数のコンポーネントで、同じインターフェイスの種類を使用することもできます。 たとえば、モデルに 2 つのサーモスタット コンポーネントを含めることができます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、デジタル ツイン、デバイス開発者

### <a name="configuration"></a>構成

[自動デバイス構成](#automatic-device-configuration)のコンテキストでは、[IoT Hub](#iot-hub) 内で、[デバイス](#device) ツインのセットに[必要な構成](#desired-configuration)を定義し、状態と進行状況を報告するためのメトリックのセットを提供します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="connection-string"></a>接続文字列

アプリのコード内で使用して、[エンドポイント](#endpoint)への接続に必要な情報をカプセル化します。 通常、接続文字列には、エンドポイントのアドレスとセキュリティ情報が含まれますが、接続文字列の形式はサービス間で異なります。 [IoT Hub](#iot-hub) サービスに関連付けられた接続文字列には次の 2 種類があります。

- [デバイス](#device)接続文字列 を使用すると、IoT Hub 上のデバイス向けエンドポイントにデバイスを接続できます。
- *IoT Hub 接続文字列* を使用すると、IoT Hub 上のサービス向けエンドポイントに[バックエンド アプリ](#back-end-app)を接続できます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、デバイス開発者

### <a name="custom-endpoints"></a>カスタム エンドポイント

[ルーティング ルール](#routing-rule)によって送信されたメッセージを配信する [IoT Hub](#iot-hub) 上のユーザー定義の[エンドポイント](#endpoint)。 これらのエンドポイントは、イベント ハブ、Service Bus キュー、または Service Bus トピックに直接接続します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="custom-gateway"></a>カスタム ゲートウェイ

[IoT Hub](#iot-hub) または  IoT Central に直接接続できない[デバイス](#device)の接続を有効にします。 Azure [IoT Edge](#iot-edge) を使用して、メッセージを処理するカスタム ロジック、カスタム プロトコルの変換、およびその他の処理を実装するカスタム [ゲートウェイ](#gateway)を構築できます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central

## <a name="d"></a>D

### <a name="default-component"></a>既定のコンポーネント

すべての [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device) [モデル](#model)に既定の[コンポーネント](#component)があります。 単純な[デバイス モデル](#device-model)には、既定のコンポーネントのみがあります。このようなモデルは、コンポーネント [デバイス](#device)なしとも呼ばれます。 より複雑なモデルでは、既定のコンポーネントの下に複数のコンポーネントが入れ子になっています。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="deployment-manifest"></a>配置マニフェスト

1 つ以上の [IoT Edge デバイス](#iot-edge-device) [モジュール ツイン](#module-twin)の[構成](#configuration)データを含む、[IoT Edge](#iot-edge) の JSON ドキュメント。

大文字と小文字の規則: 常に小文字にします。

適用対象: IoT Edge、IoT Central

### <a name="desired-configuration"></a>必要な構成

[デバイス ツイン](#device-twin)においては、必要な[構成](#configuration)とは、デバイスと同期する必要がある、[デバイス](#device) ツイン内の[プロパティ](#properties)とメタデータの完全なセットを指します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="desired-properties"></a>必要なプロパティ

[デバイス ツイン](#device-twin)においては、必要な[プロパティ](#properties)は、デバイスの[構成](#configuration)や状態を同期するために[報告されるプロパティ](#reported-properties)とともに使用される、[デバイス](#device) ツインのサブセクションです。 必要なプロパティは[バックエンド アプリ](#back-end-app)のみで設定でき、[デバイス アプリ](#device-app)によって監視されます。 IoT Central では、書き込み可能なプロパティという用語を使用します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="device"></a>Device

IoT においては、デバイスは、通常は、データの収集や他のデバイスの制御を実行できる、小規模なスタンドアロン コンピューティング デバイスです。 環境監視デバイス (温室の散水システムや換気システムのコントローラー) はデバイスの一例です。 デバイス カタログには、認定デバイスの一覧が表示されます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、IoT Edge、Device Provisioning Service、デバイス開発者

### <a name="device-provisioning-service"></a>デバイス プロビジョニング サービス

ゼロタッチ [デバイス プロビジョニング](#device-provisioning)に使用する [IoT Hub](#iot-hub) と IoT Central のヘルパー サービス。 DPS を使用すると、セキュリティで保護されたスケーラブルな方法で何百万台もの[デバイス](#device)をプロビジョニングできます。

大文字と小文字の規則: 常に *Device Provisioning Service* として各単語の先頭文字を大文字にします。

初回およびそれ以降の言及: IoT Hub Device Provisioning Service

省略形: DPS

適用対象: Iot Hub、Device Provisioning Service、IoT Central

### <a name="device-rest-api"></a>デバイス REST API

[デバイス](#device)で、[デバイスからクラウド](#device-to-cloud)へのメッセージを [IoT Hub](#iot-hub) に送信し、[クラウドからデバイス](#cloud-to-device)へのメッセージを IoT Hub から受信するために使用できる REST API。 通常は、上位レベルの [Azure IoT デバイス SDK](#azure-iot-device-sdks) のいずれかを使用する必要があります。

[詳細情報](/rest/api/iothub/device)

大文字と小文字の規則: 常に *Device REST API* にします。

適用対象: Iot Hub

### <a name="device-app"></a>デバイス アプリ

デバイス アプリは、[デバイス](#device)で実行され、[IoT Hub](#iot-hub) または IoT Central アプリケーションとの通信を処理します。 デバイス アプリを実装する場合、通常 [Azure IoT device SDK](#azure-iot-device-sdks) のいずれかを使用します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="device-builder"></a>デバイス ビルダー

[デバイス](#device)で実行するコードを作成する担当者。 デバイス ビルダーでは通常、デバイス クライアントを実装するために [Azure IoT device SDK](#azure-iot-device-sdks) のいずれかを使用します。 デバイス ビルダーは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)で実行するコードを実装するときに、[デバイス モデル](#device-model)と[インターフェイス](#interface)を使用します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、IoT Edge、デバイス開発者

### <a name="device-identity"></a>デバイス ID

[IoT Hub](#iot-hub) の [ID レジストリ](#identity-registry)または IoT Central アプリケーションに登録されているすべての[デバイス](#device)に割り当てられた一意の識別子。

大文字と小文字の規則: 常に小文字にします。 省略形を使用する場合、*ID* はすべて大文字です。

省略形: デバイス ID

適用対象: Iot Hub、IoT Central

### <a name="device-management"></a>デバイス管理

[デバイス](#device)管理には、計画、プロビジョニング、構成、監視、インベントリからの削除などを含む IoT [ソリューション](#solution)内のデバイスの管理に関連するライフ サイクル全体が含まれます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central

### <a name="device-model"></a>デバイスのモデル

[デバイス](#device)の機能の[デジタル ツイン定義言語](#digital-twins-definition-language)を使用する説明。 機能には、[テレメトリ](#telemetry)、[プロパティ](#properties)、[コマンド](#command)が含まれます。

[詳細情報](../iot-develop/concepts-modeling-guide.md)

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者、デジタル ツイン

### <a name="device-provisioning"></a>デバイス プロビジョニング

最初の[デバイス](#device) データを[ソリューション](#solution)内のストアに追加するプロセスです。 新しいデバイスをハブに接続できるようにするには、デバイスの ID とキーを [IoT Hub](#iot-hub) [ID レジストリ](#identity-registry)に追加する必要があります。 [Device Provisioning Service ](#device-provisioning-service)は、IoT hub または IoT Central アプリケーションにデバイスを自動的にプロビジョニングできます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、Device Provisioning Service、IoT Central

### <a name="device-template"></a>デバイス テンプレート

IoT Central では、[デバイス](#device) テンプレートとは、アプリケーションに接続するタイプのデバイスの特性や動作を定義したブループリントです。

たとえば、デバイス テンプレートによって、IoT Central が、適切なユニットとデータの種類を使用して視覚化を作成できるように、デバイスが送信する[テレメトリ](#telemetry)を定義できます。 [デバイス モデル](#device-model)は、デバイス テンプレートの一部です。

大文字と小文字の規則: 常に小文字にします。

省略形: "テンプレート" と省略しないでください。 IoT Central にはアプリケーション テンプレートもあるためです。

適用対象: IoT Central

### <a name="device-twin"></a>デバイス ツイン

[デバイス](#device) ツインは、デバイスの状態に関する情報 (メタデータ、[構成](#configuration)、状態など) を格納する JSON ドキュメントです。 [IoT Hub](#iot-hub) は、IoT Hub でプロビジョニングする各デバイスにデバイス ツインを保持します。 デバイス ツインを使用すると、デバイスと[ソリューション](#solution)のバック エンド間でデバイスの状態と構成を同期できます。 デバイス ツインにクエリを実行して、特定のデバイスや、長時間実行されている操作の状態を見つけることができます。

[デジタル ツイン](#digital-twin)も参照してください。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="device-to-cloud"></a>デバイスからクラウドへ

接続された[デバイス](#device)から [IoT Hub](#iot-hub) または IoT Central に送信されるメッセージを指します。

大文字と小文字の規則: 常に小文字にします。

省略形: *D2C* は使用しないでください。

適用対象: Iot Hub

### <a name="digital-twins-definition-language"></a>Digital Twins Definition Language

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)と [Azure Digital Twins](#azure-digital-twins) エンティティの[モデル](#model)と[インターフェイス](#interface)を記述するための JSON-LD 言語。 この言語を使用すると、IoT プラットフォームと IoT [ソリューション](#solution)でエンティティのセマンティクスを使用できます。

[詳細情報](https://github.com/Azure/opendigitaltwins-dtdl)

初回およびそれ以降の言及: 常に *Digital Twins Definition Language* として完全にスペルアウトします。

省略形: DTDL

適用対象: Iot Hub、IoT Central、デジタル ツイン

### <a name="digital-twin"></a>デジタル ツイン

デジタル ツインは、物理オブジェクトを表すデジタル データのコレクションです。 物理オブジェクトでの変更は、デジタル ツインに反映されます。 状況によっては、デジタル ツインを使用して物理オブジェクトを操作できます。 315[Azure Digital Twins サービス](../digital-twins/index.yml)では、[Digital Twins Definition Language](#digital-twins-definition-language) で記述された[モデル](#model)を使用して、[物理デバイス](#physical-device)のデジタル ツインまたはそれ以上のレベルの抽象的なビジネス概念を表現することで、クラウドベースの多様なデジタル ツイン [ソリューション](#solution)を実現します。 [IoT プラグ アンド プレイ](../iot-develop/index.yml) [デバイス](#device)には、Digital Twins Definition Language [デバイス モデル](#device-model)によって記述されたデジタル ツインがあります。

[デバイス ツイン](#device-twin)も参照してください。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デジタル ツイン、デバイス開発者

### <a name="digital-twin-change-events"></a>デジタル ツインの変更イベント

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)が [Iot Hub](#iot-hub) に接続されると、このハブはそのルーティング機能を使用して、[デジタル ツイン](#digital-twin)の変更に関する通知を送信できます。 IoT Central データ エクスポート機能は、デジタル ツインの変更イベントを他のサービスに転送することもできます。 たとえば、[デバイス](#device)上でプロパティ値が変更されるたびに、IoT Hub では通知をイベント ハブなどの[エンドポイント](#endpoint)に送信できます。

大文字と小文字の規則: 常に小文字にします。

省略形: 他の種類の変更イベントと区別するために、常に完全にスペルアウトします。

適用対象: Iot Hub、IoT Central

### <a name="digital-twin-graph"></a>デジタル ツイン グラフ

[Azure Digital Twins](#azure-digital-twins) サービスでは、[リレーションシップ](#relationship)を使用して[デジタル ツイン](#digital-twin)を接続することにより、物理環境全体をデジタルで表現するナレッジ グラフを作成できます。 1 つの [Azure Digital Twins インスタンス](#azure-digital-twins-instance)で、接続されていない多数のグラフ、または相互に接続された単一のグラフをホストできます。

大文字と小文字の規則: 常に小文字にします。

初回およびそれ以降の言及: 初回の言及では "デジタル ツイン グラフ" を使用し、それ以降は "ツイン グラフ" を使用します。

適用対象: Iot Hub

### <a name="direct-method"></a>ダイレクト メソッド

[IoT Hub](#iot-hub) 上で API を呼び出すことによって、[デバイス](#device)上で実行するメソッドをトリガーするための方法。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="downstream-service"></a>ダウン ストリーム サービス

現在のコンテキストからデータを取得するサービスを表す相対的な用語。 たとえば、Azure [Digital Twins](#digital-twin) から Time Series Insights へのデータ フローを設定する場合、[Azure Digital Twins](#azure-digital-twins) のコンテキストでは、Time Series Insights はダウンストリーム サービスです。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デジタル ツイン

## <a name="e"></a>E

### <a name="endpoint"></a>エンドポイント

他のサービスからデータを取得できるデータ ルーティング サービスの名前付きの表現。

[IoT Hub](#iot-hub) は、アプリの IoT Hub への接続を有効にする複数のエンドポイントを公開します。 [デバイスからクラウド](#device-to-cloud)へのメッセージの送信といった操作をデバイスで実行できるようにする、[デバイス](#device)向けのエンドポイントがあります。 [デバイス ID](#device-identity) の管理といった操作を[バックエンド アプリ](#back-end-app)で実行できるようにする、サービス向けの管理エンドポイントがあります。 デバイスからクラウドへのメッセージを読み取るためのサービス向けの[組み込みエンドポイント](#built-in-endpoints)があります。 [ルーティング ルール](#routing-rule)によって送信されたデバイスからクラウドへのメッセージを受信するカスタム [エンドポイント](#custom-endpoints)を作成できます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="enrollment"></a>登録

[Device Provisioning Service ](#device-provisioning-service)における登録とは、自動プロビジョニングを通じて[リンクされた IoT Hub](#linked-iot-hub) に登録できる個々の[デバイス](#device)や一連のデバイスを記録することです。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、Device Provisioning Service、IoT Central

### <a name="enrollment-group"></a>加入グループ

[Device Provisioning Service ](#device-provisioning-service)および IoT Central では、X.509 または対称キーの[構成証明メカニズム](#attestation-mechanism)を共有する一連の[デバイス](#device)が、[登録](#enrollment)グループによって識別されます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、Device Provisioning Service、IoT Central

### <a name="event-hubs-compatible-endpoint"></a>イベント ハブ互換エンドポイント

イベント ハブ互換メソッドを使用して、ハブに送信された[デバイス](#device) メッセージを読み取ることができる [IoT Hub](#iot-hub) [エンドポイント](#endpoint)。 イベント ハブと互換性があるメソッドには、[Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) と [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) が含まれます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="event-handler"></a>イベント ハンドラー

イベントの到着によってトリガーされるプロセス。 たとえば、Azure 関数にイベント処理コードを追加し、[エンドポイント](#endpoint)と[イベント ルーティング](#event-routing)を使用してそれにデータを送信することで、イベント ハンドラーを作成できます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="event-routing"></a>イベント ルーティング

ある[デバイス](#device)またはサービスから他のデバイスまたはサービスの[エンドポイント](#endpoint)にイベントとそのデータを送信するプロセス。

[IoT Hub](#iot-hub) で、メッセージの送信方法を表す[ルーティング規則](#routing-rule)を定義できます。 [Azure Digital Twins](#azure-digital-twins) で、この目的のために作成されるエンティティがイベント ルートです。 Azure [Digital Twins](#digital-twin) のイベント ルートには、各エンドポイントに送信されるイベントの種類を制限するフィルターを含めることができます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、デジタル ツイン

## <a name="f"></a>F

### <a name="field-gateway"></a>フィールド ゲートウェイ

[IoT Hub](#iot-hub) に直接接続できない[デバイス](#device)を接続できるようにします。通常はデバイスにローカルにデプロイされます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

ゲートウェイは、[IoT Hub](#iot-hub) に直接接続できない[デバイス](#device)の接続を有効にします。 [フィールド ゲートウェイ](#field-gateway)、[クラウド ゲートウェイ](#cloud-gateway)および[カスタム ゲートウェイ](#custom-gateway)もご確認ください。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central

### <a name="gateway-device"></a>ゲートウェイ デバイス

[フィールド ゲートウェイ](#field-gateway)の一例です。 [ゲートウェイ](#gateway) [デバイス](#device)には、標準の IoT デバイスまたは [IoT Edge デバイス](#iot-edge-device)があります。

[IoT Hub](#iot-hub) に直接接続できないダウンストリーム デバイスの接続が、ゲートウェイ デバイスによって可能になります。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、IoT Edge

## <a name="h"></a>H

### <a name="hardware-security-module"></a>ハードウェア セキュリティ モジュール

[デバイス](#device) シークレットを安全なハードウェアベースのストレージに保管するために使用します。 これは、デバイスのシークレット ストレージとして最も安全な形態です。 ハードウェア セキュリティ [モジュール](#module)では、X.509 証明書と対称キーの両方を格納できます。 [Device Provisioning Service ](#device-provisioning-service)では、[構成証明メカニズム](#attestation-mechanism)で ハードウェア セキュリティ モジュールを使用できます。

大文字と小文字の規則: 常に小文字にします。

初回およびそれ以降の言及: 初回の言及では "ハードウェア セキュリティ モジュール" として完全にスペルアウトします。

省略形: HSM

適用対象: Iot Hub、デバイス開発者、Device Provisioning Service、

## <a name="i"></a>I

### <a name="id-scope"></a>ID スコープ

[Device Provisioning Service](#device-provisioning-service) インスタンスに、その作成時に割り当てられる一意の値です。

IoT Central アプリケーションでは DPS インスタンスを活用し、IoT Central の UI を通じて ID スコープを公開します。

大文字と小文字の規則: 常に "ID スコープ" を使用します。

適用対象: Iot Hub、IoT Central、Device Provisioning Service、IoT Central

### <a name="identity-registry"></a>ID レジストリ

ハブに接続することを許可された個々の[デバイス](#device)に関する情報を保存する [IoT Hub](#iot-hub) の組み込み[コンポーネント](#component)。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="individual-enrollment"></a>個別加入

[Device Provisioning Service](#device-provisioning-service) が [IoT Hub](#iot-hub) にプロビジョニングできる 1 つの[デバイス](#device)を識別します。

大文字と小文字の規則: 常に小文字にします。

適用対象: IoT Hub、Device Provisioning Service

### <a name="interface"></a>インターフェイス

IoT プラグ アンド プレイでは、インターフェイスで [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)または[デジタル ツイン](#digital-twin)によって実装される関連機能が記述されます。 インターフェイスは、さまざまな[デバイス モデル](#device-model)で再利用できます。 インターフェイスが[デバイス](#device) [モデル](#model)で使用されるときは、デバイスの[コンポーネント](#component)が定義されます。 単純なデバイスには、既定のインターフェイスのみが含まれています。

[Azure Digital Twins](#azure-digital-twins) では、[Digital Twins Definition Language](#digital-twins-definition-language) モデル定義の最上位のコード アイテムを指す際に *インターフェイス* が使用されることがあります。

大文字と小文字の規則: 常に小文字にします。

適用対象: デバイス開発者、デジタル ツイン

### <a name="iot-edge"></a>IoT Edge

Azure サービスのクラウド中心のデプロイと、オンプレミスの[デバイス](#device)に対する[ソリューション](#solution)固有のコードを可能にするサービスおよび関連クライアント ライブラリとランタイム。 [IoT Edge デバイス](#iot-edge-device)では、他のデバイスからデータを集計して、データがクラウドに送信される前にコンピューティングと分析を実行できます。

[詳細情報](../iot-edge/index.yml)

大文字と小文字の規則: 常に *IoT Edge* として各単語の先頭文字を大文字にします。

初回およびそれ以降の言及: *Azure IoT Edge* としてスペルアウトします。

適用対象: IoT Edge

### <a name="iot-edge-agent"></a>IoT Edge エージェント

[IoT Edge ランタイム](#iot-edge-runtime)の、[モジュール](#module)のデプロイと監視を担当する部分です。

大文字と小文字の規則: 常に "IoT Edge エージェント" として各単語の先頭文字を大文字にします。

適用対象: IoT Edge

### <a name="iot-edge-device"></a>IoT Edge デバイス

Azure サービス、サードパーティのサービス、または独自のコードを実行するために、コンテナー化された [IoT Edge](#iot-edge) [モジュール](#module)を使用する[デバイス](#device)。 デバイスでは、[IoT Edge ランタイム](#iot-edge-runtime) によってモジュールが管理されます。 クラウドから、IoT Edge デバイスをリモートで監視および管理できます。

大文字と小文字の規則: 常に *IoT Edge デバイス* として各単語の先頭文字を大文字にします。

適用対象: IoT Edge

### <a name="iot-edge-hub"></a>IoT Edge ハブ

[モジュール](#module)間通信、アップストリーム通信、ダウンストリーム通信を担当する [IoT Edge ランタイム](#iot-edge-runtime)の部分。

大文字と小文字の規則: 常に *IoT Edge ハブ* として各単語の先頭文字を大文字にします。

適用対象: IoT Edge

### <a name="iot-edge-runtime"></a>IoT Edge ランタイム

Microsoft が配布する、[IoT Edge デバイス](#iot-edge-device)にインストールされるすべてのものが含まれます。 これには、Edge エージェント、Edge ハブ、[IoT Edge](#iot-edge) セキュリティ デーモンが含まれます。

大文字と小文字の規則: 常に *IoT Edge ランタイム* として各単語の先頭文字を大文字にします。

適用対象: IoT Edge

### <a name="iot-hub"></a>IoT Hub

何百万もの[デバイス](#device)と[ソリューション](#solution)のバックエンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、フル マネージドの Azure サービス。 詳細については、「[Azure IoT Hub とは](../iot-hub/about-iot-hub.md)」を参照してください。 Azure サブスクリプションを使用して、IoT メッセージングのワークロードを処理する IoT Hub を作成できます。

[詳細情報](../iot-hub/about-iot-hub.md)

大文字と小文字の規則: サービスに言及している場合は、*IoT Hub* として各単語の先頭文字を大文字にします。 インスタンスに言及している場合は、"IoT ハブ" とします。

初回およびそれ以降の言及: *Azure IoT Hub* として完全にスペルアウトします。 2 回目以降の言及では *IoT Hub* にすることができます。 コンテキストが明確な場合は、インスタンスに言及するために "ハブ" を使用します。

使用例: Azure IoT Hub サービスにより、セキュリティで保護された双方向通信が可能になります。 デバイスは IoT ハブにデータを送信します。

適用対象: Iot Hub

### <a name="iot-hub-resource-rest-api"></a>IoT Hub リソース REST API

ハブの作成、更新、削除などの操作を実行する Azure サブスクリプションの [IoT Hub](#iot-hub) を管理するために使用できる API。

大文字と小文字の規則: 常に *IoT Hub Resource REST API* として各単語の先頭文字を大文字にします。

適用対象: Iot Hub

### <a name="iot-hub-metrics"></a>IoT Hub メトリック

[IoT Hub](#iot-hub) の状態を監視できる Azure portal の機能 IoT Hub メトリックにより、IoT hub とそれに接続された[デバイス](#device)の全体的な正常性を評価することができます。

大文字と小文字の規則: 常に "IoT Hub メトリック" として各単語の先頭文字を大文字にします。

適用対象: Iot Hub

### <a name="iot-hub-query-language"></a>IoT Hub クエリ言語

[ジョブ](#job)、[デジタル ツイン](#digital-twin)、[デバイス ツイン](#device-twin)のクエリを実行できる、[IoT Hub](#iot-hub) 向けの SQL に似た言語です。

大文字と小文字の規則: 常に "IoT Hub クエリ言語" として各単語の先頭文字を大文字にします。

初回およびそれ以降のメンション: "IoT Hub クエリ言語" として完全にスペルアウトします。コンテキストが明確な場合は、2 回目以降の言及では "クエリ言語" にすることができます。

適用対象: Iot Hub

### <a name="iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジ

Windows または Linux [ゲートウェイ](#gateway)に接続されている既存のセンサーと周辺機器を [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)として接続できるようにするオープンソース アプリケーション。

大文字と小文字の規則: 常に "IoT プラグ アンド プレイ ブリッジ" として単語の先頭文字を大文字にします。

初回およびそれ以降の言及: "IoT プラグ アンド プレイ ブリッジ" として完全にスペルアウトします。 コンテキストが明確である場合、2 回目以降の言及では"ブリッジ" にすることができます。

適用対象: Iot Hub、デバイス開発者、IoT Central

### <a name="iot-plug-and-play-conventions"></a>IoT プラグ アンド プレイ規則

IoT [デバイス](#device)が[ソリューション](#solution)とデータを交換するときに従う必要がある一連の規則。

大文字と小文字の規則: 常に "IoT プラグ アンド プレイ規則" として単語の先頭文字を大文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="iot-plug-and-play-device"></a>IoT プラグ アンド プレイ デバイス

データの収集や他のデバイスの制御を行い、[デバイス モデル](#device-model)を実装するソフトウェアまたはファームウェアを実行する、一般的には小規模なスタンドアロンのコンピューティング [デバイス](#device)。 環境監視デバイス (スマート農業灌漑システムのコントローラー) は、IoT プラグ アンド プレイ デバイスの一例です。 IoT プラグ アンド プレイ デバイスは、直接または [IoT Edge](#iot-edge) [モジュール](#module)として実装できます。

大文字と小文字の規則: 常に "IoT プラグ アンド プレイ デバイス" として単語の先頭文字を大文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="iot-extension-for-azure-cli"></a>Azure CLI 向け IoT 拡張機能

Azure CLI 用拡張機能 この拡張機能を使用すると、[ID レジストリ](#identity-registry)内の[デバイス](#device)の管理、デバイス メッセージの送受信、[IoT Hub](#iot-hub) 操作の監視などのタスクを実行できます。

[詳細情報](/cli/azure/azure-cli-reference-for-IoT)

大文字と小文字の規則: 常に "Azure CLI 向け IoT 拡張機能" として各単語の先頭文字を大文字にします。

適用対象: Iot Hub、IoT Central、IoT Edge、Device Provisioning Service、デバイス開発者

## <a name="j"></a>J

### <a name="job"></a>ジョブ

[IoT Hub](#iot-hub) のコンテキストでは、[ジョブ](#device)を使用して、IoT Hub に登録されている一連のデバイスでアクティビティのスケジュール設定と追跡を行うことができます。 アクティビティには、[デバイス ツイン](#device-twin)の[必要なプロパティ](#desired-properties)の更新、デバイス ツインの[タグ](#tag)の更新、および[ダイレクト メソッド](#direct-method)の呼び出しが含まれます。 IoT Hub は、ジョブを使用して ID レジストリとの間で[インポートおよびエクスポート](#identity-registry)も行います。

IoT Central のコンテキストでジョブ を使用すると、[プロパティ](#properties)を設定して[コマンド](#command)を呼び出すことによって、接続されているデバイスを一括して管理できます。 また、IoT Central ジョブを使用すると、クラウド プロパティを一括で更新することもできます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central

## <a name="l"></a>L

### <a name="leaf-device"></a>リーフ デバイス

ダウンストリーム デバイスが接続されていない[デバイス](#device)。 通常、リーフ デバイスは[ゲートウェイ デバイス](#gateway-device)に接続されます。

大文字と小文字の規則: 常に小文字にします。

適用対象: IoT Edge、IoT Central、デバイス開発者

### <a name="lifecycle-event"></a>ライフサイクル イベント

[Azure Digital Twins](#azure-digital-twins) では、データ項目 ([デジタル ツイン](#digital-twin)、[リレーションシップ](#relationship)、[イベント ハンドラー](#event-handler)など) が [Azure Digital Twins](#azure-digital-twins-instance) インスタンスで作成されたり削除されたりすると、このタイプのイベントが発生します。

大文字と小文字の規則: 常に小文字にします。

適用対象: デジタル ツイン、Iot Hub、IoT Central

### <a name="linked-iot-hub"></a>リンクされた IoT ハブ

[Device Provisioning Service](#device-provisioning-service) インスタンスにリンクされている [IoT Hub](#iot-hub)。 DPS インスタンスは、リンクされた IoT Hub で、[デバイス](#device) ID を登録し、[デバイス ツイン](#device-twin)の初期[構成](#configuration)を設定できるようになります。

大文字と小文字の規則: 常に "リンクされた IoT ハブ" として単語の先頭文字を大文字にします。

適用対象: IoT Hub、Device Provisioning Service

## <a name="m"></a>M

### <a name="mqtt"></a>MQTT

[IoT Hub](#iot-hub) および IoT Central が[デバイス](#device)と通信するためにサポートしているメッセージング プロトコルの 1 つです。 MQTT は何の意味もありません。

[詳細情報](../iot-hub/iot-hub-devguide-protocols.md)

初回およびそれ以降の言及: MQTT

省略形: MQTT

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="model"></a>モデル

物理環境におけるエンティティのタイプ (その[プロパテ](#properties)ィやテレメトリ、[コンポーネント](#component)を含む) を定義します。 モデルを使用して、そのタイプの特定の物理オブジェクトを表す[デジタル ツイン](#digital-twin)が作成されます。 モデルは、[Digital Twins Definition Language](#digital-twins-definition-language) で記述されます。

[Azure Digital Twins](#azure-digital-twins) サービスでは、[デバイス](#device)またはそれ以上のレベルの抽象的なビジネス概念をモデルで定義することができます。 IoT プラグ アンド プレイでは、[デバイス モデル](#device-model)で、デバイスを具体的に記述します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デジタル ツイン、デバイス開発者

### <a name="model-id"></a>モデル ID

[IoT Plug and Play デバイス](#iot-plug-and-play-device)は、[IoT Hub](#iot-hub) またはIoT Central アプリケーションに接続すると、実装する [Digital Twins Definition Language](#digital-twins-definition-language) モデルの[モデル](#model) ID を送信します。 すべてのモデルを一意のモデル ID として使用します。 このモデル ID により、[ソリューション](#solution)では[デバイス モデル](#device-model)を見つけることができます。

大文字と小文字の規則: 常に "モデル ID" として大文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者、デジタル ツイン

### <a name="model-repository"></a>モデル リポジトリ

[Digital Twins Definition Language](#digital-twins-definition-language) [モデル](#model)と[インターフェイス](#interface)を保存します。 [ソリューション](#solution)では、[モデル ID](#model-id) を使用してリポジトリからモデルを取得します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デジタル ツイン

### <a name="model-repository-rest-api"></a>モデル リポジトリの REST API

[モデル リポジトリ](#model-repository)の管理と対話のための API です。 たとえば、API を使用して[デバイス モデル](#device-model)を追加および検索できます。

大文字と小文字の規則: 常に "モデル リポジトリの REST API" として大文字にします。

適用対象: Iot Hub、IoT Central、デジタル ツイン

### <a name="module"></a>モジュール

[IoT Hub](#iot-hub) [デバイス](#device) SDK を使用して、それぞれが IoT Hub への独立した接続を確立するモジュールのインスタンスを作成できます。 これにより、デバイスのさまざまな[コンポーネント](#component)ごとに個別の名前空間を使用することができます。

[モジュール ID](#module-identity) と[モジュール ツイン](#module-twin)は、[デバイス ID](#device-identity) と[デバイス ツイン](#device-twin)と同様の機能を、より細かい粒度で提供します。

[IoT Edge](#iot-edge) におけるモジュールは、[IoT Edge デバイス](#iot-edge-device)にデプロイできる Docker コンテナーです。 デバイスからのメッセージの取り込み、メッセージの変換、IoT Hub へのメッセージの送信など、特定のタスクを実行します。 他のモジュールと通信し、[IoT Edge ランタイム](#iot-edge-runtime)にデータを送信します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Edge、デバイス開発者

### <a name="module-identity"></a>モジュール ID

[デバイス](#device)に属しているすべての[モジュール](#module)に割り当てられる一意識別子です。 モジュール ID は、[ID レジストリ](#identity-registry)でも登録されます。

モジュール ID は、[IoT Hub](#iot-hub) ([IoT Edge](#iot-edge) モジュールの場合は [IoT Edge ハブ](#iot-edge-hub)) に対する認証を行うためにモジュールで使用されるセキュリティ資格情報を明示します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Edge、デバイス開発者

### <a name="module-image"></a>モジュール イメージ

[モジュール](#module) インスタンスをインスタンス化するために [IoT Edge ランタイム](#iot-edge-runtime)によって使用される Docker イメージ。

大文字と小文字の規則: 常に小文字にします。

適用対象: IoT Edge

### <a name="module-twin"></a>モジュール ツイン

[デバイス ツイン](#device-twin)と同様に、[モジュール](#module) ツインは、モジュールの状態に関する情報 (メタデータ、[構成](#configuration)、状態など) を格納する JSON ドキュメントです。 [IoT Hub](#iot-hub) は、お使いの IoT ハブ内の[デバイス ID](#device-identity) でプロビジョニングされた[モジュール ID](#module-identity) ごとにモジュール ツインを永続化します。 モジュール ツインを使用すると、モジュールと[ソリューション](#solution)のバックエンド間でモジュールの状態と構成を同期できます。 モジュール ツインにクエリを実行して、特定のモジュールを見つけ、長時間実行されている操作の状態にクエリを実行できます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

## <a name="o"></a>O

### <a name="ontology"></a>オントロジ

[デジタル ツイン](#digital-twin)のコンテキストでの不動産、スマート シティ、IoT システム、エネルギー グリッドなど、特定の分野の一連の[モデル](#model)。 オントロジは、業界標準とベスト プラクティスに基づいた開始点を提供するため、[Azure Digital Twins](#azure-digital-twins) にあるようなナレッジ グラフのスキーマとしてよく使用されます。

[詳細情報](../digital-twins/concepts-ontologies.md)

適用対象: デジタル ツイン

### <a name="operations-monitoring"></a>操作の監視

IoT Hub の操作の監視では、IoT Hub に対する操作の状態をリアルタイムで監視することができる [IoT Hub](#iot-hub) の機能。 IoT Hub は、複数のカテゴリにまたがる操作のイベントを追跡します。 1 つ以上のカテゴリから IoT Hub の[エンドポイント](#endpoint)にイベントを送信して処理するように選択できます。 データを監視してエラーがないか確認したり、データ パターンに基づいてより複雑な処理をセットアップしたりできます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

## <a name="p"></a>P

### <a name="physical-device"></a>物理デバイス

[IoT hub](#iot-hub) に接続する IoT [デバイス](#device)。 便宜上、多くのチュートリアルとクイックスタートでは、物理デバイスではなく、デスクトップ マシンで IoT デバイスコードを実行します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者、IoT Edge

### <a name="primary-and-secondary-keys"></a>主キーおよび 2 次キー

[IoT Hub](#iot-hub) または IoT Central アプリケーション上の[デバイス](#device)向け、またはサービス向け[エンドポイント](#endpoint)に接続する場合、[接続文字列](#connection-string)には、アクセスを許可するためのキーが含まれます。 デバイスを [ID レジストリ](#identity-registry)に追加するか、[共有アクセス ポリシー](#shared-access-policy)をハブに追加すると、サービスによって主キーと 2 次キーが生成されます。 2 つのキーがあることにより、キーの更新時に、IoT Hub または  IoT Central アプリケーションへのアクセスを失うことなく、1 つのキーから別のキーにロール オーバーできます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central

### <a name="properties"></a>プロパティ

[デジタル ツイン](#digital-twin)のコンテキストで、デジタル ツインのなんらかの永続的な状態を表す[インターフェイス](#interface)で定義されたデータ フィールド。 プロパティは、読み取り専用または書き込み可能として宣言できます。 シリアル番号などの読み取り専用プロパティは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)自体で実行されるコードによって設定されます。 アラームのしきい値などの書き込み可能なプロパティは、通常、クラウドベースの IoT [ソリューション](#solution)から設定されます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デジタル ツイン、デバイス開発者

### <a name="property-change-event"></a>プロパティ変更イベント

[デジタル ツイン](#digital-twin)におけるプロパティの変化に起因するイベント。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デジタル ツイン

### <a name="protocol-gateway"></a>プロトコル ゲートウェイ

[ゲートウェイ](#gateway)は、通常はクラウドに展開され、[IoT Hub](#iot-hub) または IoT Central アプリケーションに接続する[デバイス](#device)に対してプロトコル変換サービスを提供します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central

## <a name="r"></a>R

### <a name="registration"></a>登録

[IoT Hub](#iot-hub) の [ID レジストリ](#identity-registry)における[デバイス](#device)の記録。 デバイスは直接登録できるます。また、[Device Provisioning Service](#device-provisioning-service) を使用してデバイス登録を自動化することもできます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、Device Provisioning Service、IoT Central

### <a name="registration-id"></a>登録 ID

[Device Provisioning Service](#device-provisioning-service)の一意の[デバイス ID](#device-identity)。 [登録](#registration) ID と[デバイス](#device) ID は同じ値になる場合があります。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、Device Provisioning Service、IoT Central

### <a name="relationship"></a>リレーションシップ

[Azure Digital Twins](#azure-digital-twins) サービスでは、物理環境全体をデジタルで表現するナレッジ グラフに[デジタル ツイン](#digital-twin)を接続するために使用されます。 ツインが持つことができるリレーションシップの種類は、[Digital Twins Definition Language](#digital-twins-definition-language) [モデル](#model)で定義されています。

大文字と小文字の規則: 常に小文字にします。

適用対象: デジタル ツイン

### <a name="reported-configuration"></a>報告される構成

[デバイス ツイン](#device-twin)のコンテキストで、[ソリューション](#solution)のバック エンドに報告する必要がある、[デバイス](#device) ツイン内の[プロパティ](#properties)とメタデータの完全なセットを指します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、デバイス開発者

### <a name="reported-properties"></a>報告されるプロパティ

[デバイス ツイン](#device-twin)のコンテキストでは、報告される[プロパティ](#properties)は、[デバイス](#device) ツインのサブセクションです。 報告されるプロパティはデバイスのみで設定できますが、[バックエンド アプリ](#back-end-app)で読み取りおよびクエリの実行を行うことができます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、デバイス開発者

### <a name="retry-policy"></a>再試行ポリシー

クラウド サービスに接続するときに一時的なエラーを処理する方法。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者

### <a name="routing-rule"></a>ルーティング ルール

[デバイスからクラウド](#device-to-cloud)へのメッセージを、[ソリューション](#solution)のバックエンドで処理するために組み込み[エンドポイント](#endpoint)または[カスタム エンドポイント](#custom-endpoints)にルーティングするために使用される [IoT hub](#iot-hub) の機能。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

## <a name="s"></a>S

### <a name="saslplain"></a>SASL/PLAIN

[Advanced Message Queueing Protocol](#advanced-message-queueing-protocol) プロトコルがセキュリティ トークンを転送するために使用するプロトコル。

[詳細情報](https://wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

省略形: SASL/PLAIN

適用対象: Iot Hub

### <a name="service-rest-api"></a>サービス REST API

[ソリューション](#solution)のバックエンドから[デバイス](#device)を管理するために使用できる REST API。 たとえば、[Iot Hub](#iot-hub) サービス API を使用して、[デバイス ツイン](#device-twin)の[プロパティ](#properties)の取得と更新、[ダイレクト メソッド](#direct-method)の呼び出し、および[ジョブ](#job)のスケジュールを実行できます。 通常は、上位レベルのサービス SDKを使用する必要があります。

大文字と小文字の規則: 常に "サービス REST API" にします。

適用対象: Iot Hub、IoT Central、Device Provisioning Service、IoT Edge

### <a name="service-operations-endpoint"></a>サービス操作エンドポイント

サービス設定を管理するために管理者が使用する[エンドポイント](#endpoint)。 たとえば、[Device Provisioning Service](#device-provisioning-service) では、サービス エンドポイントを使用して[登録](#enrollment)を管理します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、Device Provisioning Service、IoT Edge、デジタル ツイン

### <a name="shared-access-policy"></a>共有アクセス ポリシー

そのポリシーに関連付けられた、有効な主キーまたは 2 次キーを持つすべてのユーザーに許可する権限を定義する方法。 ハブの共有アクセス ポリシーとキーは、ポータルで管理できます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Edgel、Device Provisioning Service

### <a name="shared-access-signature"></a>共有アクセス署名

共有アクセス署名は、[IoT Hub](#iot-hub) [エンドポイント](#endpoint)などの 1 つまたは複数のストレージ リソースを指す署名付き URIです。 URI には、クライアントがリソースにアクセスする方法を示すトークンが含まれています。 クエリ パラメーターの 1 つである署名は、SAS パラメーターで作成されており、SAS の作成に使用されたキーで署名されています。 この署名は、ストレージ リソースへのアクセスを承認するために、Azure Storage によって使用されます。

大文字と小文字の規則: 常に小文字にします。

省略形: SAS

適用対象: Iot Hub、デジタル ツイン、IoT Central、IoT Edge

### <a name="simulated-device"></a>シミュレートされたデバイス

利便性を高めるために、多くのチュートリアルとクイックスタートでは、シミュレートされたセンサーを使用してローカルの開発用マシンで[デバイス](#device) コードを実行しています。 これに対し、MXCHIP などの[物理デバイス](#physical-device)には実際のセンサーがあり、[IoT hub](#iot-hub) に接続します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デバイス開発者、IoT Edge、デジタル ツイン、Device Provisioning Service

### <a name="solution"></a>解決策

IoT のコンテキストで、*ソリューション* は、一般的に[デバイス](#device)、[デバイス アプリ](#device-app)、[IoT Hub](#iot-hub)、他の Azure サービス、[バックエンド アプリ](#back-end-app)などの要素を含む IoT ソリューションを指します。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、Device Provisioning Service、IoT Edge、デジタル ツイン

### <a name="system-properties"></a>システム プロパティ

[デバイス ツイン](#device-twin)のコンテキストでの、最後のアクティビティの時刻や接続状態などの[デバイス](#device)の使用状況に関する情報が含まれている読み取り専用の[プロパティ](#properties)。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

## <a name="t"></a>T

### <a name="tag"></a>タグ

[デバイス ツイン](#device-twin)のコンテキストでは、タグは、[ソリューション](#solution) バックエンドによって JSON ドキュメントの形式で保存および取得される[デバイス](#device)のメタデータです。 タグは、デバイス上のアプリが認識することはありません。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="target-condition"></a>ターゲット条件

新しい [IoT Edge](#iot-edge) のデプロイでは、ターゲット条件によってデプロイのターゲット [デバイス](#device)が選択されます。 ターゲット条件は、要求を満たす新しいデバイスを含めたり、要求を満たさなくなったデバイスを削除したりするために、継続的に評価されます。

大文字と小文字の規則: 常に小文字にします。

適用対象: IoT Edge

### <a name="telemetry"></a>テレメトリ

[デバイス](#device)のセンサーから収集され、[IoT](#iot-hub) ハブに送信される風速や温度などのデータ。

[プロパティ](#properties)とは異なり、テレメトリは[デジタル ツイン](#digital-twin)に格納されません。これは、発生時に処理される必要がある期限付きデータ イベントのストリームです。

IoT プラグ アンド プレイおよび [Azure Digital Twins](#azure-digital-twins) において、[インターフェイス](#interface)で定義されているテレメトリ フィールドは、測定値を表します。 これらの測定値は、通常、デバイス ([IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)など) からデータ ストリームとして送信される、センサーの読み取り値などの値です。

大文字と小文字の規則: 常に小文字にします。

使用例: "テレメトリ" という言葉を使わないでください。テレメトリは、デバイスが送信するデータのコレクションを指しています。 たとえば、デバイスが IoT ハブに接続すると、テレメトリの送信が開始されます。 デバイスから送信されるテレメトリ値の 1 つは環境温度です。


適用対象: Iot Hub、IoT Central、デジタル ツイン、IoT Edge、デバイス開発者

### <a name="telemetry-event"></a>テレメトリ イベント

[テレメトリ](#telemetry) データの到着を示す [IoT hub](#iot-hub) 内のイベント。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="twin-queries"></a>ツイン クエリ

SQL に似た IoT Hub クエリ言語を使用して、[デバイス ツイン](#device-twin)または[モジュール ツイン](#module-twin)から情報を取得できるようにする [IoT Hub](#iot-hub) の機能。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

### <a name="twin-synchronization"></a>ツインの同期

[デバイス ツイン](#device-twin)または[モジュール ツイン](#module-twin)の[必要なプロパティ](#desired-properties)を使用して[デバイス](#device)または[モジュール](#module)を構成し、それらから[報告されたプロパティ](#reported-properties)を取得して、ツインに格納する [IoT Hub](#iot-hub) 内のプロセス。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub

## <a name="u"></a>U

### <a name="upstream-service"></a>アップストリーム サービス

現在のコンテキストにデータをフィードするサービスを表す相対的な用語。 たとえば、データは IoT Hub から Azure [Digital Twins](#digital-twin) に流れるため、[Azure Digital Twins](#azure-digital-twins) のコンテキストでは、[IoT Hub](#iot-hub) はアップストリーム サービスと見なされます。

大文字と小文字の規則: 常に小文字にします。

適用対象: Iot Hub、IoT Central、デジタル ツイン

