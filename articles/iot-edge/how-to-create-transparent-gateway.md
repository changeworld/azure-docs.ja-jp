---
title: "Azure IoT Edge で透過的なゲートウェイ デバイスを作成する | Microsoft Docs"
description: "Azure IoT Edge を使用して、複数のデバイスの情報を処理できる透過的なゲートウェイ デバイスを作成します"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: eaabf360eec48fad6bb1b8b889f30d746520eef0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>透過的なゲートウェイとして動作する IoT Edge デバイスを作成する - プレビュー

[IoT Edge デバイスをゲートウェイとして使用する方法][lnk-edge-as-gateway]に関する記事では、IoT Edge デバイスを透過的なゲートウェイとして使用する方法や、プロトコルまたは ID 変換を実行する方法に関する概念の概要が説明されています。 この記事では、IoT Edge デバイスを透過的なゲートウェイとして使用する場合の詳細な手順について説明します。 以降、この記事に記載される *IoT Edge ゲートウェイ*とは、透過的なゲートウェイとして使用される IoT Edge デバイスを指します。

>[!NOTE]
>現時点では:
> * ダウン ストリーム デバイスは、ゲートウェイが IoT Hub から切断された場合、ゲートウェイで認証することはできません。
> * また、IoT Edge デバイスは、IoT Edge ゲートウェイに接続できません。

## <a name="use-the-azure-iot-device-sdk"></a>Azure IoT device SDK の使用


すべての IoT Edge デバイスにインストールされている Edge ハブは、次のプリミティブをダウンストリーム デバイスに公開します。

* デバイスからクラウドおよびクラウドからデバイスへのメッセージ
* ダイレクト メソッド
* デバイスのツイン操作。

>[!NOTE]
>現時点では、ダウンストリーム デバイスは、IoT Edge ゲートウェイを介して接続するときに、ファイルのアップロードを使用することはできません。

Azure IoT device SDK を使用してデバイスを IoT Edge ゲートウェイに接続する場合、以下を行う必要があります。

* ゲートウェイ デバイスの ホスト名を参照する接続文字列を使用してダウンストリーム デバイスをセットアップし、
* ダウンストリーム デバイスが、ゲートウェイ デバイスによる接続の受け入れに使用された証明書を信頼していることを確認します。

制御スクリプトを使用して Azure IoT Edge ランタイムをインストールすると、チュートリアル「[Windows][lnk-tutorial1-win] および [Linux][lnk-tutorial1-lin] 上のシミュレートされたデバイスに IoT Edge をインストール」で実行したのと同じように、Edge ハブの証明書が作成されます。 この証明書は、Edge ハブが着信 TLS 接続を受け入れるために使用され、ゲートウェイ デバイスに接続するときにダウンストリーム デバイスによって信頼される必要があります。

デバイス ゲートウェイ トポロジに必要な信頼を有効にする証明書インフラストラクチャを作成できます。 この記事では、[X.509 CA セキュリティ][lnk-iothub-x509]を IoT Hub で 有効にするために使用するのと同じ証明書のセットアップが前提となっています。これには、X.509 CA 証明書が特定の IoT Hub (*IoT Hub 所有者の CA*) および一連の証明書に関連付けられており、この CA で署名されており、IoT Edge デバイスにインストールされていることが関係しています。

>[!IMPORTANT]
>現時点では、IoT Edge デバイスおよびダウン ストリーム デバイスは、[SAS トークン][lnk-iothub-tokens]のみを使用して IoT Hub で認証することができます。 この証明書は、リーフとゲートウェイ デバイス間の TLS 接続の検証にのみ使用されます。

この構成では **IoT ハブ所有者 CA** を以下として使用します。
* すべての IoT Edge デバイスで IoT Edge ランタイムをセットアップするための署名証明書
* ダウンストリーム デバイスにインストールされている公開キー証明書。

これにより、同じ IoT ハブに接続されている限り、すべてのデバイスで任意の IoT Edge デバイスをゲートウェイとして使用するためのソリューションが実現します。

### <a name="create-the-certificates-for-test-scenarios"></a>テスト シナリオ用に証明書を作成する

「[CA 証明書のサンプルを管理する][lnk-ca-scripts]」に記載されているサンプルの Powershell および Bash スクリプトを使用して、自己署名の **IoT ハブ所有者 CA** およびそれを使用して署名されたデバイスの証明書を生成できます。

1. 手順 1 の「[CA 証明書のサンプルを管理する][lnk-ca-scripts]」に従ってスクリプトをインストールします。 必ず `modules-preview` ブランチから複製します。
                
                git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
2. 手順 2 に従って、**IoT ハブ所有者 CA** を生成します。このファイルは、ダウン ストリーム デバイスが接続を検証するために使用されます。

次の指示に従って、ゲートウェイ デバイスの証明書を生成します。

#### <a name="bash"></a>Bash

* `./certGen.sh create_edge_device_certificate myGateway` を実行して新しいデバイスの証明書を作成します。  
  これにより、公開キーおよび PFX が含まれる .\certs\new-edge-device.*と、デバイスの秘密キーが含まれるファイル .\private\new-edge-device.key.pem が作成されます。  
* `certs` ディレクトリで `cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem` を実行して、デバイスの公開キーの完全なチェーンを取得します。
* `./private/new-edge-device.cert.pem` にはデバイスの秘密キーが含まれています。

#### <a name="powershell"></a>Powershell

* `New-CACertsEdgeDevice myGateway` を実行して新しいデバイスの証明書を作成します。
  これにより、この証明書の公開キー、秘密キー、および PFX を含むファイル myEdgeDevice* が作成されます。  署名プロセス中にパスワードの入力を求められたら、「1234」と入力してください。


>[!IMPORTANT]
>このサンプルは、テストのみを目的としたものです。 実動シナリオでは、IoT ソリューションをセキュリティ保護し、それに応じて証明書をプロビジョニングする方法については、「[IoT デプロイのセキュリティ保護][lnk-iothub-secure-deployment]」を参照してください。

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>IoT Edge デバイスをゲートウェイとして構成する

IoT Edge デバイスをゲートウェイとして 構成するには、前のセクションで作成したデバイスの証明書を使用するように構成するだけで十分です。

上記のサンプル スクリプトにある次のファイル名を使用するとします。

| 出力 | Bash スクリプト | Powershell |
| ------ | ----------- | ---------- |
| デバイスの証明書 | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| デバイスの秘密キー | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| デバイスの証明書チェーン | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| IoT Hub 所有者 CA | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 「[Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] でシミュレートされたデバイスに Azure IoT Edge をデプロイする」で説明されているインストールと同様に、上記の情報を IoT Edge ランタイムに入力する必要があります。 
 
 Linux の場合:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

Windows の場合:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

既定では、サンプル スクリプトは、パスフレーズをデバイスの秘密キーに設定しません。 パスフレーズを設定する場合は、次のパラメーターを追加します。

        --device-ca-passphrase {passphrase}

スクリプトは、Edge Agent 証明書のパスフレーズを設定するようにプロンプトを出します。
このコマンドの後、IoT Edge ランタイムを再起動する必要があります。

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>ダウンストリームのデバイスとして IoT Hub デバイス アプリケーションを構成する

ダウンストリーム デバイスには、「[.NET を使用してデバイスを IoT Hub にデバイスを接続する][lnk-iothub-getstarted]」で説明されている単純なアプリケーションなど、[Azure IoT device SDK][lnk-devicesdk] を使用する任意のアプリケーションを指定できます。

まず、ダウンストリーム デバイス アプリケーションは、ゲートウェイ デバイスへの TLS 接続を検証するために **IoT Hub 所有者の CA** 証明書を信頼する必要があります。 この手順は通常、OS レベル、または (特定の言語の場合) アプリケーション レベルの 2 つの方法で実行できます。

たとえば、.NET アプリケーションの場合、次のスニペットを追加してパス `certPath` に格納されている PEM 形式の証明書を信頼することができます。 上記のスクリプトを使用する場合、パスは `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) または `RootCA.pem` (Powershell) を参照します。

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

OS レベルでこの手順を実行することは、Windows と Linux ディストリビューションでは異なります。

2 番目の手順では、ゲートウェイ デバイスのホスト名を参照する接続文字列を使用して、IoT Hub device sdk を初期化します。
これを行うには、`GatewayHostName` プロパティをデバイスの接続文字列を追加します。 デバイスの接続文字列の例を次に示します。これには `GatewayHostName` プロパティが追加されています。

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

これら 2 つの手順によって、デバイス アプリケーションをゲートウェイ デバイスに接続できるようになります。

## <a name="next-steps"></a>次のステップ
[IoT Edge モジュールを開発するための要件と ツールについて理解します][lnk-module-dev]。

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus