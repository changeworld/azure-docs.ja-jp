---
title: リモート監視ソリューションでのデバイス シミュレーション - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューション アクセラレータを使用したデバイス シミュレーターの使用方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/15/2018
ms.topic: conceptual
ms.openlocfilehash: 9c0c1ba9dd343baa453f10ad82c0cc8b8e69da7b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596156"
---
# <a name="create-a-new-simulated-device"></a>新しいシミュレートされたデバイスの作成

このチュートリアルでは、リモート監視ソリューション アクセラレータでデバイス シミュレーター マイクロサービスをカスタマイズする方法について説明します。 デバイス シミュレーターの機能を説明するために、このチュートリアルでは Contoso の IoT アプリケーションにおける 2 つのシナリオを使用して説明します。

次のビデオでは、デバイス シミュレーター マイクロサービスをカスタマイズするためのオプションの概要が説明されています。

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

最初のシナリオでは、Contoso がスマート電球の新しいデバイスをテストします。 このテストを実施するには、デバイスのシミュレーションを新規作成して次の特性を含めます。

*プロパティ*

| 名前                     | 値                      |
| ------------------------ | --------------------------- |
| 色                    | 白、赤、青            |
| 明るさ               | 0 から 100                    |
| 推定寿命 | 10,000 時間からカウントダウン |

*テレメトリ*

次の表に、電球がクラウドにデータ ストリームとしてレポートするデータを示します。

| Name   | 値      |
| ------ | ----------- |
| 状態 | "オン"、"オフ" |
| 気温 | 度 (華氏) |
| オンライン | true、false |

> [!NOTE]
> **オンライン** テレメトリ値は、シミュレートされるすべての種類で必須です。

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
| 初期のテレメトリ温度 | 200   |

2 つ目のシナリオでは、新しいテレメトリ タイプを Contoso の既存の **Chiller** デバイスに追加します。

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用したデバイス シミュレーターの使用方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

>[!div class="checklist"]
> * 新しいデバイスの種類を作成する
> * カスタム デバイスの動作をシミュレートする
> * 新しいデバイスの種類をダッシュ ボードに追加する
> * 既存のデバイスの種類からカスタム テレメトリを送信する

次のビデオでは、シミュレートされたデバイスと実際のデバイスをリモート監視ソリューションに接続する手順を示します。

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、次が必要です。

* お使いの Azure サブスクリプションにデプロイされたリモート監視ソリューション インスタンス。 まだリモート監視ソリューションをデプロイしていない場合は、「[リモート監視ソリューション アクセラレータをデプロイする](../iot-accelerators/quickstart-remote-monitoring-deploy.md)」チュートリアルを実行する必要があります。

* Visual Studio 2017。 Visual Studio 2017 をインストールしていない場合は、無料の [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) エディションをダウンロードできます。

* [Cloud Explorer for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) のVisual Studio 拡張機能。

* [Docker Hub](https://hub.docker.com/) のアカウント。 無料でサインアップして開始できます。

* デスクトップ マシンにインストールされた [Git](https://git-scm.com/downloads)。

## <a name="prepare-your-development-environment"></a>開発環境を準備する

シミュレートされた新しいデバイスをリモート監視ソリューションに追加できるように開発環境を準備するには、次のタスクを実行します。

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Azure でソリューション仮想マシンへの SSH アクセスを構成する

[www.azureiotsolutions.com](https://www.azureiotsolutions.com) でリモート監視ソリューションを作成したときに、ソリューション名を選択しました。 そのソリューション名が、ソリューションで使用されるさまざまなデプロイ済みリソースを含む Azure リソース グループの名前になります。 次のコマンドは、**Contoso-01** という名前のリソース グループを使用しています。この **Contoso-01** は、ご自身のリソース グループの名前で置き換える必要があります。

次のコマンドでは、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) の `az` コマンドを使用します。 Azure CLI 2.0 は開発用コンピューターにインストールできます。または、[Azure Portal](http://portal.azure.com) で [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を使用することもできます。 Cloud Shell には、Azure CLI 2.0 が事前にインストールされています。

1. リモート監視リソースが含まれるリソース グループの名前を確認するには、次のコマンドを実行します。

    ```sh
    az group list | grep "name"
    ```

    このコマンドにより、ご利用のサブスクリプションにあるリソース グループの一覧が表示されます。 この一覧には、リモート監視ソリューションと同じ名前のリソース グループがあるはずです。

1. ご自身のリソース グループを、以降のコマンドの既定のグループにするには、次のコマンドを実行します。**Contoso-01** は、ご自身のリソース グループ名で置き換えてください。

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. リソース グループのリソースの一覧を表示するには、次のコマンドを実行します。

    ```sh
    az resource list -o table
    ```

    仮想マシンとネットワーク セキュリティ グループの名前を書き留めておきます。 これらの値を後の手順で使用します。

1. 仮想マシンの SSH アクセスを有効にするには、前の手順のネットワーク セキュリティ グループの名前を使用して、次のコマンドを実行します。

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    ネットワークの受信規則の一覧を表示するには、次のコマンドを実行します。

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

    テストと開発時にのみ、SSH アクセスを有効にします。 SSH を有効にした場合、[できるだけ早く無効にする必要があります](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-azure-virtual-machines)。

1. 仮想マシンのパスワードを自分だけが知るパスワードに変更するには、次のコマンドを実行します。 前にメモしておいた仮想マシンの名前と、任意のパスワードを使用してください。

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. 仮想マシンの IP アドレスを見つけるには、次のコマンドを使用します。パブリック IP アドレスを書き留めておいてください。

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. これで SSH を使用して仮想マシンに接続できます。 `ssh` コマンドは、Cloud Shell に事前にインストールされています。 前の手順のパブリック IP アドレスを使用し、メッセージが表示されたら、仮想マシンに対して構成したパスワードを入力します。

    ```sh
    ssh azureuser@public-ip-address
    ```

    リモート監視ソリューションで Docker コンテナーを実行している仮想マシンのシェルにアクセスできるようになりました。 実行中のコンテナーを表示するには、次のコマンドを使用します。

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>サービス接続文字列を見つける

このチュートリアルでは、ソリューションの Cosmos DB サービスと IoT Hub サービスに接続する Visual Studio ソリューションを使用します。 次の手順では、必要な接続文字列の値を見つける方法 の 1 つを示します。

1. Cosmos DB の接続文字列を見つけるには、仮想マシンに接続されている SSH セッションで、次のコマンドを実行します。

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    接続文字列をメモします。 この値は、後ほどチュートリアルの中で使用します。

1. IoT ハブの接続文字列を見つけるには、仮想マシンに接続されている SSH セッションで、次のコマンドを実行します。

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    接続文字列をメモします。 この値は、後ほどチュートリアルの中で使用します。

> [!NOTE]
> こうした接続文字列は、Azure Portal または `az` コマンドで確認することもできます。

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>仮想マシンでデバイス シミュレーション サービスを停止する

デバイス シミュレーション サービスを変更するとき、そのサービスをローカルで実行して、変更をテストできます。 デバイス シミュレーション サービスをローカルで実行する前に、仮想マシンで実行されているインスタンスを次のようにして停止する必要があります。

1. **device-simulation-dotnet** の**コンテナー ID** を見つけるには、仮想マシンに接続されている SSH セッションで、次のコマンドを実行します。

    ```sh
    docker ps
    ```

    **device-simulation-dotnet** サービスのコンテナー ID を書き留めておきます。

1. **device-simulation-dotnet** コンテナーを停止するには、次のコマンドを実行します。

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>GitHub リポジトリを複製する

このチュートリアルでは、**device-simulation** および **storage-adapter** Visual Studio プロジェクトを使用します。 ソース コード リポジトリは GitHub から複製できます。 この手順は、Visual Studio がインストールされているローカルの開発用コンピューターで実行します。

1. コマンド プロンプトを開き、**device-simulation** および **storage-adapter** GitHub リポジトリのコピーを保存するフォルダーに移動します。

1. .NET バージョンの **device-simulation** リポジトリを複製するには、次のコマンドを実行します。

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    リモート監視ソリューションのデバイス シミュレーション サービスを使用すると、組み込みのシミュレートされたデバイスの種類を変更し、新しいシミュレートされたデバイスの種類を作成できます。 カスタムのデバイスの種類を使用すると、物理デバイスを接続する前に、リモート監視ソリューションの動作をテストできます。

1. .NET バージョンの **storage-adapter** リポジトリを複製するには、次のコマンドを実行します。

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    デバイス シミュレーション サービスは、ストレージ アダプター サービスを使用して、Azure の Cosmos DB サービスに接続します。 リモート監視ソリューションにより、シミュレートされたデバイス構成データが Cosmos DB データベースに格納されます。

### <a name="run-the-storage-adapter-service-locally"></a>ストレージ アダプター サービスをローカルで実行する

デバイス シミュレーション サービスは、ストレージ アダプター サービスを使用して、ソリューションの Cosmos DB データベースに接続します。 デバイス シミュレーション サービスをローカルで実行する場合は、ストレージ アダプター サービスもローカルで実行する必要があります。 次の手順では、Visual Studio からストレージ アダプター サービスを実行する方法を示しています。

1. Visual Studio を使用して、**storage-adapter** リポジトリのローカル複製で **pcs-storage-adapter.sln** ソリューション ファイルを開きます。

1. ソリューション エクスプローラーで、**WebService** プロジェクトを右クリックし、**[プロパティ]**、**[デバッグ]** の順に選択します。

1. **[環境変数]** セクションで、**PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 変数の値を編集して、前にメモした Cosmos DB 接続文字列に変更します。 次に、変更を保存します。

1. ソリューション エクスプローラーで、**WebService** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順に選択します。

1. ローカルでサービスの実行が開始され、`http://localhost:9022/v1/status` が既定のブラウザーで開きます。 **状態**の値が "OK: Alive and well" であることを確認します。

1. ストレージ アダプター サービスは、チュートリアルが完了するまでローカルで実行したままにします。

これですべての準備が整い、シミュレートされた新しいデバイスの種類を、リモート監視ソリューションに追加し始めることができます。

## <a name="create-a-simulated-device-type"></a>デバイスのシミュレーションの種類を作成

最も簡単にデバイスの種類をデバイス シミュレーション サービスに新しく作成する方法は、既存の種類をコピーして変更することです。 次の手順では、組み込みの **Chiller** デバイスをコピーして新しい **Lightbulb (電球)** デバイスを作成する方法を説明します。

1. ソリューション エクスプローラーで、**WebService** プロジェクトを右クリックし、**[プロパティ]**、**[デバッグ]** の順に選択します。

1. **[環境変数]** セクションで、**PCS\_IOTHUB\_CONNSTRING** 変数の値を編集して、前にメモした IoT ハブ接続文字列に変更します。 次に、変更を保存します。

1. ソリューション エクスプローラーで、**device-simulation** ソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。 **[シングル スタートアップ プロジェクト]** を選択し、**[WebService]** を選択します。 次に、 **[OK]** をクリックします

1. デバイスの種類ごとに、**Services/data/devicemodels** フォルダーに、JSON モデル ファイルと関連スクリプトがあります。 ソリューション エクスプローラーで、次の表に示すように、**Chiller** ファイルをコピーして、**Lightbulb** ファイルを作成します。

    | ソース                      | 変換先                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>新しいデバイスの種類の特性の定義

**lightbulb-01.json** ファイルでは、そのデバイスの種類が生成するテレメトリやサポートするメソッドなどの特性を定義します。 次の手順では、**lightbulb-01.json** ファイルを更新して **Lightbulb** デバイスを定義します。

1. **lightbulb-01.json** ファイルで、デバイスのメタデータを次のスニペットに示すように更新します。

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. **lightbulb-01.json** ファイルで、シミュレーション定義を次のスニペットに示すように更新します。

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Interval": "00:00:20",
      "Scripts": [
        {
          "Type": "javascript",
          "Path": "lightbulb-01-state.js"
        }
      ]
    },
    ```

1. **lightbulb-01.json** ファイルで、デバイスの種類のプロパティを次のスニペットに示すように更新します。

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. **lightbulb-01.json** ファイルで、デバイスの種類のテレメトリ定義を次のスニペットに示すように更新します。

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. **lightbulb-01.json** ファイルで、デバイスの種類のメソッドを次のスニペットに示すように更新します。

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. **lightbulb-01.json** ファイルを保存します。

### <a name="simulate-custom-device-behavior"></a>カスタム デバイスの動作をシミュレートする

**scripts/lightbulb-01-state.js** ファイルでは、**Lightbulb** デバイスの種類のシミュレーション動作を定義します。 次の手順では、**scripts/lightbulb-01-state.js** ファイルを更新して **Lightbulb** デバイスの動作を定義します。

1. 次のスニペットに示すように、**scripts/lightbulb-01-state.js** ファイルで状態定義を編集します。

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. 次の定義で **vary** 関数の後に **flip** 関数を追加します。

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
    function main(context, previousState, previousProperties) {

        // Restore the global device properties and the global state before
        // generating the new telemetry, so that the telemetry can apply changes
        // using the previous function state.
        restoreSimulation(previousState, previousProperties);

        state.temperature = vary(200, 5, 150, 250);

        // Make this flip every so often
        state.status = flip(state.status);

        updateState(state);

        return state;
    }
    ```

1. **scripts/lightbulb-01-state.js** ファイルを保存します。

**scripts/SwitchOn-method.js** ファイルによって **Lightbulb** デバイスに **Switch On** メソッドが実装されます。 次の手順では、**scripts/SwitchOn-method.js** ファイルを更新します。

1. 次のスニペットに示すように、**scripts/SwitchOn-method.js** ファイルで状態定義を編集します。

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

1. **scripts/SwitchOn-method.js** ファイルを保存します。

1. **scripts/SwitchOn-method.js** ファイルをコピーして、**scripts/SwitchOff-method.js** という名前に変更します。

1. Lightbulb をオフにするには、次に示すように **scripts/SwitchOff-method.js** ファイルで **main** 関数を編集します。

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. **scripts/SwitchOff-method.js** ファイルを保存します。

1. ソリューション エクスプローラーで、4 つの新しいファイルを順番に選択します。 各ファイルの **[プロパティ]** ウィンドウで、**[出力ディレクトリにコピー]** が **[新しい場合はコピーする]** に設定されていることを確認します。

### <a name="configure-the-device-simulation-service"></a>デバイス シミュレーション サービスを構成する

テスト中にソリューションに接続するシミュレートされたデバイスの数を制限するには、1 つの chiller および 1 つの lightbulb デバイスが実行されるようにサービスを構成します。 構成データは、ソリューションのリソース グループ内の Cosmos DB インスタンスに格納されます。 構成データを編集するには、Visual Studio で **Cloud Explorer** ビューを使用します。

1. Visual Studio で **Cloud Explorer** ビューを開くには、**[ビュー]**、**[Cloud Explorer]** の順に選択します。

1. シミュレーション構成ドキュメントを見つけるには、**[リソースの検索]** に「**simualtions.1**」と入力します。

1. **simulations.1** ドキュメントをダブルクリックして、編集のために開きます。

1. **[データ]** の値で、次のスニペットのような **DeviceModels** 配列を探します。

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. 1 つの chiller と 1 つ lightbulb のシミュレートされたデバイスを定義するには、**DeviceModels** 配列を次のコードで置き換えます。

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    **simulations.1** ドキュメントへの変更を保存します。

> [!NOTE]
> Azure Portal で Cosmos DB データ エクスプローラーを使用して、**simulations.1** ドキュメントを編集することもできます。

### <a name="test-the-lightbulb-device-type-locally"></a>Lightbulb デバイスの種類をローカルでテストする

デバイス シミュレーション プロジェクトをローカルで実行して、シミュレートされた新しい lightbulb の種類をテストする準備ができました。

1. ソリューション エクスプローラーで、**WebService** を右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順に選択します。

1. 2 つのシミュレートされたデバイスが IoT ハブに接続されていることを確認するには、お使いのブラウザーで Azure Portal を開きます。

1. 自分のリモート監視ソリューションが含まれるリソース グループの IoT ハブに移動します。

1. **[監視]** セクションで、**[メトリック]** を選択します。 次に、**[接続されているデバイス]** の数が 2 であることを確認します。

    ![接続されているデバイスの数](./media/iot-accelerators-remote-monitoring-test/connecteddevices.png)

1. お使いのブラウザーで、リモート監視ソリューションの **ダッシュボード** に移動します。 **ダッシュ ボード**のテレメトリ パネルで、**[温度]** を選択します。 すべてのシミュレートされたデバイスの温度がグラフで表示されます。

    ![温度テレメトリ](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

現在、lightbulb デバイス シミュレーションがローカルで実行されています。 次の手順では、Azure でリモート監視マイクロサービスを実行する仮想マシンに、更新されたシミュレーター コードをデプロイします。

続行する前に、Visual Studio でデバイス シミュレーション プロジェクトとストレージ アダプター プロジェクトの両方のデバッグを停止できます。

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>更新されたシミュレーターをクラウドにデプロイする

リモート監視ソリューションのマイクロサービスは、Docker コンテナーで実行されます。 コンテナーは、Azure のソリューションの仮想マシンでホストされています。 このセクションでは、次の作業を行います。

* 新しいデバイス シミュレーション Docker イメージを作成する。
* イメージを Docker ハブ リポジトリにアップロードする。
* イメージをソリューションの仮想マシンにインポートする。

次の手順は、**lightbulb** と呼ばれるリポジトリが、使用している Docker Hub アカウントにあることを前提としています。

1. Visual Studio を使用して、**device-simulation** プロジェクトで、**solution\scripts\docker\build.cmd** ファイルを開きます。

1. 行を編集することで、**DOCKER_IMAGE** 環境変数を、使用する Docker Hub リポジトリ名に設定します。

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    変更を保存します。

1. Visual Studio を使用して、**device-simulation** プロジェクトで、**solution\scripts\docker\publish.cmd** ファイルを開きます。

1. 行を編集することで、**DOCKER_IMAGE** 環境変数を、使用する Docker Hub リポジトリ名に設定します。

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    変更を保存します。

1. 管理者としてコマンド プロンプトを開きます。 次に、**device-simulation** GitHub リポジトリの複製で、**scripts\docker** フォルダーに移動します。

1. Docker イメージを構築するには、次のコマンドを実行します。

    ```cmd
    build.cmd
    ```

1. Docker Hub アカウントにサインインするには、次のコマンドを実行します。

    ```cmd
    docker login
    ```

1. 新しいイメージを Docker Hub アカウントにアップロードするには、次のコマンドを実行します。

    ```cmd
    publish.cmd
    ```

1. アップロードを確認するには、[https://hub.docker.com/](https://hub.docker.com/) に移動します。 **lightbulb** リポジトリを見つけて、**[詳細]** を選択します。 次に、**[タグ]** を選択します。

    ![Docker Hub](./media/iot-accelerators-remote-monitoring-test/dockerhub.png)

    スクリプトにより、**[テスト]** タグがイメージに追加されました。

1. SSH を使用して、Azure のソリューションの仮想マシンに接続します。 次に、**[アプリ]** フォルダーに移動して、**docker-compose.yml** ファイルを編集します。

    ```sh
    cd /app
    sudo nano docker-compose.yml
    ```

1. Docker イメージが使用されるようにデバイス シミュレーション サービスのエントリを編集します。

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    変更を保存します。

1. 新しい設定ですべてのサービスを再起動するには、次のコマンドを実行します。

    ```sh
    sudo ./start.sh
    ```

1. 新しいデバイス シミュレーション コンテナーのログ ファイルを確認するには、次のコマンドを実行して、コンテナー ID を検索します。

    ```sh
    docker ps
    ```

    次に、コンテナー ID を使用して、次のコマンドを実行します。

    ```sh
    docker logs {container ID}
    ```

これで、デバイス シミュレーション サービスの更新バージョンを、自分のリモート監視ソリューションにデプロイする手順は完了しました。

お使いのブラウザーで、リモート監視ソリューションの **ダッシュボード** に移動します。 **ダッシュ ボード**のテレメトリ パネルで、**[温度]** を選択します。 2 つのシミュレートされたデバイスの温度がグラフで表示されます。

![温度テレメトリ](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

**[デバイス]** ページで、使用する新しいデバイスの種類のインスタンスをプロビジョニングできます。

![使用可能なシミュレーションの一覧の表示](./media/iot-accelerators-remote-monitoring-test/devicesmodellist.png)

シミュレートされたデバイスからのテレメトリを表示できます。

![Lightbulb のテレメトリの表示](./media/iot-accelerators-remote-monitoring-test/devicestelemetry.png)

ご利用のデバイスで **SwitchOn** メソッドと **SwitchOff**  メソッドを呼び出すことができます。

![Lightbulb のメソッドの呼び出し](./media/iot-accelerators-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>新しいテレメトリ タイプの追加

このセクションでは、既存デバイスのシミュレーションの種類を変更して新しいテレメトリ タイプをサポートする方法について説明します。

### <a name="locate-the-chiller-device-type-files"></a>Chiller デバイスの種類ファイルの検索

次の手順では、組み込みの **Chiller** デバイスを定義するファイルの検索方法を説明します。

1. **device-simulation-dotnet** GitHub リポジトリをローカル コンピューターに複製していない場合は、次のコマンドを実行して複製してください。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. 各デバイスの種類は、`data/devicemodels` フォルダー内に JSON モデル ファイルと関連スクリプトをもっています。 **Chiller** のシミュレーションにおいてデバイスの種類を定義するファイルは次のとおりです。

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>新しいテレメトリ タイプの指定

次の手順では、新しい**内部温度**という種類を **Chiller** デバイスの種類に追加する方法を説明します。

1. **chiller-01.json** ファイルを開きます。

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

1. **chiller-01.json** ファイルを保存します。

1. **scripts/chiller-01-state.js** ファイルを開きます。

1. **state** 変数に次のフィールドを追加します。

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. **main** 関数に次の行を追加します。

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. **scripts/chiller-01-state.js** ファイルを保存します。

### <a name="test-the-chiller-device-type"></a>Chiller デバイスの種類のテスト

更新された **Chiller** デバイスの種類をテストするには、最初に、**device-simulation-dotnet** サービスのローカル コピーを実行して、使用するデバイスの種類が想定どおりに動作することをテストします。 更新されたデバイスの種類をローカルでテストおよびデバッグした場合、コンテナーを再構築して **device-simulation-dotnet** サービスを Azure に再デプロイできます。

**device-simulation-dotnet** サービスをローカルで実行すると、このサービスはご利用のリモート監視ソリューションにテレメトリを送信します。 **[デバイス]** ページで、更新されたデバイスの種類のインスタンスをプロビジョニングできます。

変更をローカルでテストおよびデバッグするには、前の「[Lightbulb デバイスの種類をローカルでテストする](#test-the-lightbulb-device-type-locally)」を参照してください。

更新されたデバイス シミュレーション サービスを、Azure のソリューションの仮想マシンにデプロイするには、前の「[更新されたシミュレーターをクラウドにデプロイする](#deploy-the-updated-simulator-to-the-cloud)」を参照してください。

**[デバイス]** ページで、更新されたデバイスの種類のインスタンスをプロビジョニングできます。

![更新された Chiller の追加](./media/iot-accelerators-remote-monitoring-test/devicesupdatedchiller.png)

シミュレートされたデバイスからの新しい**内部温度**テレメトリを表示できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 新しいデバイスの種類を作成する
> * カスタム デバイスの動作をシミュレートする
> * 新しいデバイスの種類をダッシュ ボードに追加する
> * 既存のデバイスの種類からカスタム テレメトリを送信する

ここではデバイス シミュレーション サービスをカスタマイズする方法を学習しました。 次は、[ 物理デバイスをご利用のリモート監視ソリューションに接続する ](iot-accelerators-connecting-devices-node.md) 方法について学習することをお勧めします。

リモート監視ソリューションに関する開発者向け情報の詳細については、以下をご覧ください。

* [開発者向けリファレンス ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開発者向けトラブルシューティング ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
