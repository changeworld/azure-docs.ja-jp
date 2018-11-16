---
title: Azure IoT Edge の Python モジュールをデバッグする | Microsoft Docs
description: Visual Studio Code を使用して Azure IoT Edge の Python モジュールを開発、ビルド、デバッグする
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0dfe096bb3a2a2116ead2423f53a5e44c8f02630
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567521"
---
# <a name="use-visual-studio-code-to-develop-and-debug-python-modules-for-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge の Python モジュールを開発してデバッグする

ビジネス ロジックを Azure IoT Edge のモジュールにすることができます。 この記事では、主なツールとして Visual Studio Code (VS Code) を使用して Python モジュールを開発してデバッグする方法を説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 また、IoT Edge のセキュリティ デーモンを使った開発マシンで IoT Edge デバイスをシミュレートします。

> [!NOTE]
> このデバッグに関する記事では、モジュール コンテナーのプロセスをアタッチして、これを VS Code でデバッグする方法を示します。 デバッグできるのは、linux-amd64 コンテナー内の Python モジュールだけです。 Visual Studio Code のデバッグ機能をよく知らない場合は、[デバッグ](https://code.visualstudio.com/Docs/editor/debugging)に関するページを確認してください。 

この記事ではメインの開発ツールとして Visual Studio Code を使うため、VS Code をインストールします。 その後、必要な拡張機能を追加します。
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* Visual Studio Code 用 の [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。 
* [Python](https://www.python.org/downloads/)。
* Python パッケージをインストールするための [Pip](https://pip.pypa.io/en/stable/installing/#installation) (通常は、Python のインストールに含まれています)。
* 次のコマンドを使用して、**Cookiecutter** をインストールします。
   
    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

モジュールを作成するには、モジュール イメージを構築する Docker とモジュール イメージを保持するコンテナー レジストリが必要です。
* お使いの開発用マシン上の [Docker Community Edition](https://docs.docker.com/install/)。 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。 

デバイス上でお使いのモジュールをテストするには、1 つ以上の IoT Edge デバイスを使用するアクティブな IoT ハブが必要になります。 コンピューターを IoT Edge デバイスとして使用するには、[Linux](quickstart-linux.md) 用のクイックスタートの手順に従ってください。 

## <a name="create-a-new-solution-template"></a>新しいソリューション テンプレートを作成する

以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、Azure IoT Python SDK に基づく IoT Edge モジュールを作成する方法を示します。 まずはソリューションを作成し、次にそのソリューション内に最初のモジュールを生成します。 各ソリューションには複数のモジュールを含めることができます。 

1. Visual Studio Code で、**[ビュー]** > **[統合端末]** の順に選択します。

2. **[ビュー]** > **[コマンド パレット]** の順に選択します。 

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge Solution** コマンドを入力して実行します。

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

4. 新しいソリューションの作成先フォルダーを参照します。 **[フォルダーの選択]** を選択します。 

5. ソリューションの名前を入力します。 

6. ソリューション内の最初のモジュールのテンプレートとして、**Python モジュール**を選択します。

7. モジュールの名前を入力します。 コンテナー レジストリ内に一意の名前を選択します。 

8. モジュールのイメージ リポジトリの名前を指定します。 VS Code によってモジュール名に **localhost:5000** と自動的に入力されます。 独自のレジストリ情報に置き換えます。 テスト用に Docker のローカル レジストリを使用する場合、**localhost** で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは **\<レジストリ名\>.azurecr.io** のようになります。 文字列の localhost 部分のみを置き換え、モジュール名は削除しないでください。 

   ![Docker イメージ リポジトリを指定する](./media/how-to-develop-c-module/repository.png)

VS Code は指定した情報を取り、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。

ソリューション内には 4 つの項目があります。 
* **.vscode** フォルダーにはデバッグ構成が含まれています。
* **modules** フォルダーにはモジュールごとのサブフォルダーが含まれています。 この時点では 1 つだけです。 ただし、コマンド パレットでコマンド **Azure IoT Edge: Add IoT Edge Module** を実行することで追加できます。 
* **.env** ファイルには環境変数の一覧が表示されます。 レジストリが Azure Container Registry の場合、Azure Container Registry のユーザー名とパスワードがあります。 

   > [!NOTE]
   > 環境ファイルは、モジュールのイメージ リポジトリを指定した場合にのみ作成されます。 localhost の既定値を受け入れてローカルでテストおよびデバッグする場合は、環境変数を宣言する必要はありません。 

* **deployment.template.json** ファイルには新しいモジュールと、テストに使用できるデータをシミュレートする **tempSensor** のサンプル モジュールの一覧が表示されます。 配置マニフェストがどのように機能するかについて詳しくは、「[配置マニフェストを使ってモジュールをデプロイしルートを確立する](module-composition.md)」をご覧ください。 

## <a name="develop-your-module"></a>モジュールの開発

ソリューションに付属する既定の Python モジュール コードは、**modules** > [お使いのモジュール名] > **main.py** にあります。 モジュールと deployment.template.json ファイルが設定されます。これでソリューションをビルドし、それをコンテナー レジストリにプッシュして、デバイスにデプロイすることで、コードを操作することなくテストを開始できます。 モジュールはソース (このケースでは、データをシミュレートする tempSensor モジュール) から入力を取得して、IoT Hub に送信するようにビルドされています。 

Python テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。 

## <a name="build-and-deploy-your-module-for-debugging"></a>デバッグ用にモジュールをビルドしてデプロイする

各モジュール フォルダーには、コンテナー タイプが異なるいくつかの Docker ファイルが含まれています。 テスト用のモジュールのビルドには、拡張子が **.debug** で終わるファイルであればどれでも使用できます。 現時点では、Python のモジュールは Linux amd64 コンテナーのみのデバッグをサポートします。 

1. VS Code で、`deployment.template.json` ファイルに移動します。 末尾に **.debug** を追加し、モジュール画像の URL を更新します。

2. **deployment.template.json** 内の Python モジュールの createOptions を以下のコンテンツに置き換え、このファイルを保存します。 
    
    ```json
    "createOptions": "{\"ExposedPorts\":{\"5678/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"5678/tcp\":[{\"HostPort\":\"5678\"}]}}}"
    ```

3. `main.py` に移動し、import セクションの後に次のコードを追加します。
    
    ```python
    import ptvsd
    ptvsd.enable_attach(('0.0.0.0',  5678))
    ```

4. デバッグするコールバックに次の 1 行のコードを追加します。

    ```python
    ptvsd.break_into_debugger()
    ```

   たとえば、`receive_message_callback` メソッドをデバッグする場合、 次のように 1 行のコードを挿入します。

    ```python
    def receive_message_callback(message, hubManager):
        ptvsd.break_into_debugger()
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

2. VS Code コマンド パレットで、**Azure IoT Edge: Build and Push IoT Edge solution** コマンドを入力して実行します。
3. コマンド パレットからお使いのソリューションの `deployment.template.json` ファイルを選択します。 
4. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックします。 次に、**[Create deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。 
5. ソリューションの **config** フォルダーを開きます。 `deployment.json` ファイルを選択します。 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** を選択します。 

VS Code 統合ターミナルで、デプロイが正常に作成されていることを配置 ID によって確認できます。

VS Code Docker エクスプローラーを使用して、または端末で `docker ps` コマンドを実行して、コンテナーの状態を確認します。

## <a name="start-debugging-python-module-in-vs-code"></a>VS Code で Python モジュールのデバッグを開始する
VS Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルにデバッグ構成情報が保管されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成するときに生成されたものです。 デバッグに対応している新しいモジュールを追加するたびに更新されます。 

1. VS Code デバッグ ビューに移動します。 モジュールのデバッグ構成ファイルを選択します。 デバッグ オプション名は、**<モジュール名> リモート デバッグ (Python)** のようになります。

2. `main.c` に移動します。 `ptvsd.break_into_debugger()` を配置したコールバック メソッドにブレークポイントを追加します。

3. **[デバッグの開始]** を選択するか、**F5** を押します。 アタッチするプロセスを選択します。

4. VS Code のデバッグ ビューの左側のパネルに変数が表示されます。 

前の例は、コンテナー上の Python IoT Edge モジュールをデバッグする方法を示しています。 モジュール コンテナーの createOptions 内に公開ポートを追加しました。 Python モジュールのデバッグを終了したら、稼働準備済みの IoT Edge モジュールではこれらの公開ポートを削除することをお勧めします。

## <a name="next-steps"></a>次の手順

モジュールをビルドしたら、[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)方法を確認してください。

お使いの IoT Edge デバイス用のモジュールを開発する方法について詳しくは、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」をご覧ください。
