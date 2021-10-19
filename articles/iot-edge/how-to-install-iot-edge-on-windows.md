---
title: Azure IoT Edge for Linux を Windows にインストールする | Microsoft Docs
description: Windows デバイスに Azure IoT Edge をインストールする手順
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 81032760fa559cbdbae6382084e7a2d2e629d989
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659395"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device"></a>Windows デバイスに Azure IoT Edge for Linux をインストールしてプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 PC クラスから産業用サーバーまでのデバイスにランタイムをデプロイできます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

Azure IoT Edge for Linux on Windows を使用すると、Windows デバイスで実行される Linux 仮想マシンに IoT Edge をインストールできます。 Azure IoT Edge の Linux バージョンと、それと共にデプロイされる Linux モジュールが、仮想マシン上で実行されます。 そこから、Windows アプリケーションとコードおよび IoT Edge ランタイムとモジュールが、自由にやりとりできます。

この記事では、Windows デバイスに IoT Edge を設定する手順を示します。 これらの手順により、Windows デバイスで実行される IoT Edge ランタイムが含まれる Linux 仮想マシンをデプロイした後、その IoT Hub デバイス ID を使用してデバイスをプロビジョニングします。

>[!NOTE]
>IoT Edge for Linux on Windows は、Windows 環境で Azure IoT Edge を使用する場合に推奨されるエクスペリエンスです。 ただし、Windows コンテナーは引き続き利用できます。 Windows コンテナーを使用する場合は、「[Windows コンテナーを使用して Azure IoT Edge をインストールおよび管理する](how-to-install-iot-edge-windows-on-windows.md)」を参照してください。

## <a name="prerequisites"></a>[前提条件]

* 有効なサブスクリプションがある Azure アカウント。 [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。

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

* Windows Admin Center を使用して IoT Edge デバイスのインストールと管理を行う場合は、Windows Admin Center にアクセスできることと、Azure IoT Edge 拡張機能がインストールされていることを確認してください。

   1. [Windows Admin Center のインストーラー](https://aka.ms/wacdownload)をダウンロードして実行します。 インストール ウィザードの指示に従って Windows Admin Center をインストールします。

   1. インストールが完了したら、サポートされているブラウザーを使用して Windows Admin Center を開きます。 Windows 10 上の Microsoft Edge (Windows 10 バージョン 1709 以降)、Google Chrome、Microsoft Edge Insider などのブラウザーがサポートされています。

   1. Windows Admin Center を初めて使用するときに、使用する証明書の選択を求められます。 証明書として **[Windows Admin Center Client]\(Windows Admin Center クライアント\)** を選択します。

   1. Azure IoT Edge 拡張機能をインストールします。 Windows Admin Center ダッシュボードの右上にある歯車アイコンを選択します。

      ![ダッシュボードの右上にある歯車アイコンを選択して、設定にアクセスする。](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. **[Settings]\(設定\)** メニューの **[Gateway]\(ゲートウェイ\)** で **[Extensions]\(拡張機能\)** を選択します。

   1. **[Available extensions]\(利用できる拡張\)** タブの拡張機能の一覧で、**Azure IoT Edge** を見つけます。 それを選択し、拡張機能の一覧の上にある **[Install]\(インストール\)** プロンプトを選択します。

   1. インストールが完了すると、 **[Installed extensions]\(インストールされている拡張機能\)** タブのインストールされている拡張機能の一覧に Azure IoT Edge が表示されます。

* Azure IoT Edge for Linux on Windows のデプロイで **GPU アクセラレーション対応の Linux モジュール** を使用する場合は、いくつかの構成オプションを検討する必要があります。 お使いの GPU のアーキテクチャに応じて適切なドライバーをインストールする必要があり、Windows Insider Program ビルドを入手することが必要な場合があります。 構成上の必要性を特定し、それらの前提条件を満たすには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](gpu-acceleration.md)」を参照してください。

## <a name="choose-your-provisioning-method"></a>プロビジョニング方法を選択する

Azure IoT Edge for Linux on Windows でサポートされているプロビジョニング方法は次のとおりです。

* 単一デバイスの **手動プロビジョニング**。

  * 手動のプロビジョニングを準備するには、「[IoT Edge デバイスを IoT Hub に登録する](how-to-register-device.md)」の手順に従います。 対称キー認証または x.509 証明書認証のいずれかを選択してからこの記事に戻り、IoT Edge のインストールとプロビジョニングを行います。

* 1 つ以上のデバイスために IoT Hub Device Provisioning Service (DPS) を使用する **自動プロビジョニング**。

  * 使用する認証方法を選択してから、適切な記事の手順に従って DPS のインスタンスを設定し、1 つ以上のデバイスをプロビジョニングするための登録を作成します。 登録の種類の詳細については、[Azure IoT Hub Device Provisioning Service の概念](../iot-dps/concepts-service.md#enrollment)に関するページを参照してください。

    * [DPS と対称キーを使用して IoT Edge デバイスをプロビジョニングします](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)。
    * [DPS と X.509 証明書を使用して IoT Edge デバイスをプロビジョニングします](how-to-provision-devices-at-scale-linux-on-windows-x509.md)。
    * [DPS と TPM 構成証明を使用して IoT Edge デバイスをプロビジョニングします](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)。

## <a name="create-a-new-deployment"></a>新しいデプロイを作成する

ターゲット デバイスに Azure IoT Edge for Linux on Windows をデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

まだ行っていない場合は、IoT Edge for Linux on Windows をターゲット デバイスにインストールします。

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

1. ターゲット デバイスの実行ポリシーがまだ `AllSigned` に設定されていない場合、これを設定します。 現在の実行ポリシーは、管理者特権の PowerShell プロンプトで次のコマンドを使用して確認できます。

   ```powershell
   Get-ExecutionPolicy -List
   ```

   `local machine` の実行ポリシーが `AllSigned` になっていない場合は、次のようにして実行ポリシーを設定できます。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. IoT Edge for Linux on Windows のデプロイを作成します。

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

   GPU パススルーを使用するには、`Deploy-Eflow` コマンドに **gpuName**、**gpuPassthroughType**、**gpuCount** の各パラメーターを追加する必要があります。 使用できるすべてのオプション パラメーターについては、「[IoT Edge for Linux on Windows 用の PowerShell 関数](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)」を参照してください。

   >[!WARNING]
   >ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

1. ライセンス条項に同意するには、「Y」と入力します。

1. 必要に応じて、「O」または「R」を入力して **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えます。

1. デプロイが完了すると、PowerShell ウィンドウに、「**デプロイが成功しました**」と表示されます。

   ![デプロイが成功すると、メッセージの最後に [Deployment successful]\(デプロイ成功\) と表示されます](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment-2.png)

デプロイが完了すると、デバイスをプロビジョニングする準備が整います。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center 用の Azure IoT Edge 拡張機能は、現在[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階にあります。 インストールと管理のプロセスは、一般公開される機能とは異なることがあります。

Windows Admin Center 開始ページにある接続の一覧に、Windows Admin Center を実行している PC を表すローカル ホスト接続が表示されます。 その他の管理対象のサーバー、PC、またはクラスターもここに表示されます。

Windows Admin Center を使用して、ローカル デバイスまたはリモート マネージド デバイスのいずれかに、Azure IoT Edge for Linux on Windows をインストールして管理できます。 このガイドでは、Azure IoT Edge for Linux on Windows のデプロイのターゲット デバイスとしてローカル ホストを使用します。

ローカル デバイスではなくリモート ターゲット デバイスにデプロイしようとして、目的のターゲット デバイスが一覧に表示されない場合は、[指示に従ってデバイスを追加します](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)。

   ![ターゲット デバイスの一覧が表示されている Windows Admin Center の最初のダッシュボード](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. **[追加]** を選択します。

1. **[Add or create resources]\(リソースの追加または作成\)** ペインで、**Azure IoT Edge** のタイルを見つけます。 **[Create new]\(新規作成\)** を選択して、Azure IoT Edge for Linux on Windows の新しいインスタンスをデバイスにインストールします。

   IoT Edge for Linux on Windows がデバイスで既に実行されている場合は、 **[Add]\(追加\)** を選択して既存の IoT Edge デバイスに接続し、Windows Admin Center でそれを管理できます。

   ![Windows Admin Center の Azure IoT Edge のタイルで [Create New]\(新規作成\) を選択する](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. **[Create an Azure IoT Edge for Linux on Windows deployment]\(Azure IoT Edge for Linux on Windows のデプロイを作成する\)** ペインが開きます。 **[1. 作業の開始]** タブで、最小要件を満たしていることを確認し、 **[次へ]** を選択します。

1. ライセンス条項を確認し、 **[同意する]** をオンにして、 **[次へ]** を選択します。

1. 必要に応じて、 **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えることができます。

1. **Next:デプロイ**」に進みます。

   ![[Next: Deploy]\(次へ: デプロイ\) ボタンを、オプションの診断データの設定を切り替えた後で選択する](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. **[2. Deploy]\(2. デプロイ\)** タブの **[Select a target device]\(ターゲット デバイスの選択\)** で、一覧表示されているデバイスをクリックし、最小要件を満たしていることを確認します。 状態がサポート対象であることを確認した後、 **[次へ]** を選択します。

   ![デバイスを選択し、サポートされていることを確認する](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. **[2.2 設定]** タブで、デプロイの構成設定を確認します。

   >[!NOTE]
   >IoT Edge linux on Windows では、Linux 仮想マシンに内部 IP アドレスを割り当てる既定のスイッチが使用されます。 Windows マシンの外部からは、この内部 IP アドレスに到達できません。 Windows マシンにログオンしている間は、ローカルで仮想マシンに接続できます。
   >
   >Windows Server を使おうとしている場合は、IoT Edge for Linux on Windows をデプロイする前に既定のスイッチを設定します。

   GPU をデプロイに割り当てて、GPU アクセラレーション対応の Linux モジュールを有効にできます。 これらの機能にアクセスするには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](gpu-acceleration.md)」に詳述されている前提条件をインストールする必要があります。 デプロイ プロセスのこの時点でこれらの前提条件のみをインストールする場合は、最初からもう一度開始する必要があります。

   GPU パススルーでは、デプロイに割り当てる GPU アダプターに応じて、**デバイス直接割り当て (DDA)** と **GPU 準仮想化 (GPU-PV)** の 2 つのオプションを使用できます。 それぞれの方法の例を下に示します。

   方法がデバイス直接割り当てでの場合は、Linux 仮想マシンに割り当てる GPU プロセッサの数を選択します。

   ![デバイス直接割り当ての GPU が有効になっている構成設定。](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-direct-device-assignment.png)

   準仮想化の方法の場合、追加の設定は必要はありません。

   ![準仮想化の GPU が有効になっている構成設定。](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

   設定に問題がなければ、 **[次へ]** を選択します。

1. **[2.3 Deployment]\(2.3 デプロイ\)** タブで、デプロイの進行状況を確認できます。 完全なプロセスには、Azure IoT Edge for Linux on Windows パッケージのダウンロード、パッケージのインストール、ホスト デバイスの構成、Linux 仮想マシンの設定が含まれます。 このプロセスの完了には数分かかる場合があります。 デプロイが成功したときの表示を次に示します。

   ![デプロイが成功すると、各ステップに緑色のチェックマークと [Complete]\(完了\) ラベルが表示される](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

デプロイが完了すると、デバイスをプロビジョニングする準備が整います。 **Next:Connect\(次へ: 接続\)** を選択して **3. Connect\(3. 接続\)** タブに進み、Azure IoT Edge デバイスのプロビジョニングを処理します。

---

## <a name="provision-your-device"></a>デバイスをプロビジョニングする

デバイスのプロビジョニング方法を選択し、該当するセクションの指示に従います。 この記事では、対称キーまたは X.509 証明書のいずれかを使用してデバイスを手動でプロビジョニングする手順について説明します。 DPS を使用する自動プロビジョニングを使おうとしている場合は、適切なリンクに従ってプロビジョニングを完了します。

Windows Admin Center または管理者特権の PowerShell セッションを使用して、デバイスをプロビジョニングできます。

* 手動プロビジョニング:

  * [お使いの IoT Edge デバイスの接続文字列を使用する手動プロビジョニング](#manual-provisioning-using-the-connection-string)
  * [X.509 証明書を使用する手動プロビジョニング](#manual-provisioning-using-x509-certificates)

* 自動プロビジョニング:

  * [Device Provisioning Service (DPS) と対称キーを使用する自動プロビジョニング](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md#configure-the-device-with-provisioning-information)
  * [DPS と X.509 証明書を使用する自動プロビジョニング](how-to-provision-devices-at-scale-linux-on-windows-x509.md#configure-the-device-with-provisioning-information)
  * [DPS と TPM 構成証明を使用する自動プロビジョニング](how-to-provision-devices-at-scale-linux-on-windows-tpm.md#configure-the-device-with-provisioning-information)

### <a name="manual-provisioning-using-the-connection-string"></a>接続文字列を使用する手動プロビジョニング

このセクションでは、IoT Edge デバイスの接続文字列を使用して、手動でデバイスをプロビジョニングする方法を取り上げます。

まだの場合は、「[IoT Edge デバイスを IoT Hub に登録する](how-to-register-device.md)」の手順に従ってデバイスを登録し、その接続文字列を取得します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

管理者特権の PowerShell セッションで、ターゲット デバイスに対して次のコマンドを実行します。 プレースホルダー テキストは実際の値に置き換えてください。

```powershell
Provision-EflowVm -provisioningType ManualConnectionString -devConnString "<CONNECTION_STRING_HERE>"
```

`Provision-EflowVM` コマンドの詳細については、「[IoT Edge for Linux on Windows 用の PowerShell 関数](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm)」を参照してください。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[Connection String (Manual)]\(接続文字列 (手動)\)** を選択します。

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの IoT Hub の **[IoT Edge]** タブに移動します。

1. デバイスのデバイス ID をクリックします。 **[プライマリ接続文字列]** フィールドをコピーします。

1. デバイスの登録後、IoT Hub から取得した **デバイスの接続文字列** を指定します。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニングする\)** を選択します。

   ![デバイスの接続文字列を貼り付けた後、[Provisioning with the selected method]\(選択した方法でプロビジョニング\) を選択する](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されるはずです。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

---

### <a name="manual-provisioning-using-x509-certificates"></a>X.509 証明書を使用する手動プロビジョニング

このセクションでは、IoT Edge デバイスに関する X.509 証明書を使用して手動でデバイスをプロビジョニングする方法を取り上げます。

まだの場合は、「[IoT Edge デバイスを IoT Hub に登録する](how-to-register-device.md)」の手順に従って必要な証明書を準備し、デバイスを登録します。 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

ターゲット デバイス上に、デバイス ID 証明書と、対応する秘密キーを準備しておきます。 両方のファイルへの絶対パスを確認します。

管理者特権の PowerShell セッションで、ターゲット デバイスに対して次のコマンドを実行します。 プレースホルダー テキストは実際の値に置き換えてください。

```powershell
Provision-EflowVm -provisioningType ManualX509 -iotHubHostname "<HUB HOSTNAME>" -deviceId "<DEVICE ID>" -identityCertPath "<ABSOLUTE PATH TO IDENTITY CERT>" -identityPrivKeyPath "<ABSOLUTE PATH TO PRIVATE KEY>"
```

`Provision-EflowVM` コマンドの詳細については、「[IoT Edge for Linux on Windows 用の PowerShell 関数](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm)」を参照してください。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **[Azure IoT Edge デバイスのプロビジョニング]** ペインで、プロビジョニング方法のドロップダウンから **[ManualX509]** を選択します。

   ![X.509 証明書を使用する手動プロビジョニングを選択する](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-manual-x509.png)

1. 必要なパラメーターを指定します。

   * **IoT Hub ホスト名**: このデバイスの登録先 IoT ハブの名前。
   * **デバイス ID**: このデバイスの登録に使われている名前。
   * **証明書ファイル**: デバイス ID 証明書をアップロードします。証明書は仮想マシンに移動されて、デバイスのプロビジョニングに使用されます。
   * **秘密キー ファイル**: 秘密キー ファイルをアップロードします。キーは仮想マシンに移動されて、デバイスのプロビジョニングに使用されます。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニングする\)** を選択します。

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されるはずです。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

---

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

> [!IMPORTANT]
> IoT Edge for Linux on Windows の PowerShell パブリック関数を使おうとしている場合は、ターゲット デバイスでの実行ポリシーを必ず `AllSigned` に設定します。 [IoT Edge for Linux on Windows 用の PowerShell 関数](reference-iot-edge-for-linux-on-windows-functions.md)の前提条件がすべて満たされていることを確認してください。

1. PowerShell セッションで次のコマンドを使用して、お使いの IoT Edge for Linux on Windows 仮想マシンにログインします。

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

新しい IoT Edge デバイスを作成すると、Azure portal で状態コード `417 -- The device's deployment configuration is not set` が表示されます。 これは正常な状態であり、デバイスにモジュールをデプロイできる状態であることを表します。

## <a name="next-steps"></a>次のステップ

* [IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
* [IoT Edge for Linux on Windows 仮想マシンで証明書を管理](how-to-manage-device-certificates.md)し、ホスト OS から Linux 仮想マシンにファイルを転送する方法について確認します。
* [プロキシ サーバー経由で通信するように IoT Edge デバイスを構成](how-to-configure-proxy-support.md)する方法について確認します。
