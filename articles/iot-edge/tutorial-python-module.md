---
title: Azure IoT Edge Python モジュール | Microsoft Docs
description: Python コードで IoT Edge モジュールを作成し、エッジ デバイスにデプロイします
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d5bad277e6a54b23f0e3ef7321e82d212ae885d3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Python IoT Edge モジュールを開発して、シミュレートされたデバイスにデプロイする - プレビュー

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 [Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するチュートリアルで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルで学習する内容は次のとおりです。    

> [!div class="checklist"]
> * Visual Studio Code を使用して IoT Edge Python モジュールを作成する
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する 
> * モジュールを IoT Edge デバイスにデプロイする
> * 生成されたデータを表示する


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

> [!IMPORTANT]
> 現在、Python モジュールは、amd64 Linux コンテナーでのみ実行できます。 Windows コンテナーまたは ARM ベースのコンテナーでは実行できません。 

## <a name="prerequisites"></a>前提条件

* クイック スタートまたは最初のチュートリアルで作成した Azure IoT Edge デバイス。
* IoT Edge デバイスの主キー接続文字列。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)  
* [Visual Studio Code 用 の Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。 
* [Docker](https://docs.docker.com/engine/installation/)。Visual Studio Code が含まれるコンピューターに必要です。 このチュートリアルには、Community Edition (CE) で十分です。 
* [Python](https://www.python.org/downloads/)。
* Python パッケージをインストールするための [Pip](https://pip.pypa.io/en/stable/installing/#installation)。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[Azure Container Registry]** の順に選択します。
2. レジストリに名前を付けて、サブスクリプション、リソース グループを選択し、SKU を **[Basic]** に設定します。 
3. **[作成]**を選択します。
4. コンテナー レジストリが作成されたら、そこに移動し、**[アクセス キー]** を選択します。 
5. **[管理者ユーザー]** を **[有効]** に切り替えます。
6. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値を、このチュートリアルで後ほど使用します。 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、IoT Edge Python モジュールを作成する方法を示します。
1. Visual Studio Code で、**[表示]** > **[統合ターミナル]** を選択し、VS Code 統合ターミナルを開きます。
2. 統合ターミナルで、次のコマンドを入力して、**cookiecutter** をインストール (または更新) します。

    ```cmd/sh
    pip install -U cookiecutter
    ```

3. 新しいモジュールのプロジェクトを作成します。 次のコマンドにより、プロジェクト フォルダー **FilterModule** とご自身のコンテナー リポジトリが作成されます。 Azure コンテナー レジストリを使用している場合、`image_repository` のパラメーターの形式は `<your container registry name>.azurecr.io/filtermodule` にする必要があります。 現在の作業フォルダーで次のコマンドを入力します。

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. **[ファイル]** > **[フォルダーを開く]** を選択します。
5. **FilterModule** フォルダーに移動し、**[フォルダーの選択]** をクリックして、VS Code でプロジェクトを開きます。
6. VS Code エクスプローラーで **[main.py]** をクリックして開きます。
7. **[FilterModule]** 名前空間の上部で、`json` ライブラリをインポートします。

    ```python
    import json
    ```

8. グローバル カウンターで `TEMPERATURE_THRESHOLD` と `TWIN_CALLBACKS` を追加します。 温度のしきい値により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

9. 以下のコンテンツで `receive_message_callback` 関数を更新します。

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. 新しい `device_twin_callback` 関数を追加します。 この関数は、必要なプロパティが更新されたときに呼び出されます。

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

11. `HubManager` クラスで、新しい行を `__init__` メソッドに追加して、先ほど追加した `device_twin_callback` 関数を初期化します。

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. このファイルを保存します。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker イメージを作成してレジストリに発行する

1. VS Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Azure コンテナー レジストリを作成したときにコピーしたユーザー名、パスワード、およびログイン サーバーを使用します。

2. VS Code エクスプローラーで、**module.json** ファイルを右クリックし、**[Build and Push IoT Edge module Docker image]\(IoT Edge モジュール Docker イメージをビルドしてプッシュ\)** をクリックします。 VS Code ウィンドウの上部にあるポップアップ ドロップダウン ボックスで、使用しているコンテナー プラットフォームを選択します。たとえば、Linux コンテナーの場合は **[Amd64]** を選択します。 VS Code により `main.py` と必要な依存関係がコンテナーに格納され、指定したコンテナー レジストリにプッシュされます。 イメージをビルドするのが初めての場合は数分かかることがあります。

3. VS Code 統合ターミナルで、タグを含む完全なコンテナー イメージ アドレスを取得できます。 ビルドおよびプッシュ定義の詳細については、`module.json` ファイルを参照してください。

## <a name="add-registry-credentials-to-edge-runtime"></a>Edge ランタイムにレジストリの資格情報を追加する
Edge デバイスを実行しているコンピューターの Edge ランタイムに、レジストリの資格情報を追加します。 この資格情報により、コンテナーをプルするためのアクセス権がランタイムに付与されます。 

- Windows の場合は、次のコマンドを実行します。
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Linux の場合は、次のコマンドを実行します。
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>ソリューションを実行する

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
2. **[IoT Edge (preview)]\(IoT Edge \(プレビュー\)\)** に移動し、IoT Edge デバイスを選びます。
3. **[Set Modules] \(モジュールの設定)** を選択します。 
2. **tempSensor** モジュールが自動的に設定されていることを確認します。 設定されていない場合は、次の手順を使用して追加します。
    1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選びます。
    2. **[名前]** フィールドに「`tempSensor`」と入力します。
    3. **[イメージの URI]** フィールドに「`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`」と入力します。
    4. 他の設定はそのままにして、**[保存]** をクリックします。
9. 前のセクションで作成した **filterModule** モジュールを追加します。 
    1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選びます。
    2. **[名前]** フィールドに「`filterModule`」と入力します。
    3. **[イメージの URI]** フィールドに、イメージ アドレスを入力します (`<your container registry address>/filtermodule:0.0.1-amd64` など)。 完全なイメージ アドレスは、前のセクションから見つけることができます。
    4. **[有効]** ボックスをオンにして、モジュール ツインを編集できるようにします。 
    5. モジュール ツインのテキスト ボックスの JSON を、次の JSON に置き換えます。 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. **[Save]** をクリックします。
10. **[次へ]** をクリックします。
11. **[Specify Routes] \(ルートの指定)** の手順で、下記の JSON をテキスト ボックスにコピーします。 モジュールによって、すべてのメッセージが Edge ランタイムに発行されます。 メッセージが流れる場所は、ランタイム内の宣言型ルールによって定義されます。 このチュートリアルでは、2 つのルートが必要です。 1 つ目のルートは、**FilterMessages** ハンドラーで構成された "input1" エンドポイント経由で、温度センサーからフィルター モジュールにメッセージを転送します。 2 つ目のルートは、フィルター モジュールから IoT ハブにメッセージを転送します。 このルートでは、`upstream` は特別な転送先で、Edge Hub に対して、メッセージを IoT ハブに送信するように指示します。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. **[次へ]** をクリックします。
5. **[Review Template] \(テンプレートのレビュー)** の手順で、**[送信]** をクリックします。 
6. IoT Edge デバイスの詳細ページに戻り、**[更新]** をクリックします。 新しい **filtermodule** が、**tempSensor** モジュールおよび **IoT Edge ランタイム**と一緒に実行されていることが表示されます。 

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスから IoT ハブに送信されたデバイスからクラウドへのメッセージを監視するには:
1. Azure IoT ツールキット拡張機能を IoT ハブの接続文字列で構成します。 
    1. **[表示]** > **[エクスプローラー]** の順に選択して、VS Code エクスプローラーを開きます。 
    3. エクスプローラーで、**[IOT HUB DEVICES]\(IoT ハブ デバイス\)**、**[...]** の順にクリックします。**[Set IoT Hub Connection String]\(IoT ハブ接続文字列の設定\)** をクリックし、ポップアップ ウィンドウに、IoT Edge デバイスの接続先 IoT ハブの接続文字列を入力します。 

        接続文字列を見つけるには、Azure Portal で IoT ハブのタイルをクリックし、**[共有アクセス ポリシー]** をクリックします。 **[共有アクセス ポリシー]** で、**[iothubowner]** ポリシーをクリックし、**[iothubowner]** ウィンドウで IoT ハブ接続文字列をコピーします。   

1. IoT ハブに届くデータを監視するには、**[表示]** > **[コマンド パレット]** の順に選択し、**[IoT: Start monitoring D2C message]\(IoT: D2C メッセージの監視を開始\)** メニュー コマンドを検索します。 
2. データの監視を停止するには、**[IoT: Stop monitoring D2C message] \(IoT: D2C メッセージの監視を停止)** メニュー コマンドを使用します。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 引き続き以下のチュートリアルのいずれかを実行して、エッジでデータをビジネス上の洞察に変えることに Azure IoT Edge がどのように役立つかを学習できます。

> [!div class="nextstepaction"]
> [Azure 関数をモジュールとしてデプロイする](tutorial-deploy-function.md)
> [Azure Stream Analytics をモジュールとしてデプロイする](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
