---
title: Azure IoT Edge C チュートリアル | Microsoft Docs
description: このチュートリアルでは、C コードで IoT Edge モジュールを作成し、Edge デバイスに展開する方法について説明します。
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 31560cbd4d8b4572ce930db7ffb8753f3e4a4bc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425920"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>チュートリアル: C IoT Edge モジュールを開発して、シミュレートされたデバイスに展開する

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。    

> [!div class="checklist"]
> * Visual Studio Code を使用して C の IoT Edge モジュールを作成する
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、コンテナー レジストリに発行する 
> * モジュールを IoT Edge デバイスに展開する
> * 生成されたデータを表示する


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。

クラウド リソース:

* Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* Visual Studio Code 用の [C/C++ 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)。
* Visual Studio Code 用の [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
* [Docker CE](https://docs.docker.com/install/)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

次の Azure CLI コマンドは、**IoTEdgeResources** というリソース グループにレジストリを作成します。 **{acr_name}** は、レジストリの一意の名前に置き換えてください。 

   ```azurecli-interactive
   az acr create --resource-group IoTEdgeResources --name {acr_name} --sku Basic --admin-enabled true
   ```

レジストリの資格情報を取得します。 

   ```azurecli-interactive
   az acr credential show --name {acr_name}
   ```

**ユーザー名**の値といずれかのパスワードをコピーします。 これらの値は、後ほどこのチュートリアルで、レジストリに Docker イメージを発行するときと、Edge ランタイムにレジストリの資格情報を追加するときに使用します。 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、.NET core 2.0 に基づく IoT Edge モジュール プロジェクトを作成する方法を示します。
1. Visual Studio Code で、**[表示]** > **[統合ターミナル]** を選択し、VS Code 統合ターミナルを開きます。
2. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。 
3. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行し、指示に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。
4. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 
   1. ソリューションの作成先フォルダーを選択します。 
   2. ソリューションの名前を指定するか、既定の **EdgeSolution** をそのまま使用します。
   3. モジュール テンプレートとして **C Module** を選択します。 
   4. ご自身のモジュール **CModule** に名前を付けます。 
   5. 前のセクションで作成した Azure コンテナー レジストリを、最初のモジュールのイメージ リポジトリとして指定します。 **localhost:5000** を、コピーしたログイン サーバーの値に置き換えます。 最終的には、**\<registry name\>.azurecr.io/cmodule** のような文字列になります。
 
4. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 **modules** フォルダー、**.vscode** フォルダー、配置マニフェスト テンプレート ファイル、および **.env** ファイルがあります。 既定のモジュール コードは、パイプ モジュールとして実装されます。 

5. JSON 形式のメッセージをフィルター処理するには、C 用の JSON ライブラリをインポートする必要があります。 C モジュールで JSON を解析するための、いずれかの JSON ライブラリを選択するか、独自のライブラリを作成してください。 以下の手順では、[Parson](https://github.com/kgabis/parson) を例として使用しています。
   1. [Parson Github リポジトリ](https://github.com/kgabis/parson)から **parson.c** と **parson.h** をダウンロードします。 この 2 つのファイルをコピーして **CModule** フォルダーに貼り付けます。
   2. **[modules]** > **[CModule]** > **[CMakeLists.txt]** の順に開きます。 parson ライブラリを my_parson としてインポートするために、次の行を追加します。

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. **CMakeLists.txt** の `target_link_libraries` に `my_parson` を追加します。
   4. **[modules]** > **[CModule]** > **[main.c]** の順に開きます。 include セクションの一番下に次のコードを追加して、JSON をサポートするための `parson.h` をインクルードします。

      ```c
      #include "parson.h"
      ```

6. include セクションの後にグローバル変数 `temperatureThreshold` を追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。 

    ```c
    static double temperatureThreshold = 25;
    ```

7. `CreateMessageInstance` 関数全体を次のコードに置き換えます。 この関数によって、コールバックのコンテキストが割り当てられます。 

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. `InputQueue1Callback` 関数全体を次のコードに置き換えます。 この関数は、実際のメッセージング フィルターを実装します。 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n", 
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. `moduleTwinCallback` 関数を追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n", 
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. 関数 `SetupCallbacksForModule` に `moduleTwinCallback` の新しいコールバックを追加します。 `SetupCallbacksForModule` 関数全体を次のコードに置き換えます。

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. このファイルを保存します。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションでは、IoT Edge ソリューションを作成し、CModule にコードを追加しました。これにより、許容されるしきい値をマシンの温度が下回ることを報告するメッセージが除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。 

1. Visual Studio Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。これにより、ご自身のモジュール イメージを ACR にプッシュできます。 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   最初のセクションでご自身の Azure コンテナー レジストリからコピーした、ユーザー名、パスワード、およびログイン サーバーを使用します。 または、Azure portal でご自身のレジストリの **[アクセス キー]** セクションから再度取得します。

2. VS Code エクスプローラーで、IoT Edge ソリューション ワークスペースの **deployment.template.json** ファイルを開きます。 このファイルは、**tempSensor** と **CModule** の 2 つのモジュールを配置するように `$edgeAgent` に指示します。 `CModule.image` 値は、Linux amd64 バージョンのイメージに設定されます。 配置マニフェストの詳細については、[IoT Edge モジュールを使用、構成、および再利用する方法](module-composition.md)に関するページをご覧ください。

3. **deployment.template.json** ファイルには **registryCredentials** セクションがあり、ご自身の Docker レジストリ資格情報が格納されています。 実際のユーザー名とパスワードの組み合わせは、Git で無視される .env ファイルに格納されます。

4. CModule モジュール ツインを配置マニフェストに追加します。 次の JSON コンテンツを `moduleContent` セクションの下部、`$edgeHub` モジュール ツインの後に挿入します。 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. このファイルを保存します。
5. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、**[Build IoT Edge solution]\(IoT Edge ソリューションのビルド\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、新しい **config** フォルダーに `deployment.json` ファイルが生成されます。 その後、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 この 2 つのコマンドによって、ご自身のコードがビルドされ、`CModule.dll` がコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにプッシュされます。 

タグを含む完全なコンテナー イメージ アドレスは、VS Code 統合ターミナルで確認できます。 イメージ アドレスは、**\<リポジトリ\>:\<バージョン\>-\<プラットフォーム\>** の形式で、`module.json` ファイルの情報から作成されます。 このチュートリアルでは、**registryname.azurecr.io/cmodule:0.0.1-amd64** になります。

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

1. Azure IoT ツールキット拡張機能を IoT ハブの接続文字列で構成します。 
    1. **[表示]** > **[エクスプローラー]** の順に選択して、VS Code エクスプローラーを開きます。 
    2. エクスプローラーで、**[AZURE IOT HUB DEVICES]\(Azure IoT Hub デバイス\)**、**[...]** の順にクリックします。**[Select IoT Hub]\(IoT ハブの選択\)** をクリックします。 手順に従って Azure アカウントにログインし、IoT ハブを選択します。 
       **[Set IoT Hub Connection String]\(IoT ハブ接続文字列の設定\)** をクリックして、設定することもできます。 ポップアップ ウィンドウに、IoT Edge デバイスの接続先 IoT ハブの接続文字列を入力します。

2. Azure IoT Hub Device Explorer で、ご自身の IoT Edge デバイスを右クリックし、**[Create Deployment for IoT Edge device]\(IoT Edge デバイスの展開の作成\)** をクリックします。 **config**フォルダーで **deployment.json** ファイルを選択し、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。

3. 更新ボタンをクリックします。 新しい **CModule** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。 

## <a name="view-generated-data"></a>生成されたデータを表示する

1. IoT ハブに到着するデータを監視するには、**[...]** をクリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
2. 特定のデバイスの D2C のメッセージを監視するには、一覧でデバイスを右クリックし、**[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** を選択します。
3. データの監視を停止するには、コマンド パレットで **Azure IoT Hub: Stop monitoring D2C message** コマンドを実行します。 
4. モジュール ツインを表示または更新するには、一覧でモジュールを右クリックし、**[Edit module twin]\(モジュール ツインの編集\)** を選択します。 モジュール ツインを更新するには、ツインの JSON ファイルを保存し、編集領域を右クリックして、**[Update Module Twin]\(モジュール ツインの更新\)** を選択します。
5. Docker ログを表示するには、VS Code 用の [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) をインストールし、ローカルで実行されているモジュールを Docker エクスプローラーで見つけます。 コンテキスト メニューで、**[ログを表示する]** をクリックし、統合ターミナルで表示します。
 
## <a name="clean-up-resources"></a>リソースのクリーンアップ 

次の推奨記事に進む場合は、既に作成したリソースおよび構成を維持して、再利用することができます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

> [!IMPORTANT]
> Azure のリソース グループは、削除すると元に戻すことができません。 いったん削除すると、リソース グループとそこに含まれるすべてのリソースが完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT ハブを、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

IoT ハブだけを削除するには、ハブ名とリソース グループ名を指定して次のコマンドを実行します。

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


名前でリソース グループ全体を削除するには、以下の手順を実行します。

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```



## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[Visual Studio Code 用の Azure IoT Edge で C モジュールを開発する](how-to-develop-c-module.md)方法の詳細をご覧ください。 引き続き次のチュートリアルを実行し、Azure IoT Edge が、エッジでデータをビジネス上の分析情報に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [SQL Server データベースを使用してエッジでデータを格納する](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
