---
title: クイックスタート - シミュレートされた対称キー デバイスを Microsoft Azure IoT Hub にプロビジョニングする
description: Azure IoT Hub Device Provisioning Service (DPS) で、対称キーを使用して認証するデバイスをプロビジョニングする方法について学習する
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: 43c6dc8e9d8a6438468c44fd2b8cc31d04a92c2f
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276855"
---
# <a name="quickstart-provision-a-simulated-symmetric-key-device"></a>クイックスタート: シミュレートされた対称キー デバイスをプロビジョニングする

このクイックスタートでは、シミュレートされたデバイスを Windows コンピューターに作成します。 シミュレートされたデバイスは、認証のために [対称キーの構成証明](concepts-symmetric-key-attestation.md)メカニズムを使用するように構成されます。 デバイスを構成した後、Azure IoT Hub Device Provisioning Service を使用して IoTハブにプロビジョニングします。

プロビジョニングのプロセスに慣れていない場合は、[プロビジョニング](about-iot-dps.md#provisioning-process)の概要を確認してください。

このクイックスタートでは、Windows ベース ワークステーションのソリューションについて説明します。 しかし、Linux でこの手順を行うこともできます。 Linux の例については、「[How to provision for multitenancy](how-to-provision-multitenant.md)」(マルチテナント方式のプロビジョニング) を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

* [Azure portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順を完了します。
::: zone pivot="programming-language-ansi-c"

* Windows 開発環境を使用する場合は、["C++ によるデスクトップ開発"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) のワークロードが有効になっている [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 をインストールします。 Visual Studio 2015 と Visual Studio 2017 もサポートされています。 Linux または macOS については、SDK ドキュメントの「[開発環境を準備する](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」の該当するセクションを参照してください。

::: zone-end

::: zone pivot="programming-language-csharp"

* Windows ベースのコンピューターに [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download) 以降をインストールします。 次のコマンドを使用してバージョンを確認してください。

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* [Node.js v4.0 以降](https://nodejs.org)をインストールします。

::: zone-end

::: zone pivot="programming-language-python"

* Windows ベースのコンピューターに [Python 3.7](https://www.python.org/downloads/) 以降をインストールします。 `python --version` を実行して、使用している Python のバージョンを確認できます。

::: zone-end

::: zone pivot="programming-language-java"

* コンピューターに [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) 以降をインストールします。

* [Maven](https://maven.apache.org/install.html) をダウンロードし、インストールします。

::: zone-end

* 最新バージョンの [Git](https://git-scm.com/download/) をインストールします。 コマンド ウィンドウからアクセスできる環境変数に Git が追加されていることを確認します。 *Git Bash* (ローカル Git リポジトリとやりとりする際に使用できるコマンドライン アプリ) を含む、インストールする `git` ツールの最新バージョンについては、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページを参照してください。


<a id="setupdevbox"></a>

## <a name="prepare-your-development-environment"></a>開発環境を準備する

::: zone pivot="programming-language-ansi-c"

このセクションでは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のビルドに使用する開発環境を準備します。 このサンプル コードでは、デバイスのブート シーケンス中にデバイスのプロビジョニングを試みます。

1. 最新の [CMake ビルド システム](https://cmake.org/download/)をダウンロードします。

    >[!IMPORTANT]
    >`CMake` のインストールを開始する **前に**、Visual Studio の前提条件 (Visual Studio と "C++ によるデスクトップ開発" ワークロード) がコンピューターにインストールされていることを確認します。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。 また、以前のバージョンの CMake ビルド システムでは、この記事で使用されているソリューション ファイルを生成できないことに注意してください。 必ず最新バージョンの CMake を使用してください。

2. Web ブラウザーを開き、[Azure IoT C SDK のリリース ページ](https://github.com/Azure/azure-iot-sdk-c/releases/latest)に移動します。

3. そのページの上部にある **[タグ]** タブを選択します。

4. 最新リリースの Azure IoT C SDK のタグ名をコピーします。

5. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、最新リリースの [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub リポジトリをクローンします (`<release-tag>` は、前の手順でコピーしたタグに置き換えます)。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    この操作が完了するまで数分かかる場合があります。

6. 操作が完了したら、`azure-iot-sdk-c` ディレクトリから次のコマンドを実行します。

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

7. このコード サンプルでは、対称キーを使用して構成証明を指定します。 次のコマンドを実行して、デバイス プロビジョニング クライアントを含む開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    >[!TIP]
    >`cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生する可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)でコマンドを実行してください。

8. ビルドが正常に完了すると、最後のいくつかの出力行は次の出力のようになります。

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 16 2019
    -- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19042.
    -- The C compiler identification is MSVC 19.29.30040.0
    -- The CXX compiler identification is MSVC 19.29.30040.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Git CMD または Git Bash コマンド ライン環境を開きます。

2. 次のコマンドを使用して、[C# 用 Azure IoT サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp)の GitHub リポジトリをクローンします。

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Git CMD または Git Bash コマンド ライン環境を開きます。

2. 次のコマンドを使用して、[Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node.git) の GitHub リポジトリをクローンします。

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Git CMD または Git Bash コマンド ライン環境を開きます。

2. 次のコマンドを使用して、[Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python.git) の GitHub リポジトリをクローンします。

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

3. サンプル ファイルの _provision_symmetric_key.py_ がある `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` ディレクトリに移動します。

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. 次のコマンドを実行して、_azure-iot-device_ ライブラリをインストールします。

    ```console
    pip install azure-iot-device
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Git CMD または Git Bash コマンド ライン環境を開きます。

2. 次のコマンドを使用して、[Azure IoT SDK for Java](https://github.com/Azure/azure-iot-sdk-java.git) の GitHub リポジトリをクローンします。

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. ルート `azure-iot-sdk-java` ディレクトリに移動し、プロジェクトをビルドして必要なパッケージをすべてダウンロードします。

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. 次のコマンドを実行して、_azure-iot-device_ ライブラリをインストールします。

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

::: zone-end

## <a name="create-a-device-enrollment"></a>デバイス登録を作成する

Azure IoT Device Provisioning Service では、次の 2 種類の登録がサポートされています。

* [登録グループ](concepts-service.md#enrollment-group)：複数の関連するデバイスを登録するために使用します。
* [個別登録](concepts-service.md#individual-enrollment): 単一デバイスを登録するために使用します。

この記事では、IoT ハブに単一のデバイスをプロビジョニングする個別登録のデモを行います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. ご利用の Device Provisioning Service を選択します。

4. **[設定]** メニューで、 **[登録の管理]** を選択します。

5. ページの上部にある **[+ 個別登録の追加]** を選択します。

6. **[登録の追加]** ページで、次の情報を入力します。

   * **メカニズム:** ID 構成証明のメカニズムとして *[対称キー]* を選択します。

   * **[キーの自動生成]**: このボックスをオンにします。

   * **登録 ID**: 登録を識別する登録 ID を入力します。 小文字の英字、数字、ダッシュ ('-') 文字のみを使用します。 たとえば、*symm-key-device-007* のようにします。

   * **IoT Hub のデバイス ID:** デバイス識別子を入力します。

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png" alt-text="デバイスの登録情報を入力する。":::

    ::: zone-end

    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-csharp.png" alt-text="C# デバイスの登録情報を入力する。":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-nodejs.png" alt-text="Node.js デバイスの登録情報を入力する。":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-python.png" alt-text="Python デバイスの登録情報を入力する。":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-java.png" alt-text="Java デバイスの登録情報を入力する。":::

    ::: zone-end

7. **[保存]** を選択します。 **主キー** と **2 次キー** が生成され、登録エントリに追加されて、 **[登録の管理]** ページに戻ります。

8. シミュレートされた対称キー デバイスの登録を表示するには、 **[個別登録]** タブを選択します。

9. ご利用のデバイス (*symm-key-device-007*) を選択します。

10. 生成された **主キー** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/copy-device-enrollment-primary-key.png" alt-text="デバイス登録の主キーをコピーする":::

<a id="firstbootsequence"></a>

## <a name="prepare-and-run-the-device-provisioning-code"></a>デバイス プロビジョニング コードを準備して実行する

::: zone pivot="programming-language-ansi-c"

このセクションでは、デバイスのブート シーケンスを Device Provisioning Service インスタンスに送信するようにデバイス サンプル コードを更新します。 このブート シーケンスにより、デバイスが認識され、認証されて、Device Provisioning Service インスタンスにリンクされた IoT ハブに割り当てられます。

サンプル プロビジョニング コードでは、次のタスクを順に実行します。

1. 次の 3 つのパラメーターを使用して、Device Provisioning リソースでデバイスを認証します。

    * Device Provisioning Service の ID スコープ
    * デバイス登録の登録 ID。
    * デバイス登録の主対称キー。

2. Device Provisioning Service インスタンスに既にリンクされている IoT ハブにデバイスを割り当てます。

デバイス情報を使用してプロビジョニング サンプルを更新して実行するには、次のようにします。

1. Device Provisioning Service のメイン メニューで、 **[概要]** を選択します。

2. **[ID スコープ]** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Device Provisioning Service のエンドポイント情報を抽出する":::

3. CMake を実行して生成された *azure_iot_sdks.sln* ソリューション ファイルを Visual Studio で開きます。 ソリューション ファイルは次の場所にあります。

    ```output

    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln

    ```

    >[!TIP]
    >cmake ディレクトリにファイルが生成されていない場合は、最新バージョンの CMake ビルド システムを使用していることを確認してください。

4. Visual Studio の *[ソリューション エクスプローラー]* ウィンドウで、**Provision\_Samples** フォルダーに移動します。 **prov\_dev\_client\_sample** という名前のサンプル プロジェクトを展開します。 **Source Files** を展開し、**prov\_dev\_client\_sample.c** を開きます。

5. 定数 `id_scope` を探し、以前にコピーした **ID スコープ** の値で置き換えます。

    ```c
    static const char* id_scope = "0ne00002193";
    ```

6. 同じファイル内で `main()` 関数の定義を探します。 以下に示すように `hsm_type` 変数が `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` に設定されていることを確認します。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

7. **prov\_dev\_client\_sample.c** で、コメントになっている `prov_dev_set_symmetric_key_info()` の呼び出しを見つけます。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    関数呼び出しのコメントを解除し、プレースホルダーの値 (山かっこを含む) を、自分の登録 ID と、先ほどコピーした主キーの値に置き換えます。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```

8. ファイルを保存します。

9. **prov\_dev\_client\_sample** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

10. Visual Studio のメニューで **[デバッグ]** > **[デバッグなしで開始]** の順に選択して、ソリューションを実行します。 プロジェクトをリビルドするよう求められたら、 **[はい]** を選択して、プロジェクトをリビルドしてから実行します。

    次の出力は、プロビジョニング サービス インスタンスに正常に接続して IoT ハブに割り当てられているデバイスの例です。

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

サンプル プロビジョニング コードでは次のタスクを実行します。

1. 次の 3 つのパラメーターを使用して、Device Provisioning リソースでデバイスを認証します。

    * Device Provisioning Service の ID スコープ
    * デバイス登録の登録 ID。
    * デバイス登録の主対称キー。

2. Device Provisioning Service インスタンスに既にリンクされている IoT ハブにデバイスを割り当てます。

3. テスト テレメトリ メッセージを IoT ハブに送信します。

デバイス情報を使用してプロビジョニング サンプルを更新して実行するには、次のようにします。

1. Device Provisioning Service のメイン メニューで、 **[概要]** を選択します。

2. **[ID スコープ]** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Device Provisioning Service のエンドポイント情報を抽出する":::

3. コマンド プロンプトを開き、クローンしたサンプル リポジトリ内の *SymmetricKeySample* に移動します。

    ```cmd
    cd azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample
    ```

4. *SymmetricKeySample* フォルダーの *Parameters.cs* をテキスト エディターで開きます。 このファイルには、サンプルでサポートされているパラメーターが示されます。 この記事では、サンプルを実行するときに、最初の 3 つの必須パラメーターのみを使用します。 このファイルのコードを確認します。 変更は不要です。

    | パラメーター                         | 必須 | 説明     |
    | :-------------------------------- | :------- | :-------------- |
    | `--s` または `--IdScope`              | ○     | DPS インスタンスの ID スコープ |
    | `--i` または `--Id`                   | ○     | 個別登録を使用する場合は登録 ID、グループ登録を使用する場合は目的のデバイス ID。 |
    | `--p` または `--PrimaryKey`           | ○     | 個別登録またはグループ登録の主キー。 |
    | `--e` または `--EnrollmentType`       | ×    | 登録の種類: `Individual` または `Group`。 既定値は `Individual` です |
    | `--g` または `--GlobalDeviceEndpoint` | ×    | デバイスの接続先となるグローバル エンドポイント。 既定値は `global.azure-devices-provisioning.net` です |
    | `--t` または `--TransportType`        | ×    | デバイス プロビジョニング インスタンスとの通信に使用するトランスポート。 既定値は `Mqtt` です。 指定できる値は、`Mqtt`、`Mqtt_WebSocket_Only`、`Mqtt_Tcp_Only`、`Amqp`、`Amqp_WebSocket_Only`、`Amqp_Tcp_only`、および `Http1` です。|

5. *SymmetricKeySample* フォルダーの *ProvisioningDeviceClientSample.cs* をテキスト エディターで開きます。 このファイルには、[SecurityProviderSymmetricKey](/dotnet/api/microsoft.azure.devices.shared.securityprovidersymmetrickey?view=azure-dotnet&preserve-view=true) クラスを [ProvisioningDeviceClient](/dotnet/api/microsoft.azure.devices.provisioning.client.provisioningdeviceclient?view=azure-dotnet&preserve-view=true) クラスと共に使用して、シミュレートされた対称キー デバイスをプロビジョニングする方法が示されます。 このファイルのコードを確認します。  変更は不要です。

6. 3 つのサンプル パラメーターを置き換えた後 (`<id-scope>` は Device Provisioning Service ID スコープの ID スコープに、`<registration-id>` はデバイスの登録 IDに、`<primarykey>` はデバイスの主キーに置き換えます)、次のコマンドを使用してサンプル コードをビルドして実行します。

    ```console
    dotnet run --s <id-scope> --i <registration-id> --p <primarykey>
    ```

7. これで次の出力のようになるはずです。 テスト メッセージとして "TestMessage" という文字列がハブに送信されます。

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

サンプル プロビジョニング コードでは、次のタスクを順に実行します。

1. 次の 4 つのパラメーターを使用して、Device Provisioning リソースでデバイスを認証します。
    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Device Provisioning Service インスタンスに既にリンクされている IoT ハブにデバイスを割り当てます。

3. テスト テレメトリ メッセージを IoT ハブに送信します。

デバイス情報を使用してプロビジョニング サンプルを更新して実行するには、次のようにします。

1. Device Provisioning Service のメイン メニューで、 **[概要]** を選択します。

2. **[ID スコープ]** と **[サービス エンドポイント]** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Device Provisioning Service のエンドポイント情報を抽出する":::

3. Node.js コマンドを実行するためのコマンド プロンプトを開き、次のディレクトリに移動します。

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

4. *provisioning/device/samples* フォルダーの *register_symkey.js* を開いて、コードを確認します。 サンプル コードでカスタム ペイロードが設定されていることに注目してください。

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    このコードは、このクイックスタートでは必要ないため、コメント アウトできます。 カスタム ペイロードは、カスタム割り当て関数を使用して IoT ハブにデバイスを割り当てる場合に必要になります。 詳細については、[カスタム割り当てポリシーの使用に関するチュートリアル](tutorial-custom-allocation-policies.md)を参照してください。

     `provisioningClient.register()` は、デバイスの登録を試行するメソッドです。

    これ以上の変更は必要ありません。

5. 次は、コマンド プロンプトで、環境変数を設定します (`<id-scope>` は Device Provisioning Service ID スコープの ID スコープに、`<registration-id>` はデバイスの登録 ID に、`<primarykey>` はデバイスの主キーに、`<provisioning-host>` は Device Provisioning Service のサービス エンドポイント URL に置き換えます)。

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

6. 次のコマンドを使用して、サンプル コードをビルドし、実行します。

   ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

7. これで次の出力のようになるはずです。 テスト メッセージとして "Hello World" という文字列がハブに送信されます。

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-python"

サンプル プロビジョニング コードでは、次のタスクを順に実行します。

1. 次の 4 つのパラメーターを使用して、Device Provisioning リソースでデバイスを認証します。

    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Device Provisioning Service インスタンスに既にリンクされている IoT ハブにデバイスを割り当てます。

3. テスト テレメトリ メッセージを IoT ハブに送信します。

デバイス情報を使用してプロビジョニング サンプルを更新して実行するには、次のようにします。

1. Device Provisioning Service のメイン メニューで、 **[概要]** を選択します。

2. **[ID スコープ]** と **[サービス エンドポイント]** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Device Provisioning Service のエンドポイント情報を抽出する":::

3. 次は、コマンド プロンプトで、環境変数を設定します (`<id-scope>` は Device Provisioning Service ID スコープの ID スコープに、`<registration-id>` はデバイスの登録 ID に、`<primarykey>` はデバイスの主キーに、`<provisioning-host>` は Device Provisioning Service のサービス エンドポイント URL に置き換えます)。

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

4. コマンド プロンプト ウィンドウで、次のディレクトリに移動します。

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

5. *_provision_symmetric_key.py_* の python サンプル コードを実行します。

    ```console
    python provision_symmetric_key.py
    ```

6. これで次の出力のようになるはずです。 一部の風速テレメトリ メッセージも、テストとしてハブに送信されます。

     ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```

::: zone-end

::: zone pivot="programming-language-java"

サンプル プロビジョニング コードでは、次のタスクを順に実行します。

1. 次の 4 つのパラメーターを使用して、Device Provisioning リソースでデバイスを認証します。

    * `GLOBAL_ENDPOINT`
    * `SCOPE_ID`
    * `REGISTRATION_ID`
    * `SYMMETRIC_KEY`

2. Device Provisioning Service インスタンスに既にリンクされている IoT ハブにデバイスを割り当てます。

3. テスト テレメトリ メッセージを IoT ハブに送信します。

デバイス情報を使用してプロビジョニング サンプルを更新して実行するには、次のようにします。

1. Device Provisioning Service のメイン メニューで、 **[概要]** を選択します。

2. **[ID スコープ]** と **[サービス エンドポイント]** の値をコピーします。 これらはそれぞれ `SCOPE_ID` と `GLOBAL_ENDPOINT` です。

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Device Provisioning Service のエンドポイント情報を抽出する":::

3. Java デバイス サンプル コードを開いて編集します。 デバイスのサンプル コードの完全パスは次のとおりです。

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

4. DPS およびデバイス登録の次の変数の値を置き換えます (`<id-scope>` は Device Provisioning Service ID スコープの ID スコープに、`<registration-id>`はデバイスの登録 ID に、`<primarykey>` はデバイスの主キーに、`<provisioning-host>` は Device Provisioning Service のサービス エンドポイント URL に置き換えます)。

    ```java
    private static final String SCOPE_ID = "<id-scope>";
    private static final String GLOBAL_ENDPOINT = "<provisioning-host>";
    private static final String SYMMETRIC_KEY = "<primarykey>";
    private static final String REGISTRATION_ID = "<registration-id>";
    ```

5. ビルドを実行するために、コマンド プロンプトを開きます。 Java SDK リポジトリのプロビジョニング サンプル プロジェクト フォルダーに移動します。

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

6. サンプルをビルドしてから、`target` フォルダーに移動して、作成した `.jar` ファイルを実行します。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

7. これで次の出力のようになるはずです。

     ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>デバイス プロビジョニングの登録を確認する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスが割り当てられた IoT ハブを選択します。

4. **[エクスプローラー]** メニューで、 **[IoT デバイス]** を選択します。

5. デバイスが正常にプロビジョニングされた場合は、デバイス ID が一覧に表示され、 **[状態]** が *[有効]* として設定されているはずです。 デバイスが表示されていない場合は、ページの上部にある **[最新の情報に更新]** を選択します。

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration.png" alt-text="IoT ハブに登録されたデバイス":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-csharp.png" alt-text="CSharp デバイスが IoT ハブに登録されている":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-nodejs.png" alt-text="Node.js デバイスが IoT ハブに登録されている":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-python.png" alt-text="Python デバイスが IoT ハブに登録されている":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-java.png" alt-text="Java デバイスが IoT ハブに登録されている":::

    ::: zone-end

> [!NOTE]
> *[Initial device twin state]\(初期のデバイス ツインの状態\)* をデバイスの登録エントリの既定値から変更した場合、デバイスはハブから目的のツインの状態をプルし、それに従って動作することができます。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。
>

## <a name="clean-up-resources"></a>リソースをクリーンアップする

デバイス クライアント サンプルでの作業と確認を続行する予定の場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 続行する予定がない場合は、次の手順を使用して、このクイックスタートで作成したすべてのリソースを削除してください。

### <a name="delete-your-device-enrollment"></a>デバイスの登録を削除する

1. マシンに表示されているデバイス クライアント サンプルの出力ウィンドウを閉じます。

2. Azure portal の左側のメニューから、 **[すべてのリソース]** を選択します。

3. ご利用の Device Provisioning Service を選択します。

4. **[設定]** メニューで、 **[登録の管理]** を選択します。

5. **[個別登録]** タブを選択します。

6. このクイックスタートで登録したデバイスの ''*登録 ID*'' の横にあるチェック ボックスをオンにします。

7. ページの上部にある **[削除]** を選択します。

### <a name="delete-your-device-registration-from-iot-hub"></a>IoT Hub からデバイスの登録を削除する

1. Azure portal の左側のメニューから、 **[すべてのリソース]** を選択します。

2. IoT ハブを選択します。

3. **[エクスプローラー]** メニューで、 **[IoT デバイス]** を選択します。

4. このクイックスタートで登録したデバイスの "*デバイス ID*" の横にあるチェック ボックスをオンにします。

5. ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

X.509 証明書デバイスをプロビジョニングする:

> [!div class="nextstepaction"]
> [クイックスタート - Azure IoT C SDK を使用して X.509 デバイスをプロビジョニングする](quick-create-simulated-device-x509.md)