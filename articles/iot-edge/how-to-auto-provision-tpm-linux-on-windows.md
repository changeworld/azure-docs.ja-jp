---
title: DPS と TPM を使用した Windows デバイスの自動プロビジョニング - Azure IoT Edge | Microsoft Docs
description: Device Provisioning Service と TPM 構成証明を使用して、IoT Edge for Linux on Windows の自動デバイス プロビジョニングを使用します
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 03ed23fbc22c632e3bc9a0e5afeb1163e7f99c80
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278502"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-with-tpm-attestation"></a>TPM 構成証明を使用して IoT Edge for Linux on Windows デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge デバイスは、[Device Provisioning Service](../iot-dps/index.yml) を使用して、Edge に対応していないデバイスと同じようにプロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニング](../iot-dps/about-iot-dps.md#provisioning-process)の概要を読んでから先に進んでください。

DPS では、グループ登録ではなく個別登録に対してのみ、IoT Edge デバイスのトラステッド プラットフォーム モジュール (TPM) 構成証明がサポートされます。

この記事では、次の手順に従って、IoT Edge for Linux on Windows を実行しているデバイスで自動プロビジョニングを使用する方法について説明します。

* IoT Edge for Linux on Windows をインストールする。
* 自分のデバイスから TPM 情報を取得する。
* デバイスの個別登録を作成する。
* TPM 情報を使用して自分のデバイスをプロビジョニングする。

## <a name="prerequisites"></a>前提条件

* Windows デバイス。 サポートされている Windows バージョンについては、「[オペレーティング システム](support.md#operating-systems)」を参照してください。
* アクティブな IoT ハブ。
* 自分の IoT ハブにリンクした、Azure 内の IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service インスタンスがない場合は、[IoT Hub DPS を設定する](../iot-dps/quick-setup-auto-provision.md)手順に従ってください。
  * Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge デバイスをプロビジョニングするときに使用します。

> [!NOTE]
> DPS で TPM 構成証明を使用する場合は、TPM 2.0 が必要です。これは、グループではなく個別の登録を作成する場合にのみ使用できます。

## <a name="install-iot-edge-for-linux-on-windows"></a>IoT Edge for Linux on Windows をインストールする

このセクションのインストール手順は、TPM プロビジョニング シナリオに固有の手順を強調するために簡略化されています。 前提条件とリモート インストール手順を含む詳細な手順については、「[Windows デバイスに Azure IoT Edge for Linux をインストールしてプロビジョニングする](how-to-install-iot-edge-on-windows.md)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows デバイス上で管理者特権の PowerShell セッションを開きます。

1. IoT Edge for Linux on Windows をダウンロードします。

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. IoT Edge for Linux on Windows をデバイスにインストールします。

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

1. デプロイが正常に実行されるようにするには、デバイスで実行ポリシーを `AllSigned` に設定する必要があります (まだ設定されていない場合)。

   1. 現在の実行ポリシーを確認します。

      ```powershell
      Get-ExecutionPolicy -List
      ```

   1. `local machine` の実行ポリシーが `AllSigned` でない場合は、実行ポリシーを更新します。

      ```powershell
      Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
      ```

1. IoT Edge for Linux on Windows をデプロイします。

   ```powershell
   Deploy-Eflow
   ```

1. 「`Y`」と入力して、ライセンス条項に同意します。

1. 必要に応じて、「`O`」または「`R`」を入力して **[オプションの診断データ]** をオンまたはオフに切り替えます。

1. IoT Edge for Linux on Windows がデバイスに正常にデプロイされると、出力に "**Deployment successful (デプロイ成功)** " とレポートされます。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center 用の Azure IoT Edge 拡張機能は、現在[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階にあります。 インストールと管理のプロセスは、一般公開される機能とは異なることがあります。

1. **Azure IoT Edge** 拡張機能を使用して Windows Admin Center を構成します。

1. Windows Admin Center の接続ページで、 **[追加]** を選択します。

1. **[Add or create resources]\(リソースの追加または作成\)** ペインで、**Azure IoT Edge** のタイルを見つけます。 **[Create new]\(新規作成\)** を選択して、Azure IoT Edge for Linux on Windows の新しいインスタンスをデバイスにインストールします。

1. デプロイ ウィザードの手順に従って、IoT Edge for Linux on Windows をインストールして構成します。

   1. **[作業の開始]** の手順で、前提条件を確認し、ライセンス条項に同意して、診断データを送信するかどうかを選択します。

   1. **[デプロイ]** の手順で、デバイスとその構成設定を選択します。 IoT Edge が自分のデバイスにデプロイされるので、その進行状況を観察します。

   1. **[次へ]** を選択して **[接続]** ステップに進み、デバイスのプロビジョニング情報を指定します。

1. **[プロビジョニング]** ページでデプロイ ウィザードの手順を中止します。続行する前にいくつかの手順を実行して、TPM を使用したプロビジョニング用にデバイスを準備します。 Windows Admin Center ウィンドウは開いたままにしておきます。この記事の最後に戻ってプロビジョニングの手順を完了するためです。

---

## <a name="enable-tpm-passthrough"></a>TPM パススルーを有効にする

IoT Edge for Linux on Windows 仮想マシンには TPM 機能があり、有効または無効にすることができます。 これは既定で無効になっています。 この機能が有効になっている場合、仮想マシンはホスト マシンの TPM にアクセスできます。

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

デバイスをプロビジョニングするには、TPM チップから情報を収集して Device Provisioning Service (DPS) のインスタンスに提供し、サービスが接続を試みたときにデバイスを認識できるようにする必要があります。

最初に、**保証キー** を確認する必要があります。これは、各 TPM チップに固有であり、それに関連付けられている TPM チップの製造元から取得されます。 次に、自分のデバイスの **登録 ID** を指定する必要があります。 たとえば、保証キーの SHA-256 ハッシュを作成することによって、TPM デバイスの一意の登録 ID を派生させることができます。

IoT Edge for Linux on Windows には、TPM からこの情報を取得するのに役立つ PowerShell 関数が用意されています。 スクリプトを使用するには、自分のデバイスでこれらの手順を実行します。

1. 管理者特権のセッションで PowerShell を開きます。

1. コマンドを実行します。

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

### <a name="simulate-a-tpm-optional"></a>TPM をシミュレートする (省略可能)

使用可能な物理 TPM がないときにこのプロビジョニング方法をテストする必要がある場合は、デバイス上で TPM をシミュレートすることができます。

IoT Hub Device Provisioning Service には、TPM をシミュレートして保証キーと登録 ID を返すサンプルが用意されています。

1. 希望の言語に基づいて、次の一覧からいずれかのサンプルを選択します。
1. シミュレートされた TPM を実行し、保証キーと登録 ID を収集したら、DPS サンプルの手順の実行を中止します。 サンプル アプリケーションで登録を実行するために *Enter* キーを押さないでください。
1. このシナリオのテストが完全に終了するまで、シミュレートされた TPM をホストしているウィンドウを実行し続けます。
1. この記事に戻って、DPS 登録を作成し、デバイスを構成します。

個々の登録を作成する方法については、[シミュレートされた TPM デバイスのプロビジョニングに関するクイックスタート ガイド](../iot-dps/quick-create-simulated-device-tpm.md)を参照してください。


## <a name="create-a-device-enrollment-entry"></a>デバイス登録エントリを作成する

Device Provisioning Service では、2 種類の登録がサポートされています。 登録グループは、複数の関連するデバイスを登録するために使用されます。 個別登録は、1 台のデバイスを登録するために使用されます。 TPM 情報をデバイスのグループで共有することはできないため、TPM 構成証明では個別登録のみがサポートされます。 したがって、この記事では、個別登録の使用方法を示します。

1. [Azure portal](https://portal.azure.com) にサインインし、Device Provisioning Service のインスタンスに移動します。

1. Device Provisioning Service のメニューで、 **[登録を管理します]** を選択します。 上部にある **[個別登録の追加]** ボタンをクリックします。

1. **[登録の追加]** パネルで、次の情報を入力します。

   * ID 構成証明メカニズムとして **[TPM]** を選択します。
   * 前にメモしておいた値を基に、TPM デバイスの登録 ID と保証キーを入力します。
   * プロビジョニング サービスにリンクされた IoT ハブを選択します。
   * 必要に応じて、一意のデバイス ID を入力します。 指定しない場合には、デバイスを識別するために登録 ID が代わりに使用されます。
   * **[True]** を選択して、この登録が IoT Edge デバイス用のものであることを宣言します。

1. **[保存]** を選択して、個別登録を保存します。

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows デバイス上で管理者特権の PowerShell セッションを開きます。

1. Device Provisioning Service のインスタンスから収集した **スコープ ID** を使用して、デバイスをプロビジョニングします。

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **[接続]** 手順で、デバイスをプロビジョニングします。

   1. **DpsTpm** プロビジョニング方法を選択します。
   1. **スコープ ID** を指定します。これは、Device Provisioning Service のインスタンスから取得できます。

   ![DPS と TPM 構成証明を使用してデバイスをプロビジョニングします。](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニングする\)** を選択します。

1. IoT Edge がデバイスに正常にインストールおよびプロビジョニングされたら、 **[完了]** を選択してデプロイ ウィザードを終了します。

---

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

自分のデバイスで次のコマンドを使用して、IoT Edge が正常にインストールされ、起動されたことを確認します。

1. PowerShell セッションで次のコマンドを使用して、自分の IoT Edge for Linux on Windows 仮想マシンに接続します。

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >仮想マシンへの SSH 接続が許可されるアカウントは、それを作成したユーザーだけです。

1. ログインすると、次の Linux コマンドを使用して、実行中の IoT Edge モジュールの一覧を確認できます。

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

   * **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** セクションに、デバイスで実行中のモジュールが表示されます。 IoT Edge サービスが初めて開始されたときは、**edgeAgent** モジュールだけが実行中であると表示されるはずです。 edgeAgent モジュールが既定で実行され、デバイスにデプロイする追加モジュールのインストールと起動を支援します。

   * **[IoT Edge Status]\(IoT Edge の状態\)** セクションにはサービスの状態が表示され、 **[active (running)]\(アクティブ (実行中)\)** と報告されるはずです。

---

## <a name="next-steps"></a>次のステップ

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
