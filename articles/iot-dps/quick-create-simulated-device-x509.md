---
title: クイックスタート - シミュレートされた X.509 証明書デバイスを Microsoft Azure IoT Hub にプロビジョニングする
description: Azure IoT Hub Device Provisioning Service で、X.509 証明書を使用して認証するシミュレートされたデバイスをプロビジョニングする方法について学習する
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/07/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: c0c3d486e2a886a49c51e7ed78ed935c1a1c5c22
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276702"
---
# <a name="quickstart-provision-an-x509-certificate-simulated-device"></a>クイックスタート: シミュレートされた X.509 証明書デバイスをプロビジョニングする

このクイックスタートでは、シミュレートされたデバイスを Windows コンピューターに作成します。 シミュレートされたデバイスは、認証のために [X.509 証明書の構成証明](concepts-x509-attestation.md)メカニズムを使用するように構成されます。 デバイスを構成した後、Azure IoT Hub Device Provisioning Service を使用して IoT ハブにプロビジョニングします。

プロビジョニングのプロセスに慣れていない場合は、[プロビジョニング](about-iot-dps.md#provisioning-process)の概要を確認してください。  また、先に進む前に、[Azure Portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順も済ませておいてください。

このクイックスタートでは、Windows ベース ワークステーションのソリューションについて説明します。 しかし、Linux でこの手順を行うこともできます。 Linux の例については、「[How to provision for multitenancy](how-to-provision-multitenant.md)」(マルチテナント方式のプロビジョニング) を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

* [Azure portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順を完了します。

Windows 開発環境の前提条件は次のとおりです。 Linux または macOS については、SDK ドキュメントの「[開発環境を準備する](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」の該当するセクションを参照してください。

::: zone pivot="programming-language-ansi-c"

* [C++ によるデスクトップ開発''](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ワークロードを有効にした [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 をインストールします。 Visual Studio 2015 と Visual Studio 2017 もサポートされています。 Linux または macOS については、SDK ドキュメントの「[開発環境を準備する](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」の該当するセクションを参照してください。

::: zone-end

::: zone pivot="programming-language-csharp"

* Windows ベースのコンピューターに [.NET Core 3.1 SDK 以降](https://dotnet.microsoft.com/download)をインストールします。 次のコマンドを使用してバージョンを確認してください。

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* コンピューターに [Node.js v4.0 以降](https://nodejs.org)をインストールします。

* コンピューターに [ OpenSSL](https://www.openssl.org/) をインストールします。これは、コマンド ウィンドウからアクセスできる環境変数に追加されます。 このライブラリは、ソースからビルドしてインストールするか、または[サード パーティ](https://wiki.openssl.org/index.php/Binaries) ([例](https://sourceforge.net/projects/openssl/)) からダウンロードしてインストールすることができます。

::: zone-end

::: zone pivot="programming-language-python"

* コンピューター上の [Python 3.6 以降](https://www.python.org/downloads/)。

* コンピューターに [ OpenSSL](https://www.openssl.org/) をインストールします。これは、コマンド ウィンドウからアクセスできる環境変数に追加されます。 このライブラリは、ソースからビルドしてインストールするか、または[サード パーティ](https://wiki.openssl.org/index.php/Binaries) ([例](https://sourceforge.net/projects/openssl/)) からダウンロードしてインストールすることができます。

::: zone-end

::: zone pivot="programming-language-java"

* コンピューターに [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) 以降をインストールします。

* [Maven](https://maven.apache.org/install.html) をダウンロードし、インストールします。

::: zone-end

* 最新バージョンの [Git](https://git-scm.com/download/) をインストールします。 コマンド ウィンドウからアクセスできる環境変数に Git が追加されていることを確認します。 *Git Bash* (ローカル Git リポジトリとやりとりする際に使用できるコマンドライン アプリ) を含む、インストールする `git` ツールの最新バージョンについては、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページを参照してください。

## <a name="prepare-your-development-environment"></a>開発環境を準備する

::: zone pivot="programming-language-ansi-c"

このセクションでは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のビルドに使用する開発環境を準備します。 このサンプル コードでは、デバイスのブート シーケンス中にデバイスのプロビジョニングを試みます。

1. 最新の [CMake ビルド システム](https://cmake.org/download/)をダウンロードします。

    >[!IMPORTANT]
    >`CMake` のインストールを開始する **前に**、Visual Studio の前提条件 (Visual Studio と "C++ によるデスクトップ開発" ワークロード) がコンピューターにインストールされていることを確認します。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。 また、以前のバージョンの CMake ビルド システムでは、この記事で使用されているソリューション ファイルを生成できないことに注意してください。 必ず最新バージョンの CMake を使用してください。

2. Web ブラウザーを開き、[Azure IoT C SDK のリリース ページ](https://github.com/Azure/azure-iot-sdk-c/releases/latest)に移動します。

3. そのページの上部にある **[タグ]** タブを選択します。

4. 最新リリースの Azure IoT C SDK のタグ名をコピーします。

5. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、最新リリースの [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub リポジトリを複製します。 (`<release-tag>` は、前のステップでコピーしたタグで置き換えます)。

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

7. このコード サンプルでは、X.509 証明書を使用し、X.509 認証によって構成証明を提示します。 次のコマンドを実行して、デバイス プロビジョニング クライアントを含む開発プラットフォームに固有の SDK のバージョンをビルドします。 シミュレートされたデバイスの Visual Studio ソリューションが `cmake` ディレクトリに生成されます。

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    >[!TIP]
    >`cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生する可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)でコマンドを実行してください。

8. ビルドが成功すると、最後のいくつかの出力行は次の出力のようになります。

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
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

2. 次のコマンドを使用して、[Node.js 用 Azure IoT サンプル](https://github.com/Azure/azure-iot-sdk-node.git)の GitHub リポジトリをクローンします。

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Git CMD または Git Bash コマンド ライン環境を開きます。

2. 次のコマンドを使用して、[Python 用 Azure IoT サンプル](https://github.com/Azure/azure-iot-sdk-node.git)の GitHub リポジトリをクローンします。

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Git CMD または Git Bash コマンド ライン環境を開きます。

2. 次のコマンドを使用して、[Java 用 Azure IoT サンプル](https://github.com/Azure/azure-iot-sdk-java.git)の GitHub リポジトリをクローンします。

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. ルート `azure-iot-sdk-`java` ディレクトリに移動し、プロジェクトをビルドして、必要なパッケージすべてをダウンロードします。

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

4. 証明書ジェネレーター プロジェクトに移動し、プロジェクトをビルドします。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

::: zone-end

## <a name="create-a-self-signed-x509-device-certificate"></a>自己署名 X.509 デバイス証明書を作成する

このセクションでは、Azure IoT SDK のサンプル コードを使用して、自己署名 X.509 証明書を作成します。 この証明書はプロビジョニング サービスにアップロードされ、サービスによって検証される必要があります。

> [!CAUTION]
> 開発テスト専用の SDK ツールで作成された証明書を使用します。
> 運用環境ではこれらの証明書を使用しないでください。
> SDK で生成された証明書には、*1234* などのハード コーディングされたパスワードが含まれており、30 日後に有効期限が切れます。
> 運用環境での使用に適した証明書の取得について詳しくは、Azure IoT Hub ドキュメントの「[X.509 CA 証明書の入手方法](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate)」をご覧ください。
>

::: zone pivot="programming-language-csharp"

IoT デバイスのデバイス ID が、証明書におけるサブジェクトの共通名になります。 必ず、[デバイス ID の文字列要件](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)に準拠したサブジェクト名を使用してください。

::: zone-end

X.509 証明書を作成するには、次のようにします。

::: zone pivot="programming-language-ansi-c"

### <a name="clone-the-azure-iot-c-sdk"></a>Azure IoT C SDK をクローンする

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) には、X.509 証明書チェーンを作成し、そのチェーンからルートまたは中間証明書をアップロードし、サービスで所有証明を行って証明書を検証するために役立つテスト ツールが含まれています。

最新リリースの [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub リポジトリを既にクローンしている場合は、[次のセクション](#create-a-test-certificate)に進んでください。

1. Web ブラウザーを開き、[Azure IoT C SDK のリリース ページ](https://github.com/Azure/azure-iot-sdk-c/releases/latest)に移動します。

2. 最新リリースの Azure IoT C SDK のタグ名をコピーします。

3. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、最新リリースの [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub リポジトリを複製します。 (`<release-tag>` は、前のステップでコピーしたタグで置き換えます)。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    この操作が完了するまでに数分かかる場合があります。

4. これでテスト ツールは、クローンしたリポジトリの *azure-iot-sdk-c/tools/CACertificates* にあるはずです。

### <a name="create-a-test-certificate"></a>テスト証明書を作成する

「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) の手順に従います。

C SDK のツールに加えて、*Microsoft Azure IoT SDK for .NET* に含まれている [グループ証明書の検証のサンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)では、既存の X.509 中間またはルート CA 証明書で C# の所有証明を実行する方法について説明されています。

::: zone-end

::: zone pivot="programming-language-csharp"

1. PowerShell プロンプトで、ディレクトリを X.509 デバイス プロビジョニング サンプルのプロジェクト ディレクトリに変更します。

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. このサンプル コードは、パスワードで保護された PKCS12 形式のファイル (`certificate.pfx`) 内に格納された X.509 証明書を使用するように設定されています。 また、このクイックスタートの後半で個別登録を作成するために、公開キーの証明書ファイル (`certificate.cer`) が必要になります。 自己署名証明書とそれに関連する `.cer` および `.pfx` ファイルを生成するには、次のコマンドを実行します。

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. PFX パスワードの入力を求められます。 このパスワードは、後でサンプルの実行時に使用するため、覚えておいてください。 必要に応じて、`certutil` を実行して証明書をダンプし、サブジェクト名を検証することができます。

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. コマンド プロンプトを開き、証明書ジェネレーター スクリプトに移動して、プロジェクトをビルドします。

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

2. 独自の _certificate-name_ を使用してスクリプトを実行し、"_リーフ_" X.509 証明書を作成します。 リーフ証明書の共通名は[登録 ID](./concepts-service.md#registration-id) になるため、必ず小文字の英数字とハイフンのみを使用してください。

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Git Bash プロンプトで、次のコマンドを実行します。

    # <a name="windows"></a>[Windows](#tab/windows)

    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > サブジェクト名 (`//CN=Python-device-01`) に追加されているスラッシュは、Windows プラットフォーム上の Git で文字列をエスケープする場合にのみ必要となります。

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```

    ---

2. **PEM のパス フレーズを入力** するよう求められたら、パス フレーズ `1234` を使用します。

3. **PEM パス フレーズの確認入力** を求められたら、同じパス フレーズ `1234` をもう一度使用します。

これで、`openssl` コマンドを実行したディレクトリに、テスト証明書ファイル (*python-device.pem*) と秘密キー ファイル (*python-device.key.pem*) が生成されるはずです。

::: zone-end

::: zone pivot="programming-language-java"

1. 前の手順のコマンド プロンプトを使用して、`target` フォルダーに移動します。

2. 前のセクションで作成した .jar ファイルを実行します。

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

3. _[Do you want to input common name]\(共通名を入力しますか\)_ に「**N**」と入力します。 

4. `Client Cert` の出力の *-----BEGIN CERTIFICATE-----* から *-----END CERTIFICATE-----* までをクリップボードにコピーします。

   ![個別証明書ジェネレーター](./media/quick-create-simulated-device-x509/cert-generator-java.png)

5. Windows コンピューターに *_X509individual.pem_* という名前のファイルを作成します。

6. 任意のエディターで *_X509individual.pem_* を開き、クリップボードの内容をこのファイルにコピーします。 

7. このファイルを保存して、エディターを閉じます。

8. コマンド プロンプトから _[Do you want to input Verification Code]\(確認コードを入力しますか\)_ に「**N**」と入力し、クイックスタートで後から参照できるようにプログラムの出力を開いたままにします。 次のセクションで使用するために、`Client Cert` と `Client Cert Private Key` の値をコピーします。

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

::: zone pivot="programming-language-ansi-c"

6. **[登録の追加]** ページで、次の情報を入力します。

    * **メカニズム:** ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
    * **プライマリ証明書の .pem ファイルまたは .cer ファイル:** **[ファイルの選択]** を選び、前のセクションで作成した証明書ファイル *X509testcert.pem* を選択します。
    * **IoT Hub のデバイス ID**:デバイスに ID を割り当てるために、「*test-docs-cert-device*」と入力します。

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 構成証明を使用して、個別登録としてデバイスを追加する。":::

::: zone-end

::: zone pivot="programming-language-csharp"

6. **[登録の追加]** ページで、次の情報を入力します。

    * **メカニズム:** ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
    * **プライマリ証明書の .pem ファイルまたは .cer ファイル:** **[ファイルの選択]** を選び、前のセクションで作成した証明書ファイル *certificate.cer* を選択します。
    * **[IoT Hub のデバイス ID]** は空白のままにします。 デバイス ID が X.509 証明書の共通名 (CN) *iothubx509device1* に設定されたデバイスがプロビジョニングされます。 この共通名は、個別登録エントリの登録 ID に使用される名前にもなります。
    * 必要に応じて、次の情報を入力することができます。
        * プロビジョニング サービスにリンクされた IoT ハブを選択します。
        * **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 構成証明を使用して、個別登録としてデバイスを追加する。":::

::: zone-end

::: zone pivot="programming-language-nodejs"

6. **[登録の追加]** ページで、次の情報を入力します。

    * **メカニズム:** ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
    * **プライマリ証明書の .pem ファイルまたは .cer ファイル:** **[ファイルの選択]** を選び、前のセクションで作成した証明書ファイル *{certificate-name}_cert.pem* を選択します。
    * 必要に応じて、次の情報を入力することができます。
        * プロビジョニング サービスにリンクされた IoT ハブを選択します。
        * 一意のデバイス ID を入力します。 デバイスに名前を付ける際に機密データを含めないようにしてください。
        * **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 構成証明を使用して、個別登録としてデバイスを追加する。":::

::: zone-end

::: zone pivot="programming-language-python"

6. **[登録の追加]** ページで、次の情報を入力します。

    * **メカニズム:** ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
    * **プライマリ証明書の .pem ファイルまたは .cer ファイル:** 先ほど作成したテスト証明書を使用する場合は、 **[ファイルの選択]** を選び、証明書ファイル *python-device.pem* を選択します。
    * 必要に応じて、次の情報を入力することができます。
        * プロビジョニング サービスにリンクされた IoT ハブを選択します。
        * **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 構成証明を使用して、個別登録としてデバイスを追加する。":::

::: zone-end

::: zone pivot="programming-language-java"

6. **[登録の追加]** パネルで、次の情報を入力します。
   * ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
   * *[Primary certificate .pem or .cer file]\(プライマリ証明書 .pem または .cer ファイル\)* の *[ファイルの選択]* を選択し、前の手順で作成した証明書ファイル *X509individual.pem* を選択します。  
   * 必要に応じて、次の情報を入力することができます。
     * プロビジョニング サービスにリンクされた IoT ハブを選択します。
     * 一意のデバイス ID を入力します。 デバイスに名前を付ける際に機密データを含めないようにしてください。 
     * **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。
    
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 構成証明を使用して、個別登録としてデバイスを追加する。":::

::: zone-end
    
7. **[保存]** を選択します。 **[登録の管理]** に戻ります。

8. **[個別の登録]** を選択します。 X.509 登録エントリが登録テーブルに表示されるはずです。

## <a name="prepare-and-run-the-device-provisioning-code"></a>デバイス プロビジョニング コードを準備して実行する

::: zone pivot="programming-language-ansi-c"

このセクションでは、デバイスのブート シーケンスを Device Provisioning Service インスタンスに送信するようにサンプル コードを更新します。 このブート シーケンスにより、デバイスが認識され、Device Provisioning Service インスタンスにリンクされた IoT ハブに割り当てられます。

1. Azure portal で、Device Provisioning Service の **[概要]** タブを選択します。

2. **_[ID スコープ]_** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="ポータルから ID スコープをコピーする。":::

3. Visual Studio の "*ソリューション エクスプローラー*" ウィンドウで、**Provision\_Samples** フォルダーに移動します。 **prov\_dev\_client\_sample** という名前のサンプル プロジェクトを展開します。 **Source Files** を展開し、**prov\_dev\_client\_sample.c** を開きます。

4. 定数 `id_scope` を探し、以前にコピーした **ID スコープ** の値で置き換えます。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 同じファイル内で `main()` 関数の定義を探します。 以下に示すように `hsm_type` 変数が `SECURE_DEVICE_TYPE_TPM` ではなく `SECURE_DEVICE_TYPE_X509` に設定されていることを確認します。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. **prov\_dev\_client\_sample** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

7. Visual Studio のメニューで **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、ソリューションを実行します。 プロジェクトをリビルドするよう求められたら、 **[はい]** を選択して、プロジェクトをリビルドしてから実行します。

    次の出力は、プロビジョニング デバイス クライアントのサンプルが正常に起動し、Provisioning Service インスタンスに接続して、IoT ハブの情報を取得し、登録した場合の例です。

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Azure portal で、Device Provisioning Service の **[概要]** タブを選択します。

2. **_[ID スコープ]_** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="ポータルから ID スコープをコピーする。":::

3. コマンド プロンプト ウィンドウを開きます。

4. 次のコマンドを入力して、x.509 デバイス プロビジョニング サンプルをビルドして実行します (`<IDScope>` の値は、前のセクションでコピーした ID スコープに置き換えます)。 証明書ファイルは既定の *./certificate.pfx* になり、.pfx のパスワードを入力するように求められます。 パスワードを入力します。

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    すべての指定項目をパラメーターで渡す場合は、次の例に示す形式を使用してください。

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```

5. これでデバイスが DPS に接続されて、IoT ハブに割り当てられるようになります。 その後、デバイスからハブにテレメトリ メッセージが送信されます。

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Azure portal で、Device Provisioning Service の **[概要]** タブを選択します。 

2. **_[ID スコープ]_** と **[グローバル デバイス エンドポイント]** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="ポータルから ID スコープをコピーする。":::

3. "_証明書_" と "_キー_" を同じフォルダーにコピーします。

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

4. デバイスのテスト スクリプトに移動し、プロジェクトをビルドします。

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

5. **register\_x509.js** ファイルを次のように変更して編集します。

    * `provisioning host` を、上記の **手順 1** で書き留めた " **_グローバル デバイス エンドポイント_** " に置き換えます。
    * `id scope` を、上記の **手順 1** で書き留めた " **_ID スコープ_** " に置き換えます。 
    * `registration id` を、前のセクションで書き留めた " **_登録 ID_** " に置き換えます。
    * `cert filename` と `key filename` を、上記の **手順 2** でコピーしたファイルに置き換えます。

6. ファイルを保存します。

7. スクリプトを実行し、デバイスが正常にプロビジョニングされたことを検証します。

    ```cmd/sh
    node register_x509.js
    ``` 

>[!TIP]
>[Azure IoT Hub Node.js device SDK](https://github.com/Azure/azure-iot-sdk-node) を使用すれば、デバイスを簡単にシミュレートできます。 詳細については、[デバイスの概念](./concepts-service.md)に関するページを参照してください。

::: zone-end

::: zone pivot="programming-language-python"

Python プロビジョニング サンプル [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) は、`azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` ディレクトリに格納されています。 DPS を使用して IoT デバイスの認証とプロビジョニングを行うために、このサンプルでは 6 つの環境変数を使用しています。 それらの環境変数を次に示します。

| 変数名              | 説明                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  この値は、DPS リソースへの接続に使用されるグローバル エンドポイントです |    
| `PROVISIONING_IDSCOPE`     |  この値は、DPS リソースの ID スコープです |    
| `DPS_X509_REGISTRATION_ID` |  この値は、デバイスの ID です。 デバイス証明書上のサブジェクト名とも一致している必要があります |    
| `X509_CERT_FILE`           |  デバイス証明書のファイル名 |    
| `X509_KEY_FILE`            |  デバイス証明書の秘密キーのファイル名 |
| `PASS_PHRASE`              |  証明書と秘密キー ファイルを暗号化する際に使用したパス フレーズ (`1234`)。 |    

1. Azure portal で、Device Provisioning Service の **[概要]** タブを選択します。

2. **_[ID スコープ]_** と **[グローバル デバイス エンドポイント]** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="ポータルから ID スコープをコピーする。":::

3. Git Bash プロンプトで、次のコマンドを使用して、グローバル デバイス エンドポイントと ID スコープの環境変数を追加します。

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

4. IoT デバイスの登録 ID は、そのデバイス証明書上のサブジェクト名と一致している必要があります。 自己署名テスト証明書を生成した場合は、`Python-device-01` がサブジェクト名と、デバイスの登録 ID の両方となります。

    既にデバイスの証明書がある場合は、以下のように、`certutil` を使用して、デバイスに使用されているサブジェクトの共通名を検証できます。

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

5. Git Bash プロンプトで、次のように登録 ID の環境変数を設定します。

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

6. Git Bash プロンプトで、証明書ファイル、秘密キー ファイル、パス フレーズに使用する環境変数を設定します。

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

7. [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) のコードを確認します。 **Python バージョン 3.7** 以降を使用しない場合は、[こちらに記載されているコード変更](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk)を行って `asyncio.run(main())` を置き換えてください。

8. 変更を保存します。

9. サンプルを実行します。 このサンプルは、デバイスをハブに接続してプロビジョニングし、そのハブにいくつかのテスト メッセージを送信します。

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Azure portal で、Device Provisioning Service の **[概要]** タブを選択します。

2. **_[ID スコープ]_** と **[グローバル デバイス エンドポイント]** の値をコピーします。

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="ポータルから ID スコープをコピーする。":::

3. コマンド プロンプトを開きます。 Java SDK リポジトリのサンプル プロジェクト フォルダーに移動します。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

4. プロビジョニング サービスと X.509 の ID 情報をコードに入力します。 これはプロビジョニング中、デバイス登録の前に、シミュレートされたデバイスの構成証明に使用されます。

   * ファイル `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` を編集して、前にメモした "_ID スコープ_" と "_プロビジョニング サービス グローバル エンドポイント_" を含めます。 また、前のセクションでメモした "_クライアント証明書_" と "_クライアント証明書の秘密キー_" も含めます。

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   * 証明書と秘密キーをコピー/貼り付ける際は、次の形式を使用します。
        
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

5. サンプルをビルドしてから、`target` フォルダーに移動し、作成した .jar ファイルを実行します。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>デバイス プロビジョニングの登録を確認する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. デバイスが割り当てられた IoT ハブを選択します。

4. **[エクスプローラー]** メニューで、 **[IoT デバイス]** を選択します。

5. デバイスが正常にプロビジョニングされた場合は、デバイス ID が一覧に表示され、 **[状態]** が *[有効]* として設定されているはずです。 デバイスが表示されていない場合は、ページの上部にある **[最新の情報に更新]** を選択します。

   :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration.png" alt-text="IoT ハブに登録されたデバイス":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-csharp.png" alt-text="CSharp デバイスが IoT ハブに登録されている":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-nodejs.png" alt-text="Node.js デバイスが IoT ハブに登録されている":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-python.png" alt-text="Python デバイスが IoT ハブに登録されている":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-java.png" alt-text="Java デバイスが IoT ハブに登録されている":::

    ::: zone-end


::: zone pivot="programming-language-csharp,programming-language-nodejs,programming-language-python,programming-language-java"

>[!IMPORTANT]
>*[Initial device twin state]\(初期のデバイス ツインの状態\)* をデバイスの登録エントリの既定値から変更した場合、デバイスはハブから目的のツインの状態をプルし、それに従って動作することができます。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。

::: zone-end

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

プログラムによる X.509 デバイスの登録方法について学習する場合は、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure クイックスタート - X.509 デバイスを Azure IoT Hub Device Provisioning Service に登録する](quick-enroll-device-x509.md)