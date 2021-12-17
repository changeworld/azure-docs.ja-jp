---
title: Azure IoT Central でのアーキテクチャの概念 | Microsoft Docs
description: この記事では、Azure IoT Central のアーキテクチャに関連する主要な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 58c9f69b898a41a1d07ae41755f67d926b2ac74f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082457"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central のアーキテクチャ

この記事では、IoT Central ソリューションのアーキテクチャにおける重要な要素の概要について説明します。

:::image type="content" source="media/concepts-architecture/architecture.png" alt-text="IoT Central ソリューションのアーキテクチャ概要" border="false":::

IoT Central アプリケーション:

- ユーザーはソリューション内の IoT デバイスを管理できます。
- ユーザーはデバイスのデータを表示および分析できます。
- ソリューションの一部となっている他のサービスに対するエクスポートと統合が可能です。

## <a name="iot-central"></a>IoT Central

IoT Central は、IoT ソリューション開発用にあらかじめ用意されている環境です。 これはサービスとしてのプラットフォーム (PaaS) IoT ソリューションであり、主なインターフェイスは Web UI です。 また、プログラムからアプリケーションを操作できるようにする [REST API](#rest-api) もあります。

このセクションでは、IoT Central アプリケーションの重要な機能について説明します。

### <a name="manage-devices"></a>デバイスの管理

IoT Central では、ソリューションにデータを送信する一連の [IoT デバイス](#devices)を管理できます。 たとえば、次のようなことができます。

- アプリケーションに[接続](concepts-get-connected.md)できるデバイスと、それらがどのように認証するかを制御します。
- アプリケーションに接続できるデバイスの種類は、[デバイス テンプレート](concepts-device-templates.md)を使用して定義します。
- 接続されるデバイスに対してプロパティを設定するかコマンドを呼び出すことでデバイスを管理します。 たとえば、サーモスタット デバイスの目標温度プロパティを設定したり、デバイスをトリガーするコマンドを呼び出して、ファームウェアを更新したりします。 以下に対してプロパティの設定やコマンドの呼び出しを行えます。
  - 個々のデバイス ([カスタマイズ可能](concepts-device-templates.md#views)な Web UI を通して)。
  - 複数のデバイス (スケジュールされた[ジョブ](howto-manage-devices-in-bulk.md)またはオンデマンド ジョブによって)。
- 顧客の住所や最後のサービスの日付[などのデバイス メタデータ](concepts-device-templates.md#cloud-properties)を管理します。

### <a name="view-and-analyze-data"></a>データを表示して分析する

IoT Central アプリケーションでは、個々のデバイスのデータや、複数のデバイスからの集計データを表示し、分析することができます。

- デバイス テンプレートを使用して、特定の種類の個々のデバイスについて[カスタム ビュー](howto-set-up-template.md#views)を定義します。 たとえば、個々のサーモスタットの温度を長時間にわたってプロットしたり、配送トラックの実際の場所を表示したりできます。
- 組み込みの[分析](tutorial-use-device-groups.md)を使用して、複数のデバイスの集計データを表示します。 たとえば、複数の小売店にわたる全体的混雑状況を表示したり、混雑率が最大の店舗や最小の店舗を識別したりすることができます。
- カスタム [ダッシュボード](howto-manage-dashboards.md)を作成して、デバイスの管理に役立てます。 たとえば、デバイス テレメトリを表示するためのマップ、タイル、グラフを追加できます。  

### <a name="secure-your-solution"></a>ソリューションをセキュリティで保護する

IoT Central アプリケーションでは、ソリューションの、以下のようなセキュリティ面を管理できます。

- [デバイス接続](concepts-get-connected.md): アプリケーションへの接続を確立するためにデバイスで使用するセキュリティ キーを作成、取り消し、更新します。
- [アプリの統合](howto-authorize-rest-api.md#get-an-api-token): アプリケーションとの安全な接続を確立するために、他のアプリケーションで使用するセキュリティ キーを作成、取り消し、更新します。
- [データのエクスポート](howto-export-data.md#connection-options): マネージド ID を使用して、データのエクスポート先への接続をセキュリティで保護します。
- [ユーザー管理](howto-manage-users-roles.md): アプリケーションにサインインできるユーザーと、それらのユーザーがどのようなアクセス許可を持つかを決定するロールを管理します。
- [組織](howto-create-organizations.md): IoT Central アプリケーションで、どのユーザーがどのデバイスを表示できるかを管理するための階層を定義します。

### <a name="rest-api"></a>REST API

お使いのアプリケーションを、他のアプリケーションやサービスから管理できるようにする統合を構築します。 たとえば、プログラムによって、お使いのアプリケーションで[デバイスを管理](howto-control-devices-with-rest-api.md)したり、[ユーザー情報](howto-manage-users-roles-with-rest-api.md)を外部システムと同期したりします。

## <a name="devices"></a>デバイス

デバイスはセンサーからデータを収集して、テレメトリのストリームとして IoT Central アプリケーションに送信します。 たとえば、冷却装置が温度値のストリームを送信したり、配送トラックがその場所をストリーミングしたりします。

デバイスでは、プロパティを使用して、その状態を報告できます。バルブが開いているか閉じているかなどです。 IoT Central アプリケーションでは、サーモスタットの目標温度を設定するなど、プロパティを使用してデバイスの状態を設定することもできます。

IoT Central では、デバイスに対してコマンドを呼び出してデバイスを制御することもできます。 たとえば、ファームウェア更新プログラムをダウンロードしてインストールするように、デバイスに指示します。

1 つのデバイスが実装する[テレメトリ、プロパティ、コマンド](concepts-telemetry-properties-commands.md)は、集合的にデバイス機能と呼ばれます。 これらの機能は、デバイスと IoT Central アプリケーションとの間で共有されるモデル内で定義します。 IoT Central において、このモデルは、特定の種類のデバイスを定義するデバイス テンプレートの一部となります。

[デバイスの実装](tutorial-connect-device.md)は、IoT Central と確実に通信できるようにする [IoT Plug and Play の規則](../../iot-develop/concepts-convention.md)に従っている必要があります。 詳細については、さまざまな言語の [SDK とサンプル](../../iot-develop/libraries-sdks.md)を参照してください。

デバイスは、サポートされているプロトコル [MQTT、AMQP、HTTP](../../iot-hub/iot-hub-devguide-protocols.md) のいずれかを使用して IoT Central に接続します。

## <a name="gateways"></a>ゲートウェイ

ローカル デバイス ゲートウェイは、以下のようないくつかのシナリオで役に立ちます。

- デバイスは、インターネットに接続できないために、IoT Central に直接接続できない場合があります。 たとえば、ゲートウェイ経由で接続する必要がある、Bluetooth 対応の混雑状況センサーのコレクションを使用している場合があります。
- お使いのデバイスによって生成されるデータの量が多い場合があります。 コストを削減するため、IoT Central アプリケーションに送信する前にローカル ゲートウェイ内でデータを結合したり集計したりすることができます。
- ソリューションで、データ内の異常に対してすばやい応答が必要な場合があります。 IoT Central アプリケーションにデータを送信する必要なしに、ローカルで異常を識別してアクションを実行するルールをゲートウェイで実行できます。

詳細については、「[Azure IoT Edge デバイスを Azure IoT Central アプリケーションに接続する](concepts-iot-edge.md)」を参照してください。

## <a name="data-export"></a>データのエクスポート

IoT Central には組み込みの分析機能が用意されていますが、他のサービスやアプリケーションにデータをエクスポートできます。 データをエクスポートする理由として、以下が挙げられます。

### <a name="storage-and-analysis"></a>ストレージと分析

アーカイブと保有のポリシーに関して長期的な保存と制御を行う場合は、他の保存先ストレージに[データを継続的にエクスポート](howto-export-data.md)できます。 別個のストレージを使用すると、他の分析ツールを使用して分析情報を引き出して、お使いのソリューション内でデータを表示することもできます。

### <a name="business-automation"></a>ビジネスの自動化

IoT Central の[ルール](howto-configure-rules-advanced.md)を使用すると、IoT Central 内で条件に応答して、電子メールの送信やイベントの発生などの外部アクションをトリガーできます。 たとえば、デバイスの周辺温度がしきい値に達した場合にエンジニアに通知できます。

### <a name="additional-computation"></a>追加の計算

データを IoT Central や別のサービスで使用する前に、データに対して[変換や計算](howto-transform-data.md)を行う必要が生じる場合があります。 たとえば、配送トラックによって報告された場所データに、地域の気象情報を追加することができます。

## <a name="next-steps"></a>次のステップ

ここでは、Azure IoT Central のアーキテクチャについて学習しました。推奨される次の手順は、Azure IoT Central での[スケーラビリティと高可用性](concepts-scalability-availability.md)の学習です。
