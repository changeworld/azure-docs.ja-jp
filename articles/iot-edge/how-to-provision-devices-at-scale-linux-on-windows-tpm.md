---
title: TPM を使用して IoT Edge for Linux on Windows デバイスを作成してプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: Linux on Windows デバイスでシミュレートされた TPM を使用して Azure IoT Edge の Azure Device Provisioning Service をテストします。
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: b286dfd6fd4e494427b1094fc38039e575af1e17
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846085"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-by-using-a-tpm"></a>TPM を使用して IoT Edge for Linux on Windows デバイスを大規模に作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、トラステッド プラットフォーム モジュール (TPM) を使用して Azure IoT Edge for Linux on Windows デバイスを自動プロビジョニングする手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、「[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)」を読んでから先に進んでください。

この記事では、次の 2 つの手法の概要を説明します。 ソリューションのアーキテクチャに基づいて、使用する手法を選択してください。

- 物理 TPM ハードウェアを使用して Linux on Windows デバイスを自動プロビジョニングします。
- シミュレートされた TPM を使用して、Linux on Windows デバイスを自動プロビジョニングします。 この手法は、テスト シナリオとしてのみお勧めします。 シミュレートされた TPM では、物理 TPM と同じセキュリティは提供されません。

タスクは次のとおりです。

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

* IoT Edge for Linux on Windows をインストールする。
* 自分のデバイスから TPM 情報を取得する。
* デバイスの個別登録を作成する。
* TPM 情報を使用して自分のデバイスをプロビジョニングする。

# <a name="simulated-tpm"></a>[シミュレートされた TPM](#tab/simulated-tpm)

* IoT Edge for Linux on Windows をインストールする。
* シミュレートされた TPM を設定し、そのプロビジョニング情報を取得します。
* デバイスの個別登録を作成する。
* TPM 情報を使用して自分のデバイスをプロビジョニングする。

---

## <a name="prerequisites"></a>前提条件

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

> [!NOTE]
> Device Provisioning Service で TPM 構成証明を使用する場合は、TPM 2.0 が必要です。
>
> TPM を使用する場合、グループではなく個別の Device Provisioning Service の登録のみ作成できます。

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

TPM によるプロビジョニングを行うためにデバイスを準備するのには、いくつかの手順があります。 デバイスを準備する間は、デプロイを開いたままにしてください。 この記事の後半でデプロイに戻ります。

## <a name="enable-tpm-passthrough"></a>TPM パススルーを有効にする

IoT Edge for Linux on Windows VM には TPM 機能があり、有効または無効にすることができます。 これは既定で無効になっています。 この機能が有効になっている場合、VM はホスト マシンの TPM にアクセスできます。

1. 管理者特権のセッションで PowerShell を開きます。

1. まだ行っていない場合は、自分のデバイスの実行ポリシーを `AllSigned` に設定して、IoT Edge for Linux on Windows の PowerShell 関数を実行できるようにします。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. TPM 機能をオンにします。

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>デバイスから TPM 情報を取得する

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

デバイスをプロビジョニングするには、TPM チップの **保証キー** と、デバイスの **登録 ID** が必要です。 デバイス プロビジョニング サービスのインスタンスにこの情報を提供して、サービスが接続を試みたときにデバイスを認識できるようにします。

保証キーは、各 TPM チップに固有です。 これは、関連付けられている TPM チップの製造元から取得されます。 たとえば、保証キーの SHA-256 ハッシュを作成することによって、TPM デバイスの一意の登録 ID を派生させることができます。

IoT Edge for Linux on Windows には、TPM からこの情報を取得するのに役立つ PowerShell スクリプトが用意されています。 スクリプトを使用するには、自分のデバイスでこれらの手順を実行します。

1. 管理者特権のセッションで PowerShell を開きます。

1. コマンドを実行します。

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

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

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows デバイス上で管理者特権の PowerShell セッションを開きます。

1. Device Provisioning Service のインスタンスから収集した **スコープ ID** を使用して、デバイスをプロビジョニングします。

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "SCOPE_ID_HERE"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **[接続]** 手順で、デバイスをプロビジョニングします。

   1. **DpsTpm** プロビジョニング方法を選択します。
   1. **スコープ ID** を指定します。これは、Device Provisioning Service のインスタンスから取得します。

      ![Device Provisioning Service と TPM 構成証明を使用したデバイスのプロビジョニングを示すスクリーンショット。](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニングする\)** を選択します。

1. IoT Edge がデバイスに正常にインストールおよびプロビジョニングされたら、 **[完了]** を選択してデプロイ ウィザードを終了します。

---

## <a name="verify-successful-installation"></a>インストールの成功を確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

自分のデバイスで次のコマンドを使用して、IoT Edge が正常にインストールされ、起動されたことを確認します。

1. PowerShell セッションで次のコマンドを使用して、自分の IoT Edge for Linux on Windows VM に接続します。

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >VM に SSH 接続できる唯一のアカウントは、VM を作成したユーザーです。

1. サインインすると、次の Linux コマンドを使用して、実行中の IoT Edge モジュールの一覧を確認できます。

   ```bash
   sudo iotedge list
   ```

1. IoT Edge サービスのトラブルシューティングを行う必要がある場合は、次の Linux コマンドを使用します。

    1. サービスのトラブルシューティングが必要な場合は、サービス ログを取得します。

       ```bash
       sudo journalctl -u iotedge
       ```

    2. `check` ツールを使用して、デバイスの構成と接続の状態を確認します。

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Windows Admin Center で接続されているデバイスの一覧から IoT Edge デバイスを選択して、それに接続します。

1. デバイスの概要ページに、デバイスに関するいくつかの情報が表示されます。

   * **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** セクションに、デバイスで実行中のモジュールが表示されます。 IoT Edge サービスが初めて開始されたときは、**edgeAgent** モジュールだけが実行中であると表示されるはずです。 edgeAgent モジュールが既定で実行され、デバイスにデプロイする他のモジュールのインストールと起動を支援します。
   * **[IoT Edge Status]\(IoT Edge の状態\)** セクションにはサービスの状態が表示され、 **[active (running)]\(アクティブ (実行中)\)** と報告されるはずです。

---

## <a name="next-steps"></a>次の手順

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。

使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
