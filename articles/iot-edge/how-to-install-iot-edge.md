---
title: Azure IoT Edge をインストールする | Microsoft Docs
description: Windows デバイスまたは Linux デバイスに Azure IoT Edge をインストールする手順
author: kgremban
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: kgremban
ms.openlocfilehash: b062f4984bd5630f282d181e773e853f2234bb81
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710766"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Azure IoT Edge for Linux をインストールまたはアンインストールする

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、Linux デバイスに Azure IoT Edge ランタイムをインストールする手順を示します。

## <a name="prerequisites"></a>前提条件

* [登録されたデバイス ID](how-to-register-device.md)

  対称キー認証を使用してデバイスを登録した場合は、デバイス接続文字列を用意しておいてください。

  X.509 自己署名証明書認証を使用してデバイスを登録した場合は、デバイスを登録するために使用した ID 証明書を少なくとも 1 つと、それに対応する秘密キーをデバイス上で使用できるようにしておいてください。

* Linux デバイス

  X64、ARM32、または ARM64 の Linux デバイスを使用します。 Microsoft からは、Ubuntu Server 18.04 および Raspberry Pi OS Stretch の各オペレーティング システム用のインストール パッケージが提供されています。

  運用シナリオ向けに現在サポートされているオペレーティング システムに関する最新の情報については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください

  >[!NOTE]
  >ARM64 デバイスのサポートは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)にあります。

* Microsoft インストール パッケージにアクセスするようにデバイスを準備します。

  デバイスのオペレーティング システムに対応するリポジトリ構成をインストールします。

  * **Ubuntu Server 18.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  生成されたリストを sources.list.d ディレクトリにコピーします。

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Microsoft GPG 公開キーをインストールします。

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge ソフトウェア パッケージには、パッケージ内にあるライセンス条項 (`usr/share/doc/{package-name}` または `LICENSE` ディレクトリ) が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、そのパッケージを使用しないでください。

## <a name="install-a-container-engine"></a>コンテナー エンジンをインストールする

Azure IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 運用環境のシナリオでは、Moby エンジンを使用することをお勧めします。 Moby エンジンは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

Moby エンジンをインストールします。

   ```bash
   sudo apt-get install moby-engine
   ```

Moby コンテナー エンジンをインストールするときにエラーが発生する場合は、Linux カーネルに Moby との互換性があることを確認します。 埋め込みデバイスの一部の製造元からは、コンテナー エンジンとの互換性のために必要な機能を備えていないカスタム Linux カーネルを含むデバイス イメージが提供されています。 Moby から提供されている [check-config スクリプト](https://github.com/moby/moby/blob/master/contrib/check-config.sh)を使用する次のコマンドを実行して、カーネルの構成を確認します。

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

スクリプトの出力で、`Generally Necessary` と `Network Drivers` の下のすべての項目が有効になっていることを確認します。 足りない機能がある場合は、カーネルをソースからリビルドし、適切なカーネルの .config に含める関連モジュールを選択することで、それらを有効にします。同様に、`defconfig` や `menuconfig` などのカーネル構成ジェネレーターを使用している場合は、それぞれの機能を見つけて有効にし、ご自分のカーネルを適宜再構築します。 新たに変更されたカーネルを展開したら、check-config スクリプトをもう一度実行して、必要なすべての機能が正常に有効になっていることを確認します。

## <a name="install-iot-edge"></a>IoT Edge をインストールする

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge セキュリティ デーモンによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、この記事で後述する「[オフラインまたは特定のバージョンのインストール](#offline-or-specific-version-installation-optional)」の手順に従ってください。

デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

IoT Edge バージョン 1.1* と **libiothsm-std** パッケージをインストールします。

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge バージョン 1.1 は、IoT Edge の長期サポート ブランチです。 古いバージョンを実行している場合は、サポートされなくなるため、最新のパッチのインストールまたは更新をお勧めします。

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge サービスによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 サービスは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

IoT ID サービスは、IoT Edge のバージョン 1.2 と共に導入されました。 このサービスにより、IoT Edge と、IoT Hub と通信する必要があるその他のデバイス コンポーネントの ID のプロビジョニングと管理が処理されます。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、この記事で後述する「[オフラインまたは特定のバージョンのインストール](#offline-or-specific-version-installation-optional)」の手順に従ってください。

>[!NOTE]
>このセクションの手順では、IoT Edge バージョン 1.2 をインストールする方法について説明します。
>
>以前のバージョンを実行している IoT Edge デバイスが既にあり、1.2 にアップグレードする場合は、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」の手順を使用します。 バージョン 1.2 は、以前のバージョンの IoT Edge とは異なる点が多いため、アップグレードするには特定の手順が必要です。

デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

IoT Edge がどのバージョンであるかと、IoT ID サービスが使用できることを確認します。

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

最新バージョンの IoT Edge と IoT ID サービス パッケージをインストールするには、次のコマンドを使用します。

   ```bash
   sudo apt-get install aziot-edge
   ```

また、最新バージョンではないバージョンの IoT Edge をインストールする場合は、`aziot-edge` サービスと `aziot-identity-service` サービスの両方に必ず同じバージョンをインストールしてください。

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

これでコンテナー エンジンと IoT Edge ランタイムがデバイスにインストールされたので、次のステップに進み、クラウド ID と認証情報を使用してデバイスを設定できます。

使用する認証の種類に基づいて、次のセクションを選択してください。

* [オプション 1: 対称キーを使用した認証](#option-1-authenticate-with-symmetric-keys)
* [オプション 2: X.509 証明書を使用した認証](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>オプション 1: 対称キーを使用した認証

この時点で、IoT Edge ランタイムは Linux デバイスにインストールされています。クラウドの ID と認証情報を使用してデバイスをプロビジョニングする必要があります。

このセクションでは、対称キー認証を使用してデバイスをプロビジョニングする手順について説明します。 デバイスを IoT Hub に登録し、デバイス情報から接続文字列を取得している必要があります。 そうでない場合は、「[IoT Edge デバイスを IoT Hub に登録する](how-to-register-device.md)」の手順に従ってください。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

ファイルのプロビジョニング構成を見つけ、"**Manual provisioning configuration using a connection string**" セクションがまだコメント解除されていない場合はコメント解除します。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

**device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。**provisioning:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。

クリップボードの内容を Nano に貼り付けるには、`Shift+Right Click` キーまたは `Shift+Insert` キーを押します。

ファイルを保存して閉じます。

   `CTRL + X`, `Y`, `Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

次のコマンドで、対称キー認証を使用する IoT Edge デバイスを簡単に構成できます。

   ```bash
   sudo iotedge config mp --connection-string 'PASTE_CONNECTION_STRING_HERE'
   ```

`iotedge config mp` コマンドを実行して、デバイスに構成ファイルを作成し、接続文字列を提供し、構成の変更を適用します。

構成ファイルを表示する場合は、次の方法で開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

構成ファイルに変更を加えた場合は、`iotedge config apply` コマンドを使用して変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>オプション 2:X.509 証明書を使用した認証

この時点で、IoT Edge ランタイムは Linux デバイスにインストールされています。クラウドの ID と認証情報を使用してデバイスをプロビジョニングする必要があります。

このセクションでは、X.509 証明書認証を使用してデバイスをプロビジョニングする手順について説明します。 デバイスを IoT Hub に登録し、IoT Edge デバイスにある証明書と秘密キーに一致する拇印を提供している必要があります。 そうでない場合は、「[IoT Edge デバイスを IoT Hub に登録する](how-to-register-device.md)」の手順に従ってください。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

ファイルのプロビジョニング構成セクションを見つけ、「**Manual provisioning configuration using an X.509 identity certificate**」セクションをコメント解除します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。**provisioning:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

次のフィールドを更新します。

* **iothub_hostname**: デバイスの接続先となる IoT ハブのホスト名。 たとえば、`{IoT hub name}.azure-devices.net` のようにします。
* **device_id**: デバイスを登録したときに指定した ID。
* **identity_cert**: デバイス上の ID 証明書の URI。 たとえば、`file:///path/identity_certificate.pem` のようにします。
* **identity_pk**: 指定された ID 証明書の秘密キー ファイルの URI。 たとえば、`file:///path/identity_key.pem` のようにします。

ファイルを保存して閉じます。

   `CTRL + X`, `Y`, `Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge のインストールの一部として提供されるテンプレート ファイルに基づいて、デバイスの構成ファイルを作成します。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

ファイルの "**Provisioning**" セクションを見つけて、X.509 ID 証明書を使用した手動プロビジョニングの行をコメント解除します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

次のフィールドを更新します。

* **iothub_hostname**: デバイスの接続先となる IoT ハブのホスト名。 たとえば、`{IoT hub name}.azure-devices.net` のようにします。
* **device_id**: デバイスを登録したときに指定した ID。
* **identity_cert**: デバイス上の ID 証明書の URI。たとえば、`file:///path/identity_certificate.pem` です。 または、EST またはローカル証明機関を使用して証明書を動的に発行します。
* **identity_pk**: 指定された ID 証明書の秘密キー ファイルの URI。たとえば、`file:///path/identity_key.pem` です。 または、PKCS#11 URI を指定し、構成ファイルの後半の **PKCS#11** セクションで実際の構成情報を指定します。

ファイルを保存して閉じます。

   `CTRL + X`, `Y`, `Enter`

構成ファイルにプロビジョニング情報を入力した後、変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge デバイスにランタイムが正常にインストールされ、構成されていることを確認します。

>[!TIP]
>`iotedge` コマンドの実行には、昇格された特権が必要です。 IoT Edge ランタイムのインストール後に初めてマシンにサインインし直すと、アクセス許可は自動的に更新されます。 それまでは、コマンドの前に `sudo` を使用します。

IoT Edge システム サービスが実行されていることを確認します。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

正常な状態の応答は `Ok` です。

::: moniker-end

サービスのトラブルシューティングが必要な場合は、サービス ログを取得します。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

`check` ツールを使用して、デバイスの構成と接続の状態を確認します。

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>アクセス許可が更新された後でも、必ず `sudo` を使用してチェック ツールを実行してください。 このツールには、構成状態を確認するために、config ファイルにアクセスするための昇格された特権が必要です。

IoT Edge デバイス上で実行されているすべてのモジュールを表示します。 初めてサービスが開始されると、**edgeAgent** モジュールが実行されていることだけを確認できます。 edgeAgent モジュールが既定で実行され、デバイスにデプロイする追加モジュールのインストールと起動を支援します。

   ```bash
   sudo iotedge list
   ```

新しい IoT Edge デバイスを作成すると、Azure portal で状態コード `417 -- The device's deployment configuration is not set` が表示されます。 これは正常な状態であり、デバイスにモジュールをデプロイできる状態であることを表します。

## <a name="offline-or-specific-version-installation-optional"></a>オフラインまたは特定のバージョンのインストール (省略可能)

このセクションの手順は、標準のインストール手順では説明されていないシナリオを対象としています。 次のような場合が含まれます。

* オフラインの間に IoT Edge をインストールする
* リリース候補バージョンをインストールする

`apt-get install` を介して使用できない特定のバージョンの Azure IoT Edge ランタイムをインストールする場合は、このセクションの手順を使用します。 Microsoft パッケージのリストには、最近のバージョンとそのサブバージョンの限られたセットしか含まれていないので、これらの手順は、古いバージョンまたはリリース候補バージョンをインストールするユーザーが対象となります。

curl コマンドを使用すると、IoT Edge GitHub リポジトリから直接、コンポーネント ファイルをターゲットにすることができます。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に移動し、対象とするリリース バージョンを見つけます。

2. そのバージョンの **[Assets]** セクションを展開します。

3. 各リリースには、IoT Edge セキュリティ デーモンと hsmlib の新しいファイルが必要です。 オフライン デバイスに IoT Edge をインストールする場合は、事前にこれらのファイルをダウンロードします。 それ以外の場合は、以下のコマンドを使用して、これらのコンポーネントを更新します。

   1. IoT Edge デバイスのアーキテクチャに対応する **libiothsm-std** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   2. コピーしたリンクを次のコマンドで使用して、そのバージョンの hsmlib をインストールします。

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. IoT Edge デバイスのアーキテクチャに対応する **iotedge** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   4. コピーしたリンクを次のコマンドで使用して、そのバージョンの IoT Edge セキュリティ デーモンをインストールします。

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>デバイスで現在 IoT Edge バージョン 1.1 以前が実行されている場合は、このセクションの手順を行う前に、**IoT Edge** と **libiothsm-std** パッケージをアンインストールします。 詳細については、[1.0 または 1.1 から 1.2 への更新](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)に関するセクションを参照してください。

1. [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に移動し、対象とするリリース バージョンを見つけます。

2. そのバージョンの **[Assets]** セクションを展開します。

3. 各リリースには、IoT Edge と ID サービスの新しいファイルがあります。 オフライン デバイスに IoT Edge をインストールする場合は、事前にこれらのファイルをダウンロードします。 それ以外の場合は、以下のコマンドを使用して、これらのコンポーネントを更新します。

   1. IoT Edge デバイスのアーキテクチャに一致する **aziot-identity-service** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   2. コピーしたリンクを次のコマンドで使用して、そのバージョンの ID サービスをインストールします。

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. IoT Edge デバイスのアーキテクチャに対応する **aziot-edge** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   4. コピーしたリンクを次のコマンドで使用して、そのバージョンの IoT Edge をインストールします。

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

これでコンテナー エンジンと IoT Edge ランタイムがデバイスにインストールされたので、次のステップに進み、[クラウド ID を使用してデバイスをプロビジョニング](#provision-the-device-with-its-cloud-identity)できます。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

デバイスから IoT Edge のインストールを削除する必要がある場合は、次のコマンドを使用します。

IoT Edge ランタイムを削除します。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

構成ファイルを含め、IoT Edge に関連付けられているすべてのファイルを削除する場合は、`--purge` フラグを使用します。 IoT Edge を再インストールし、今後同じ構成情報を使用する場合は、このフラグを使用しないでください。

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには残っています。 すべてのコンテナーを表示して、どのコンテナーが残っているかを確認します。

```bash
sudo docker ps -a
```

2 つのランタイム コンテナーを含め、デバイスからコンテナーを削除します。

```bash
sudo docker rm -f <container name>
```

最後に、デバイスからコンテナー ランタイムを削除します。

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
