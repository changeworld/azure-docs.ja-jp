---
title: "Azure IoT Edge でゲートウェイ デバイスを作成する | Microsoft Docs"
description: "Azure IoT Edge を使用して、複数のデバイスの情報を処理できるゲートウェイ デバイスを作成する"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e1337ddf5ed84a06a62e2faa198f3e8fb49bc3bd
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-iot-edge-gateway-device-to-process-data-from-other-iot-devices---preview"></a>IoT Edge ゲートウェイ デバイスを作成してその他の IoT デバイスからのデータを処理する - プレビュー

IoT Edge デバイスをゲートウェイとして使用する次の 2 つの方法があります。

* [Azure IoT device SDK][lnk-devicesdk] を使用するダウンストリーム接続を接続する
* IoT Hub プロトコルを使用しないデバイスを接続する。

IoT Edge デバイスをゲートウェイとして 使用することには、次のような利点があります。

* **接続の多重化**。 IoT Edge デバイスを介して IoT Hub に接続するすべてのデバイスは、同じ基になる接続を使用します。
* **トラフィックのスムージング**。 IoT Edge デバイスは、メッセージをローカルに保持しながら、IoT Hub を調整する場合に指数のバックオフを自動的に実装します。 これにより、急増するトラフィックに対するソリューションの回復力が高まります。
* **制限されるオフライン サポート**。 ゲートウェイ デバイスは、IoT Hub に配信できなかったメッセージ をローカルに保存します。

この記事では、*IoT Edge ゲートウェイ*をゲートウェイとして使用される IoT Edge デバイスと呼びます。

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

1. 手順 1 の「[CA 証明書のサンプルを管理する][lnk-ca-scripts]」に従ってスクリプトをインストールします。
2. 手順 2 に従って、**IoT ハブ所有者 CA** を生成します。このファイルは、ダウン ストリーム デバイスが接続を検証するために使用されます。

次の指示に従って、ゲートウェイ デバイスの証明書を生成します。

#### <a name="bash"></a>Bash

* `./certGen.sh create_edge_device_certificate myGateway` を実行して新しいデバイスの証明書を作成します。  
  これにより、公開キーおよび PFX が含まれる .\certs\new-edge-device.*と、デバイスの秘密キーが含まれるファイル .\private\new-edge-device.key.pem が作成されます。  
* 公開キーを取得するための `cat new-edge-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-edge-device-full-chain.cert.pem`。
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

たとえば、.NET アプリケーションの場合、次のスニペットを追加してパス `certPath` に格納されている PEM 形式の証明書を信頼することができます。

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

上記のサンプル スクリプトによってファイル `certs/azure-iot-test-only.root.ca.cert.pem` (Bash)、または `RootCA.pem` (Powershell) に公開キーが生成されることに注意してください。

OS レベルでこの手順を実行することは、Windows と Linux ディストリビューションでは異なります。

2 番目の手順では、ゲートウェイ デバイスのホスト名を参照する接続文字列を使用して、IoT Hub device sdk を初期化します。
これを行うには、`GatewayHostName` プロパティをデバイスの接続文字列を追加します。 デバイスの接続文字列の例を次に示します。これには `GatewayHostName` プロパティが追加されています。

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

これら 2 つの手順によって、デバイス アプリケーションをゲートウェイ デバイスに接続できるようになります。

## <a name="use-other-protocols"></a>その他のプロトコルを使用する

ゲートウェイ デバイスの主な機能の 1 つは、ダウンストリーム デバイスのプロトコルを解釈することです。 IoT Hub プロトコルを使用しないデバイスを接続するには、この変換を実行する IoT Edge モジュールを開発し、ダウンストリーム デバイスに代わって IoT Hub に接続します。

*透過的*または*非透過的*なゲートウェイを作成することができます。

| &nbsp; | 透過的なゲートウェイ | 非透過的なゲートウェイ|
|--------|-------------|--------|
| IoT Hub ID レジストリに格納されている ID | 接続されているすべてのデバイスの ID | ゲートウェイ デバイスの ID のみ |
| デバイス ツイン | 各接続のデバイスには、独自のデバイス ツインが含まれている | デバイスとモジュール ツインはゲートウェイにのみ含まれている |
| ダイレクト メソッドおよびクラウドからデバイスへのメッセージ | クラウドは接続された各デバイスを個別にアドレスできる | クラウドはゲートウェイ デバイスのみをアドレスできる |
| [IoT Hub のスロットルおよびクォータ][lnk-iothub-throttles-quotas] | 各デバイスに適用する | ゲートウェイ デバイスに適用する |

非透過的なゲートウェイ パターンを使用する場合、そのゲートウェイを介して接続するすべてのデバイスは、同一のクラウドからデバイスのキューを共有します。これには最大で 50 のメッセージを含めることができます。 したがって、各フィールド ゲートウェイを介して接続するデバイスが非常に少なく、クラウドからデバイスへのトラフィックが少ない場合にのみ、非透過的なゲートウェイ パターンを使用する必要があります。

非透過的なゲートウェイを実装する場合、プロトコル変換モジュールは、モジュールの接続文字列を使用します。

透過的なゲートウェイを実装する場合、モジュールは、ダウンストリーム デバイス用の接続文字列を使用して、IoT Hub デバイス クライアントの複数のインスタンスを作成します。

## <a name="next-steps"></a>次のステップ

- [IoT Edge モジュールを開発するための要件と ツールについて理解します][lnk-module-dev]。

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/CACertToolEdge/tools/CACertificates/CACertificateOverview.md