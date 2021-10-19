---
title: TPM を使用して IoT Edge for Linux on Windows デバイスをプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: Linux on Windows デバイスでシミュレートされた TPM を使用して Azure IoT Edge の Azure デバイス プロビジョニング サービスをテストする
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d6943fa78960760a665f0abad51f0839da9d63bb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661607"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-using-a-tpm"></a>TPM を使用して IoT Edge for Linux on Windows デバイスを大規模に作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、トラステッド プラットフォーム モジュール (TPM) を使用して IoT Edge for Linux on Windows デバイスを自動プロビジョニングするエンドツーエンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニング](../iot-dps/about-iot-dps.md#provisioning-process)の概要を読んでから先に進んでください。

この記事では、次の 2 つの手法の概要を説明します。 ソリューションのアーキテクチャに基づいて、使用する手法を選択してください。

1. 物理 TPM ハードウェアを使用して Linux on Windows デバイスを自動プロビジョニングします。
1. シミュレートされた TPM を使用して、Linux on Windows デバイスを自動プロビジョニングします。 シミュレートされた TPM では、物理 TPM と同じセキュリティが提供されないため、この手法は、テスト シナリオとしてのみ推奨されます。

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

クラウド リソース:

* アクティブな IoT Hub。
* 自分の IoT ハブにリンクした、Azure 内の IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service インスタンスをお持ちでない場合は、IoT Hub Device Provisioning Service クイックスタートの[新しい IoT Hub Device Provisioning Service の作成](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)および [IoT Hub と Device Provisioning Service のリンク](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)に関するセクションの手順に従ってください。
  * Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

開発マシン:

* 以下の最小システム要件を備えた Windows デバイス:
  * Windows 10 バージョン 1809 以降、ビルド 17763 以降
  * Professional、Enterprise、または Server エディション
  * 最小空きメモリ容量: 1 GB
  * 最小空きディスク領域:10 GB
  * 仮想化サポート
    * Windows 10 で Hyper-V を有効にします。 詳細については、「[Windows 10 上に Hyper-V をインストールする](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)」を参照してください。
    * Windows サーバーで、Hyper-V ロールをインストールし、既定のネットワーク スイッチを作成します。 詳細については、「[Azure IoT Edge for Linux on Windows の入れ子になった仮想化](nested-virtualization.md)」を参照してください。
    * 仮想マシンで、入れ子になった仮想化を構成します。 詳細については、[入れ子になった仮想化](nested-virtualization.md)に関するページを参照してください。
  * ネットワークのサポート
    * Windows Server には既定のスイッチは付属していません。 EFLOW を Windows Server デバイスにデプロイする前に、仮想スイッチを作成する必要があります。  詳細については、[Windows 上の Linux のために仮想スイッチを作成する](how-to-create-virtual-switch.md)ことに関する記事を参照してください。
    * デスクトップ バージョンの Windows には、EFLOW のインストールに使用できる既定のスイッチが付属しています。 必要な場合は、独自のカスタム仮想スイッチを作成できます。

   > [!TIP]
   > Azure IoT Edge for Linux on Windows のデプロイで **GPU アクセラレーション対応の Linux モジュール** を使用する場合は、いくつかの構成オプションを検討する必要があります。 お使いの GPU のアーキテクチャに応じて適切なドライバーをインストールする必要があり、Windows Insider Program ビルドを入手することが必要な場合があります。 構成上の必要性を特定し、それらの前提条件を満たすには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](gpu-acceleration.md)」を参照してください。

**PowerShell** または **Windows Admin Center** を使用して、IoT Edge デバイスを管理できます。 ユーティリティごとに個別の前提条件があります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用する場合は、次の手順に従って、Azure IoT Edge for Linux on Windows のインストールと Linux 仮想マシンのデプロイに使用するターゲット デバイスを準備してください。

1. ターゲット デバイスの実行ポリシーを `AllSigned` に設定します。 現在の実行ポリシーは、管理者特権の PowerShell プロンプトで次のコマンドを使用して確認できます。

   ```powershell
   Get-ExecutionPolicy -List
   ```

   `local machine` の実行ポリシーが `AllSigned` になっていない場合は、次のようにして実行ポリシーを設定できます。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Azure IoT Edge for Linux on Windows PowerShell モジュールの詳細については、[PowerShell 関数リファレンス](reference-iot-edge-for-linux-on-windows-functions.md)を参照してください。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center を使用する場合は、次の手順に従って Windows Admin Center をダウンロードし、Windows Admin Center Azure IoT Edge 拡張機能をインストールしてください。

   1. [Windows Admin Center のインストーラー](https://aka.ms/wacdownload)をダウンロードして実行します。 インストール ウィザードの指示に従って Windows Admin Center をインストールします。

   1. インストールが完了したら、サポートされているブラウザーを使用して Windows Admin Center を開きます。 Windows 10 上の Microsoft Edge (Windows 10 バージョン 1709 以降)、Google Chrome、Microsoft Edge Insider などのブラウザーがサポートされています。

   1. Windows Admin Center を初めて使用するときに、使用する証明書の選択を求められます。 証明書として **[Windows Admin Center Client]\(Windows Admin Center クライアント\)** を選択します。

   1. Azure IoT Edge 拡張機能をインストールします。 Windows Admin Center ダッシュボードの右上にある歯車アイコンを選択します。

      ![ダッシュボードの右上にある歯車アイコンを選択して、設定にアクセスする (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-gear-icon.png)

   1. **[Settings]\(設定\)** メニューの **[Gateway]\(ゲートウェイ\)** で **[Extensions]\(拡張機能\)** を選択します。

   1. **[Available extensions]\(利用できる拡張\)** タブの拡張機能の一覧で、**Azure IoT Edge** を見つけます。 それを選択し、拡張機能の一覧の上にある **[Install]\(インストール\)** プロンプトを選択します。

   1. インストールが完了すると、 **[Installed extensions]\(インストールされている拡張機能\)** タブのインストールされている拡張機能の一覧に Azure IoT Edge が表示されます。

---

> [!NOTE]
> DPS で TPM 構成証明を使用する場合は、TPM 2.0 が必要です。
>
> TPM を使用する場合は、グループではなく個々の DPS の登録のみを作成できます。

## <a name="install-iot-edge"></a>IoT Edge をインストールする

ターゲット デバイスに Azure IoT Edge for Linux on Windows をデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

IoT Edge for Linux on Windows をターゲット デバイスにインストールします。

> [!NOTE]
> 下記の PowerShell の手順では、IoT Edge for Linux on Windows をローカル デバイスにデプロイする方法の概要を示しています。 PowerShell を使用してリモート ターゲット デバイスにデプロイする場合は、[リモート PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) を使用してリモート デバイスへの接続を確立し、そのデバイスでこれらのコマンドをリモートで実行することができます。

1. 管理者特権の PowerShell セッションで、次の各コマンドを実行し、IoT Edge for Linux on Windows をダウンロードします。

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. IoT Edge for Linux on Windows をデバイスにインストールします。

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   インストール コマンドに `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` パラメーターと `VHDXDIR="<FULLY_QUALIFIED_PATH>"` パラメーターを追加することで、IoT Edge for Linux on Windows のカスタム インストールと VHDX ディレクトリを指定できます。

1. ターゲット デバイスの実行ポリシーがまだ `AllSigned` に設定されていない場合、これを設定します。 最新の実行ポリシーをチェックしたり実行ポリシーを `AllSigned` に設定したりするためのコマンドについては、PowerShell の前提条件を参照してください。

1. IoT Edge for Linux on Windows のデプロイを作成します。 このデプロイによって Linux 仮想マシンが作成され、IoT Edge ランタイムが自動的にインストールされます。

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >既定では `Deploy-Eflow` コマンドを実行すると、1 GB の RAM、1 つの仮想 CPU コア、16 GB のディスク領域を持つ Linux 仮想マシンを作成します。 ですが、VM に必要なリソースは、デプロイするワークロードに大きく左右されます。 ワークロードを処理するのに十分なメモリがなければ、VM は起動に失敗します。
   >
   >`Deploy-Eflow` コマンドのオプションでパラメーターを指定することで、仮想マシンで使用できるリソースをカスタマイズできます。
   >
   >たとえば下のコマンドでは、4 つの仮想 CPU コア、4 GB の RAM、20 GB のディスク領域を持つ仮想マシンを作成します。
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >使用できるすべてのオプション パラメーターについては、「[IoT Edge for Linux on Windows 用の PowerShell 関数](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)」を参照してください。

   GPU をデプロイに割り当てて、GPU アクセラレーション対応の Linux モジュールを有効にできます。 これらの機能にアクセスするには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](gpu-acceleration.md)」に詳述されている前提条件をインストールする必要があります。

   GPU パススルーを使用するには、`Deploy-Eflow` コマンドに **gpuName**、**gpuPassthroughType**、**gpuCount** の各パラメーターを追加します。 使用できるすべてのオプション パラメーターについては、「[IoT Edge for Linux on Windows 用の PowerShell 関数](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)」を参照してください。

   >[!WARNING]
   >ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

1. ライセンス条項に同意するには、「Y」と入力します。

1. 必要に応じて、「O」または「R」を入力して **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えます。

1. デプロイが完了すると、PowerShell ウィンドウに、「**デプロイが成功しました**」と表示されます。

   ![デプロイが成功すると、メッセージの最後に "デプロイが成功しました" と表示される (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-powershell-deployment.png)

デプロイが完了すると、デバイスをプロビジョニングする準備が整います。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center 用の Azure IoT Edge 拡張機能は、現在[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階にあります。 インストールと管理のプロセスは、一般公開される機能とは異なることがあります。

Azure IoT Edge for Linux on Windows をデバイスにインストールします。

1. [Azure IoT Edge for Linux on Windows インストーラー](https://aka.ms/AzEflowMSI)をダウンロードします。

1. Azure IoT Edge for Linux on Windows をインストールしたら、Windows Admin Center を開きます。

   Windows Admin Center 開始ページにある接続の一覧に、Windows Admin Center を実行している PC を表すローカル ホスト接続が表示されます。 その他の管理対象のサーバー、PC、またはクラスターもここに表示されます。

   Windows Admin Center を使用して、ローカル デバイスまたはリモート マネージド デバイスのいずれかに、Azure IoT Edge for Linux on Windows をインストールして管理できます。 このガイドでは、Azure IoT Edge for Linux on Windows のデプロイのターゲット デバイスとしてローカル ホストを使用します。

1. **[すべての接続]** に、次のようにローカル デバイスが一覧表示されていることを確認します。

   ![ターゲット デバイスの一覧が表示されている Windows Admin Center の最初のダッシュボード (PNG)](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/windows-admin-center-initial-dashboard.png)

   ローカル デバイスではなくリモート ターゲット デバイスにデプロイしようとして、目的のターゲット デバイスが一覧に表示されない場合は、[指示に従ってデバイスを追加します](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)。

1. **[+ 追加]** を選択してデプロイの作成を開始します。 このデプロイによって Linux 仮想マシンが作成され、IoT Edge ランタイムが自動的にインストールされます。

1. **[Add or create resources]\(リソースの追加または作成\)** ペインで、**Azure IoT Edge** のタイルを見つけます。 **[Create new]\(新規作成\)** を選択して、Azure IoT Edge for Linux on Windows の新しいインスタンスをデバイスにインストールします。

   IoT Edge for Linux on Windows がデバイスで既に実行されている場合は、 **[Add]\(追加\)** を選択して既存の IoT Edge デバイスに接続し、Windows Admin Center でそれを管理できます。

   ![Windows Admin Center の Azure IoT Edge のタイルで [Create New]\(新規作成\) を選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/resource-creation-tiles.png)

1. **[Create an Azure IoT Edge for Linux on Windows deployment]\(Azure IoT Edge for Linux on Windows のデプロイを作成する\)** ペインが開きます。 **[1. 作業の開始]** タブで、最小要件を満たしていることを確認し、 **[次へ]** を選択します。

1. ライセンス条項を確認し、 **[同意する]** をオンにして、 **[次へ]** を選択します。

1. 必要に応じて、 **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えることができます。

1. **Next:デプロイ**」に進みます。

   ![[Next: Deploy]\(次へ: デプロイ\) ボタンを、オプションの診断データの設定を切り替えた後で選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-next-deploy.png)

1. **[2. Deploy]\(2. デプロイ\)** タブの **[Select a target device]\(ターゲット デバイスの選択\)** で、一覧表示されているデバイスをクリックし、最小要件を満たしていることを確認します。 状態がサポート対象であることを確認した後、 **[次へ]** を選択します。

   ![デバイスを選択し、サポートされていることを確認する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/evaluate-supported-device.png)

1. **[2.2 設定]** タブで、デプロイの構成設定を確認します。

   >[!NOTE]
   >IoT Edge linux on Windows では、Linux 仮想マシンに内部 IP アドレスを割り当てる既定のスイッチが使用されます。 Windows マシンの外部からは、この内部 IP アドレスに到達できません。 Windows マシンにログオンしている間は、ローカルで仮想マシンに接続できます。
   >
   >Windows Server を使おうとしている場合は、IoT Edge for Linux on Windows をデプロイする前に[既定のスイッチを設定](how-to-create-virtual-switch.md)します。

   GPU をデプロイに割り当てて、GPU アクセラレーション対応の Linux モジュールを有効にできます。 これらの機能にアクセスするには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](gpu-acceleration.md)」に詳述されている前提条件をインストールする必要があります。 デプロイ プロセスのこの時点でこれらの前提条件のみをインストールする場合は、最初からもう一度開始する必要があります。

   GPU パススルーでは、デプロイに割り当てる GPU アダプターに応じて、**デバイス直接割り当て (DDA)** と **GPU 準仮想化 (GPU-PV)** の 2 つのオプションを使用できます。 それぞれの方法の例を下に示します。

   方法がデバイス直接割り当てでの場合は、Linux 仮想マシンに割り当てる GPU プロセッサの数を選択します。

   ![デバイス直接割り当ての GPU が有効になっている構成設定 (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-direct-device-assignment.png)

   準仮想化の方法の場合、追加の設定は必要はありません。

   ![準仮想化の GPU が有効になっている構成設定 (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

   設定に問題がなければ、 **[次へ]** を選択します。

1. **[2.3 Deployment]\(2.3 デプロイ\)** タブで、デプロイの進行状況を確認できます。 完全なプロセスには、Azure IoT Edge for Linux on Windows パッケージのダウンロード、パッケージのインストール、ホスト デバイスの構成、Linux 仮想マシンの設定が含まれます。 このプロセスの完了には数分かかる場合があります。 デプロイが成功したときの表示を次に示します。

   ![デプロイが成功すると、各ステップに緑色のチェックマークと [Complete]\(完了\) ラベルが表示される (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-deployment.png)

1. デプロイが完了すると、デバイスをプロビジョニングする準備が整います。 **Next:Connect\(次へ: 接続\)** を選択して **3. Connect\(3. 接続\)** タブに進み、Azure IoT Edge デバイスのプロビジョニングを処理します。

**[プロビジョニング]** ページでデプロイ ウィザードの手順を中止します。続行する前にいくつかの手順を実行して、TPM を使用したプロビジョニング用にデバイスを準備します。 Windows Admin Center ウィンドウは開いたままにしておきます。この記事の最後に戻ってプロビジョニングの手順を完了するためです。

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

# <a name="physical-tpm"></a>[物理 TPM](#tab/physical-tpm)

デバイスをプロビジョニングするには、TPM チップから情報を収集して Device Provisioning Service (DPS) のインスタンスに提供し、サービスが接続を試みたときにデバイスを認識できるようにする必要があります。

最初に、**保証キー** を確認する必要があります。これは、各 TPM チップに固有であり、それに関連付けられている TPM チップの製造元から取得されます。 次に、自分のデバイスの **登録 ID** を指定する必要があります。 たとえば、保証キーの SHA-256 ハッシュを作成することによって、TPM デバイスの一意の登録 ID を派生させることができます。

IoT Edge for Linux on Windows には、TPM からこの情報を取得するのに役立つ PowerShell スクリプトが用意されています。 スクリプトを使用するには、自分のデバイスでこれらの手順を実行します。

1. 管理者特権のセッションで PowerShell を開きます。

1. [iotedge-eflow](https://github.com/Azure/iotedge-eflow) リポジトリをクローンします。

   ```powershell
   git clone https://github.com/Azure/iotedge-eflow.git
   ```

1. ダウンロードしたモジュールをインポートします。

   ```powershell
   Import-Module <path>\iotedge-eflow\samples\scripts\EflowTpmProvisioningInfo.ps1
   ```

1. コマンドを実行します。

   ```powershell
   Get-EflowVmTpmProvisioningInformation
   ```

# <a name="simulated-tpm"></a>[シミュレートされた TPM](#tab/simulated-tpm)

使用可能な物理 TPM がないときにこのプロビジョニング方法をテストする必要がある場合は、デバイス上で TPM をシミュレートすることができます。

IoT Hub Device Provisioning Service には、TPM をシミュレートし、**保証キー** と **登録 ID** を返すサンプルが用意されています。

1. 希望の言語に基づいて、次の一覧からいずれかのサンプルを選択します。
1. シミュレートされた TPM を実行し、**保証キー** と **登録 ID** を収集したら、DPS サンプルの手順の実行を中止します。 サンプル アプリケーションで登録を実行するために *Enter* キーを押さないでください。
1. このシナリオのテストが完了するまで、シミュレートされた TPM をホストしているウィンドウを実行し続けます。
1. この記事に戻って、DPS 登録を作成し、デバイスを構成します。

シミュレートされた TPM のサンプル:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

TPM のプロビジョニング情報を使用して、Device Provisioning Service で個別登録を作成します。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[個別登録の追加]** を選択し、登録を構成する次の手順を完了します。

   1. **[メカニズム]** で **[TPM]** を選択します。

   1. 仮想マシンまたは物理デバイスからコピーした **保証キー** と **登録 ID** を指定します。

   1. 必要に応じて、デバイス ID を指定します。 デバイス ID を指定しなかった場合は、登録 ID が使用されます。

   1. **[True]** を選択して、仮想マシンまたは物理デバイスが IoT Edge デバイスであることを宣言します。

   1. デバイスの接続先になるリンクされた IoT ハブを選択するか、 **[Link to new IoT Hub]\(新しい IoT ハブへのリンク\)** を選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 詳細については、[大規模な IoT Edge モジュールの展開](how-to-deploy-at-scale.md)に関する記事を参照してください。

   1. **[保存]** を選択します。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってプロビジョニングできます。

## <a name="configure-the-device-with-provisioning-information"></a>Configure the device with provisioning information

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