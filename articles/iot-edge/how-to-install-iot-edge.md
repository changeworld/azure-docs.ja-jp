---
title: Azure IoT Edge をインストールする | Microsoft Docs
description: Windows デバイスまたは Linux デバイスに Azure IoT Edge をインストールする手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: 3a02459f5b92aa7d708c29c737ed9428ed14215a
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045688"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>Azure IoT Edge ランタイムをインストールまたはアンインストールする

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

IoT Edge デバイスを設定するには、2 つのステップがあります。 1 番目のステップでは、ランタイムとその依存関係をインストールします。この記事ではそれについて説明します。 2 番目のステップでは、デバイスをクラウド内にあるその ID に接続し、IoT Hub で認証を設定します。 それらの手順については、次の記事で説明されています。

この記事では、Linux デバイスまたは Windows デバイスに Azure IoT Edge ランタイムをインストールする手順を示します。 Windows デバイスの場合はさらに、Linux コンテナーと Windows コンテナーのどちらを使用するかを選択できます。 現在、運用シナリオにおいては、Windows では Windows コンテナーを使用することをお勧めします。 Windows 上の linux コンテナーは、開発およびテストのシナリオに役立ちます。特に、Linux デバイスに展開するために Windows PC で開発している場合に便利です。

## <a name="prerequisites"></a>前提条件

運用シナリオ向けに現在サポートされているオペレーティング システムに関する最新の情報については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください

# <a name="linux"></a>[Linux](#tab/linux)

X64、ARM32、または ARM64 の Linux デバイスを使用します。 Microsoft からは、Ubuntu Server 16.04、Ubuntu Server 18.04、Raspbian Stretch の各オペレーティング システム用のインストール パッケージが提供されています。

>[!NOTE]
>ARM64 デバイスのサポートは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)にあります。

Microsoft インストール パッケージにアクセスするようにデバイスを準備します。

1. デバイスのオペレーティング システムに対応するリポジトリ構成をインストールします。

   * **Ubuntu Server 16.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspbian Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. 生成されたリストを sources.list.d ディレクトリにコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Microsoft GPG 公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Windows のバージョン

Windows コンテナーを使用する IoT Edge には、Windows バージョン 1809、ビルド 17762 が必要です。これは、最新の [Windows 長期サポート ビルド](/windows/release-information/)です。 開発とテストのシナリオの場合は、コンテナー機能をサポートするどの SKU (Pro、Enterprise、Server など) でも機能します。 ただし、運用環境に移行する前に、サポートされているシステムの一覧を必ず確認してください。

Linux コンテナーを使用する IoT Edge は、[Docker Desktop の要件](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)を満たす任意のバージョンの Windows で実行できます。

### <a name="container-engine-requirements"></a>コンテナー エンジンの要件

Azure IoT Edge は、[OCI と互換性のある](https://www.opencontainers.org/)コンテナー エンジンに依存します。 デバイスがコンテナーをサポートできることを確認します。

IoT Edge を仮想マシンに インストールしている場合は、入れ子になった仮想化を有効にし、2 GB 以上のメモリを割り当てます。 Hyper-V の場合、第 2 世代仮想マシンでは入れ子になった仮想化が既定で有効になっています。 VMWare の場合、仮想マシンでこの機能を有効に切り替えます。

---

Azure IoT Edge ソフトウェア パッケージには、パッケージ内にあるライセンス条項 (`usr/share/doc/{package-name}` または `LICENSE` ディレクトリ) が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、そのパッケージを使用しないでください。

## <a name="install-a-container-engine"></a>コンテナー エンジンをインストールする

Azure IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 運用環境のシナリオでは、Moby ベースのエンジンを使用することをお勧めします。 Moby エンジンは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="windows"></a>[Windows](#tab/windows)

運用環境のシナリオでは、インストール スクリプトに含まれる Moby ベースのエンジンを使用します。 エンジンをインストールするための追加の手順はありません。

Linux コンテナーを使用する IoT Edge の場合は、独自のコンテナー ランタイムを用意する必要があります。 続ける前に、[Docker Desktop](https://docs.docker.com/docker-for-windows/install/) をデバイスにインストールし、[Linux コンテナーを使用する](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)ように構成します。

---

## <a name="install-the-iot-edge-security-daemon"></a>IoT Edge セキュリティ デーモンをインストールする

IoT Edge セキュリティ デーモンによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

これらのセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、次のセクションの「[オフラインまたは特定のバージョンのインストール](#offline-or-specific-version-installation)」の手順に従ってください。

# <a name="linux"></a>[Linux](#tab/linux)

デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

使用できる IoT Edge のバージョンを確認します。

   ```bash
   apt list -a iotedge
   ```

最新バージョンのセキュリティ デーモンをインストールするには、次のコマンドを使用します。これにより、**libiothsm-std** パッケージの最新バージョンもインストールされます。

   ```bash
   sudo apt-get install iotedge
   ```

特定のバージョンのセキュリティ デーモンをインストールする場合は、apt リスト出力から該当するバージョンを指定します。 また、**libiothsm-std** パッケージにも同じバージョンを指定します。そうしないと、最新バージョンがインストールされます。 たとえば、次のコマンドでは、1.0.8 リリースの最新バージョンがインストールされます。

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

インストールするバージョンが一覧にない場合は、次のセクションの「[オフラインまたは特定のバージョンのインストール](#offline-or-specific-version-installation)」の手順に従います。 そのセクションでは、IoT Edge セキュリティ デーモンの以前の任意のバージョン、またはリリース候補のバージョンをターゲットとする方法が説明されています。

# <a name="windows"></a>[Windows](#tab/windows)

1. PowerShell を管理者として実行します。

   PowerShell(x86) ではなく、PowerShell の AMD64 セッションを使用します。 使用しているセッションの種類がわからない場合は、次のコマンドを実行します。

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) コマンドを実行します。これにより、次のタスクが実行されます。

   * お使いの Windows コンピューターがサポートされているバージョンであることを確認します。
   * コンテナー機能をオンにします。
   * Moby エンジンと IoT Edge ランタイムをダウンロードします。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge` コマンドの既定値では Windows コンテナーが使用されます。 Linux コンテナーを使用する場合は、`ContainerOs` パラメーターを追加します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. この時点で、再起動を求めるメッセージが出力される場合があります。 その場合、デバイスをすぐに再起動してください。

デバイスに IoT Edge をインストールするときは、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* オフライン インストール用にローカル ディレクトリをインストーラーに指定する。

これらの追加パラメーターの詳細については、「[Windows 上の IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)」を参照してください。

---

これでコンテナー エンジンと IoT Edge ランタイムがデバイスにインストールされたので、次のステップに進み、デバイスを IoT Hub に登録して、そのクラウド ID と認証情報でデバイスを設定することがきます。

使用する認証の種類に基づいて、次の記事を選択してください。

* [対称キー認証](how-to-manual-provision-symmetric-key.md)を使用すると、短い時間で始めることができます。
* [X.509 証明書認証](how-to-manual-provision-x509.md)は、運用環境向けにセキュリティが強化されます。

## <a name="offline-or-specific-version-installation"></a>オフラインまたは特定のバージョンのインストール

このセクションの手順は、標準のインストール手順では説明されていないシナリオを対象としています。 次のような場合が含まれます。

* オフラインの間に IoT Edge をインストールする
* リリース候補バージョンをインストールする
* Windows で、最新以外のバージョンをインストールする

# <a name="linux"></a>[Linux](#tab/linux)

`apt-get install` を介して使用できない特定のバージョンの Azure IoT Edge ランタイムをインストールする場合は、このセクションの手順を使用します。 Microsoft パッケージのリストには、最近のバージョンとそのサブバージョンの限られたセットしか含まれていないので、これらの手順は、古いバージョンまたはリリース候補バージョンをインストールするユーザーが対象となります。

curl コマンドを使用すると、IoT Edge GitHub リポジトリから直接、コンポーネント ファイルをターゲットにすることができます。

<!-- TODO: Offline installation? -->

1. [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に移動し、対象とするリリース バージョンを見つけます。

2. そのバージョンの **[Assets]** セクションを展開します。

3. 各リリースには、IoT Edge セキュリティ デーモンと hsmlib の新しいファイルが必要です。 以下のコマンドを使用して、これらのコンポーネントを更新します。

   1. IoT Edge デバイスのアーキテクチャに対応する **libiothsm-std** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   2. コピーしたリンクを次のコマンドで使用して、そのバージョンの hsmlib をインストールします。

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. IoT Edge デバイスのアーキテクチャに対応する **iotedge** ファイルを見つけます。 ファイル リンクを右クリックし、リンクのアドレスをコピーします。

   4. コピーしたリンクを次のコマンドで使用して、そのバージョンの IoT Edge セキュリティ デーモンをインストールします。

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

インストール中に、次の 3 つのファイルがダウンロードされます。

* PowerShell スクリプト。これには、インストール手順が含まれています。
* Microsoft Azure IoT Edge cab。これには、IoT Edge セキュリティ デーモン (iotedged)、Moby コンテナー エンジン、および Moby CLI が含まれています。
* Visual C++ 再頒布可能パッケージ (VC ランタイム) インストーラー

インストール中にデバイスがオフラインになる予定の場合、または特定のバージョンの IoT Edge をインストールする場合は、これらのファイルを事前にデバイスにダウンロードできます。 インストール時には、インストール スクリプトによって、ダウンロード済みのファイルを含むディレクトリを指定します。 インストーラーでは、最初にそのディレクトリをチェックし、見つからないコンポーネントのみをダウンロードします。 すべてのファイルがオフラインで利用可能な場合、インターネット接続なしでインストールできます。

1. IoT Edge の最新のインストール ファイルとその以前のバージョンについては、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を参照してください。

2. インストールするバージョンを見つけ、リリース ノートの **[アセット]** セクションから次のファイルを IoT デバイスにダウンロードします。

   * IoTEdgeSecurityDaemon.ps1
   * リリース 1.0.9 以降は Microsoft-Azure-IoTEdge-amd64.cab、リリース 1.0.8 以前は Microsoft-Azure-IoTEdge.cab。

   1\.0.9 以降は、テスト目的でのみ Microsoft-Azure-IotEdge-arm32.cab を使用することもできます。 IoT Edge では、現在、Windows ARM32 デバイスはサポートされていません。

   各リリースの機能をサポートするために機能が変更されるため、使用する .cab ファイルと同じリリースの PowerShell スクリプトを使用することが重要です。

3. ダウンロードした .cab ファイルにアーキテクチャのサフィックスが付いている場合は、そのファイルの名前を **Microsoft-Azure-IoTEdge.cab** のみに変更します。

4. 必要に応じて、Visual C++ 再頒布可能パッケージ インストーラーをダウンロードします。 たとえば、PowerShell スクリプトでは、バージョン [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe) を使用します。 IoT デバイスの IoT Edge ファイルと同じフォルダーにインストーラーを保存します。

5. オフライン コンポーネントを使用してインストールするには、PowerShell スクリプトのローカル コピーを[ドット ソース](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)で実行します。 

6. `-OfflineInstallationPath` パラメーターを指定して [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) コマンドを実行します。 ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   デプロイ コマンドでは、指定されたローカル ファイル ディレクトリにあるコンポーネントが使用されます。 .cab ファイルまたは Visual C++ インストーラーが見つからない場合は、ダウンロードが試行されます。

---

これでコンテナー エンジンと IoT Edge ランタイムがデバイスにインストールされたので、次のステップに進み、[IoT Hub で IoT Edge デバイスの認証を行う](how-to-manual-provision-symmetric-key.md)ことができます。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

デバイスから IoT Edge のインストールを削除する必要がある場合は、次のコマンドを使用します。

# <a name="linux"></a>[Linux](#tab/linux)

IoT Edge ランタイムを削除します。

```bash
sudo apt-get remove --purge iotedge
```

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

# <a name="windows"></a>[Windows](#tab/windows)

IoT Edge のインストールを Windows デバイスから削除する場合は、管理用 PowerShell ウィンドウから [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) コマンドを使用します。 このコマンドは、IoT Edge ランタイムを既存の構成と Moby エンジン データと一緒に削除します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

アンインストール オプションの詳細については、`Get-Help Uninstall-IoTEdge -full` コマンドを使用してください。

---

## <a name="next-steps"></a>次のステップ

IoT Edge ランタイムをインストールした後は、IoT Hub と接続するようにデバイスを構成します。 以下の記事では、クラウドに新しいデバイスを登録し、その ID と認証情報をデバイスに提供する手順が説明されています。

使用する認証の種類に基づいて、次の記事を選択してください。

* **対称キー**: IoT Hub と IoT Edge デバイスの両方に、セキュリティで保護されたキーのコピーがあります。 デバイスが IoT Hub に接続すると、キーが一致しているかどうかがチェックされます。 この認証方法は比較的すばやく開始できますが、それほど安全ではありません。

  [Azure IoT Edge デバイスに対称キー認証を設定する](how-to-manual-provision-symmetric-key.md)

* **X.509 自己署名**: IoT Edge デバイスには X.509 ID 証明書があり、IoT Hub には証明書の拇印が提供されます。 デバイスが IoT Hub に接続すると、証明書がその拇印と比較されます。 この認証方法はより安全であり、運用環境のシナリオの場合に推奨されます。

  [Azure IoT Edge デバイスに X.509 証明書認証を設定する](how-to-manual-provision-x509.md)