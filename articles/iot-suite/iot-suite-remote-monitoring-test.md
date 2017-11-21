---
title: "リモート監視ソリューションでのデバイス シミュレーション - Azure | Microsoft Docs"
description: "このチュートリアルでは、リモート監視の事前構成済みソリューションを使用したデバイス シミュレーターの使用方法について説明します。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 732ec45003481b0e2f2eca03b6ae13772d325ef1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>シミュレートされたデバイスを使用したソリューションのテスト

このチュートリアルでは、リモート監視の事前構成済みソリューションを使用したデバイス シミュレーター マイクロサービスのカスタマイズ方法について説明します。 デバイス シミュレーターの機能を説明するために、このチュートリアルでは Contoso の IoT アプリケーションにおける 2 つのシナリオを使用して説明します。

最初のシナリオでは、Contoso が新しい電球のスマート デバイスをテストします。 テストを実施するために、次の特性をもった新しいシミュレートされたデバイスを作成します。

*プロパティ*

| 名前                     | 値                      |
| ------------------------ | --------------------------- |
| 色                    | 白、赤、青            |
| 明るさ               | 0 から 100                    |
| 推定寿命 | 10,000 時間からカウントダウン |

*テレメトリ*

次の表に、電球がクラウドにデータ ストリームとしてレポートするデータを示します。

| 名前   | 値      |
| ------ | ----------- |
| 状態 | "オン"、"オフ" |

*メソッド*

次の表に、新しいデバイスがサポートするアクションを示します。

| 名前        |
| ----------- |
| オンにする   |
| オフにする  |

*初期状態*

次の表に、デバイスの初期状態を示します。

| 名前                     | 値 |
| ------------------------ | -------|
| 初期の色            | 白  |
| 初期の明るさ       | 75     |
| 初期の寿命   | 10,000 |
| 初期のテレメトリ状態 | "オン"   |

2 つ目のシナリオでは、新しいテレメトリ タイプを Contoso の既存の **Chiller** デバイスに追加します。

このチュートリアルでは、リモート監視の事前構成済みソリューションを使用したデバイス シミュレーターの使用方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

>[!div class="checklist"]
> * 新しいデバイスの種類を作成する
> * カスタム デバイスの動作をシミュレートする
> * 新しいデバイスの種類をダッシュ ボードに追加する
> * 既存のデバイスの種類からカスタム テレメトリを送信する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにデプロイされたリモート監視ソリューションのインスタンスが必要です。

リモート監視ソリューションをまだデプロイしていない場合は、「[Deploy the remote monitoring preconfigured solution (リモート監視の事前構成済みソリューションのデプロイ)](iot-suite-remote-monitoring-deploy.md)」のチュートリアルを完了しておく必要があります。

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>デバイス シミュレーション サービス

事前構成済みソリューションのデバイス シミュレーション サービスを使用すると、組み込みのシミュレートされたデバイスの種類を変更し、新しいシミュレートされたデバイスの種類を作成できます。 カスタムのデバイスの種類を使用すれば、ソリューションに物理デバイスを接続する前に、リモート監視ソリューションの動作をテストできます。

## <a name="create-a-simulated-device-type"></a>シミュレートされたデバイスの種類の作成

シミュレーション マイクロサービスで新しいデバイスの種類を作成する最も簡単な方法は、既存の種類をコピーして変更することです。 次の手順では、組み込みの **Chiller** デバイスをコピーして新しい **Lightbulb (電球)** デバイスを作成する方法を説明します。

1. 次のコマンドを実行して、**デバイス シミュレーション** GitHub リポジトリをローカル コンピューターに複製します。

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. 各デバイスの種類は、`Services/data/devicemodels` フォルダー内に JSON モデル ファイルと関連スクリプトをもっています。 **Chiller** ファイルをコピーして、次の表で示すように **Lightbulb** ファイルを作成します。

    | ソース                      | 変換先                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>新しいデバイスの種類の特性の定義

`lightbulb-01.json` ファイルでは、そのデバイスの種類が生成するテレメトリやサポートするメソッドなどの特性を定義します。 次の手順では、`lightbulb-01.json` ファイルを更新して **Lightbulb** デバイスを定義します。

1. `lightbulb-01.json` ファイルで、デバイスのメタデータを次のスニペットに示すように更新します。

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. `lightbulb-01.json` ファイルで、シミュレーション定義を次のスニペットに示すように更新します。

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. `lightbulb-01.json` ファイルで、デバイスの種類のプロパティを次のスニペットに示すように更新します。

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. `lightbulb-01.json` ファイルで、デバイスの種類のテレメトリ定義を次のスニペットに示すように更新します。

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. `lightbulb-01.json` ファイルで、デバイスの種類のメソッドを次のスニペットに示すように更新します。

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      },
    }
    ```

1. `lightbulb-01.json` ファイルを保存します。

### <a name="simulate-custom-device-behavior"></a>カスタム デバイスの動作をシミュレートする

`scripts/lightbulb-01-state.js` ファイルでは、**Lightbulb** デバイスの種類のシミュレーション動作を定義します。 次の手順では、`scripts/lightbulb-01-state.js` ファイルを更新して **Lightbulb** デバイスの動作を定義します。

1. 次のスニペットに示すように、`scripts/lightbulb-01-state.js` ファイルで状態定義を編集します。

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. **vary** 関数を次の **flip** 関数に置き換えます。

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. 次のスニペットに示すように、**main** 関数を編集して動作を実装します。

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. `scripts/lightbulb-01-state.js` ファイルを保存します。

`scripts/SwitchOn-method.js` ファイルで **Lightbulb** デバイスに **Switch On (オンにする)** メソッドを実装します。 次の手順では、`scripts/SwitchOn-method.js` ファイルを更新します。

1. 次のスニペットに示すように、`scripts/SwitchOn-method.js` ファイルで状態定義を編集します。

    ```js
    var state = {
       status: "on"
    };
    ```

1. Lightbulb をオンにするには、次に示すように **main** 関数を編集します。

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. `scripts/SwitchOn-method.js` ファイルを保存します。

1. `scripts/SwitchOn-method.js` ファイルをコピーして `scripts/SwitchOff-method.js` という名前に変更します。

1. Lightbulb をオフにするには、次に示すように `scripts/SwitchOff-method.js` ファイルで **main** 関数を編集します。

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. `scripts/SwitchOff-method.js` ファイルを保存します。

### <a name="test-the-lightbulb-device-type"></a>Lightbulb デバイスの種類のテスト

**Lightbulb** デバイスの種類をテストするために、**デバイス シミュレーション** サービスのローカル コピーを実行することで、使用するデバイスの種類が想定どおりに動作するかどうかをはじめにテストできます。 新しいデバイスの種類をローカルでテストおよびデバッグした場合、コンテナーを再構築して**デバイス シミュレーション** サービスを Azure に再デプロイできます。

変更をローカルでテストおよびデバッグする方法については、「[Device simulation overview (デバイス シミュレーションの概要)](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)」をご覧ください。

新しい **Lightbulb** デバイス ファイルを出力ディレクトリにコピーするよう、プロジェクトを構成します。

デプロイされたソリューション内で新しいデバイスをテストする方法については、次の記事のいずれかをご覧ください。

* [カスタム docker-hub アカウントからのコンテナーのデプロイ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [デプロイされたコンテナーの手動コピーによる更新](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

**[デバイス]** ページで、使用する新しいデバイスの種類のインスタンスをプロビジョニングできます。

![使用可能なシミュレーションの一覧の表示](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

シミュレートされたデバイスからのテレメトリを表示できます。

![Lightbulb のテレメトリの表示](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

ご利用のデバイスで **SwitchOn (オンにする)** メソッドと **SwitchOff (オフにする)** メソッドを呼び出すことができます。

![Lightbulb のメソッドの呼び出し](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Azure にデプロイするために新しいデバイスの種類を使用して Docker イメージをビルドする方法については、「[Building a customized Docker image (カスタマイズされた Docker イメージのビルド)](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image)」をご覧ください。

## <a name="add-a-new-telemetry-type"></a>新しいテレメトリ タイプの追加

このセクションでは、既存のシミュレートされたデバイスの種類を変更して新しいテレメトリ タイプをサポートする方法について説明します。

### <a name="locate-the-chiller-device-type-files"></a>Chiller デバイスの種類ファイルの検索

次の手順では、組み込みの **Chiller** デバイスを定義するファイルの検索方法を説明します。

1. **デバイス シミュレーション** GitHub リポジトリをローカル コンピューターに複製していない場合は、次のコマンドを実行して複製してください。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. 各デバイスの種類は、`data/devicemodels` フォルダー内に JSON モデル ファイルと関連スクリプトをもっています。 シミュレートされた **Chiller** デバイスの種類を定義するファイルは次のとおりです。
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>新しいテレメトリ タイプの指定

次の手順では、新しい**内部温度**という種類を **Chiller** デバイスの種類に追加する方法を説明します。

1. `chiller-01.json` ファイルを開きます。

1. **SchemaVersion** 値を次のとおりに更新します。

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. **InitialState** セクションに、次の 2 つの定義を追加します。

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. **Telemetry** 配列に、次の定義を追加します。

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. `chiller-01.json` ファイルを保存します。

1. `scripts/chiller-01-state.js` ファイルを開きます。

1. **state** 変数に次のフィールドを追加します。

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. **main** 関数に次の行を追加します。

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. `scripts/chiller-01-state.js` ファイルを保存します。

### <a name="test-the-chiller-device-type"></a>Chiller デバイスの種類のテスト

更新された **Chiller** デバイスの種類をテストするために、**デバイス シミュレーション** サービスのローカル コピーを実行することで、使用するデバイスの種類が想定どおりに動作するかどうかをはじめにテストできます。 更新されたデバイスの種類をローカルでテストおよびデバッグした場合、コンテナーを再構築して**デバイス シミュレーション** サービスを Azure に再デプロイできます。

**デバイス シミュレーション** サービスをローカルで実行すると、このサービスはご利用のリモート監視ソリューションにテレメトリを送信します。 **[デバイス]** ページで、更新されたデバイスの種類のインスタンスをプロビジョニングできます。

変更をローカルでテストおよびデバッグする方法については、「[Running the service with Visual Studio (Visual Studio でのサービスの実行)](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio)」または「[Build and Run from the command line (コマンド ラインからのビルドと実行)](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line)」をご覧ください。

デプロイされたソリューション内で新しいデバイスをテストする方法については、次の記事のいずれかをご覧ください。

* [カスタム docker-hub アカウントからのコンテナーのデプロイ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [デプロイされたコンテナーの手動コピーによる更新](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

**[デバイス]** ページで、更新されたデバイスの種類のインスタンスをプロビジョニングできます。

![更新された Chiller の追加](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

シミュレートされたデバイスからの新しい**内部温度**テレメトリを表示できます。

Azure にデプロイするために新しいデバイスの種類を使用して Docker イメージをビルドする方法については、「[Building a customized Docker image (カスタマイズされた Docker イメージのビルド)](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image)」をご覧ください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 新しいデバイスの種類を作成する
> * カスタム デバイスの動作をシミュレートする
> * 新しいデバイスの種類をダッシュ ボードに追加する
> * 既存のデバイスの種類からカスタム テレメトリを送信する

デバイス シミュレーション サービスの使用方法を学習しました。おすすめの次のステップは、[物理デバイスをご利用のリモート監視ソリューションに接続する](iot-suite-connecting-devices-node.md)方法の学習です。

リモート監視ソリューションに関する開発者向け情報の詳細については、以下をご覧ください。

* [開発者向けリファレンス ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開発者向けトラブルシューティング ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->