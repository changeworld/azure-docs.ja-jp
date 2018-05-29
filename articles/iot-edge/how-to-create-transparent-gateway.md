---
title: Azure IoT Edge で透過的なゲートウェイ デバイスを作成する | Microsoft Docs
description: Azure IoT Edge を使用して、複数のデバイスの情報を処理できる透過的なゲートウェイ デバイスを作成します
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0378cb2964a496a2bfe5a0bc08296cbab462a409
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170712"
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>透過的なゲートウェイとして動作する IoT Edge デバイスを作成する - プレビュー

この記事では、IoT Edge デバイスを透過的なゲートウェイとして使用する場合の詳細な手順について説明します。 以降、この記事に記載される "*IoT Edge ゲートウェイ*" という用語は、透過的なゲートウェイとして使われる IoT Edge デバイスを指します。 概念に関する詳細については、「[IoT Edge デバイスをゲートウェイとして使用する方法][lnk-edge-as-gateway]」をご覧ください。 

>[!NOTE]
>現時点では:
> * ゲートウェイが IoT Hub から切断された場合、ダウンストリーム デバイスはゲートウェイで認証を行うことができません。
> * また、IoT Edge デバイスは、IoT Edge ゲートウェイに接続できません。

## <a name="understand-the-azure-iot-device-sdk"></a>Azure IoT device SDK の概要


すべての IoT Edge デバイスにインストールされている Edge ハブは、次のプリミティブをダウンストリーム デバイスに公開します。

* デバイスからクラウドおよびクラウドからデバイスへのメッセージ
* ダイレクト メソッド
* デバイスのツイン操作

現時点では、ダウンストリーム デバイスは、IoT Edge ゲートウェイを介して接続するときに、ファイルのアップロードを使用することはできません。

Azure IoT device SDK を使用してデバイスを IoT Edge ゲートウェイに接続する場合、以下を行う必要があります。

* ゲートウェイ デバイスの ホスト名を参照する接続文字列を使用してダウンストリーム デバイスをセットアップし、
* ダウンストリーム デバイスが、ゲートウェイ デバイスによる接続の受け入れに使用された証明書を信頼していることを確認します。

制御スクリプトを使用して Azure IoT Edge ランタイムをインストールすると、チュートリアル「[Windows][lnk-tutorial1-win] および [Linux][lnk-tutorial1-lin] 上のシミュレートされたデバイスに IoT Edge をインストール」で実行したのと同じように、Edge ハブの証明書が作成されます。 この証明書は、Edge ハブが着信 TLS 接続を受け入れるために使用され、ゲートウェイ デバイスに接続するときにダウンストリーム デバイスによって信頼される必要があります。

デバイス ゲートウェイ トポロジに必要な信頼を有効にする証明書インフラストラクチャを作成できます。 この記事では、[X.509 CA セキュリティ][lnk-iothub-x509]を IoT Hub で有効にするために使用するのと同じ証明書のセットアップが前提となっています。これには、X.509 CA 証明書が特定の IoT Hub (*IoT Hub 所有者の CA*) および一連の証明書に関連付けられており、この CA で署名されており、IoT Edge デバイスにインストールされていることが関係しています。

>[!IMPORTANT]
>現時点では、IoT Edge デバイスおよびダウン ストリーム デバイスは、[SAS トークン][lnk-iothub-tokens]のみを使用して IoT Hub で認証することができます。 この証明書は、リーフとゲートウェイ デバイス間の TLS 接続の検証にのみ使用されます。

この構成では、**IoT ハブ所有者 CA** を次の両方として使います。
* すべての IoT Edge デバイスで IoT Edge ランタイムをセットアップするための署名証明書。
* ダウンストリーム デバイスにインストールされている公開キー証明書。

これにより、同じ IoT ハブに接続されている限り、すべてのデバイスで任意の IoT Edge デバイスをゲートウェイとして使用するためのソリューションが実現します。

## <a name="create-the-certificates-for-test-scenarios"></a>テスト シナリオ用に証明書を作成する

「[CA 証明書のサンプルを管理する][lnk-ca-scripts]」に記載されているサンプルの Powershell および Bash スクリプトを使用して、自己署名の **IoT ハブ所有者 CA** およびそれを使用して署名されたデバイスの証明書を生成できます。

>[!IMPORTANT]
>このサンプルは、テストのみを目的としたものです。 実動シナリオでは、IoT ソリューションをセキュリティ保護し、それに応じて証明書をプロビジョニングする方法については、「[IoT デプロイのセキュリティ保護][lnk-iothub-secure-deployment]」を参照してください。


1. GitHub から Microsoft Azure IoT SDK と C のライブラリを複製します。

   ```cmd/sh
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. 証明書スクリプトをインストールするには、「[Managing CA Certificate Sample][lnk-ca-scripts]」(CA 証明書の管理サンプル) の「**Step 1 - Initial Setup**」(ステップ 1 - 初期セットアップ) の説明に従います。 
3. **IoT Hub 所有者 CA** を生成するには、「**Step 2 - Create the certificate chain**」(ステップ 2 -証明書チェーンを作成する) の説明に従います。 このファイルは、接続を検証するためにダウンストリームのデバイスで使われます。
4. ゲートウェイ デバイスの証明書を生成するには、Bash または PowerShell の手順を使います。

### <a name="bash"></a>Bash

新しいデバイス証明書を作成します。  `myGatewayCAName` にゲートウェイ ホストと同じ名前を**付けないでください**。  そのようにすると、これらの証明書に対するクライアント証明が失敗します。

   ```bash
   ./certGen.sh create_edge_device_certificate myGatewayCAName
   ```

新しいファイルが作成されます .\certs\new-edge-device.* には公開キーと PFX が含まれ、.\private\new-edge-device.key.pem にはデバイスの秘密キーが含まれます。
 
`certs` ディレクトリで次のコマンドを実行して、デバイスの公開キーの完全なチェーンを取得します。

   ```bash
   cd ./certs
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>Powershell

新しいデバイス証明書を作成します。 
   ```powershell
   New-CACertsEdgeDevice myGateway
   ```

この証明書の公開キー、秘密キー、および PFX を含む新しい myEdgeDevice* ファイルが作成されます。 

署名プロセス中にパスワードの入力を求められたら、「1234」と入力してください。

## <a name="configure-a-gateway-device"></a>ゲートウェイ デバイスを構成する

IoT Edge デバイスをゲートウェイとして 構成するには、前のセクションで作成したデバイスの証明書を使用するように構成するだけで十分です。

上記のサンプル スクリプトにある次のファイル名を使用するとします。

| 出力 | ファイル名 |
| ------ | --------- |
| デバイスの証明書 | `certs/new-edge-device.cert.pem` |
| デバイスの秘密キー | `private/new-edge-device.cert.pem` |
| デバイスの証明書チェーン | `certs/new-edge-device-full-chain.cert.pem` |
| IoT Hub 所有者 CA | `certs/azure-iot-test-only.root.ca.cert.pem`  |

IoT Edge ランタイムにデバイスと証明書の情報を提供します。 
 
Linux では、Bash の出力を使います。

   ```bash
   sudo iotedgectl setup --connection-string {device connection string} \
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com} \
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem \
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem \
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem \
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

Windows では、PowerShell の出力を使います。

   ```powershell
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

既定では、サンプル スクリプトは、パスフレーズをデバイスの秘密キーに設定しません。 パスフレーズを設定する場合は、次のパラメーターを追加します: `--device-ca-passphrase {passphrase}`。

スクリプトで、Edge Agent 証明書のパスフレーズを設定するように要求されます。 このコマンドの後で、IoT Edge ランタイムを再起動します。

   ```cmd/sh
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>ダウンストリームのデバイスを構成する

ダウンストリーム デバイスには、「[.NET を使用してデバイスを IoT Hub にデバイスを接続する][lnk-iothub-getstarted]」で説明されている単純なアプリケーションなど、[Azure IoT device SDK][lnk-devicesdk] を使用する任意のアプリケーションを指定できます。

まず、ダウンストリーム デバイス アプリケーションは、ゲートウェイ デバイスへの TLS 接続を検証するために **IoT Hub 所有者の CA** 証明書を信頼する必要があります。 この手順は通常、OS レベル、または (特定の言語の場合) アプリケーション レベルの 2 つの方法で実行できます。

たとえば、.NET アプリケーションの場合、次のスニペットを追加してパス `certPath` に格納されている PEM 形式の証明書を信頼することができます。 使ったスクリプトのバージョンに応じて、パスは `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) または `RootCA.pem` (Powershell) を参照します。

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

OS レベルでこの手順を実行することは、Windows と Linux ディストリビューションでは異なります。

2 番目の手順では、ゲートウェイ デバイスのホスト名を参照する接続文字列を使用して、IoT Hub device sdk を初期化します。
これを行うには、`GatewayHostName` プロパティをデバイスの接続文字列を追加します。 デバイスの接続文字列の例を次に示します。これには `GatewayHostName` プロパティが追加されています。

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

これら 2 つの手順によって、デバイス アプリケーションをゲートウェイ デバイスに接続できるようになります。

## <a name="next-steps"></a>次の手順
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
