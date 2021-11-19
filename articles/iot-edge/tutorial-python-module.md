---
title: チュートリアル - Azure IoT Edge を使用したカスタム Python モジュールのチュートリアル
description: このチュートリアルでは、Python コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します。
services: iot-edge
author: kgremban
ms.reviewer: kgremban
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f315e3987d46bc4a27b5ad5566b0424f410cd16a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485208"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-using-linux-containers"></a>チュートリアル: Linux コンテナーを使用して Python IoT Edge モジュールを開発してデプロイする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Visual Studio Code を使用して Python コードを開発し、Azure IoT Edge を実行しているデバイスにデプロイします。

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、クイック スタートで設定した IoT Edge デバイス上のセンサー データをフィルター処理する IoT Edge モジュールの作成とデプロイについて段階的に説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Visual Studio Code を使用して IoT Edge Python モジュールを作成する。
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、**Visual Studio Code** を使用して **Python** でモジュールを開発し、それを IoT Edge デバイスにデプロイする方法について説明します。

IoT Edge は、Windows コンテナーを使用した Python モジュールをサポートしていません。

Linux コンテナーを使用して Python モジュールを開発してデプロイする際のオプションを次の表でご確認ください。

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Linux AMD64 の Python モジュールに VS Code を使用する](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Linux ARM32 の Python モジュールに VS Code を使用する](./media/tutorial-c-module/green-check.png) |  |

このチュートリアルを開始する前に、前のチュートリアル「[Linux コンテナーを使用して IoT Edge モジュールを開発する](tutorial-develop-for-linux.md)」を完了して、Linux コンテナー開発用の開発環境を設定しておく必要があります。 このチュートリアルを完了すると、次の前提条件が満たされます。

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Azure IoT Edge を実行しているデバイス。 クイックスタートを使用して、[Linux デバイス](quickstart-linux.md)または [Windows デバイス](quickstart.md)を設定できます。
* コンテナー レジストリ ([Azure Container Registry](../container-registry/index.yml) など)。
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して構成された [Visual Studio Code](https://code.visualstudio.com/)。
* Linux コンテナーを実行するように構成された [Docker CE](https://docs.docker.com/install/)。

Python で IoT Edge モジュールを開発するには、開発マシンに次の追加の前提条件をインストールします。

* Visual Studio Code 用 の [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。
* [Python](https://www.python.org/downloads/)。
* Python パッケージをインストールするための [Pip](https://pip.pypa.io/en/stable/installing/#installation) (通常は、Python のインストールに含まれています)。

>[!Note]
>`bin` フォルダーがプラットフォームのパスにあることを確認してください。 通常、UNIX および macOS では `~/.local/`、Windows では `%APPDATA%\Python` です。

## <a name="create-a-module-project"></a>モジュール プロジェクトを作成する

以下の手順では、Visual Studio Code と Azure IoT Tools を使用して、IoT Edge Python モジュールを作成します。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

独自のコードでカスタマイズできる Python ソリューション テンプレートを作成します。

1. Visual Studio Code で、 **[表示]**  >  **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

2. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、手順に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。

3. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。 プロンプトに従って、次の情報を指定してソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **[Python モジュール]** を選択します。 |
   | Provide a module name (モジュール名の指定) | ご自身のモジュール **PythonModule** に名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 前の手順で指定した名前がコンテナー イメージに事前設定されます。 **localhost:5000** を、Azure Container Registry の **ログイン サーバー** の値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br>最終的なイメージ リポジトリは、\<registry name\>.azurecr.io/pythonmodule のようになります。 |

   ![Docker イメージ リポジトリを指定する](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー リポジトリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。

IoT Edge 拡張機能は、Azure からコンテナー レジストリの資格情報をプルし、それらを環境ファイルに取り込もうとします。 資格情報が既に含まれているかどうかを確認します。 含まれていない場合は、次のようにして追加します。

1. VS Code エクスプローラーで、 **.env** ファイルを開きます。
2. ご自身の Azure コンテナー レジストリからコピーした **ユーザー名** と **パスワード** の値を使用して、フィールドを更新します。
3. .env ファイルを保存します。

>[!NOTE]
>このチュートリアルでは、開発とテストのシナリオに便利な、Azure Container Registry の管理者ログイン資格情報を使用します。 運用環境のシナリオに向けて準備ができたら、サービス プリンシパルのような最小限の特権で認証できるオプションを使用することをお勧めします。 詳細については、「[コンテナー レジストリへのアクセスを管理する](production-checklist.md#manage-access-to-your-container-registry)」を参照してください。

### <a name="select-your-target-architecture"></a>ターゲット アーキテクチャを選択する

現在、Visual Studio Code では、Linux AMD64 および Linux ARM32v7 デバイス用の Python モジュールを開発できます。 ソリューションごとにターゲットとするアーキテクチャを選択する必要があります。これは、アーキテクチャの種類によって、コンテナーのビルド方法と実行方法が異なるためです。 既定値は Linux AMD64 です。

1. コマンド パレットを開き、次を検索します: 「**Azure IoT Edge: Set Default Target Platform for Edge Solution (Azure IoT Edge: Edge ソリューションの既定のターゲット プラットフォームの設定)** 」。または、ウィンドウの下部にあるサイド バーで、ショートカット アイコンを選択します。

2. コマンド パレットで、オプションの一覧からターゲット アーキテクチャを選択します。 このチュートリアルでは、Ubuntu 仮想マシンを IoT Edge デバイスとして使用するため、既定値の **amd64** のままにします。

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

各テンプレートにはサンプル コードが付属しており、シミュレートされたセンサー データが **SimulatedTemperatureSensor** モジュールから取得され、IoT Hub にルーティングされます。 このセクションでは、メッセージを送信する前に、**PythonModule** を展開してそのメッセージを分析するコードを追加します。

1. VS Code エクスプローラーで、 **[モジュール]**  >  **[PythonModule]**  >  **[main.py]** を開きます。

2. **main.py** ファイルの上部で、**json** ライブラリをインポートします。

    ```python
    import json
    ```

3. **TEMPERATURE_THRESHOLD**、**RECEIVED_MESSAGES** および **TWIN_CALLBACKS** 変数のグローバル定義を追加します。 温度のしきい値により、データが IoT Hub に送信される基準値が設定されます。データは、マシンの測定温度がこの値を超えると送信されます。

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. **create_client** 関数のコードを次のコードに置き換えます。

    ```python
    def create_client():
        client = IoTHubModuleClient.create_from_edge_environment()

        # Define function for handling received messages
        async def receive_message_handler(message):
            global RECEIVED_MESSAGES
            print("Message received")
            size = len(message.data)
            message_text = message.data.decode('utf-8')
            print("    Data: <<<{data}>>> & Size={size}".format(data=message.data, size=size))
            print("    Properties: {}".format(message.custom_properties))
            RECEIVED_MESSAGES += 1
            print("Total messages received: {}".format(RECEIVED_MESSAGES))

            if message.input_name == "input1":
                message_json = json.loads(message_text)
                if "machine" in message_json and "temperature" in message_json["machine"] and message_json["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                    message.custom_properties["MessageType"] = "Alert"
                    print("ALERT: Machine temperature {temp} exceeds threshold {threshold}".format(
                        temp=message_json["machine"]["temperature"], threshold=TEMPERATURE_THRESHOLD
                    ))
                    await client.send_message_to_output(message, "output1")

        # Define function for handling received twin patches
        async def receive_twin_patch_handler(twin_patch):
            global TEMPERATURE_THRESHOLD
            global TWIN_CALLBACKS
            print("Twin Patch received")
            print("     {}".format(twin_patch))
            if "TemperatureThreshold" in twin_patch:
                TEMPERATURE_THRESHOLD = twin_patch["TemperatureThreshold"]
            TWIN_CALLBACKS += 1
            print("Total calls confirmed: {}".format(TWIN_CALLBACKS))

        try:
            # Set handler on the client
            client.on_message_received = receive_message_handler
            client.on_twin_desired_properties_patch_received = receive_twin_patch_handler
        except:
            # Cleanup if failure occurs
            client.shutdown()
            raise

        return client
    ```

7. main.py ファイルを保存します。

8. VS Code のエクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。

9. **PythonModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを **moduleContent** セクションの下部、 **$edgeHub** モジュール ツインの後に挿入します。

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![モジュール ツインをデプロイ テンプレートに追加する](./media/tutorial-python-module/module-twin.png)

10. deployment.template.json ファイルを保存します。

## <a name="build-and-push-your-module"></a>モジュールをビルドしてプッシュする

前のセクションでは、IoT Edge ソリューションを作成したほか、レポートされるマシン温度が許容範囲内である場合にメッセージを除外するコードを PythonModule に追加しました。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

1. **[表示]**  >  **[ターミナル]** を選択して、VS Code 統合ターミナルを開きます。

2. ターミナルで次のコマンドを入力して、Docker にサインインします。 自分の Azure コンテナー レジストリのユーザー名、パスワード、ログイン サーバーを使用してサインインします。 これらの値は、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから取得できます。

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` の使用を推奨するセキュリティ警告が表示される場合があります。 このベスト プラクティスは、運用環境のシナリオを対象に推奨されていますが、それはこのチュートリアルの範囲外になります。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) のリファレンスをご覧ください。

3. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、 **[Build and Push IoT Edge Solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。

   ビルドおよびプッシュ コマンドでは、3 つの操作を開始します。 最初に、デプロイ テンプレートと他のソリューション ファイルの情報からビルドされた完全な配置マニフェストを保持する、**config** という新しいフォルダーをソリューション内に作成します。 次に、`docker build` を実行して、お使いのターゲット アーキテクチャ用の適切な Dockerfile に基づいてコンテナー イメージをビルドします。 そして、`docker push` を実行して、イメージ リポジトリをコンテナー レジストリにプッシュします。

   このプロセスは、初回は数分間かかる可能性がありますが、次回これらのコマンドを実行するときは、それより速くなります。

## <a name="deploy-modules-to-device"></a>モジュールをデバイスにデプロイする

Visual Studio Code エクスプローラーと Azure IoT Tools 拡張機能を使用して、モジュール プロジェクトを自分の IoT Edge デバイスにデプロイします。 シナリオ用の配置マニフェストである **deployment.amd64.json** ファイルは、config フォルダーに既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

お使いの IoT Edge デバイスが稼働していることを確認します。

1. Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションで、 **[デバイス]** を展開して IoT デバイスの一覧を表示します。

2. IoT Edge デバイスの名前を右クリックして、 **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

3. **config** フォルダーで **deployment.amd64.json** ファイルを選択し、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 deployment.template.json ファイルは使用しないでください。

4. お使いのデバイスの **[モジュール]** を展開し、デプロイされて実行中のモジュールの一覧を表示します。 更新ボタンをクリックします。 新しい **PythonModule** が、**SimulatedTemperatureSensor** モジュール、 **$edgeAgent** および **$edgeHub** と一緒に実行されていることがわかります。

    両方のモジュールが開始するまでに数分かかる場合があります。 IoT Edge ランタイムは、新しい配置マニフェストを受け取り、コンテナー ランタイムからモジュール イメージを取得して、それぞれの新しいモジュールを開始する必要があります。

## <a name="view-the-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスに配置マニフェストを適用すると、デバイス上の IoT Edge ランタイムが新しいデプロイ情報を収集し、そこで実行を開始します。 デバイス上で動作しているモジュールのうち、配置マニフェストに含まれていないものはすべて停止されます。 デバイスから不足しているモジュールがあればすべて開始されます。

IoT Edge デバイスのステータスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを使用して確認できます。 デバイスの詳細を展開すると、デプロイされて実行中のモジュールが一覧表示されます。

1. Visual Studio Code のエクスプローラーで、IoT Edge デバイスの名前を右クリックし、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

2. 自分の IoT ハブに到着するメッセージを表示します。 メッセージが到着するまでに時間がかかる場合があります。 IoT Edge デバイスでは、新しいデプロイを受け取って、すべてのモジュールを開始する必要があります。 次に、PythonModule コードに加えられた変更により、マシンの温度が 25 度に達するまで待機してから、メッセージが送信されます。 また、その温度しきい値に達するどのメッセージにも、メッセージ型 **Alert** が追加されます。

## <a name="edit-the-module-twin"></a>モジュール ツインを編集する

配置マニフェストで PythonModule モジュール ツインを使用して、温度のしきい値を 25 度に設定しました。 モジュール ツインを使用することで、モジュール コードを更新することなく機能を変更できます。

1. Visual Studio Code で、自分の IoT Edge デバイスの詳細を展開し、実行中のモジュールを表示します。

2. **[PythonModule]** を右クリックし、 **[Edit module twin]\(モジュール ツインの編集\)** を選択します。

3. 目的のプロパティの中から **TemperatureThreshold** を見つけます。 その値を、新しい温度 (最後にレポートされた温度より 5 度から 10 度高い温度) に変更します。

4. モジュール ツイン ファイルを保存します。

5. モジュール ツイン編集ウィンドウ内の任意の場所を右クリックして、 **[Update module twin]\(モジュール ツインの更新\)** を選択します。

6. 到着する device-to-cloud メッセージを監視します。 新しい温度しきい値に達するまでメッセージが停止するのを確認できるはずです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

そうでない場合は、課金されないようにするために、ローカル構成と、この記事で使用した Azure リソースを削除できます。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。

次のチュートリアルに進むと、Azure のクラウド サービスをデプロイしてエッジでデータの処理と分析を行ううえで、Azure IoT Edge がどのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)