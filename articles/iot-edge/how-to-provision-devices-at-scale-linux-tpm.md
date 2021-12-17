---
title: Linux で仮想 TPM を使用してデバイスをプロビジョニングする - Azure IoT Edge
description: Linux デバイスでシミュレートされた TPM を使用して Azure IoT Edge 用の Azure IoT Hub Device Provisioning Service をテストします。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 07/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e55d4f2bcb6e8f86a45d22e4a3a16555fefe8f81
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246626"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>Linux で TPM を使用して大規模な IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

この記事では、トラステッド プラットフォーム モジュール (TPM) を使用して Azure IoT Edge for Linux デバイスを自動プロビジョニングする手順について説明します。 IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)に関する記事を読んでから先に進んでください。

この記事では、次の 2 つの手法の概要を説明します。 ソリューションのアーキテクチャに基づいて、使用する手法を選択してください。

- 物理 TPM ハードウェアを使用して Linux デバイスを自動プロビジョニングします。 例は、[Infineon OPTIGA&trade; TPM SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e) などです。
- Hyper-V が有効な Windows 開発用コンピューターで実行されているシミュレートされた TPM を使用して、Linux 仮想マシン (VM) を自動プロビジョニングします。 この手法は、テスト シナリオとしてのみ使用することをお勧めします。 シミュレートされた TPM では、物理 TPM と同じセキュリティは提供されません。

手順は使用する手法によって異なるので、先に進む前に正しいタブが表示されていることをご確認ください。

# <a name="physical-device"></a>[物理デバイス](#tab/physical-device)

タスクは次のとおりです。

1. TPM のプロビジョニング情報を取得します。
1. IoT Hub Device Provisioning Service のインスタンスでデバイスに対する個別の登録を作成します。
1. IoT Edge ランタイムをインストールして、デバイスを IoT ハブに接続します。

# <a name="virtual-machine"></a>[仮想マシン](#tab/virtual-machine)

タスクは次のとおりです。

1. ハードウェア セキュリティ用にシミュレートされた TPM を使用して Hyper-V に Linux VM を作成します。
1. TPM のプロビジョニング情報を取得します。
1. IoT Hub Device Provisioning Service のインスタンスでデバイスに対する個別の登録を作成します。
1. IoT Edge ランタイムをインストールして、デバイスを IoT ハブに接続します。

---

## <a name="prerequisites"></a>前提条件

# <a name="physical-device"></a>[物理デバイス](#tab/physical-device)

* アクティブな IoT ハブ。
* 自分の IoT ハブにリンクした、Azure 内の IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service のインスタンスがない場合は、IoT Hub Device Provisioning Service クイックスタートの 2 つのセクションの手順に従ってください。
     - [新しい IoT Hub Device Provisioning Service を作成する](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
     - [IoT ハブと Device Provisioning Service とをリンクさせる](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

# <a name="virtual-machine"></a>[仮想マシン](#tab/virtual-machine)

* アクティブな IoT ハブ。
* 自分の IoT ハブにリンクした、Azure 内の IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service のインスタンスがない場合は、IoT Hub Device Provisioning Service クイックスタートの 2 つのセクションの手順に従ってください。
    - [新しい IoT Hub Device Provisioning Service を作成する](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
    - [IoT ハブと Device Provisioning Service とをリンクさせる](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。
* [Hyper-V 対応の](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 開発マシン。 この記事では、Ubuntu Server VM を実行中の Windows 10 を使用します。

---

> [!NOTE]
> Device Provisioning Service で TPM 構成証明を使用する場合は、TPM 2.0 が必要です。
>
> TPM を使用する場合、グループではなく個別の Device Provisioning Service の登録のみ作成できます。

## <a name="set-up-your-device"></a>デバイスのセットアップ

# <a name="physical-device"></a>[物理デバイス](#tab/physical-device)

TPM で物理 Linux デバイスを使用している場合は、デバイスをセットアップするための追加の手順はありません。

作業を続ける準備ができました。

# <a name="virtual-machine"></a>[仮想マシン](#tab/virtual-machine)

このセクションでは、Hyper-V で新しい Linux VM を作成します。 IoT Edge で自動プロビジョニングがどのように機能するかをテストするために、シミュレートされた TPM を使用してこの VM を構成します。

> [!TIP]
> VM を使用している場合は、この記事全体を通じて VM を何度もコピーして貼り付けます。 Hyper-V マネージャー接続アプリケーションを使用すると、コピーと貼り付けは簡単ではありません。 Hyper-V マネージャー経由でいったん VM に接続して、その IP アドレスを取得することをお勧めします。 最初に `sudo apt install net-tools` を実行してから、`hostname -I` を実行します。 その後、この IP アドレスを使用して、SSH 経由で接続できます: `ssh <username>@<ipaddress>`。

### <a name="create-a-virtual-switch"></a>仮想スイッチを作成する

仮想スイッチを使用して、VM を物理ネットワークに接続できます。

1. Windows マシンで Hyper-V マネージャーを開きます。

1. **[アクション]** メニューの **[仮想スイッチ マネージャー]** を選びます。

1. **[外部]** 仮想スイッチを選び、 **[仮想スイッチの作成]** を選びます。

1. 新しい仮想スイッチに名前を付けます。 たとえば、**EdgeSwitch** を使用します。 接続の種類が **[外部ネットワーク]** に設定されていることを確認し、 **[OK]** を選びます。

1. ネットワーク接続が中断される可能性があることを警告するポップアップが表示されます。 **[はい]** を選択して続行します。

> [!TIP]
> 新しい仮想スイッチの作成中にエラーが発生した場合は、イーサネット アダプターを使用している他のスイッチがないこと、および同じ名前を使用している他のスイッチがないことを確認します。

### <a name="create-the-virtual-machine"></a>仮想マシンの作成

起動可能なイメージ ファイルから新しい VM を作成します。

1. VM で使用するディスク イメージ ファイルをダウンロードし、ローカル環境に保存します。 たとえば、[Ubuntu Server 18.04](http://releases.ubuntu.com/18.04/) をダウンロードします。 IoT Edge デバイスでサポートされているオペレーティング システムの詳細については、「[Azure IoT Edge のサポートされるシステム](./support.md)」を参照してください。

1. Hyper-V マネージャーの **[操作]** メニューで、 **[操作]**  >  **[新規]**  >  **[仮想マシン]** を選びます。

1. **新しい仮想マシン ウィザード** を、次の構成で完了します。

   1. **世代の指定**: **[第 2 世代]** を選択します。 第 2 世代の VM では、入れ子になった仮想化が有効になっており、これは VM で IoT Edge を実行するのに必要です。
   1. **ネットワークの構成**: **[接続]** の値を、前のセクションで作成した仮想スイッチに設定します。
   1. **インストール オプション**: **[ブート イメージ ファイルからオペレーティング システムをインストールする]** を選択し、ローカルに保存したディスク イメージ ファイルを参照します。

1. VM を作成するウィザードで、 **[完了]** を選びます。

新しい VM の作成には数分かかることがあります。

### <a name="enable-virtual-tpm"></a>仮想 TPM を有効にする

VM が作成されたら、その設定を開いて、デバイスの自動プロビジョニングに使用する仮想 TPM を有効にします。

1. Hyper-V マネージャーで、VM を右クリックし、 **[設定]** を選びます。

1. **[セキュリティ]** に移動します。

1. **[セキュア ブートを有効にする]** をオフにします。

1. **[トラステッド プラットフォーム モジュールを有効にする]** をオンにします。

1. **[OK]** を選択します。

### <a name="start-the-virtual-machine"></a>仮想マシンの開始

VM を起動して、インストール プロセスを完了します。

1. Hyper-V マネージャーで、VM を起動して接続します。

1. VM 内のプロンプトに従ってインストール プロセスを実行し、コンピューターを再起動します。

インストールが完了し、VM に再度サインインすると、続行する準備が整います。

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>TPM のプロビジョニング情報を取得する

このセクションでは、TPM の登録 ID と保証キーを取得するために使用できるツールをビルドします。

1. お使いのデバイスにサインインし、[Linux 開発環境の設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)に関するセクションの手順に従って、Azure IoT device SDK for C をインストールしてビルドします。

1. 次のコマンドを実行して、TPM 用のデバイス プロビジョニング情報を取得する SDK ツールをビルドします。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 出力ウィンドウに、デバイスの **登録 ID** と **保障キー** が表示されます。 後ほど Device Provisioning Service でデバイスの個別登録を作成するときに使用できるように、これらの値をコピーします。

> [!TIP]
> SDK ツールを使用してプロビジョニング情報を取得しない場合は、情報を取得するための別の方法を見つける必要があります。 TPM チップごとに固有の保証キーを、それに関連付けられている TPM チップの製造元から取得します。 TPM デバイス用の一意の登録 ID を生成できます。 たとえば、保証キーの SHA-256 ハッシュを作成できます。

登録 ID と保証キーを取得したら、次に進むことができます。

## <a name="create-a-device-provisioning-service-enrollment"></a>Device Provisioning Service 登録の作成

TPM からプロビジョニング情報を取得し、その情報を使用して Device Provisioning Service 内に個別登録を作成します。

Device Provisioning Service 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで使用される任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[個別登録の追加]** を選択し、登録を構成する次の手順を完了します。

   1. **[メカニズム]** で **[TPM]** を選択します。

   1. VM または物理デバイスからコピーした **保証キー** と **登録 ID** を指定します。

   1. 必要に応じて、デバイス ID を指定します。 デバイス ID を指定しなかった場合は、登録 ID が使用されます。

   1. **[True]** を選択し、VM または物理デバイスが IoT Edge デバイスであることを宣言します。

   1. デバイスの接続先になるリンクされた IoT ハブを選択するか、 **[Link to new IoT Hub]\(新しい IoT ハブへのリンク\)** を選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 詳細については、[大規模な IoT Edge モジュールの展開](how-to-deploy-at-scale.md)に関する記事を参照してください。

   1. **[保存]** を選択します。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

このセクションでは、Linux VM または IoT Edge の物理デバイスを準備します。 次に、IoT Edge をインストールします。

IoT Edge ランタイムをインストールするように準備するには、デバイスで 2 つの手順を完了しておく必要があります。 デバイスは Microsoft インストール パッケージにアクセスする必要があります。また、コンテナー エンジンがインストールされている必要があります。

### <a name="access-the-microsoft-installation-packages"></a>Microsoft インストール パッケージにアクセスする

お使いのデバイスは、Microsoft インストール パッケージにアクセスできる必要があります。

1. デバイスのオペレーティング システムに一致するリポジトリ構成をインストールします。

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. 生成されたリストを sources.list.d ディレクトリにコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Microsoft GPG 公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Azure IoT Edge ソフトウェア パッケージには、パッケージ内にあるライセンス条項 (`usr/share/doc/{package-name}` または `LICENSE` ディレクトリ) が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、そのパッケージを使用しないでください。

### <a name="install-a-container-engine"></a>コンテナー エンジンをインストールする

IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 運用環境のシナリオでは、Moby エンジンを使用することをお勧めします。 Moby エンジンは、IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

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

### <a name="install-iot-edge"></a>IoT Edge をインストールする

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge セキュリティ デーモンによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、オフラインまたは特定のバージョンのインストールに関する手順に従ってください。

1. デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

1. IoT Edge バージョン 1.1* と **libiothsm-std** パッケージをインストールします。

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge バージョン 1.1 は、IoT Edge の長期サポート ブランチです。 以前のバージョンを実行している場合は、サポートされなくなるため、最新のパッチをインストールまたは更新することをお勧めします。

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge サービスによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 サービスは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

IoT ID サービスは、IoT Edge のバージョン 1.2 と共に導入されました。 このサービスにより、IoT Edge と、IoT Hub と通信する必要があるその他のデバイス コンポーネントの ID のプロビジョニングと管理が処理されます。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、オフラインまたは特定のバージョンのインストールに関する手順に従ってください。

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

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

ランタイムがデバイスにインストールされたら、Device Provisioning Service と IoT Hub に接続するために使用される情報でデバイスを構成します。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 前に集めた Device Provisioning Service の **ID スコープ** とデバイスの **登録 ID** を把握しておきます。

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. ファイルのプロビジョニング構成セクションを見つけます。 TPM プロビジョニング行のコメントを解除し、他にもプロビジョニング行があれば、それらのコメントが解除されていることを確認します。

   `provisioning:` の行の先頭には空白文字を入れず、入れ子の項目には 2 つの空白でインデントする必要があります。

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. `scope_id` と `registration_id` の値を、Device Provisioning Service とデバイス情報で更新します。 `scope_id` の値は、Device Provisioning Service のインスタンスの概要ページにある **ID スコープ** です。

1. 必要に応じて、`always_reprovision_on_startup` または `dynamic_reprovisioning` の行を使用して、デバイスの再プロビジョニング動作を構成します。 起動時に再プロビジョニングするようにデバイスが設定されている場合、常に最初に Device Provisioning Service を使用してプロビジョニングが試行され、失敗した場合はプロビジョニングのバックアップにフォールバックします。 動的にプロビジョニングするようにデバイスが設定されている場合は、再プロビジョニング イベントが検出されると、IoT Edge が再起動されて、再プロビジョニングされます。 詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](../iot-dps/concepts-device-reprovision.md)」を参照してください。

1. ファイルを保存して閉じます。

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 前に集めた Device Provisioning Service の **ID スコープ** とデバイスの **登録 ID** を把握しておきます。

1. IoT Edge のインストールの一部として提供されるテンプレート ファイルに基づいて、デバイスの構成ファイルを作成します。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. ファイルのプロビジョニング構成セクションを見つけます。 TPM プロビジョニング行のコメントを解除し、他にもプロビジョニング行があれば、それらのコメントが解除されていることを確認します。

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. `id_scope` と `registration_id` の値を、Device Provisioning Service とデバイス情報で更新します。 `scope_id` の値は、Device Provisioning Service のインスタンスの概要ページにある **ID スコープ** です。

1. 必要に応じて、ファイルの自動再プロビジョニング モード セクションを検索します。 `auto_reprovisioning_mode` パラメーターを使用して、デバイスの再プロビジョニング動作を、`Dynamic`、`AlwaysOnStartup`、または `OnErrorOnly` のいずれかに構成します。 詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](../iot-dps/concepts-device-reprovision.md)」を参照してください。

1. ファイルを保存して閉じます。

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge に TPM へのアクセス権を付与する

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

デバイスを自動的にプロビジョニングするには、IoT Edge ランタイムが TPM にアクセスする必要があります。

IoT Edge ランタイムに TPM へのアクセス権を付与するには、systemd 設定をオーバーライドして、`iotedge` サービスに root 特権を付与します。 サービス権限を昇格したくない場合は、次の手順を使用して、TPM へのアクセス権を手動で付与することもできます。

1. IoT Edge ランタイムに `tpm0` と `tpmrm0` へのアクセス権を付与する新しいルールを作成します。

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. 規則ファイルを開きます。

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. 次のアクセス情報を規則ファイルにコピーします。 4\.12 より前のカーネルを使用するデバイスには、`tpmrm0` が存在しない場合があります。 `tpmrm0` が存在しないデバイスでは、そのルールは安全に無視されます。

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. ファイルを保存して終了します。

1. `udev` システムをトリガーして、新しい規則を評価します。

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. 規則が正常に適用されたことを確認します。

   ```bash
   ls -l /dev/tpm*
   ```

   成功した場合の出力は、次のようになります。

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   適切なアクセス許可が適用されない場合は、コンピューターを再起動して `udev` を更新してみてください。

1. デバイスで行ったすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge ランタイムは、デバイスの TPM へのアクセスを仲介する TPM サービスに依存しています。 デバイスを自動的にプロビジョニングするには、このサービスが TPM にアクセスする必要があります。

TPM へのアクセス権を付与するには、systemd 設定をオーバーライドして、`aziottpm` サービスに root 特権を付与します。 サービス権限を昇格したくない場合は、次の手順を使用して、TPM へのアクセス権を手動で付与することもできます。

1. IoT Edge ランタイムに `tpm0` と `tpmrm0` へのアクセス権を付与する新しいルールを作成します。

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. 規則ファイルを開きます。

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. 次のアクセス情報を規則ファイルにコピーします。 4\.12 より前のカーネルを使用するデバイスには、`tpmrm0` が存在しない場合があります。 `tpmrm0` が存在しないデバイスでは、そのルールは安全に無視されます。

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. ファイルを保存して終了します。

1. `udev` システムをトリガーして、新しい規則を評価します。

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. 規則が正常に適用されたことを確認します。

   ```bash
   ls -l /dev/tpm*
   ```

   成功した場合の出力は、次のようになります。

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   適切なアクセス許可が適用されない場合は、コンピューターを再起動して `udev` を更新してみてください。

1. デバイスで行った構成の変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>IoT Edge を再起動して正常にインストールされたことを確認する

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
まだ行っていない場合は、デバイスに加えたすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

IoT Edge ランタイムが実行されていることを確認します。

   ```bash
   sudo systemctl status iotedge
   ```

デーモンのログを調べます。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

プロビジョニング エラーが発生した場合は、構成の変更がまだ有効になっていない可能性があります。 IoT Edge を再起動をもう一度試してください。

   ```bash
   sudo systemctl daemon-reload
   ```

または、変更が新たな開始時に有効になる場合は、VM の再起動を試してください。
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
まだ行っていない場合は、デバイスに加えた構成の変更を適用します。

   ```bash
   sudo iotedge config apply
   ```

IoT Edge ランタイムが実行されていることを確認します。

   ```bash
   sudo iotedge system status
   ```

デーモンのログを調べます。

   ```cmd/sh
   sudo iotedge system logs
   ```

プロビジョニング エラーが発生した場合は、構成の変更がまだ有効になっていない可能性があります。 IoT Edge デーモンを再起動してみてください。

   ```bash
   sudo systemctl daemon-reload
   ```

または、変更が新たな開始時に有効になる場合は、VM の再起動を試してください。
:::moniker-end
<!-- end 1.2 -->

ランタイムが正常に起動した場合は、お使いの IoT ハブに移動して、新しいデバイスが自動的にプロビジョニングされたことを確認できます。 これで、デバイスで IoT Edge モジュールを実行する準備ができました。

実行中のモジュールを一覧表示します。

```cmd/sh
iotedge list
```

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

## <a name="next-steps"></a>次のステップ

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。

[Azure portal を使用して大規模な IoT Edge モジュールの展開と監視を行う](how-to-deploy-at-scale.md)方法または [Azure CLI を使用する](how-to-deploy-cli-at-scale.md)方法を理解します。