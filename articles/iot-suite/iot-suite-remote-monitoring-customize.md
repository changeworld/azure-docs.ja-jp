---
title: "リモート監視ソリューションのカスタマイズ - Azure | Microsoft Docs"
description: "この記事では、リモート監視の構成済みソリューションのソース コードにアクセスする方法について説明します。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f5d38091b59110859d4376a5cd16a19f24dad65b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>リモート監視の構成済みソリューションのカスタマイズ

この記事では、構成済みソリューションのソース コードにアクセスし、リモート監視をカスタマイズする方法について説明します。 この記事では、次の内容について説明します。

* ソース コードと構成済みソリューションを構成するマイクロサービスのリソースが保存されている GitHub リポジトリ。
* 新しいデバイスの種類を追加するなどの一般的なカスタマイズのシナリオ。

次のビデオでは、リモート監視構成済みソリューションをカスタマイズするためのオプションの概要が説明されています。

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

## <a name="project-overview"></a>プロジェクトの概要

### <a name="implementations"></a>実装

リモート監視ソリューションには、.NET と Java の実装の両方が含まれています。 どちらの実装も同様の機能が利用でき、基盤となる同じ Azure サービスに依存します。 最上位の GitHub リポジトリは、こちらをご覧ください。

* [.NET ソリューション](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java ソリューション](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>マイクロサービス

ソリューションの特定の機能に関心をお持ちの場合は、各マイクロサービスの GitHub リポジトリに個別にアクセスできます。 各マイクロサービスには、ソリューションの機能の異なる部分が実装されています。 アーキテクチャ全般の詳細については、「[構成済みリモート監視ソリューションのアーキテクチャ](iot-suite-remote-monitoring-sample-walkthrough.md)」をご覧ください。

次の表は、言語ごとに各マイクロサービスの現在の状況をまとめたものです。

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| マイクロサービス      | [説明] | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Web UI            | リモート監視ソリューション向け Web アプリ。 React.js フレームワークを使用して UI を実装します。 | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| IoT Hub マネージャー   | IoT Hub との通信を処理します。        | [使用可能](https://github.com/Azure/iothub-manager-java) | [使用可能](https://github.com/Azure/iothub-manager-dotnet)   |
| 認証    |  Azure Active Directory の統合を管理します。  | まだ使用できません | [使用可能](https://github.com/Azure/pcs-auth-dotnet)   |
| デバイスのシミュレーション | シミュレートされたデバイスのプールを管理します。 | まだ使用できません | [使用可能](https://github.com/Azure/device-simulation-dotnet)   |
| テレメトリ         | デバイスのテレメトリを UI で使用できるようにします。 | [使用可能](https://github.com/Azure/device-telemetry-java) | [使用可能](https://github.com/Azure/device-telemetry-dotnet)   |
| テレメトリ エージェント   | テレメトリ ストリームを分析し、Azure IoT Hub のメッセージを保存して、定義した規則に従ってアラートを生成します。  | [使用可能](https://github.com/Azure/telemetry-agent-java) | [使用可能](https://github.com/Azure/telemetry-agent-dotnet)   |
| UI コンフィグ         | UI からの構成データを管理します。 | [使用可能](https://github.com/azure/pcs-ui-config-java) | [使用可能](https://github.com/azure/pcs-ui-config-dotnet)   |
| ストレージ アダプター   |  ストレージ サービスとのやりとりを管理します。   | [使用可能](https://github.com/azure/pcs-storage-adapter-java) | [使用可能](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| リバース プロキシ     | 管理されている方法で一意のエンドポイントを介してプライベート リソースを公開します。 | まだ使用できません | [使用可能](https://github.com/Azure/reverse-proxy-dotnet)   |

現在 Java ソリューションでは .NET 認証、シミュレーション、およびリバース プロキシ マイクロサービスが使用されています。 これらのマイクロサービスは Java 版のサービスが利用可能になり次第、置き換えられます。

## <a name="presentation-and-visualization"></a>プレゼンテーションと視覚化

以下のセクションでは、リモート監視ソリューションのプレゼンテーションおよび視覚化レイヤーをカスタマイズするオプションについて説明します。

### <a name="change-the-logo-in-the-ui"></a>UI のロゴを変更する

既定のデプロイでは、Contoso 社の会社名とロゴを UI で使用します。 自分の会社名とロゴが表示されるように UI 要素を変更するには、次のようにします。

1. 次のコマンドを使用して、Web UI リポジトリを複製します。

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. 会社名を変更するには、`src/common/lang.js` ファイルをテキスト エディターで開きます。

1. ファイル内で次の行を探します。

    ```js
    CONTOSO: 'Contoso',
    ```

1. `Contoso` を自分の会社名に置き換えます。 例: 

    ```js
    CONTOSO: 'YourCo',
    ```

1. ファイルを保存します。

1. ロゴを更新するには、`assets/icons` フォルダーに新しい SVG ファイルを追加します。 既存のロゴは `assets/icons/Contoso.svg` ファイルです。

1. テキスト エディターで `src/components/layout/leftNav/leftNav.js` ファイルを開きます。

1. ファイル内で次の行を探します。

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. `Contoso.svg` を自分のロゴ ファイルの名前に置き換えます。 例: 

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. ファイル内で次の行を探します。

    ```js
    alt="ContosoIcon"
    ```

1. `ContosoIcon` を自分の `alt` テキストに置き換えます。 例: 

    ```js
    alt="YourCoIcon"
    ```

1. ファイルを保存します。

1. 変更をテストするには、更新した `webui` をローカル コンピューター上で実行します。 `webui` ソリューションをローカルで構築して実行する方法については、`webui` GitHub リポジトリの Readme ファイルにある「[Build, run and test locally (ローカルでの構築、実行、テスト)](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally)」をご覧ください。

1. 変更をデプロイするには、「[Developer Reference Guide (開発者向けリファレンス ガイド)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)」をご覧ください。

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>マップをカスタマイズする

ソリューションのマップ コンポーネントの詳細については、GitHub の[マップのカスタマイズ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)に関するページをご覧ください。

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>その他のカスタマイズのオプション

リモート監視ソリューションのプレゼンテーションおよび視覚化レイヤーをさらに変更するには、コードを編集します。 関連する GitHub リポジトリは次のとおりです。

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure PCS リモート監視 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>デバイスの接続とストリーミング

以下のセクションでは、デバイスの接続とリモート監視ソリューションのストリーミング レイヤーをカスタマイズするオプションについて説明します。 [デバイス モデル](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models)には、ソリューションにおけるデバイスの種類とテレメトリを記述します。 デバイス モデルはシミュレートされたデバイスと物理デバイスの両方で使用されます。

物理デバイスの実装の例については、[デバイスのリモート監視構成済みソリューションへの接続](iot-suite-connecting-devices-node.md)に関する記事をご覧ください。

_物理デバイス_を使用する場合、デバイスのメタデータとテレメトリの仕様が記述されたデバイス モデルを、クライアント アプリケーションで指定する必要があります。

次のセクションでは、シミュレートされたデバイスでのデバイス モデルの使用について説明します。

### <a name="add-a-telemetry-type"></a>テレメトリ タイプの追加

Contoso デモ ソリューションのデバイスの種類に、各デバイスの種類が送信するテレメトリを指定します。 その他のテレメトリの種類を指定すると、デバイスはテレメトリの定義をメタデータとしてソリューションに送信できます。 この形式を使用すると、ダッシュボードはデバイスのテレメトリと利用可能な手段を動的に使用します。UI を変更する必要はありません。 また、ソリューションのデバイスの種類の定義を変更することもできます。

カスタム テレメトリを追加する方法については、_"デバイス シミュレーター"_ マイクロサービスの「[Test your solution with simulated devices (シミュレートされたデバイスを使用したソリューションのテスト)](iot-suite-remote-monitoring-test.md)」をご覧ください。

### <a name="add-a-device-type"></a>デバイスの種類を追加する

Contoso デモ ソリューションでは、デバイスの種類のサンプルをいくつか定義します。 このソリューションでは、カスタムのデバイスの種類を定義して、アプリケーション固有の要件を満たすことができます。 たとえば、お客様の会社でソリューションに接続するプライマリ デバイスとして産業用ゲートウェイを使用できます。

デバイスを正確に再現するには、デバイスで実行されているアプリケーションに変更を加え、デバイスの要件に一致させる必要があります。

_"デバイス シミュレーター"_ マイクロサービスに新しいデバイスの種類を追加する方法については、「[Test your solution with simulated devices (シミュレートされたデバイスを使用したソリューションのテスト)](iot-suite-remote-monitoring-test.md)」をご覧ください。

### <a name="define-custom-methods-for-simulated-devices"></a>シミュレートされたデバイスのカスタム メソッドを定義する

リモート監視ソリューションでシミュレートされたデバイスのカスタム メソッドを定義する方法については、GitHub リポジトリの[デバイス モデル](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models)に関するページをご覧ください。

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>物理デバイスを使用する

物理デバイスのメソッドとジョブを実装するには、IoT Hub の次の記事をご覧ください。

* [IoT Hub からのダイレクト メソッドの呼び出しについて](../iot-hub/iot-hub-devguide-direct-methods.md)
* [複数デバイスでのジョブをスケジュール設定する](../iot-hub/iot-hub-devguide-jobs.md)

### <a name="other-customization-options"></a>その他のカスタマイズのオプション

デバイスの接続とリモート監視ソリューションのストリーミング レイヤーをさらに変更するには、コードを編集します。 関連する GitHub リポジトリは次のとおりです。

* [デバイス テレメトリ (.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [デバイス テレメトリ (Java)](https://github.com/Azure/device-telemetry-java)
* [テレメトリ エージェント (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [テレメトリ エージェント (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>データ処理と分析

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

リモート監視ソリューションのデータ処理と分析レイヤーを変更するには、コードを編集します。 関連する GitHub リポジトリは次のとおりです。

* [テレメトリ エージェント (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [テレメトリ エージェント (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>インフラストラクチャ

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

リモート監視ソリューションのインフラストラクチャを変更するには、コードを編集します。 関連する GitHub リポジトリは次のとおりです。

* [IoTHub マネージャー (.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [IoTHub マネージャー (Java)](https://github.com/Azure/iothub-manager-java)
* [ストレージ アダプター (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [ストレージ アダプター (Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>次の手順

この記事では、構成済みソリューションのカスタマイズを容易にするために使用できるリソースについて説明します。

リモート監視の構成済みソリューションの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-suite-remote-monitoring-sample-walkthrough.md)に関するページをご覧ください。

リモート監視ソリューションのカスタマイズの詳細については次をご覧ください。

* [開発者向けリファレンス ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開発者向けトラブルシューティング ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->