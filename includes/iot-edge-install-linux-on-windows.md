---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 890ec787bfc7a4316df9d27271460a90d5047175
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842699"
---
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
   >たとえば下のコマンドでは、4 つの vCPU コア、4 GB の RAM (MB で表現)、20 GB のディスク領域を持つ仮想マシンを作成します。
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >使用できるすべてのオプション パラメーターについては、「[IoT Edge for Linux on Windows 用の PowerShell 関数](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)」を参照してください。

   GPU をデプロイに割り当てて、GPU アクセラレーション対応の Linux モジュールを有効にできます。 これらの機能にアクセスするには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](../articles/iot-edge/gpu-acceleration.md)」に詳述されている前提条件をインストールする必要があります。

   GPU パススルーを使用するには、`Deploy-Eflow` コマンドに **gpuName**、**gpuPassthroughType**、**gpuCount** の各パラメーターを追加します。 使用できるすべてのオプション パラメーターについては、「[IoT Edge for Linux on Windows 用の PowerShell 関数](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)」を参照してください。

   >[!WARNING]
   >ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

1. ライセンス条項に同意するには、「Y」と入力します。

1. 必要に応じて、「O」または「R」を入力して **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えます。

1. デプロイが完了すると、PowerShell ウィンドウに、「**デプロイが成功しました**」と表示されます。

   ![デプロイが成功すると、メッセージの最後に "デプロイが成功しました" と表示される (PNG)。](./media/iot-edge-install-linux-on-windows/successful-powershell-deployment.png)

   デプロイが正常に完了したら、デバイスをプロビジョニングする準備が整いました。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center 用の Azure IoT Edge 拡張機能は、現在[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階にあります。 インストールと管理のプロセスは、一般公開される機能とは異なることがあります。

Azure IoT Edge for Linux on Windows をデバイスにインストールします。

1. [Azure IoT Edge for Linux on Windows インストーラー](https://aka.ms/AzEflowMSI)をダウンロードします。

1. Azure IoT Edge for Linux on Windows をインストールしたら、Windows Admin Center を開きます。

   Windows Admin Center 開始ページにある接続の一覧に、Windows Admin Center を実行している PC を表すローカル ホスト接続が表示されます。 その他の管理対象のサーバー、PC、またはクラスターもここに表示されます。

   Windows Admin Center を使用して、ローカル デバイスまたはリモート マネージド デバイスのいずれかに、Azure IoT Edge for Linux on Windows をインストールして管理できます。 このガイドでは、Azure IoT Edge for Linux on Windows のデプロイのターゲット デバイスとしてローカル ホストを使用します。

1. **[すべての接続]** に、次のようにローカル デバイスが一覧表示されていることを確認します。

   ![ターゲット デバイスの一覧が表示されている Windows Admin Center の最初のダッシュボード (PNG)](./media/iot-edge-install-linux-on-windows/windows-admin-center-initial-dashboard.png)

   ローカル デバイスではなくリモート ターゲット デバイスにデプロイしようとして、目的のターゲット デバイスが一覧に表示されない場合は、[指示に従ってデバイスを追加します](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)。

1. **[+ 追加]** を選択してデプロイの作成を開始します。 このデプロイによって Linux 仮想マシンが作成され、IoT Edge ランタイムが自動的にインストールされます。

1. **[Add or create resources]\(リソースの追加または作成\)** ペインで、**Azure IoT Edge** のタイルを見つけます。 **[Create new]\(新規作成\)** を選択して、Azure IoT Edge for Linux on Windows の新しいインスタンスをデバイスにインストールします。

   IoT Edge for Linux on Windows がデバイスで既に実行されている場合は、 **[Add]\(追加\)** を選択して既存の IoT Edge デバイスに接続し、Windows Admin Center でそれを管理できます。

   ![Windows Admin Center の Azure IoT Edge のタイルで [Create New]\(新規作成\) を選択する (PNG)。](./media/iot-edge-install-linux-on-windows/resource-creation-tiles.png)

1. **[Create an Azure IoT Edge for Linux on Windows deployment]\(Azure IoT Edge for Linux on Windows のデプロイを作成する\)** ペインが開きます。 **[1. 作業の開始]** タブで、最小要件を満たしていることを確認し、 **[次へ]** を選択します。

1. ライセンス条項を確認し、 **[同意する]** をオンにして、 **[次へ]** を選択します。

1. 必要に応じて、 **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えることができます。

1. **Next:デプロイ**」に進みます。

   ![[Next: Deploy]\(次へ: デプロイ\) ボタンを、オプションの診断データの設定を切り替えた後で選択する (PNG)。](./media/iot-edge-install-linux-on-windows/select-next-deploy.png)

1. **[2. Deploy]\(2. デプロイ\)** タブの **[Select a target device]\(ターゲット デバイスの選択\)** で、一覧表示されているデバイスをクリックし、最小要件を満たしていることを確認します。 状態がサポート対象であることを確認した後、 **[次へ]** を選択します。

   ![デバイスを選択し、サポートされていることを確認する (PNG)。](./media/iot-edge-install-linux-on-windows/evaluate-supported-device.png)

1. **[2.2 設定]** タブで、デプロイの構成設定を確認します。 次の表は、設定とその既定値を示しています。

   | 設定 | 既定値 |
   | ------- | ------------- |
   | 仮想ディスクのサイズ (GB) | 16 |
   | メモリ (MB) | 1024 |
   | コア数 | 2 |
   | Azure IoT Edge のバージョン | 1.1 (LTS) |

   >[!NOTE]
   >IoT Edge linux on Windows では、Linux 仮想マシンに内部 IP アドレスを割り当てる既定のスイッチが使用されます。 Windows マシンの外部からは、この内部 IP アドレスに到達できません。 Windows マシンにログオンしている間は、ローカルで仮想マシンに接続できます。
   >
   >Windows Server を使おうとしている場合は、IoT Edge for Linux on Windows をデプロイする前に[既定のスイッチを設定](../articles/iot-edge/how-to-create-virtual-switch.md)します。

   GPU をデプロイに割り当てて、GPU アクセラレーション対応の Linux モジュールを有効にできます。 これらの機能にアクセスするには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](../articles/iot-edge/gpu-acceleration.md)」に詳述されている前提条件をインストールする必要があります。 デプロイ プロセスのこの時点でこれらの前提条件のみをインストールする場合は、最初からもう一度開始する必要があります。

   GPU パススルーでは、デプロイに割り当てる GPU アダプターに応じて、**デバイス直接割り当て (DDA)** と **GPU 準仮想化 (GPU-PV)** の 2 つのオプションを使用できます。

   方法がデバイス直接割り当てでの場合は、Linux 仮想マシンに割り当てる GPU プロセッサの数を選択します。

   準仮想化の方法の場合、追加の設定は必要はありません。

   >[!WARNING]
   >ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

   設定に問題がなければ、 **[次へ]** を選択します。

1. **[2.3 Deployment]\(2.3 デプロイ\)** タブで、デプロイの進行状況を確認できます。 完全なプロセスには、Azure IoT Edge for Linux on Windows パッケージのダウンロード、パッケージのインストール、ホスト デバイスの構成、Linux 仮想マシンの設定が含まれます。 このプロセスの完了には数分かかる場合があります。 デプロイが成功したときの表示を次に示します。

   ![デプロイが成功すると、各ステップに緑色のチェックマークと [Complete]\(完了\) ラベルが表示される (PNG)。](./media/iot-edge-install-linux-on-windows/successful-deployment.png)

1. デプロイが完了すると、デバイスをプロビジョニングする準備が整います。 **Next:Connect\(次へ: 接続\)** を選択して **3. Connect\(3. 接続\)** タブに進み、Azure IoT Edge デバイスのプロビジョニングを処理します。

---
