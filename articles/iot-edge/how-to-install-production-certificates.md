---
title: デバイスへの証明書のインストール - Azure IoT Edge | Microsoft Docs
description: テスト証明書を作成し、それらを Azure IoT Edge デバイスにインストールして運用環境のデプロイの準備をする方法について説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf073572cd5b371ec484c99f14cbefb4cba75ce7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509905"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>運用証明書を IoT Edge デバイスにインストールする

すべての IoT Edge デバイスは、証明書を使用して、デバイス上で実行されるランタイムと任意のモジュール間の安全な接続を作成します。
ゲートウェイとして機能している IoT Edge デバイスは、これらの同じ証明書を使用してダウンストリームのデバイスに接続します。

初めて IoT Edge をインストールしてデバイスをプロビジョニングする場合、サービスをテストできるように、デバイスは一時的な証明書を使用して設定されます。
これらの一時的な証明書は 90 日で有効期限が切れます。もしくは、マシンを再起動するとリセットされます。
デバイスを運用環境に移行する準備ができたか、ゲートウェイ シナリオを作成したい場合は、独自の証明書を指定する必要があります。
この記事では、証明書を IoT Edge デバイスにインストールするステップを説明します。

別の種類の証明書や、IoT Edge シナリオでのそれらの役割についての詳細は、「[Azure IoT Edge 証明書の使用方法の詳細](iot-edge-certs.md)」を参照してください。

>[!NOTE]
>この記事全体で使用される「ルート CA」という用語は、自分の IoT ソリューションの証明書チェーンのうち、最上位の公開証明機関を示しています。 シンジケート証明機関の証明書ルートや、組織の証明機関のルートを使用する必要はありません。 多くの場合、実際は中間 CA の公開証明書です。

## <a name="prerequisites"></a>前提条件

* [Windows](how-to-install-iot-edge-windows.md) または [Linux](how-to-install-iot-edge-linux.md) 上で実行されている IoT Edge デバイス。
* 自己署名したか、Baltimore、Verisign、DigiCert、GlobalSign などの信頼されている商用証明機関から購入したルート証明機関 (CA) の証明書がある。

ルート証明機関がまだないものの、運用証明書を必要とする IoT Edge 機能を試してみたい (ゲートウェイ シナリオなど) 場合、「[デモ証明書を作成して IoT Edge デバイスの機能をテストする](how-to-create-test-certificates.md)」を参照してください。

## <a name="create-production-certificates"></a>運用証明書を作成する

独自の証明機関を使用して次のファイルを作成する必要があります。

* ルート CA
* デバイス CA 証明書
* デバイス CA 秘密キー

この記事で*ルート CA* と呼ばれているものは、組織の最上位の証明機関ではありません。 これは IoT Edge シナリオの最上位の証明機関であり、IoT Edge ハブ モジュール、ユーザー モジュール、他のダウンストリームのデバイスはこれを利用してお互いの間で信頼関係を確立します。

これらの証明書の例を確認するには、「[サンプルとチュートリアルのためのテスト CA 証明書を管理する](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)」にある、デモ証明書を作成するスクリプトをご確認ください。

## <a name="install-certificates-on-the-device"></a>証明書をデバイスにインストールする

証明書チェーンを IoT Edge デバイスにインストールし、新しい証明書を参照するように IoT Edge ランタイムを構成します。

たとえば、サンプル スクリプトを使用して[デモ証明書を作成](how-to-create-test-certificates.md)した場合、次の 3 つのファイルを IoT Edge デバイスにコピーする必要があります。

* デバイス CA 証明書: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* デバイス CA 秘密キー: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* ルート CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 3 つの証明書とキー ファイルを IoT Edge デバイスにコピーします。

   [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) のようなサービスや、[Secure copy protocol](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。  IoT Edge デバイス自体で証明書を生成した場合は、この手順をスキップして、作業ディレクトリへのパスを使用することができます。

2. IoT Edge セキュリティ デーモン構成ファイルを開きます。

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. config.yaml ファイル内の **certificate** プロパティを、IoT Edge デバイス上の証明書ファイルとキー ファイルの URI に設定します。 証明書プロパティの前の `#` 文字を削除して、4 行をコメント解除します。 **certificates:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。 次に例を示します。

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. Linux デバイスでは、証明書を保持しているディレクトリの読み取り権限をユーザー **iotedge**が必ず保持しているようにします。

## <a name="next-steps"></a>次のステップ

証明書を IoT Edge デバイス上にインストールするステップは、ソリューションを運用環境にデプロイする前に必ず実行する必要があります。 詳細については、「[IoT Edge ソリューションを運用環境にデプロイするための準備を行う](production-checklist.md)」を参照してください。
