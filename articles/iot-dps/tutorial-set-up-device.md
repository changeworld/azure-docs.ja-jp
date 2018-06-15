---
title: Azure IoT Hub Device Provisioning Service 用にデバイスをセットアップする
description: デバイスの製造プロセス中に、IoT Hub Device Provisioning Service を使用してプロビジョニングするデバイスを設定します。
author: dsk-2015
ms.author: dkshir
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1e4e93c276fe62caae17c85bf9ac92282dfdfb88
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631270"
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を使用してプロビジョニングするデバイスの設定

前のチュートリアルでは、デバイスを IoT ハブに自動的にプロビジョニングするために、Azure IoT Hub Device Provisioning Service を設定する方法を説明しました。 このチュートリアルでは、製造プロセス中にデバイスをセットアップし、IoT Hub で自動プロビジョニングされるようにする方法について説明します。 デバイスは、初めて起動してプロビジョニング サービスに接続するときに、[構成証明メカニズム](concepts-device.md#attestation-mechanism)に基づいてプロビジョニングされます。 このチュートリアルでは、次のプロセスについて説明します。

> [!div class="checklist"]
> * プラットフォーム固有の Device Provisioning Services Client SDK を構築する
> * セキュリティ アーティファクトを抽出する
> * デバイス登録ソフトウェアを作成する

## <a name="prerequisites"></a>前提条件

以降の手順に進む前に、前の「[1 - クラウド リソースを設定する](./tutorial-set-up-cloud.md)」チュートリアルの指示に従って、Device Provisioning Service インスタンスと IoT ハブを作成してください。

このチュートリアルでは、[Azure IoT SDKs and libraries for C repository (C 用の Azure IoT SDK とライブラリのリポジトリ)](https://github.com/Azure/azure-iot-sdk-c) を使用します。これには、Device Provisioning Service Client SDK for C が含まれています。この SDK は、現在、Windows または Ubuntu 実装上で実行されているデバイスに対して TPM および X.509 サポートを提供しています。 このチュートリアルは、Windows 開発クライアントの使用をベースとしています。また、Visual Studio 2017 に関する基本的な知識を前提としています。 

自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](concepts-auto-provisioning.md)」を読んでから先に進んでください。 

## <a name="build-a-platform-specific-version-of-the-sdk"></a>SDK のプラットフォーム固有のバージョンを構築する

Device Provisioning Service Client SDK は、デバイス登録ソフトウェアを実装するために役立ちます。 しかし、使用する前に、開発クライアント プラットフォームと構成証明メカニズムに固有の SDK のバージョンを構築する必要があります。 このチュートリアルでは、Windows 開発プラットフォーム上で Visual Studio 2017 を使用する、以下のサポートされている種類の構成証明書用の SDK を構築します。

1. 必要なツールをインストールし、プロビジョニング サービス Client SDK for C が含まれている GitHub リポジトリを複製します。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 マシンに Visual Studio 2015 または [Visual Studio 2017](https://www.visualstudio.com/vs/) がインストールされていることを確認します。 Visual Studio のインストールで [[C++ によるデスクトップ開発]](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ワークロードを有効にしておく必要があります。

   b. [CMake ビルド システム](https://cmake.org/download/)をダウンロードしてインストールします。 CMake のインストールより**前に**、[C++ によるデスクトップ開発] ワークロードが有効な Visual Studio がマシンにインストールされていることが重要です。

   c. マシンに `git` がインストールされ、コマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 最新の `git` ツールについては、[Software Freedom Conservancy の Git クライアント ツールのページ](https://git-scm.com/download/)を参照してください。たとえば、ローカル Git リポジトリとやり取りするためのコマンド ライン Bash シェルである **Git Bash** などがあります。 

   d. Git Bash を開き、"Azure IoT SDKs and libraries for C" リポジトリを複製します。 clone コマンドは、いくつかの従属サブモジュールもダウンロードするため、完了までに数分かかる場合があります。
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   e. 新しく作成されたリポジトリのサブディレクトリ内に、新しい `cmake` サブディレクトリを作成します。

   ```cmd/sh
   mkdir azure-iot-sdk-c/cmake
   ``` 

2. Git Bash コマンド プロンプトから、azure-iot-sdk-c リポジトリの `cmake` サブディレクトリに移動します。

   ```cmd/sh
   cd azure-iot-sdk-c/cmake
   ```

3. 次のいずれかのコマンドを使用して、開発プラットフォームとサポートされているいずれかの構成証明メカニズムに応じた SDK を構築します (末尾の 2 つのピリオドにも注意してください)。 完了すると、CMake は次のようなデバイス固有の内容を持つ `/cmake` サブディレクトリを作成します。
    - 物理 TPM/HSM を使用するか、構成証明のためにシミュレートされた X.509 証明書を使用するデバイスの場合:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - 構成証明のために TPM シミュレーターを使用するデバイスの場合:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

これで、SDK を使用して、デバイス登録コードをビルドする準備ができました。 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>セキュリティ アーティファクトを抽出する 

次に、デバイスで使用される構成証明メカニズムのために、セキュリティ アーティファクトを抽出します。 

### <a name="physical-device"></a>物理デバイス 

物理 TPM/HSM の構成証明を使用するように SDK を構築した場合:

- TPM デバイスの場合、デバイスに関連付けられている、TPM チップの製造元の**保証キー**を判断する必要があります。 保証キーをハッシュすることによって、TPM デバイスの一意の**登録 ID** を派生させることができます。  

- X.509 デバイスの場合、デバイスに発行された証明書 (デバイスの個別加入の場合はエンド エンティティ証明書、デバイスのグループ加入の場合はルート証明書) を取得する必要があります。 

### <a name="simulated-device"></a>シミュレートされたデバイス

シミュレートされた TPM または X.509 証明書の構成証明を使用するように SDK を構築した場合:

- シミュレートされた TPM デバイスの場合:
   1. 別の新しいコマンド プロンプトで `azure-iot-sdk-c` サブディレクトリに移動し、TPM シミュレーターを実行します。 これは、ソケットでポート 2321 とポート 2322 をリッスンします。 このコマンド ウィンドウを閉じないでください。この次のクイック スタートの終了まで、このシミュレーターを実行状態にしておく必要があります。 

      `azure-iot-sdk-c` サブディレクトリで次のコマンドを実行して、シミュレーターを起動します。

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

   2. Visual Studio を使用して、*cmake* フォルダーに生成された `azure_iot_sdks.sln` という名前のソリューションを開き、[ビルド] メニューの [ソリューションのビルド] コマンドを使用してビルドします。

   3. Visual Studio の "*ソリューション エクスプローラー*" ウィンドウで、**Provision\_Tools** フォルダーに移動します。 **tpm_device_provision** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。 

   4. [デバッグ] メニューのいずれかの [開始] コマンドを使用してソリューションを実行します。 出力ウィンドウに、TPM シミュレーターの "**_登録 ID_**" と "**_保証キー_**" が表示されます。これらは、デバイスの加入と登録に必要です。 後で使用するために、これらの値をコピーしておきます。 このウィンドウ (登録 ID と保証キーのウィンドウ) は閉じてもかまいませんが、手順 1. で開始した TPM シミュレーター ウィンドウは実行したままにしておきます。

- シミュレートされた X.509 デバイスの場合:
  1. Visual Studio を使用して、*cmake* フォルダーに生成された `azure_iot_sdks.sln` という名前のソリューションを開き、[ビルド] メニューの [ソリューションのビルド] コマンドを使用してビルドします。

  2. Visual Studio の "*ソリューション エクスプローラー*" ウィンドウで、**Provision\_Tools** フォルダーに移動します。 **[dice\_device\_enrollment]** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。 
  
  3. [デバッグ] メニューのいずれかの [開始] コマンドを使用してソリューションを実行します。 出力ウィンドウで、確認を求められたら個々の登録を行うための「**i**」を入力します。 シミュレートされたデバイスについて、ローカルで生成された X.509 証明書が出力ウィンドウに表示されます。 出力内容の *-----BEGIN CERTIFICATE-----* から最初の *-----END CERTIFICATE-----* までをクリップボードにコピーします。この両方の行を確実に含めるようにしてください。 必要なのは出力ウィンドウの最初の証明書のみであることに注意してください。
 
  4. **_X509testcert.pem_** という名前のファイルを作成して任意のテキスト エディターで開き、クリップボードの内容をこのファイルにコピーします。 後でデバイスの加入に使用するため、ファイルを保存しておきます。 登録ソフトウェアを実行すると、自動プロビジョニング中に、同じ証明書が使用されます。    

これらのセキュリティ アーティファクトは、デバイスを Device Provisioning Service に加入させる際に必要となります。 プロビジョニング サービスは、デバイスが起動し、後でこのサービスに接続してくるまで待機します。 デバイスの初回起動時に、クライアント SDK ロジックはチップ (またはシミュレーター) と対話してデバイスからセキュリティ アーティファクトを抽出し、Device Provisioning Service への登録を確認します。 

## <a name="create-the-device-registration-software"></a>デバイス登録ソフトウェアを作成する

手順の最後に、Device Provisioning Service Client SDK を使用してデバイスを IoT Hub サービスに登録する登録アプリケーションを記述します。 

> [!NOTE]
> この手順では、シミュレートされたデバイスの使用を想定して、ワークステーションから SDK サンプル登録アプリケーションを実行します。 ただし、物理デバイスにデプロイするための登録アプリケーションを構築する場合も、同じ概念が適用されます。 

1. Azure Portal で、Device Provisioning サービスの **[概要]** ブレードを選択し、**[_ID スコープ_]** の値をコピーします。 サービスによって "*ID スコープ*" が生成され、一意性が保証されます。 ID スコープは不変であり、登録 ID を一意に識別するために使用されます。

    ![ポータルのブレードから DPS エンドポイント情報を取得](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

2. お使いのコンピューターの Visual Studio の "*ソリューション エクスプローラー*" で、**Provision\_Samples** フォルダーに移動します。 **prov\_dev\_client\_sample** という名前のサンプル プロジェクトを選択し、**prov\_dev\_client\_sample.c** ソース ファイルを開きます。

3. 手順 1. で取得した "_ID スコープ_" 値を `id_scope` 変数に割り当てます (`[` かっこと `]` かっこは削除します)。 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    なお、`global_prov_uri` 変数は、IoT Hub クライアント登録 API `IoTHubClient_LL_CreateFromDeviceAuth` が、指定された Device Provisioning Service インスタンスに接続できるようにします。

4. 同じファイルの **main()** 関数で、デバイスの登録ソフトウェアによって使用されている構成証明メカニズム (TPM または X.509) に対応する `hsm_type` 変数をコメント/コメント解除します。 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. 変更を保存し、[ビルド] メニューの [ソリューションのビルド] を選択して、**prov\_dev\_client\_sample** サンプルを再ビルドします。 

6. **Provision\_Samples** フォルダーの **prov\_dev\_client\_sample** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。 まだサンプル アプリケーションを実行しないでください。

> [!IMPORTANT]
> まだデバイスを実行/起動しないでください。 デバイスを起動する前に、デバイスを Device Provisioning Service に加入させて、プロセスを完了する必要があります。 後の「次の手順」セクションで、次の記事に移動することができます。

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>登録中に使用される SDK API (参考情報)

SDK では、登録中にアプリケーションで使用できるように、以下の API が用意されています。 これらの API を使用することで、デバイスは起動時に Device Provisioning Service に接続して登録することができます。 代わりに、デバイスは IoT Hub インスタンスへの接続を確立するために必要な情報を受け取ります。

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

まずはシミュレートされたデバイスと、テスト用のサービス セットアップを使用することで、Device Provisioning Service クライアント登録アプリケーションの改良が必要であることがわかる場合もあります。 テスト環境でアプリケーションが動作したら、特定のデバイス用にアプリケーションをビルドし、実行可能ファイルをデバイス イメージにコピーします。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この時点で、Device Provisioning Service と IoT Hub サービスがポータルで実行されているでしょう。 デバイス プロビジョニングのセットアップを破棄したり、このチュートリアル シリーズの完了を遅らせる場合は、不要なコストが発生しないようにサービスをシャットダウンすることをお勧めします。

1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning サービスを選択します。 **[すべてのリソース]** ブレードの上部にある **[削除]** をクリックします。  
2. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 **[すべてのリソース]** ブレードの上部にある **[削除]** をクリックします。  

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * プラットフォーム固有の Device Provisioning Service Client SDK を構築する
> * セキュリティ アーティファクトを抽出する
> * デバイス登録ソフトウェアを作成する

次のチュートリアルに進み、自動プロビジョニングのために Azure IoT Hub Device Provisioning Service にデバイスを登録して、IoT ハブにデバイスをプロビジョニングする方法を学習してください。

> [!div class="nextstepaction"]
> [IoT ハブにデバイスをプロビジョニングする](tutorial-provision-device-to-hub.md)

