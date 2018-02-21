---
title: "シミュレートされた X.509 デバイスを C を使用して Azure IoT Hub にプロビジョニングする | Microsoft Docs"
description: "Azure クイックスタート - Azure IoT Hub Device Provisioning Service 対応の C デバイス SDK を使用して、シミュレートされた X.509 デバイスを作成してプロビジョニングする"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 12/20/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4d723a3b78a43d3b609d5a884591a92606ca11cc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-provision-an-x509-simulated-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 対応の C デバイス SDK を使用して、X.509 のシミュレートされた デバイスを作成してプロビジョニングする
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

以下の手順では、Windows OS を実行する開発マシン上で X.509 デバイスをシミュレートすると共に、コード サンプルを使って、そのシミュレートされたデバイスを Device Provisioning Service および IoT ハブに接続する方法について説明します。 

事前に、[Azure Portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順を済ませておいてください。

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>開発環境の準備 

1. マシンに Visual Studio 2015 または [Visual Studio 2017](https://www.visualstudio.com/vs/) がインストールされていることを確認します。 Visual Studio のインストールで [[C++ によるデスクトップ開発]](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ワークロードを有効にしておく必要があります。

2. [CMake ビルド システム](https://cmake.org/download/)をダウンロードしてインストールします。 `cmake` のインストール**より前に**、[C++ によるデスクトップ開発] ワークロードが有効な Visual Studio がコンピューターにインストールされていることが重要です。 

3. マシンに `git` がインストールされ、コマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 **Git Bash** (ローカル Git リポジトリと対話する際に使用するコマンドライン アプリ) など、インストールする各種 `git` ツールの最新バージョンについては、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページを参照してください。 

4. コマンド プロンプトまたは Git Bash を開きます。 デバイス シミュレーション コード サンプルの GitHub リポジトリを複製します。
    
    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. この GitHub リポジトリのローカル コピーに、CMake ビルド プロセス用のフォルダーを作成します。 

    ```cmd
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. 次のコマンドを実行して、プロビジョニング クライアントの Visual Studio ソリューションを作成します。

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    `cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生している可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)でこのコマンドを実行してください。 


<a id="portalenroll"></a>

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Device Provisioning Service でのデバイス登録エントリの作成

1. *cmake* フォルダーに生成されたソリューション (`azure_iot_sdks.sln`) を開き、Visual Studio でビルドします。

2. **Provision\_Tools** フォルダーの **dice\_device\_enrollment** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。 ソリューションを実行する 出力ウィンドウで、確認を求められたら個々の登録を行うための「**i**」を入力します。 シミュレートされたデバイスについて、ローカルで生成された X.509 証明書が出力ウィンドウに表示されます。 出力内容の *-----BEGIN CERTIFICATE-----* から最初の *-----END CERTIFICATE-----* までをクリップボードにコピーします。この両方の行を確実に含めるようにしてください。 必要なのは出力ウィンドウの最初の証明書のみであることに注意してください。
 
3. ご使用の Windows マシンに **_X509testcert.pem_** という名前のファイルを作成して任意のエディターで開き、クリップボードの内容をこのファイルにコピーします。 ファイルを保存します。 

4. Azure Portal にログインし、左側のメニューの **[すべてのリソース]** をクリックして、Provisioning Service を開きます。

4. サービスの **[登録の管理]** ブレードを開きます。 **[個々の登録]** タブを選択し、上部にある **[追加]** ボタンをクリックします。 

5. **[Add enrollment list entry]\(登録リスト エントリの追加\)** で、次の情報を入力します。
    - ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
    - "*[.pem または .cer の証明書ファイル]*" で、前の手順で "*ファイル エクスプローラー*" ウィジェットを使用して作成した証明書ファイル (**_X509testcert.pem_**) を選択します。
    - 必要に応じて、次の情報を入力することができます。
        - プロビジョニング サービスにリンクされた IoT ハブを選択します。
        - 一意のデバイス ID を入力します。 デバイスに名前を付ける際に機密データを含めないようにしてください。 
        - **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。
    - 作業が完了したら、**[保存]** をクリックします。 

    ![ポータルのブレードに X.509 デバイス登録情報を入力します。](./media/quick-create-simulated-device-x509/enter-device-enrollment.png)  

   登録に成功すると、*[個々の登録]* タブの *[登録 ID]* 列に X.509 デバイスが **riot-device-cert** として表示されます。 



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>デバイスの初回ブート シーケンスのシミュレーション

1. Azure Portal で、Device Provisioning サービスの **[概要]** ブレードを選択し、**_[ID スコープ]_** の値を書き留めます。

    ![ポータルのブレードから DPS エンドポイント情報を取得](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. お使いのマシンの Visual Studio で、**Provision\_Samples** フォルダーの **prov\_dev\_client\_sample** という名前のサンプル プロジェクトに移動し、**prov\_dev\_client\_sample.c** ファイルを開きます。

3. _[ID Scope]\(ID スコープ\)_ の値を `id_scope` 変数に代入します。 

    ```c
    static const char* id_scope = "[ID Scope]";
    ```

4. 同じファイルの **main()** 関数で、**SECURE_DEVICE_TYPE** が X.509 に設定されていることを確認します。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

   存在する可能性がある `hsm_type = SECURE_DEVICE_TYPE_TPM;` ステートメントをコメント アウトするか削除します。 

5. **prov\_dev\_client\_sample** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。 サンプルを実行します。 デバイスが起動して Device Provisioning Service に接続し、IoT ハブの情報を取得する動作がシミュレートされるので、そのメッセージに注目してください。 ハブへの登録に成功したことを示す "*Registration Information received from service: yourhuburl! (次のサービスから登録情報を受信しました: <ハブの URL>)*" というメッセージを探します。 確認を求めるメッセージが表示されたらウィンドウを閉じます。

6. ポータルで、ご利用のプロビジョニング サービスにリンクされている IoT Hub に移動し、**[IoT デバイス]** ブレードを開きます。 シミュレートされた X.509 デバイスをハブにプロビジョニングすると、そのデバイス ID が**有効**な "*状態*" で **[IoT デバイス]** ブレードに表示されます。 サンプル デバイス アプリケーションを実行する前に既にブレードが開いていた場合は、必要に応じて一番上にある **[最新の情報に更新]** ボタンをクリックしてください。 

    ![IoT ハブに登録されたデバイス](./media/quick-create-simulated-device/hub-registration.png) 

    *[Initial device twin state]\(初期のデバイス ツインの状態\)* をデバイスの登録エントリの既定値から変更した場合、デバイスはハブから目的のツインの状態をプルし、それに従って動作することができます。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。


> [!IMPORTANT]
> このクイックスタートの手順を次のように変更することで、X.509 デバイスの "*グループ登録*" を行うこともできます。
>    1. [Windows 開発環境の設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#windows)に関するガイドの **WebSockets** のセクションに従って、既定の **SChannel** の代わりに **OpenSSL** ライブラリを使用するように Windows コンピューターを構成します。 Linux コンピューターでは、既定で OpenSSL が使用されます。 
>    2. 前述の「[Device Provisioning Service でのデバイス登録エントリの作成](#portalenroll)」セクションの手順 2 で、グループ登録を行うための「**g**」を入力します。
>    3. [同じセクション](#portalenroll)の手順 4 と手順 5 で、**[登録グループ]** を選択し、グループ エントリに必要な情報を入力します。  
>

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きデバイス クライアント サンプルを使用する場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 使用する予定がない場合は、次の手順を使用して、このクイックスタートで作成したすべてのリソースを削除してください。

1. マシンに表示されているデバイス クライアント サンプルの出力ウィンドウを閉じます。
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning サービスを選択します。 サービスの **[登録を管理します]** ブレードを開き、**[個々の登録]** タブをクリックします。このクイックスタートで登録したデバイスの*登録 ID* を選択し、上部の **[削除]** ボタンをクリックします。 
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 ハブの **[IoT Devices]\(IoT デバイス\)** ブレードを開き、このクイックスタートで登録したデバイスの "*デバイス ID*" を選択し、一番上の **[削除]** ボタンをクリックします。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、ポータルでシミュレートされた X.509 デバイスを Windows コンピューター上に作成し、Azure IoT Hub Device Provisioning Service を使用して IoT ハブにプロビジョニングしました。 プログラミングによって X.509 デバイスを登録する方法については、X.509 デバイスのプログラミングによる登録のクイックスタートに進みます。 

> [!div class="nextstepaction"]
> [Azure クイックスタート - Azure IoT Hub Device Provisioning Service への X.509 デバイスの登録](quick-enroll-device-x509-java.md)
