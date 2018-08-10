---
title: Azure IoT Edge Node.js チュートリアル | Microsoft Docs
description: このチュートリアルでは、Node.js コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6922262856d6fba97349377d5d1b18b75638d88f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436813"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>チュートリアル: Node.js IoT Edge モジュールを開発して、シミュレートされたデバイスに展開する

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 [Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するチュートリアルで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルで学習する内容は次のとおりです。    

> [!div class="checklist"]
> * Visual Studio Code を使用して IoT Edge Node.js モジュールを作成する
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する 
> * モジュールを IoT Edge デバイスにデプロイする
> * 生成されたデータを表示する


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。
* Azure Machine Learning モジュールでは、ARM プロセッサをサポートしていません。

クラウド リソース:

* Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* Visual Studio Code 用の [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [Docker CE](https://docs.docker.com/engine/installation/)。 
* [Node.js および npm](https://nodejs.org)。 この npm パッケージは、Node.js を使用して配布されます。つまり、Node.js をダウンロードすると、お使いのコンピューターに npm が自動的にインストールされます。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[Azure Container Registry]** の順に選択します。
2. レジストリに名前を付けて、サブスクリプション、リソース グループを選択し、SKU を **[Basic]** に設定します。 
3. **作成**を選択します。
4. コンテナー レジストリが作成されたら、そこに移動し、**[アクセス キー]** を選択します。 
5. **[管理者ユーザー]** を **[有効]** に切り替えます。
6. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値を、このチュートリアルで後ほど使用します。 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、IoT Edge Node.js モジュールを作成する方法を示します。

### <a name="create-a-new-solution"></a>新しいソリューションの作成

**npm** を使用して、ソリューションのベースにする Node.js ソリューション テンプレートを作成します。 

1. Visual Studio Code で、**[表示]** > **[統合ターミナル]** を選択し、VS Code 統合ターミナルを開きます。

2. 統合ターミナルで、次のコマンドを入力して、**yeoman** および Node.js Azure IoT Edge モジュールのジェネレーターをインストールします。 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。 

3. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、指示に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。

4. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 

   1. ソリューションの作成先フォルダーを選択します。 
   2. ソリューションの名前を指定するか、既定の **EdgeSolution** をそのまま使用します。
   3. モジュール テンプレートとして **Node.js Module** を選択します。 
   4. ご自身のモジュール **NodeModule** に名前を付けます。 
   5. 前のセクションでご自身の最初のモジュールのイメージ リポジトリとして作成した Azure コンテナー レジストリを指定します。 **localhost:5000** を、コピーしたログイン サーバーの値に置き換えます。 最終的には、**\<registry name\>.azurecr.io/nodemodule** のような文字列になります。
 
VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 **.vscode** フォルダー、**modules** フォルダー、**.env** ファイル、および配置マニフェスト テンプレート ファイルがあります

### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー リポジトリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。 

1. VS Code エクスプローラーで、**.env** ファイルを開きます。 
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。 
3. このファイルを保存します。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

各テンプレートにはサンプル コードが付属しており、シミュレートされたセンサー データが **tempSensor** モジュールから取得され、IoT ハブにルーティングされます。 このセクションでは、メッセージを送信する前に、そのメッセージが NodeModule で分析されるようにするコードを追加します。 

1. VS Code エクスプローラーで、**[モジュール]** > **[NodeModule]** > **[app.js]** を開きます。

5. 温度のしきい値の変数を、必要な Node モジュールの下に追加します。 温度のしきい値により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。

    ```javascript
    var temperatureThreshold = 30;
    ```

6. `PipeMessage` 関数全体を `FilterMessage` 関数に置き換えます。
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. `client.on()` 関数で関数名 `pipeMessage` を `filterMessage` に置き換えます。

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. 次のコード スニペットを、`client.on()` の後、`else` ステートメント内にある `client.open()` 関数コールバックにコピーします。 この関数は、必要なプロパティが更新されたときに呼び出されます。

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. このファイルを保存します。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションでは、IoT Edge ソリューションを作成し、NodeModule にコードを追加しました。これにより、許容されるしきい値をマシンの温度が下回ることを報告するメッセージが除外されます。 次は、ソリューションをコンテナー イメージとしてビルドし、ご自身のコンテナー レジストリにプッシュする必要があります。 

1. Visual Studio Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。これにより、ご自身のモジュール イメージを ACR にプッシュできます。 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   最初のセクションでご自身の Azure コンテナー レジストリからコピーした、ユーザー名、パスワード、およびログイン サーバーを使用します。 または、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから再度取得します。

2. VS Code エクスプローラーで、ご自身の IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。 

   このファイルは、デバイス データをシミュレートする **tempSensor** と、**NodeModule** の 2 つのモジュールを配置するよう `$edgeAgent` に指示します。 `NodeModule.image` 値は、Linux amd64 バージョンのイメージに設定されます。 配置マニフェストの詳細については、[IoT Edge モジュールを使用、構成、および再利用する方法](module-composition.md)に関するページをご覧ください。

   このファイルには、ご自身のレジストリの資格情報も含まれています。 テンプレート ファイルには、プレースホルダーにご自身のユーザー名とパスワードが入力されています。 配置マニフェストを生成すると、フィールドは、**.env** に追加した値で更新されます。 

4. NodeModule モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを `moduleContent` セクションの下部、`$edgeHub` モジュール ツインの後に挿入します。 
    ```json
        "NodeModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```
5. このファイルを保存します。
6. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、**[Build IoT Edge solution]\(IoT Edge ソリューションのビルド\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、新しい **config** フォルダーに `deployment.json` ファイルが生成されます。 その後、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 この 2 つのコマンドによって、ご自身のコードがビルドされ、ご自身の Node.js コードがコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにプッシュされます。 

タグを含む完全なコンテナー イメージ アドレスは、VS Code 統合ターミナルで実行される `docker build` コマンドで確認できます。 イメージ アドレスは、**\<repository\>:\<version\>-\<platform\>** の形式で、`module.json` ファイルの情報から作成されます。 このチュートリアルでは、**registryname.azurecr.io/nodemodule:0.0.1-amd64** になります。

## <a name="deploy-and-run-the-solution"></a>ソリューションを配置して実行する

クイック スタートで行ったように、Azure portal を使用して Node.ms モジュールを IoT Edge デバイスに展開できますが、Visual Studio Code 内からモジュールを配置して、監視することもできます。 以降のセクションでは、前提条件で示されている VS Code の Azure IoT Edge 拡張機能が使用されます。 まだインストールしていない場合は、ここでインストールしてください。 

1. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

2. **Azure: Sign in** コマンドを検索して、実行します。 手順に従って Azure アカウントにサインインします。 

3. コマンド パレットで、**Azure IoT Hub: Select IoT Hub** コマンドを検索して実行します。 

4. 自分の IoT ハブが含まれているサブスクリプションを選択し、アクセス先の IoT ハブを選択します。

5. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

6. ご自身の IoT Edge デバイスの名前を右クリックし、**[Create Deployment for IoT Edge device]\(IoT Edge デバイスの展開の作成\)** を選択します。 

7. NodeModuleを含むソリューション フォルダーに移動します。 **config** フォルダーを開いて、**deployment.json** ファイルを選択します。 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。

8. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを更新します。 新しい **NodeModule** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。 


## <a name="view-generated-data"></a>生成されたデータを表示する

1. IoT ハブに到着するデータを監視するには、**[...]** をクリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
2. 特定のデバイスの D2C のメッセージを監視するには、一覧でデバイスを右クリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
3. データの監視を停止するには、コマンド パレットで **Azure IoT Hub: Stop monitoring D2C message** コマンドを実行します。 
4. モジュール ツインを表示または更新するには、一覧でモジュールを右クリックし、**[Edit module twin]\(モジュール ツインの編集\)** を選択します。 モジュール ツインを更新するには、ツインの JSON ファイルを保存し、編集領域を右クリックして、**[Update Module Twin]\(モジュール ツインの更新\)** を選択します。
5. Docker ログを表示するには、VS Code 用の [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) をインストールし、ローカルで実行されているモジュールを Docker エクスプローラーで見つけます。 コンテキスト メニューで、**[ログを表示する]** をクリックし、統合ターミナルで表示します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

次の推奨記事に進む場合は、既に作成したリソースおよび構成を維持して、再利用することができます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

> [!IMPORTANT]
> Azure リソースとリソース グループは、削除すると元に戻すことができません。 いったん削除すると、リソース グループとそこに含まれるすべてのリソースが完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT ハブを、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

IoT ハブだけを削除するには、ハブ名とリソース グループ名を指定して次のコマンドを実行します。

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


名前でリソース グループ全体を削除するには、以下の手順を実行します。

1. [Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]** をクリックします。

2. **[名前でフィルター処理してください]** ボックスに、IoT ハブが含まれているリソース グループの名前を入力します。 

3. 結果一覧のでリソース グループの **[...]** をクリックし、**[リソース グループの削除]** をクリックします。

4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を再度入力し、**[削除]** をクリックします。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 引き続き以下のチュートリアルのいずれかを実行して、エッジでデータをビジネス上の洞察に変えることに Azure IoT Edge がどのように役立つかを学習できます。

> [!div class="nextstepaction"]
> [Azure 関数をモジュールとしてデプロイする](tutorial-deploy-function.md)
> [Azure Stream Analytics をモジュールとしてデプロイする](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
