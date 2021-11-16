---
title: Linux で仮想 TPM を使用してデバイスを作成およびプロビジョニングする - Azure IoT Edge
description: Linux デバイスでシミュレートされた TPM を使用して Azure IoT Edge 用の Azure IoT Hub Device Provisioning Service をテストします。
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b353f683a64444a0ad89f062d0b826484260681f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852049"
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

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge のインストール

# <a name="physical-device"></a>[物理デバイス](#tab/physical-device)

IoT Edge デバイスとなる物理 Linux デバイス。

# <a name="virtual-machine"></a>[仮想マシン](#tab/virtual-machine)

[Hyper-V 対応の](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 開発マシン。 この記事では、Ubuntu Server VM を実行中の Windows 10 を使用します。

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

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

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
     scope_id: "SCOPE_ID_HERE"
     attestation:
       method: "tpm"
       registration_id: "REGISTRATION_ID_HERE"
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
   id_scope = "SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "REGISTRATION_ID_HERE"
   ```

1. `id_scope` と `registration_id` の値を、Device Provisioning Service とデバイス情報で更新します。 `scope_id` の値は、Device Provisioning Service のインスタンスの概要ページにある **ID スコープ** です。

1. 必要に応じて、ファイルの自動再プロビジョニング モード セクションを見つけます。 `auto_reprovisioning_mode` パラメーターを使用して、デバイスの再プロビジョニング動作を、`Dynamic`、`AlwaysOnStartup`、または `OnErrorOnly` のいずれかに構成します。 詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](../iot-dps/concepts-device-reprovision.md)」を参照してください。

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

## <a name="verify-successful-installation"></a>インストールの成功を確認する

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
