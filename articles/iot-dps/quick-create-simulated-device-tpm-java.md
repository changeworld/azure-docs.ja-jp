---
title: "シミュレートされた TPM デバイスを Java を使って Azure IoT Hub にプロビジョニングする | Microsoft Docs"
description: "Azure クイックスタート - Azure IoT Hub Device Provisioning Service 対応の Java デバイス SDK を使い、シミュレートされた TPM デバイスを作成してプロビジョニングする"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/05/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: e0e50cdb987f2632d11753940327d1a88be4f3ab
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2018
---
# <a name="create-and-provision-a-simulated-tpm-device-using-java-device-sdk-for-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service 対応の Java デバイス SDK を使い、シミュレートされた TPM デバイスを作成してプロビジョニングする
> [!div class="op_single_selector"]
> * [C](quick-create-simulated-device.md)
> * [Java](quick-create-simulated-device-tpm-java.md)
> * [C#](quick-create-simulated-device-tpm-csharp.md)
> * [Python](quick-create-simulated-device-tpm-python.md)

以下の手順では、Windows OS を実行する開発マシンにシミュレートされたデバイスを作成し、そのデバイスの[ハードウェア セキュリティ モジュール (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) として Windows TPM シミュレーターを実行します。その後、コード サンプルを使って、そのシミュレートされたデバイスを Device Provisioning Service および IoT ハブに接続します。 

事前に、[Azure Portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順を済ませておいてください。


## <a name="prepare-the-environment"></a>環境の準備 

1. [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) がマシンにインストールされていることを確認します。

1. [Maven をダウンロードし、インストールします](https://maven.apache.org/install.html)。

1. マシンに `git` がインストールされ、コマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 **Git Bash** (ローカル Git リポジトリと対話する際に使用するコマンドライン アプリ) など、インストールする各種 `git` ツールの最新バージョンについては、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページを参照してください。 

1. コマンド プロンプトを開きます。 デバイス シミュレーション コード サンプルの GitHub リポジトリを複製します。
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) シミュレーターを実行します。 **[アクセスを許可]** をクリックし、_[Windows ファイアウォール]_ 設定への変更を許可します。 これは、ソケットでポート 2321 とポート 2322 をリッスンします。 このウィンドウを閉じないでください。このクイックスタート ガイドの終了まで、このシミュレーターを実行状態にしておく必要があります。 

    ```cmd/sh
    .\azure-iot-sdk-java\provisioning\provisioning-tools\tpm-simulator\Simulator.exe
    ```

    ![TPM シミュレーター](./media/java-quick-create-simulated-device/simulator.png)

1. 別のコマンド プロンプトでルート フォルダーに移動し、サンプルの依存関係を作成します。

    ```cmd/sh
    cd azure-iot-sdk-java
    mvn install -DskipTests=true
    ```

1. サンプル フォルダーに移動します。

    ```cmd/sh
    cd provisioning/provisioning-samples/provisioning-tpm-sample
    ```

1. Azure Portal にログインし、左側のメニューの **[すべてのリソース]** をクリックして、Device Provisioning Service を開きます。 "_ID スコープ_" と "_プロビジョニング サービス グローバル エンドポイント_" をメモします。

    ![DPS 情報](./media/java-quick-create-simulated-device/extract-dps-endpoints.png)

1. `src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningTpmSample.java` を編集して、メモした "_ID スコープ_" と "_プロビジョニング サービス グローバル エンドポイント_" を含めます。  

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    ```

1. プロジェクトをビルドします。 ターゲット フォルダーに移動し、作成した jar ファイルを実行します。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-tpm-sample-{version}-with-deps.jar
    ```

1. プログラムの実行が始まっています。 次のセクションのために "_保証キー_" と "_登録 ID_" をメモします。プログラムは実行したままにしてください。

    ![Java TPM デバイス プログラム](./media/java-quick-create-simulated-device/program.png)
    

## <a name="create-a-device-enrollment-entry"></a>デバイス登録エントリの作成

1. Azure Portal にログインし、左側のメニューの **[すべてのリソース]** をクリックして、Device Provisioning Service を開きます。

1. Device Provisioning Service の概要ブレードで、**[Manage enrollments]\(登録の管理\)** を選択します。 **[Individual Enrollments]\(個々の登録\)** タブの上部にある **[追加]** ボタンをクリックします。 

1. **[Add enrollment list entry]\(登録リスト エントリの追加\)** で、次の情報を入力します。
    - ID 構成証明の "*メカニズム*" として **[TPM]** を選択します。
    - 前にメモした TPM デバイスの "*登録 ID*" と "*保証キー*" を入力します。 
    - プロビジョニング サービスにリンクされた IoT ハブを選択します。
    - 一意のデバイス ID を入力します。 デバイスに名前を付ける際に機密データを含めないようにしてください。
    - **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。
    - 作業が完了したら、**[保存]** をクリックします。 

    ![ポータルのブレードにデバイス登録情報を入力します。](./media/java-quick-create-simulated-device/enter-device-enrollment.png)  

   登録に成功すると、*[個々の登録]* タブの一覧に、対象デバイスの "*登録 ID*" が表示されます。 


## <a name="simulate-the-device"></a>デバイスのシミュレート

1. ご使用のマシンで Java サンプル コードを実行しているコマンド ウィンドウで、Enter キーを押してアプリケーションの実行を続けます。 デバイスが起動して Device Provisioning Service に接続し、IoT ハブの情報を取得する動作がシミュレートされるので、そのメッセージに注目してください。  

    ![最終段階の Java TPM デバイス プログラム](./media/java-quick-create-simulated-device/program-final.png)

1. プロビジョニング サービスにリンクされた IoT ハブに対し、シミュレートされたデバイスが正常にプロビジョニングされると、ハブの **[デバイス エクスプローラー]** ブレードにデバイス ID が表示されます。

    ![IoT ハブに登録されたデバイス](./media/java-quick-create-simulated-device/hub-registration.png) 

    *[Initial device twin state]\(初期のデバイス ツインの状態\)* をデバイスの登録エントリの既定値から変更した場合、デバイスはハブから目的のツインの状態をプルし、それに従って動作することができます。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きデバイス クライアント サンプルを使用する場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 使用する予定がない場合は、次の手順を使用して、このクイックスタートで作成したすべてのリソースを削除してください。

1. マシンに表示されているデバイス クライアント サンプルの出力ウィンドウを閉じます。
1. マシンに表示されている TPM シミュレーター ウィンドウを閉じます。
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning サービスを選択します。 サービスの **[登録を管理します]** ブレードを開き、**[個々の登録]** タブをクリックします。このクイックスタートで登録したデバイスの "*登録 ID*" を選択し、一番上の **[削除]** ボタンをクリックします。 
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 ハブの **[IoT Devices]\(IoT デバイス\)** ブレードを開き、このクイックスタートで登録したデバイスの "*デバイス ID*" を選択し、一番上の **[削除]** ボタンをクリックします。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、TPM のシミュレートされたデバイスをマシンに作成し、IoT Hub Device Provisioning Service を使って IoT ハブにプロビジョニングしました。 プログラミングによって TPM デバイスを登録する方法については、TPM デバイスのプログラミングによる登録のクイックスタートに進みます。 

> [!div class="nextstepaction"]
> [Azure クイックスタート - Azure IoT Hub Device Provisioning Service への TPM デバイスの登録](quick-enroll-device-tpm-java.md)
