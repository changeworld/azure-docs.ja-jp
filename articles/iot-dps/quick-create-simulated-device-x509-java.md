---
title: "シミュレートされた X.509 デバイスを Java を使用して Azure IoT Hub にプロビジョニングする | Microsoft Docs"
description: "Azure クイックスタート - IoT Hub Device Provisioning Service 対応の Java デバイス SDK を使用して、シミュレートされた X.509 デバイスを作成してプロビジョニングする"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 508df6e7c677d87c6b893519c59b02880e16d7ec
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 対応の Java デバイス SDK を使用して、シミュレートされた X.509 デバイスを作成してプロビジョニングする
> [!div class="op_single_selector"]
> * [C](quick-create-simulated-device-x509.md)
> * [Java](quick-create-simulated-device-x509-java.md)
> * [C#](quick-create-simulated-device-x509-csharp.md)
> * [Python](quick-create-simulated-device-x509-python.md)

以下の手順では、Windows OS を実行する開発マシン上で X.509 デバイスをシミュレートすると共に、コード サンプルを使って、そのシミュレートされたデバイスを Device Provisioning Service および IoT ハブに接続する方法について説明します。 

事前に、[Azure Portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順を済ませておいてください。


## <a name="prepare-the-environment"></a>環境の準備 

1. [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) がマシンにインストールされていることを確認します。

1. [Maven](https://maven.apache.org/install.html) をダウンロードし、インストールします。

1. マシンに `git` がインストールされ、コマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 **Git Bash** (ローカル Git リポジトリと対話する際に使用するコマンドライン アプリ) など、インストールする各種 `git` ツールの最新バージョンについては、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページを参照してください。 

1. コマンド プロンプトを開きます。 デバイス シミュレーション コード サンプルの GitHub リポジトリを複製します。
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. 証明書ジェネレーター プロジェクトに移動し、プロジェクトをビルドします。 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. ターゲット フォルダーに移動し、作成した jar ファイルを実行します。

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. 設定に従って、次のいずれかの方法で登録情報を作成します。

    - **個々の登録**：

        1. _[Do you want to input common name]\(共通名を入力しますか\)_ に「**N**」と入力します。 `Client Cert` の出力の *-----BEGIN CERTIFICATE-----* から *-----END CERTIFICATE-----* までクリップボードにコピーします。

            ![個別証明書ジェネレーター](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Windows マシンに **_X509individual.pem_** というファイルを作成して任意のエディターで開き、クリップボードの内容をこのファイルにコピーします。 ファイルを保存します。

        1. _[Do you want to input Verification Code]\(確認コードを入力しますか\)_ に「**N**」と入力し、クイックスタートで後で参照できるようにプログラムの出力を開いたままにします。 _[Client Cert]\(クライアント証明書\)_ と _[Client Cert Private Key]\(クライアント証明書の秘密キー\)_ の値をメモします。
    
    - **登録グループ**：

        1. _[Do you want to input common name]\(共通名を入力しますか\)_ に「**N**」と入力します。 `Root Cert` の出力の *-----BEGIN CERTIFICATE-----* から *-----END CERTIFICATE-----* までクリップボードにコピーします。

            ![グループ証明書ジェネレーター](./media/java-quick-create-simulated-device-x509/group.png)

        1. Windows マシンに **_X509group.pem_** というファイルを作成して任意のエディターで開き、クリップボードの内容をこのファイルにコピーします。 ファイルを保存します。

        1. _[Do you want to input Verification Code]\(確認コードを入力しますか\)_ に「**Y**」と入力し、クイックスタートで後で使用できるようにプログラムを開いたままにします。 _[Client Cert]\(クライアント証明書\)_、_[Client Cert Private Key]\(クライアント証明書の秘密キー\)_、_[Signer Cert]\(署名者証明書\)_、および _[Root Cert]\(ルート証明書\)_ の値をメモします。


## <a name="create-a-device-enrollment-entry"></a>デバイス登録エントリを作成する

1. Azure Portal にログインし、左側のメニューの **[すべてのリソース]** をクリックして、Provisioning Service を開きます。

1. 設定に従って、次のいずれかの方法で登録情報を入力します。

    - **個々の登録**： 

        1. Device Provisioning Service の概要ブレードで、**[Manage enrollments]\(登録の管理\)** を選択します。 **[Individual Enrollments]\(個々の登録\)** タブの上部にある **[追加]** ボタンをクリックします。 

        1. **[Add enrollment list entry]\(登録リスト エントリの追加\)** で、次の情報を入力します。
            - ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
            - *[.pem または .cer の証明書ファイルです]* で、前の手順で*ファイル エクスプローラー* ウィジェットを使用して作成した証明書ファイル (**_X509individual.pem_**) を選択します。
            - 必要に応じて、次の情報を入力することができます。
                - プロビジョニング サービスにリンクされた IoT ハブを選択します。
                - 一意のデバイス ID を入力します。 デバイスに名前を付ける際に機密データを含めないようにしてください。 
                - **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。
            - 作業が完了したら、**[保存]** をクリックします。 

        ![ポータルのブレードに X.509 デバイス登録情報を入力します。](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       登録に成功すると、*[個々の登録]* タブの *[登録 ID]* 列に X.509 デバイスが **microsoftriotcore** と表示されます。 

    - **登録グループ**： 

        1. Device Provisioning Service の概要ブレードで **[証明書]** を選択し、上部の **[追加]** ボタンをクリックします。

        1. **[証明書の追加]** に次の情報を入力します。
            - 一意の証明書名を入力します。
            - 前に作成した **_X509group.pem_** ファイルを選択します。
            - 作業が完了したら、**[保存]** をクリックします。

        ![証明書を追加する](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. 新しく作成された証明書を選択します。
            - **[確認コードを生成します]** をクリックします。 生成されたコードをコピーします。
            - _確認コード_を入力するか、右クリックして実行中の _provisioning-x509-cert-generator_ ウィンドウに貼り付けます。  **Enter** キーを押します。
            - `Verification Cert` の出力の *-----BEGIN CERTIFICATE-----* から *-----END CERTIFICATE-----* までクリップボードにコピーします。
            
                ![検証ジェネレーター](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Windows マシンに **_X509validation.pem_** というファイルを作成して任意のエディターで開き、クリップボードの内容をこのファイルにコピーします。 ファイルを保存します。
            - Azure Portal で **_X509validation.pem_** ファイルを選択します。 **[検証]** をクリックします。

            ![証明書を検証する](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. **[登録を管理します]** を選択します。 **[登録グループ]** タブを選択して、上部の **[追加]** ボタンをクリックします。
            - 一意のグループ名を入力します。
            - 前に作成した一意の証明書名を選択します
            - 必要に応じて、次の情報を入力することができます。
                - プロビジョニング サービスにリンクされた IoT ハブを選択します。
                - **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。

        ![ポータルのブレードに X.509 グループの登録情報を入力します。](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        登録が正常に完了すると、X.509 デバイス グループが *[登録グループ]* タブの *[グループ名]* 列に表示されます。


## <a name="simulate-the-device"></a>デバイスをシミュレートする

1. Device Provisioning Service の概要ブレードで、**[概要]** を選択し、_ID スコープ_と _Provisioning Service のグローバル エンドポイント_をメモします。

    ![サービス情報](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. コマンド プロンプトを開きます。 サンプル プロジェクト フォルダーに移動します。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. 設定に従って、次のいずれかの方法で登録情報を入力します。

    - **個々の登録**： 

        1. `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` を編集して、メモした _ID スコープ_と _Provisioning Service のグローバル エンドポイント_を含めます。 また、メモした_クライアント証明書_と_クライアント証明書の秘密キー_も含めます。

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - 次の形式で証明書とキーを含めます。
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **登録グループ**： 

        1. 前述の**個々の登録**の手順に従います。

        1. `main` 関数の先頭に次のコード行を追加します。
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(root);
            ```
    
            - 次の形式で証明書を含めます。
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. サンプルをビルドします。 ターゲット フォルダーに移動し、作成した jar ファイルを実行します。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. ポータルで、ご利用のプロビジョニング サービスにリンクされている IoT Hub に移動し、**[Device Explorer]** ブレードを開きます。 シミュレートされた X.509 デバイスをハブにプロビジョニングすると、そのデバイス ID が**有効**な "*状態*" として **[Device Explorer]** ブレードに表示されます。 サンプル デバイス アプリケーションを実行する前に既にブレードが開いていた場合は、必要に応じて一番上にある **[最新の情報に更新]** ボタンをクリックしてください。 

    ![IoT ハブに登録されたデバイス](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> *[Initial device twin state]\(初期のデバイス ツインの状態\)* をデバイスの登録エントリの既定値から変更した場合、デバイスはハブから目的のツインの状態をプルし、それに従って動作することができます。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。
>


## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きデバイス クライアント サンプルを使用する場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 使用する予定がない場合は、次の手順を使用して、このクイックスタートで作成したすべてのリソースを削除してください。

1. マシンに表示されているデバイス クライアント サンプルの出力ウィンドウを閉じます。
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning サービスを選択します。 サービスの **[登録を管理します]** ブレードを開き、**[個々の登録]** タブをクリックします。このクイックスタートで登録したデバイスの*登録 ID* を選択し、上部の **[削除]** ボタンをクリックします。 
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 ハブの **[IoT Devices]\(IoT デバイス\)** ブレードを開き、このクイックスタートで登録したデバイスの*デバイス ID* を選択し、上部の **[削除]** ボタンをクリックします。


## <a name="next-steps"></a>次の手順

このクイックスタートでは、ポータルでシミュレートされた X.509 デバイスを Windows コンピューター上に作成し、Azure IoT Hub Device Provisioning Service を使用して IoT ハブにプロビジョニングしました。 プログラミングによって X.509 デバイスを登録する方法については、X.509 デバイスのプログラミングによる登録のクイックスタートに進みます。 

> [!div class="nextstepaction"]
> [Azure クイックスタート - Azure IoT Hub Device Provisioning Service への X.509 デバイスの登録](quick-enroll-device-x509-java.md)
