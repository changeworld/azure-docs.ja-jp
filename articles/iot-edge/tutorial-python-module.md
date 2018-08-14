---
title: Azure IoT Edge Python チュートリアル | Microsoft Docs
description: このチュートリアルでは、Python コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します。
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5766f9708d2439f42f9ad77169fd1fe7f7dc451e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439114"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>チュートリアル: Python IoT Edge モジュールを開発して、シミュレートされたデバイスに展開する

Azure IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 [Windows][lnk-quickstart-win] または [Linux][lnk-quickstart-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するクイック スタートで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルで学習する内容は次のとおりです。    

> [!div class="checklist"]
> * Visual Studio Code を使用して IoT Edge Python モジュールを作成する。
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する。
> * モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux](quickstart-linux.md) のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。
* IoT Edge 用 Python モジュールは、ARM プロセッサと Windows デバイスのいずれにも対応していません。

クラウド リソース:

* Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* Visual Studio Code 用の [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
* Visual Studio Code 用 の [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。 
* [Docker CE](https://docs.docker.com/engine/installation/)。 
* [Python](https://www.python.org/downloads/)。
* Python パッケージをインストールするための [Pip](https://pip.pypa.io/en/stable/installing/#installation) (通常は、Python のインストールに含まれています)。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[Azure Container Registry]** の順に選択します。
2. レジストリに名前を付けて、サブスクリプション、リソース グループを選択し、SKU を **[Basic]** に設定します。 
3. **作成**を選択します。
4. コンテナー レジストリが作成されたら、その場所を参照し、**[アクセス キー]** を選択します。 
5. **[管理者ユーザー]** を **[有効]** に切り替えます。
6. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値を、このチュートリアルで後ほど使用します。 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、IoT Edge Python モジュールを作成します。

### <a name="create-a-new-solution"></a>新しいソリューションの作成

Python パッケージ **cookiecutter** を使用して、ソリューションのベースにする Python ソリューション テンプレートを作成します。 

1. Visual Studio Code で、**[表示]** > **[統合ターミナル]** を選択し、VS Code 統合ターミナルを開きます。

2. 統合ターミナルで、次のコマンドを入力して、**cookiecutter** をインストール (または更新) します。これは、VS Code で IoT Edge ソリューション テンプレートを作成するときに使用します。

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。 

4. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、指示に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。

5. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 

   1. ソリューションの作成先フォルダーを選択します。 
   2. ソリューションの名前を指定するか、既定の **EdgeSolution** をそのまま使用します。
   3. モジュール テンプレートとして **Python Module** を選択します。 
   4. ご自身のモジュール **PythonModule** に名前を付けます。 
   5. 前のセクションで作成した Azure Container Registry を、最初のモジュールのイメージ リポジトリとして指定します。 **localhost:5000** を、コピーしたログイン サーバーの値に置き換えます。 最終的には、\<registry name\>.azurecr.io/pythonmodule のような文字列になります。
 
VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペース (modules フォルダー、配置マニフェスト テンプレート ファイル、\.env ファイル) が読み込まれます。 

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー リポジトリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 

1. VS Code エクスプローラーで、.env ファイルを開きます。 
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。 
3. このファイルを保存します。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

各テンプレートにはサンプル コードが付属しており、シミュレートされたセンサー データが **tempSensor** モジュールから取得され、IoT Hub にルーティングされます。 このセクションでは、メッセージを送信する前に、**PythonModule** を展開してそのメッセージを分析するコードを追加します。 

1. VS Code エクスプローラーで、**[モジュール]** > **[PythonModule]** > **[main.py]** を開きます。

2. **main.py** ファイルの上部で、**json** ライブラリをインポートします。

    ```python
    import json
    ```

3. グローバル カウンターで **TEMPERATURE_THRESHOLD** 変数と **TWIN_CALLBACKS** 変数を追加します。 温度のしきい値により、データが IoT Hub に送信される基準値が設定されます。データは、マシンの測定温度がこの値を超えると送信されます。

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. **receive_message_callback** 関数を次のコードに置き換えます。

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
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

5. **module_twin_callback** という新しい関数を追加します。 この関数は、必要なプロパティが更新されたときに呼び出されます。

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
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

6. **HubManager** クラスで、新しい行を **__init__** メソッドに追加して、先ほど追加した **module_twin_callback** 関数を初期化します。

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. このファイルを保存します。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションでは、IoT Edge ソリューションを作成し、**PythonModule** にコードを追加しました。これにより、許容されるしきい値をマシンの温度が下回ることを報告するメッセージが除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。 

1. Visual Studio Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。 これで、必要なモジュール イメージを Azure Container Registry にプッシュすることができます。 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   最初のセクションで Azure Container Registry からコピーしたユーザー名、パスワード、ログイン サーバーを使用します。 または、Azure portal でご自身のレジストリの **[アクセス キー]** セクションからこれらの値を取得することもできます。

2. VS Code エクスプローラーで、IoT Edge ソリューション ワークスペースの deployment.template.json ファイルを開きます。 

   このファイルは、デバイス データをシミュレートする **tempSensor** と、**PythonModule** の 2 つのモジュールを配置するよう **$edgeAgent** に指示します。 **PythonModule.image** 値は、Linux amd64 バージョンのイメージに設定されます。 配置マニフェストの詳細については、[IoT Edge モジュールを使用、構成、再利用する方法の確認](module-composition.md)に関するページをご覧ください。

   このファイルには、ご自身のレジストリの資格情報も含まれています。 テンプレート ファイルには、プレースホルダーにご自身のユーザー名とパスワードが入力されています。 配置マニフェストを生成すると、フィールドは、.env ファイルに追加した値で更新されます。 

3. **PythonModule** モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを **moduleContent** セクションの下部、**$edgeHub** モジュール ツインの後に挿入します。 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. このファイルを保存します。

5. VS Code エクスプローラーで、deployment.template.json ファイルを右クリックし、**[Build IoT Edge solution]\(IoT Edge ソリューションのビルド\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 この 2 つのコマンドによって、ご自身のコードがビルドされ、Python コードがコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

タグを含む完全なコンテナー イメージ アドレスは、VS Code 統合ターミナルで実行される `docker build` コマンドで確認できます。 イメージ アドレスは、\<リポジトリ\>:\<バージョン\>-\<プラットフォーム\> の形式で、module.json ファイルの情報から作成されます。 このチュートリアルでは、registryname.azurecr.io/pythonmodule:0.0.1-amd64 になります。

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

クイック スタートで行ったように、Azure portal を使用して Python モジュールを IoT Edge デバイスに展開できます。 また、Visual Studio Code 内からモジュールを配置して、監視することもできます。 以降のセクションでは、前提条件で示された VS Code 用の Azure IoT Edge 拡張機能が使用されます。 この拡張機能をまだインストールしていない場合は、ここでインストールしてください。 

1. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

2. **Azure: Sign in** コマンドを検索して、実行します。 手順に従って Azure アカウントにサインインします。 

3. コマンド パレットで、**Azure IoT Hub: Select IoT Hub** コマンドを検索して実行します。 

4. 自分の IoT Hub が含まれているサブスクリプションを選択し、アクセス先の IoT Hub を選択します。

5. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

6. IoT Edge デバイスの名前を右クリックして、**[Create Deployment for IoT Edge device]\(IoT Edge デバイスの配置の作成\)** を選択します。 

7. **PythonModule** を含むソリューション フォルダーの場所を参照します。 config フォルダーを開き、deployment.json ファイルを選択して、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** を選択します。

8. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを更新します。 新しい **PythonModule** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。 

## <a name="view-generated-data"></a>生成されたデータを表示する

1. IoT Hub に到着するデータを監視するには、省略記号 (**...**) を選択し、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
2. 特定のデバイスの D2C のメッセージを監視するには、一覧でデバイスを右クリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
3. データの監視を停止するには、コマンド パレットで **Azure IoT Hub: Stop monitoring D2C message** コマンドを実行します。 
4. モジュール ツインを表示または更新するには、一覧でモジュールを右クリックし、**[Edit module twin]\(モジュール ツインの編集\)** を選択します。 モジュール ツインを更新するには、ツインの JSON ファイルを保存し、編集領域を右クリックして、**[Update Module Twin]\(モジュール ツインの更新\)** を選択します。
5. Docker のログを表示するために、[Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code をインストールします。 ローカルで実行されているモジュールを Docker エクスプローラーで確認できます。 コンテキスト メニューで、**[ログを表示する]** をクリックし、統合ターミナルで表示します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

> [!IMPORTANT]
> Azure のリソースとリソース グループは、削除すると元に戻すことができません。 これらの項目を削除すると、リソース グループとそこに含まれるすべてのリソースが完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT Hub を、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

IoT Hub だけを削除するには、ハブ名とリソース グループ名を指定して次のコマンドを実行します。

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


名前でリソース グループ全体を削除するには、以下の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインし、**[リソース グループ]** を選択します。

2. **[名前でフィルター処理してください]** ボックスに、IoT Hub が含まれているリソース グループの名前を入力します。 

3. 結果一覧で、目的のリソース グループの右側にある省略記号 (**[...]**) を選択し、**[リソース グループの削除]** を選択します。

4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を再入力し、**[削除]** を選択します。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 引き続き次のチュートリアルに進み、Azure IoT Edge が、エッジでデータをビジネス上の分析情報に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Azure 関数をモジュールとしてデプロイする](tutorial-deploy-function.md)
> [Azure Stream Analytics をモジュールとしてデプロイする](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
