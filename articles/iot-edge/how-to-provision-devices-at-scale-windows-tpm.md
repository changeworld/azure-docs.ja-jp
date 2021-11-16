---
title: Windows 上で仮想 TPM を使用してデバイスを作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: Windows デバイス上のシミュレートされた TPM を使用して、Azure IoT Edge 用の Azure Device Provisioning Service をテストします
author: kgremban
ms.author: kgremban
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4211cd69cddfea77ccd3f6e2a095ced6ce5effc3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853873"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-windows"></a>Windows で TPM を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、トラステッド プラットフォーム モジュール (TPM) を使用して Azure IoT Edge for Windows デバイスを自動プロビジョニングする手順について説明します。 IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)に関する記事を読んでから先に進んでください。

>[!NOTE]
>Windows コンテナーを使用する Azure IoT Edge は、Azure IoT Edge のバージョン 1.2 以降サポートされなくなります。
>
>IoT Edge を Windows デバイスで実行するための新しい方法である [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用を検討してください。
>
>Azure IoT Edge for Linux on Windows を使用する場合は、[同等のハウツーガイド](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)に記載されている手順に従います。

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

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge のインストール

Windows 開発マシン。 この記事では、Windows 10 を使用します。

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

<!-- Create an enrollment for your device using TPM provisioning information H2 and content -->
[!INCLUDE [tpm-create-a-device-provision-service-enrollment.md](../../includes/tpm-create-a-device-provision-service-enrollment.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

ランタイムがデバイスにインストールされたら、Device Provisioning Service と IoT Hub に接続するために使用される情報でデバイスを構成します。

1. 前のセクションで集めた Device Provisioning Service の **ID スコープ** とデバイスの **登録 ID** を把握しておきます。

1. 管理者モードで PowerShell ウィンドウを開きます。 IoT Edge をインストールするときは、PowerShell (x86) ではなく、PowerShell の AMD64 セッションを必ず使用してください。

1. `Initialize-IoTEdge` コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。 手動プロビジョニングではなく Device Provisioning Service を使用するには、`-Dps` フラグを使用します。

   `paste_scope_id_here` および `paste_registration_id_here` のプレースホルダー値を、前に収集したデータに置き換えます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId paste_scope_id_here -RegistrationId paste_registration_id_here
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
