---
title: Windows 上で仮想 TPM を使用してデバイスをプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: Windows デバイス上のシミュレートされた TPM を使用して、Azure IoT Edge 用の Azure Device Provisioning Service をテストします
author: kgremban
ms.author: kgremban
ms.date: 10/06/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0b3d6bba1e99131b5a03b8ba5acfb760566437b1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234757"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Windows で TPM を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、トラステッド プラットフォーム モジュール (TPM) を使用して Azure IoT Edge for Windows デバイスを自動プロビジョニングする手順について説明します。 IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)に関する記事を読んでから先に進んでください。

この記事では、次の 2 つの手法の概要を説明します。 ソリューションのアーキテクチャに基づいて、使用する手法を選択してください。

- 物理 TPM ハードウェアを使用して Windows デバイスを自動プロビジョニングする。
- シミュレートされた TPM を実行して Windows デバイスを自動プロビジョニングする。 この手法は、テスト シナリオとしてのみお勧めします。 シミュレートされた TPM では、物理 TPM と同じセキュリティは提供されません。

手順は使用する手法によって異なるので、先に進む前に正しいタブが表示されていることをご確認ください。

タスクは次のとおりです。

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

* デバイスのプロビジョニング情報を取得します。
* デバイスの個別登録を作成する。
* IoT Edge ランタイムをインストールし、デバイスを IoT Hub に接続する。

# <a name="simulated-tpm"></a>[シミュレートされた TPM](#tab/simulated-tpm)

* シミュレートされた TPM を設定し、そのプロビジョニング情報を取得します。
* デバイスの個別登録を作成する。
* IoT Edge ランタイムをインストールし、デバイスを IoT Hub に接続する。

---

## <a name="prerequisites"></a>前提条件

前提条件は、物理 TPM と仮想 TPM ソリューションで同じです。

* Windows 開発マシン。 この記事では、Windows 10 を使用します。
* アクティブな IoT ハブ。
* 自分の IoT ハブにリンクされた、Azure 内にある IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service のインスタンスがない場合は、IoT Hub Device Provisioning Service クイックスタートの 2 つのセクションの手順に従ってください。
    - [新しい IoT Hub Device Provisioning Service を作成する](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)
    - [IoT ハブと Device Provisioning Service とをリンクさせる](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)
  * Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

> [!NOTE]
> Device Provisioning Service で TPM 構成証明を使用する場合は、TPM 2.0 が必要です。
>
> TPM を使用する場合、グループではなく個別の Device Provisioning Service の登録のみ作成できます。

## <a name="set-up-your-tpm"></a>TPM を設定する

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

このセクションでは、TPM の登録 ID と保証キーを取得するために使用できるツールをビルドします。

1. [Windows 開発環境の設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment)に関するセクションの手順に従って、Azure IoT device SDK for C をインストールしてビルドします。

1. 管理者特権のある PowerShell セッションで次のコマンドを実行して、TPM 用のデバイス プロビジョニング情報を取得する SDK ツールをビルドします。

   ```powershell
   cd azure-iot-sdk-c\cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client\tools\tpm_device_provision
   make
   .\tpm_device_provision
   ```

1. 出力ウィンドウに、デバイスの **登録 ID** と **保障キー** が表示されます。 後ほど Device Provisioning Service でデバイスの個別登録を作成するときに使用できるように、これらの値をコピーします。

> [!TIP]
> SDK ツールを使用してプロビジョニング情報を取得しない場合は、情報を取得するための別の方法を見つける必要があります。 TPM チップごとに固有の保証キーを、それに関連付けられている TPM チップの製造元から取得します。 TPM デバイス用の一意の登録 ID を生成できます。 たとえば、保証キーの SHA-256 ハッシュを作成できます。

登録 ID と保証キーを取得したら、次に進むことができます。

# <a name="simulated-tpm"></a>[シミュレートされた TPM](#tab/simulated-tpm)

使用可能な物理 TPM がないときにこのプロビジョニング方法をテストする必要がある場合は、デバイス上で TPM をシミュレートすることができます。

IoT Hub Device Provisioning Service には、TPM をシミュレートし、保証キーと登録 ID を返すサンプルが用意されています。

1. 希望の言語に基づいて、次の一覧からいずれかのサンプルを選択します。
1. シミュレートされた TPM を実行し、**保証キー** と **登録 ID** を収集したら、Device Provisioning Service のサンプルの手順の実行を中止します。 サンプル アプリケーションで登録を実行するために **Enter** キーを選択しないでください。
1. このシナリオのテストが完了するまで、シミュレートされた TPM をホストしているウィンドウを実行し続けます。
1. この記事に戻り、Device Provisioning Service の登録を作成し、デバイスを構成します。

シミュレートされた TPM のサンプル:

* [C](../iot-dps/quick-create-simulated-device-tpm.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm.md)

---

## <a name="create-a-device-provisioning-service-enrollment"></a>Device Provisioning Service 登録の作成

TPM のプロビジョニング情報を使用して、Device Provisioning Service で個別登録を作成します。

Device Provisioning Service 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[個別登録の追加]** を選択し、登録を構成する次の手順を完了します。

   1. **[メカニズム]** で **[TPM]** を選択します。

   1. 仮想マシン (VM) または物理デバイスからコピーした **保証キー** と **登録 ID** を指定します。

   1. 必要に応じて、デバイス ID を指定します。 デバイス ID を指定しなかった場合は、**登録 ID** が使用されます。

   1. **[True]** を選択し、VM または物理デバイスが IoT Edge デバイスであることを宣言します。

   1. デバイスの接続先になるリンクされた IoT ハブを選択するか、 **[Link to new IoT Hub]\(新しい IoT ハブへのリンク\)** を選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 詳細については、[大規模な IoT Edge モジュールの展開](how-to-deploy-at-scale.md)に関する記事を参照してください。

   1. **[保存]** を選択します。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

このセクションでは、Windows VM または IoT Edge の物理デバイスを準備します。 次に、IoT Edge をインストールします。

IoT Edge ランタイムをインストールできるように準備するには、デバイス上で完了する必要がある手順が 1 つあります。 デバイスには、コンテナー エンジンがインストールされている必要があります。

### <a name="install-iot-edge"></a>IoT Edge をインストールする

IoT Edge セキュリティ デーモンによって、IoT Edge デバイス上にセキュリティ標準が提供されて維持されます。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。

このセクションの手順では、インターネットに接続されているデバイスに最新バージョンをインストールする一般的なプロセスが示されています。 プレリリース バージョンなどの特定のバージョンをインストールする必要がある場合、またはオフラインの間にインストールする必要がある場合は、オフラインまたは特定のバージョンのインストールに関する手順に従ってください。

1. PowerShell を管理者として実行します。

   PowerShell (x86) ではなく、PowerShell の AMD64 セッションを使用します。 使用しているセッションの種類がわからない場合は、次のコマンドを実行します。

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

3. メッセージが表示されたら、デバイスを再起動します。

デバイスに IoT Edge をインストールするときは、他のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する。
* オフライン インストール用にローカル ディレクトリをインストーラーに指定する。

これらの他のパラメーターの詳細については、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

ランタイムがデバイスにインストールされたら、Device Provisioning Service と IoT Hub に接続するために使用される情報でデバイスを構成します。

1. 前のセクションで集めた Device Provisioning Service の **ID スコープ** とデバイスの **登録 ID** を把握しておきます。

1. 管理者モードで PowerShell ウィンドウを開きます。 IoT Edge をインストールするときは、PowerShell (x86) ではなく、PowerShell の AMD64 セッションを必ず使用してください。

1. `Initialize-IoTEdge` コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。 手動プロビジョニングではなく Device Provisioning Service を使用するには、`-Dps` フラグを使用します。

   `{scope_id}` および `{registration_id}` のプレースホルダー値を、前に収集したデータに置き換えます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に開始したら、IoT Hub に移動し、デバイスへの IoT Edge モジュールの展開を始めます。 ランタイムが正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。

1. IoT Edge サービスの状態を確認します。

    ```powershell
    Get-Service iotedge
    ```

1. 過去 5 分のサービス ログを調べます。

    ```powershell
    . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
    ```

1. 実行中のモジュールを一覧表示します。

    ```powershell
    iotedge list
    ```

## <a name="next-steps"></a>次のステップ

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。

[Azure portal を使用して大規模な IoT Edge モジュールの展開と監視を行う](how-to-deploy-at-scale.md)方法または [Azure CLI を使用する](how-to-deploy-cli-at-scale.md)方法を理解します。