---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e4c78fe2037beb23f69700ae7a340a4d5c6cc160
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490568"
---
## <a name="install-iot-edge"></a>IoT Edge をインストールする

このセクションでは、Linux VM または IoT Edge の物理デバイスを準備します。 次に、IoT Edge をインストールします。

IoT Edge ランタイムをインストールするように準備するには、デバイスで 2 つの手順を完了しておく必要があります。 デバイスは Microsoft インストール パッケージにアクセスする必要があります。また、コンテナー エンジンがインストールされている必要があります。

### <a name="access-the-microsoft-installation-packages"></a>Microsoft インストール パッケージにアクセスする

1. デバイスのオペレーティング システムに対応するリポジトリ構成パッケージをダウンロードします。

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/packages-microsoft-prod.deb > ./packages-microsoft-prod.deb
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/packages-microsoft-prod.deb > ./packages-microsoft-prod.deb
      ```

1. 構成パッケージをインストールして、Microsoft のパッケージ リポジトリと GPG 公開キーを追加します。

   ```bash
   sudo apt install ./packages-microsoft-prod.deb
   ```

> [!NOTE]
> Azure IoT Edge ソフトウェア パッケージには、パッケージ内にあるライセンス条項 (`usr/share/doc/{package-name}` または `LICENSE` ディレクトリ) が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、そのパッケージを使用しないでください。

### <a name="install-a-container-engine"></a>コンテナー エンジンをインストールする

Azure IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 運用環境のシナリオでは、Moby エンジンを使用することをお勧めします。 Moby エンジンは、IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

1. デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

1. Moby エンジンをインストールします。

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Moby コンテナー エンジンをインストールするときにエラーが発生する場合は、Linux カーネルに Moby との互換性があることを確認します。 埋め込みデバイスの一部の製造元からは、コンテナー エンジンとの互換性のために必要な機能を備えていないカスタム Linux カーネルを含むデバイス イメージが提供されています。 Moby から提供されている [check-config スクリプト](https://github.com/moby/moby/blob/master/contrib/check-config.sh)を使用する次のコマンドを実行して、カーネルの構成を確認します。
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > スクリプトの出力で、`Generally Necessary` と `Network Drivers` の下のすべての項目が有効になっていることを確認します。 機能が欠けている場合は、カーネルをソースから再構築し、カーネルの適切な .config に含める関連モジュールを選択することで、それらを有効にします。同様に、`defconfig` や `menuconfig` などのカーネル構成ジェネレーターを使用している場合は、それぞれの機能を見つけて有効にし、ご自分のカーネルを適宜再構築します。 新たに変更されたカーネルを展開したら、check-config スクリプトをもう一度実行して、必要なすべての機能が正常に有効になっていることを確認します。

### <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge セキュリティ デーモンによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、この記事で後述する「**オフラインまたは特定のバージョンのインストール**」の手順に従ってください。

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

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、この記事で後述する「**オフラインまたは特定のバージョンのインストール**」の手順に従ってください。

>[!NOTE]
>このセクションの手順では、IoT Edge バージョン 1.2 をインストールする方法について説明します。
>
>以前のバージョンを実行している IoT Edge デバイスが既にあり、1.2 にアップグレードする場合は、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](../articles/iot-edge/how-to-update-iot-edge.md)」の手順を使用します。 バージョン 1.2 は、以前のバージョンの IoT Edge とは異なる点が多いため、アップグレードするには特定の手順が必要です。

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

<!-- end 1.2 -->
::: moniker-end
