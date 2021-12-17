---
title: Azure IoT Central アプリケーションで X.509 証明書を使用するデバイスを接続する
description: IoT Central アプリケーション用の Node.js デバイス SDK を使用して、x.509 証明書を使用するデバイスを接続する方法
author: dominicbetts
ms.author: dobett
ms.date: 06/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 308cd5186d97e87ff044db496809b04def41265b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667126"
---
# <a name="how-to-connect-devices-with-x509-certificates-to-iot-central-application"></a>X.509 証明書を使用するデバイスを Azure IoT Central アプリケーションに接続する方法

IoT Central では、デバイスとアプリケーションの間の通信をセキュリティで保護するために、共有アクセス署名 (SAS) と X.509 証明書の両方がサポートされています。 SAS は、「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device.md)」チュートリアルで使用されます。 この記事では、X.509 証明書を使用するようにコード サンプルを変更する方法について説明します。 運用環境では、X.509 証明書はお勧めしません。 詳細については、「[Azure IoT Central に接続する](./concepts-get-connected.md)」を参照してください。

このガイドでは、X.509 証明書の 2 つの使用方法を示します。通常は運用環境で使用される[グループ登録](how-to-connect-devices-x509.md#use-group-enrollment)と、テストに役立つ[個別登録](how-to-connect-devices-x509.md#use-individual-enrollment)です。 また、証明書の有効期限が切れても接続性を維持できるように、[デバイス証明書を展開する](#roll-x509-device-certificates)方法についても説明します。

このガイドは、[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)チュートリアルに示されている、C#、Java、JavaScript、Python を使用するサンプルに基づいています。 C プログラミング言語を使用した例については、[登録グループを使って複数の X.509 デバイスをプロビジョニングする](../../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md)チュートリアルを参照してください。

## <a name="prerequisites"></a>前提条件

[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device.md)チュートリアルを完了していること。 これには、選択したプログラミング言語の前提条件となるもののインストールも含まれます。

この攻略ガイドでは、テスト用の X.509 証明書を複数生成します。 これらの証明書を生成するには、次のものが必要です。

- [Node.js](https://nodejs.org/) バージョン 6 以降がインストールされた開発用マシン。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 このチュートリアルの手順は、Windows コマンド プロンプトから **node** コマンドを実行することを前提としています。 ただし、他の多くのオペレーティング システムで Node.js を使用できます。
- テスト用の X.509 証明書を生成するスクリプトが格納された [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub リポジトリのローカル コピー。 このリンク ([ZIP のダウンロード](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip)) を使用してリポジトリのコピーをダウンロードしてください。 次に、ローカル コンピューター上の適切な場所にファイルを解凍します。

## <a name="use-group-enrollment"></a>グループ登録を使用する

運用環境のグループ登録では X.509 証明書を使用します。 グループ登録では、IoT Central アプリケーションにルートまたは中間の X.509 証明書が追加されます。 ルートまたは中間の証明書から派生したリーフ証明書を使用するデバイスをアプリケーションに接続できます。

### <a name="generate-root-and-device-certificates"></a>ルート証明書とデバイス証明書を生成する

このセクションでは、X.509 証明書を使用して、IoT Central 登録グループの証明書から派生した証明書によりデバイスを接続します。

> [!WARNING]
> この X.509 証明書の生成方法は、テストのみを目的としています。 運用環境では、証明書を生成するためのセキュリティで保護された正式なメカニズムを使用する必要があります。

1. ダウンロードした Microsoft Azure IoT SDK for Node.js で、証明書生成スクリプトがある場所に移動します。 必要なパッケージをインストールします。

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. ルート証明書を作成してから、スクリプトを実行してデバイス証明書を取得します。

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > デバイス ID には、文字、数字、および `-` 文字を含めることができます。

これらのコマンドによって、以下のルート証明書とデバイス証明書が生成されます

| filename | 目次 |
| -------- | -------- |
| mytestrootcert_cert.pem | X.509 ルート証明書の公開部分 |
| mytestrootcert_key.pem | X.509 ルート証明書の秘密キー |
| mytestrootcert_fullchain.pem | X.509 ルート証明書のキーチェーン全体。 |
| sampleDevice01_cert.pem | X.509 デバイス証明書の公開部分 |
| sampleDevice01_key.pem | X.509 デバイス証明書の秘密キー |
| sampleDevice01_fullchain.pem | X.509 デバイス証明書のキーチェーン全体。 |

これらのファイルの場所を書き留めておいてください。 この情報は後で必要になります。

### <a name="create-a-group-enrollment"></a>グループ登録を作成する

1. IoT Central アプリケーションを開き、左側のペインで **[管理]** に移動し、 **[デバイスの接続]** を選択します。

1. **[+ 新規作成]** を選択し、構成証明書の種類として **証明書 (X.509)** を使用する、_MyX509Group_ という名前の新しい登録グループを作成します。

1. 作成した登録グループを開き、 **[プライマリの管理]** を選択します。

1. ファイル オプションを選択して、先ほど生成した _mytestrootcert_cert.pem_ という名前のルート証明書ファイルをアップロードします。

    ![証明書のアップロード](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 検証を完了するには、確認コードを生成し、それをコピーした後、それを使用してコマンド プロンプトで X.509 検証証明書を作成します。

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. **[認証]** を選択して、署名された検証証明書 _verification_cert.pem_ をアップロードして検証を完了します。

    ![検証済みの証明書](./media/how-to-connect-devices-x509/verified.png)

これで、このプライマリ ルート証明書から派生した X.509 証明書があるデバイスを接続できます。

登録グループを保存した後、ID スコープをメモしておきます。

### <a name="run-sample-device-code&quot;></a>サンプル デバイス コードを実行する

:::zone pivot="programming-language-csharp"

Windows を使用している場合、サンプルを機能させるには、X.509 証明書を Windows 証明書ストアに格納する必要があります。 証明書をストアに追加するには:

1. `openssl` を使用して、PEM ファイルから PFX ファイルを作成します。 これらのコマンドを実行すると、パスワードを作成するように求められます。 パスワードは次の手順で必要になるため、書き留めておいてください。

    ```bash
    openssl pkcs12 -inkey sampleDevice001_key.pem -in sampleDevice001_cert.pem -export -out sampledevice001.pfx
    openssl pkcs12 -inkey mytestrootcert_key.pem -in mytestrootcert_cert.pem -export -out mytestrootcert.pfx
    ```

1. Windows エクスプローラーで、各 PFX ファイルをダブルクリックします。 **証明書のインポート** ウィザードで、ストアの場所として **[現在のユーザー]** を選択し、前の手順のパスワードを入力して、ウィザードに証明書ストアを自動的に選択させます。 ウィザードにより、現在のユーザーの個人用ストアに証明書がインポートされます。

証明書を使用するようにサンプル コードを変更するには:

1. Visual Studio の **IoTHubDeviceSamples** ソリューションで、**TemperatureController** プロジェクトの *Parameter.cs* ファイルを開きます。

1. 次の 2 つのパラメーター定義をクラスに追加します。

    ```csharp
    [Option(
        'x',
        &quot;CertificatePath&quot;,
        HelpText = &quot;(Required if DeviceSecurityType is \"dps\"). \nThe device PFX file to use during device provisioning." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_CERT\".")]
    public string CertificatePath { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_CERT");

    [Option(
        'p',
        "CertificatePassword",
        HelpText = "(Required if DeviceSecurityType is \"dps\"). \nThe password of the PFX certificate file." +
        "\nDefaults to environment variable \"IOTHUB_DEVICE_X509_PASSWORD\".")]
    public string CertificatePassword { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_DEVICE_X509_PASSWORD");
    ```

    変更を保存します。

1. Visual Studio の **IoTHubDeviceSamples** ソリューションで、**TemperatureController** プロジェクトの *Program.cs* ファイルを開きます。

1. 次の `using` ステートメントを追加します。

    ```csharp
    using System.Security.Cryptography.X509Certificates;
    using System.IO;
    ```

1. 次のメソッドをクラスに追加します。

    ```csharp
    private static X509Certificate2 LoadProvisioningCertificate(Parameters parameters)
    {
        var certificateCollection = new X509Certificate2Collection();
        certificateCollection.Import(
            parameters.CertificatePath,
            parameters.CertificatePassword,
            X509KeyStorageFlags.UserKeySet);

        X509Certificate2 certificate = null;

        foreach (X509Certificate2 element in certificateCollection)
        {
            Console.WriteLine($"Found certificate: {element?.Thumbprint} {element?.Subject}; PrivateKey: {element?.HasPrivateKey}");
            if (certificate == null && element.HasPrivateKey)
            {
                certificate = element;
            }
            else
            {
                element.Dispose();
            }
        }

        if (certificate == null)
        {
            throw new FileNotFoundException($"{parameters.CertificatePath} did not contain any certificate with a private key.");
        }

        Console.WriteLine($"Using certificate {certificate.Thumbprint} {certificate.Subject}");

        return certificate;
    }
    ```

1. `SetupDeviceClientAsync` メソッド内で、`case "dps"` のコード ブロックを次のコードに置き換えます。

    ```csharp
    case "dps":
        s_logger.LogDebug($"Initializing via DPS");
        Console.WriteLine($"Loading the certificate...");
        X509Certificate2 certificate = LoadProvisioningCertificate(parameters);
        DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, certificate, cancellationToken);
        var authMethod = new DeviceAuthenticationWithX509Certificate(dpsRegistrationResult.DeviceId, certificate);
        deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
        break;
    ```

1. `ProvisionDeviceAsync` メソッドを次のコードに置き換えます。

    ```csharp
    private static async Task<DeviceRegistrationResult> ProvisionDeviceAsync(Parameters parameters, X509Certificate2 certificate, CancellationToken cancellationToken)
    {
        SecurityProvider security = new SecurityProviderX509Certificate(certificate);
        ProvisioningTransportHandler mqttTransportHandler = new ProvisioningTransportHandlerMqtt();
        ProvisioningDeviceClient pdc = ProvisioningDeviceClient.Create(parameters.DpsEndpoint, parameters.DpsIdScope, security, mqttTransportHandler);

        var pnpPayload = new ProvisioningRegistrationAdditionalData
        {
            JsonData = PnpConvention.CreateDpsPayload(ModelId),
        };
        return await pdc.RegisterAsync(pnpPayload, cancellationToken);
    }
    ```

    変更を保存します。

1. 次の環境変数をプロジェクトに追加します。

    - `IOTHUB_DEVICE_X509_CERT`: `<full path to folder that contains PFX files>sampleDevice01.pfx`
    - `IOTHUB_DEVICE_X509_PASSWORD`: *sampleDevice01.pfx* ファイルの作成時に使用したパスワード。

1. アプリケーションをビルドして実行します。 デバイスが正常にプロビジョニングされたことを確認します。

:::zone-end

:::zone pivot="programming-language-java"

1. 温度コントローラー デバイス サンプル用の *pom.xml* ファイルと *src* フォルダーが含まれる、_azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/temperature-controller-device-sample_ フォルダーに移動します。

1. *pom.xml* ファイルを編集して、`<dependencies>` ノードに次の依存関係構成を追加します。

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.sdk.iot.provisioning.security</groupId>
        <artifactId>${x509-provider-artifact-id}</artifactId>
        <version>${x509-provider-version}</version>
    </dependency>
    ```

    変更を保存します。

1. テキスト エディターで *src/main/java/samples/com/microsoft/azure/sdk/iot/device/TemperatureController.java* ファイルを開きます。

1. `SecurityProviderSymmetricKey` の import を次の import に置き換えます。

    ```java
    import com.microsoft.azure.sdk.iot.provisioning.security.SecurityProvider;
    import com.microsoft.azure.sdk.iot.provisioning.security.hsm.SecurityProviderX509Cert;
    import com.microsoft.azure.sdk.iot.provisioning.security.exceptions.SecurityProviderException;
    ```

1. 次の import を追加します。

    ```java
    import java.nio.file.*;
    ```

1. `main` メソッドでスローされる例外リストに `SecurityProviderException` を追加します。

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, ProvisioningDeviceClientException, InterruptedException, SecurityProviderException {
    ```

1. `initializeAndProvisionDevice` メソッドを次のコードに置き換えます。

    ```java
    private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException, SecurityProviderException {
        String deviceX509Key = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_KEY"))));
        String deviceX509Cert = new String(Files.readAllBytes(Paths.get(System.getenv("IOTHUB_DEVICE_X509_CERT"))));
        SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(deviceX509Cert, deviceX509Key, null);
        ProvisioningDeviceClient provisioningDeviceClient;
        ProvisioningStatus provisioningStatus = new ProvisioningStatus();

        provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityProviderX509);

        AdditionalData additionalData = new AdditionalData();
        additionalData.setProvisioningPayload(com.microsoft.azure.sdk.iot.provisioning.device.plugandplay.PnpHelper.createDpsPayload(MODEL_ID));

        provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

        while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
        {
            if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                    provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
            {
                provisioningStatus.exception.printStackTrace();
                System.out.println("Registration error, bailing out");
                break;
            }
            System.out.println("Waiting for Provisioning Service to register");
            Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
        }

        ClientOptions options = new ClientOptions();
        options.setModelId(MODEL_ID);

        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
            System.out.println("IotHUb Uri : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri());
            System.out.println("Device ID : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId());

            String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
            String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

            log.debug("Opening the device client.");
            deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityProviderX509, IotHubClientProtocol.MQTT, options);
            deviceClient.open();
        }
    }
    ```

    変更を保存します。

1. シェル環境で、次の 2 つの環境変数を追加します。 PEM ファイルへの完全なパスを指定し、オペレーティング システムに合ったパス区切り記号を使用してください。

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > その他の必要な環境変数は、[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続](./tutorial-connect-device.md)に関するチュートリアルを完了したときに設定しています。

1. アプリケーションをビルドして実行します。 デバイスが正常にプロビジョニングされたことを確認します。

:::zone-end

:::zone pivot="programming-language-javascript"

1. **pnpTemperatureController.js** アプリケーションが格納されている _azure-iot-sdk-node/device/samples/pnp_ フォルダーに移動し、次のコマンドを実行して X.509 パッケージをインストールします。

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. テキスト エディターで **pnpTemperatureController.js** ファイルを開きます。

1. `require` ステートメントを編集して、次のコードを含めます。

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. 次の 4 行を "DPS 接続情報" セクションに追加して、`deviceCert` 変数が初期化されるようにします。

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. クライアントを作成する `provisionDevice` 関数を編集し、最初の行を次のコードに置き換えます。

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. 同じ関数で、`deviceConnectionString` 変数を設定する行を次のように変更します。

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. `main` 関数で、`Client.fromConnectionString` を呼び出す行の後に次の行を追加します。

    ```javascript
    client.setOptions(deviceCert);
    ```

    変更を保存します。

1. シェル環境で、次の 2 つの環境変数を追加します。 PEM ファイルへの完全なパスを指定し、オペレーティング システムに合ったパス区切り記号を使用してください。

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > その他の必要な環境変数は、[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続](./tutorial-connect-device.md)に関するチュートリアルを完了したときに設定しています。

1. スクリプトを実行し、デバイスが正常にプロビジョニングされたことを確認します。

    ```cmd/sh
    node pnpTemperatureController.js
    ```

:::zone-end

:::zone pivot="programming-language-python"

1. _azure-iot-device/samples/pnp_ フォルダーに移動し、テキスト エディターで **temp_controller_with_thermostats.py** ファイルを開きます。

1. 次の `from` ステートメントを追加して、X.509 機能をインポートします。

    ```python
    from azure.iot.device import X509
    ```

1. `provision_device` 関数の最初の部分を次のように変更します。

    ```python
    async def provision_device(provisioning_host, id_scope, registration_id, x509, model_id):
        provisioning_device_client = ProvisioningDeviceClient.create_from_x509_certificate(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            x509=x509,
        )
    ```

1. `main` 関数で、`symmetric_key` 変数を設定する行を以下のコードに置き換えます。

    ```python
    x509 = X509(
        cert_file=os.getenv("IOTHUB_DEVICE_X509_CERT"),
        key_file=os.getenv("IOTHUB_DEVICE_X509_KEY"),
    )
    ```

1. `main` 関数で、`provision_device` 関数を呼び出す行を以下のコードに置き換えます。

    ```python
    registration_result = await provision_device(
        provisioning_host, id_scope, registration_id, x509, model_id
    )
    ```

1. `main` 関数で、`IoTHubDeviceClient.create_from_symmetric_key` 関数を呼び出す行を以下のコードに置き換えます。

    ```python
    device_client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=registration_result.registration_state.assigned_hub,
        device_id=registration_result.registration_state.device_id,
        product_info=model_id,
    )
    ```

    変更を保存します。

1. シェル環境で、次の 2 つの環境変数を追加します。 PEM ファイルへの完全なパスを指定し、オペレーティング システムに合ったパス区切り記号を使用してください。

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=<full path to folder that contains PEM files>sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=<full path to folder that contains PEM files>sampleDevice01_key.pem
    ```

    > [!TIP]
    > その他の必要な環境変数は、[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続](./tutorial-connect-device.md)に関するチュートリアルを完了したときに設定しています。

1. スクリプトを実行し、デバイスが正常にプロビジョニングされたことを確認します。

    ```cmd/sh
    python temp_controller_with_thermostats.py
    ```

:::zone-end

IoT Central アプリケーションのデバイス ビューにテレメトリが表示されることを確認します。

![IoT Central アプリケーションで受信されたテレメトリを示すスクリーンショット。](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-individual-enrollment"></a>個別登録を使用する

個別登録の X.509 証明書を使用して、デバイスとソリューションをテストします。 個別登録では、IoT Central アプリケーション内にルートまたは中間の X.509 証明書は存在しません。 デバイスでは、自己署名された X.509 証明書を使用してアプリケーションに接続します。

### <a name="generate-self-signed-device-certificate"></a>自己署名されたデバイス証明書を生成する

このセクションでは、自己署名された X.509 証明書を使用して、個別登録用のデバイスを接続します。これらは、単一のデバイスを登録するために使用されます。 自己署名証明書はテスト目的専用です。

> [!WARNING]
> この X.509 証明書の生成方法は、テストのみを目的としています。 運用環境では、証明書を生成するためのセキュリティで保護された正式なメカニズムを使用する必要があります。

次のコマンドを実行して、自己署名された X.509 デバイス証明書を作成します。

```cmd/sh
  cd azure-iot-sdk-node/provisioning/tools
  node create_test_cert.js device mytestselfcertprimary
  node create_test_cert.js device mytestselfcertsecondary 
```

> [!TIP]
> デバイス ID には、文字、数字、および `-` 文字を含めることができます。

### <a name="create-individual-enrollment"></a>個別登録を作成する

1. Azure IoT Central アプリケーションで、 **[デバイス]** を選択し、サーモスタット デバイス テンプレートから **デバイス ID** として _mytestselfcertprimary_ を使用する新しいデバイスを作成します。 **ID スコープ** の値をメモしておきます。後で使用します。

1. 作成したデバイスを開き、 **[接続]** を選択します。

1. 次のように、 **[接続方法]** として **[個別登録]** を、[メカニズム] として **[証明書 (X.509)]** を選択します。

    ![個別加入](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. プライマリ用のファイル オプションを選択し、先ほど生成した _mytestselfcertprimary_cert.pem_ という名前の証明書ファイルをアップロードします。

1. セカンダリ証明書用のファイル オプションを選択し、_mytestselfcertsecondary_cert.pem_ という名前の証明書ファイルをアップロードします。 次に、 **[保存]** を選択します。

    ![個別登録証明書のアップロード](./media/how-to-connect-devices-x509/individual-enrollment.png)

これで、デバイスに X.509 証明書がプロビジョニングされました。

### <a name="run-a-sample-individual-enrollment-device"></a>サンプルの個別登録デバイス実行する

1. _mytestselfcertprimary_key.pem_ と _mytestselfcertprimary_cert.pem_ ファイルを **pnpTemperatureController.js** アプリケーションが格納されている _azure-iot-sdk-node/device/samples/pnp_ フォルダーにコピーします。 このアプリケーションは、[デバイスの接続 (JavaScript) に関するチュートリアル](./tutorial-connect-device.md)の完了時に使用しました。

1. 上記のサンプルで使用した環境変数を次のように変更します。

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. スクリプトを実行し、デバイスが正常にプロビジョニングされたことを確認します。

    ```cmd/sh
    node environmentalSensor.js
    ```

    また、デバイス ビューにテレメトリが表示されることも確認します。

    ![テレメトリの個別登録](./media/how-to-connect-devices-x509/telemetry-primary.png)

_mytestselfcertsecondary_ 証明書に対して、上記の手順を繰り返すことができます。

## <a name="connect-an-iot-edge-device"></a>IoT Edge デバイスの接続

このセクションでは、IoT Edge デバイスへの接続にグループ登録を使用していることを前提としています。 前述のセクションの手順に従い、以下を行ってください。

- [ルート証明書とデバイス証明書を生成する](#generate-root-and-device-certificates)
- [グループ登録を作成する](#create-a-group-enrollment) <!-- No slightly different type of enrollment group - UPDATE!! -->

X.509 デバイス証明書を使用して IoT Edge デバイスを IoT Central に接続するには:

- デバイス証明書とキー ファイルを IoT Edge デバイスにコピーします。 前述のグループ登録の例では、これらのファイルの名前は **sampleDevice01_key.pem** と **sampleDevice01_cert.pem** です。
- IoT Edge デバイスで、 **/etc/iotedge/config.yaml** 構成ファイルの `provisioning` セクションを次のように編集します。

    ```yaml
    # DPS X.509 provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "<SCOPE_ID>"
      attestation:
        method: "x509"
    #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
        identity_cert: "file:///<path>/sampleDevice01_cert.pem"
        identity_pk: "file:///<path>/sampleDevice01_key.pem"
    #  always_reprovision_on_startup: true
    #  dynamic_reprovisioning: false
    ```

    > [!TIP]
    > `registration_id` の値を追加する必要はありません。 IoT Edge では X.509 証明書の **CN** 値を使用できます。

- 次のコマンドを実行して、IoT Edge ランタイムを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

詳細については、[X.509 証明書を使用して IoT Edge デバイスを Linux で大規模に作成およびプロビジョニングする](../../iot-edge/how-to-provision-devices-at-scale-linux-x509.md)方法に関する記事を参照してください。

## <a name="connect-an-iot-edge-leaf-device"></a>IoT Edge リーフ デバイスを接続する

IoT Edge では、リーフ デバイスとゲートウェイ役の IoT Edge デバイスの間の接続を保護するために X.509 証明書を使用します。 このシナリオの詳細については、「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](../../iot-edge/how-to-connect-downstream-device.md)」を参照してください。

## <a name="roll-x509-device-certificates"></a>X.509 デバイス証明書を展開する

IoT Central アプリケーションのライフサイクルの間に、X.509 証明書を展開する必要があります。 次に例を示します。

- セキュリティ違反が発生した場合、システムを保護するためのセキュリティのベスト プラクティスとして証明書を展開することをお勧めします。
- X.509 証明書には有効期限があります。 証明書の展開の頻度は、ソリューションのセキュリティのニーズに左右されます。 非常に機密性の高いデータを扱うソリューションを持つ顧客は証明書を毎日展開する場合がある一方で、数年ごとに展開する顧客もいます。

IoT Central では、接続が常時保たれるように、プライマリおよびセカンダリの X.509 証明書を構成できます。 プライマリ証明書とセカンダリ証明書の有効期限が異なれば、有効期限が切れていない方の証明書を使用してデバイスの接続を維持したまま、もう片方の証明書を展開できます。

詳細については、[侵害想定の方法](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)に関するドキュメントを参照してください。

このセクションでは、IoT Central で証明書を展開する方法について説明します。 IoT Central で証明書を展開する場合、デバイスへの新しいデバイス証明書のコピーも必要になます。

### <a name="obtain-new-x509-certificates"></a>新しい X.509 証明書を取得する

証明書プロバイダーから新しい X.509 証明書を取得します。 OpenSSL などのツールを使用して、独自の X.509 証明書を作成することができます。 この方法は X.509 証明書をテストするうえで役立ちますが、セキュリティはほとんど保証されません。 この方法は、独自の CA プロバイダーとして機能する準備ができていない場合に限り、テスト目的のみで使用してください。

### <a name="enrollment-groups-and-security-breaches"></a>登録グループとセキュリティ違反

セキュリティ違反に対する応答としてグループ登録を更新する場合、次のアプローチを使用して、現在の証明書をすぐに更新してください。 以下の手順は、プライマリとセカンダリの証明書の両方が侵害されている場合に実行します。

1. 左側のペインで **[管理]** に移動し、 **[デバイスの接続]** を選択します。

2. **[登録グループ]** を選択し、一覧からグループ名を選択します。

3. 証明書を更新する場合は、 **[プライマリの管理]** または **[Manage Secondary]\(セカンダリの管理\)** を選択します。

4. 登録グループで、ルート X.509 証明書を追加して検証します。

### <a name="individual-enrollments-and-security-breaches"></a>個々の登録とセキュリティ違反

セキュリティ違反に対する応答として証明書を展開する場合は、次のアプローチを使用して、現在の証明書をすぐに更新します。 これらの手順は、プライマリとセカンダリの証明書の両方が侵害されている場合に実行します。

1. **[デバイス]** を選択し、デバイスを選択します。

1. **[接続]** を選択し、接続方法として **[個別の登録]** を選択します。

1. メカニズムとして **[証明書 (X.509)]** を選択します。

1. 証明書を更新する場合は、フォルダー アイコンを選択し、登録エントリ用にアップロードする新しい証明書を選択します。 **[保存]** を選択します。

### <a name="enrollment-groups-and-certificate-expiration"></a>登録グループと証明書の期限切れ

証明書の有効期限切れに対応する場合は、次の方法を使用して、現在の証明書をすぐに更新します。

1. 左側のペインで **[管理]** に移動し、 **[デバイスの接続]** を選択します。

2. **[登録グループ]** を選択し、一覧からグループ名を選択します。

3. 証明書の更新については、 **[プライマリの管理]** を選択します。

4. 登録グループで、ルート X.509 証明書を追加して検証します。

5. その後、セカンダリ証明書の有効期限が切れたら、ここに戻ってセカンダリ証明書を更新します。

### <a name="individual-enrollments-and-certificate-expiration"></a>個々の登録と証明書の期限切れ

証明書の期限切れを処理するために証明書を展開する場合、プロビジョニングを試行するデバイスのダウンタイムが短縮されるように、セカンダリ証明書の構成を使用することをお勧めします。

セカンダリ証明書の期限切れが近づき、展開することが必要になったら、プライマリの構成を使用するよう切り替えることができます。 このようにプライマリとセカンダリの証明書を切り替えることで、プロビジョニングを試行するデバイスのダウンタイムを短縮できます。

1. **[デバイス]** を選択し、デバイスを選択します。

2. **[接続]** を選択し、接続方法として **[個別の登録]** を選択します。

3. メカニズムとして **[証明書 (X.509)]** を選択します。

4. セカンダリ証明書を更新する場合は、フォルダー アイコンを選択し、登録エントリ用にアップロードする新しい証明書を選択します。 **[保存]** を選択します。

5. その後、プライマリ証明書の有効期限が切れたら、ここに戻ってプライマリ証明書を更新します。

## <a name="next-steps"></a>次のステップ

これで、X.509 証明書を使用してデバイスを接続する方法を学習できました。推奨される次の手順は、[Azure CLI を使用してデバイスの接続を監視する](howto-monitor-devices-azure-cli.md)方法を確認することです。
