---
title: Linux on Windows で対称キーを使用して IoT Edge デバイスを作成してプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: Device Provisioning Service で対称キーの構成証明を使用し、Azure IoT Edge に対する Linux on Windows デバイスの大規模プロビジョニングをテストします
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d7d33b2f840b88226883bc9e6aff2823d7aa6cd2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699318"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>対称キーを使用して IoT Edge for Linux on Windows デバイスを大規模に作成してプロビジョニングする 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、対称キーを使用して 1 つ以上の [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) デバイスを自動プロビジョニングするエンド ツー エンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)を読んでから先に進んでください。

タスクは次のとおりです。

1. 単一のデバイスの **個別登録** を作成するか、一連のデバイスに対して **グループ登録** を作成する。
1. IoT Edge ランタイムがインストールされた Linux 仮想マシンをデプロイし、それを IoT ハブに接続する。

対称キーの構成証明は、Device Provisioning Service インスタンスを使用してデバイスを認証する簡単なアプローチです。 この構成証明の方法では、初めてデバイスのプロビジョニングを行う開発者または厳密なセキュリティ要件がない開発者に対して、"Hello world" エクスペリエンスを提示します。 [TPM](../iot-dps/concepts-tpm-attestation.md) または [X.509 証明書](../iot-dps/concepts-x509-attestation.md)を使用するデバイス構成証明は、より安全であり、さらに厳格なセキュリティ要件に対して使用する必要があります。

## <a name="prerequisites"></a>前提条件

クラウド リソース:

* アクティブな IoT Hub。
* 自分の IoT ハブにリンクした、Azure 内の IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service インスタンスをお持ちでない場合は、IoT Hub Device Provisioning Service クイックスタートの「[新しい IoT Hub Device Provisioning Service を作成する](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)」および「[IoT ハブと Device Provisioning Service とをリンクさせる](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)」セクションの手順に従ってください。
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

      ![ダッシュボードの右上にある歯車アイコンを選択して、設定にアクセスする (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. **[Settings]\(設定\)** メニューの **[Gateway]\(ゲートウェイ\)** で **[Extensions]\(拡張機能\)** を選択します。

   1. **[Available extensions]\(利用できる拡張\)** タブの拡張機能の一覧で、**Azure IoT Edge** を見つけます。 それを選択し、拡張機能の一覧の上にある **[Install]\(インストール\)** プロンプトを選択します。

   1. インストールが完了すると、 **[Installed extensions]\(インストールされている拡張機能\)** タブのインストールされている拡張機能の一覧に Azure IoT Edge が表示されます。

---

## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

DPS を介して 1 つ以上のデバイスをプロビジョニングする登録を作成します。

単一の IoT Edge デバイスをプロビジョニングする方法を求めている場合は、**個別の登録** を作成します。 複数のデバイスをプロビジョニングする必要がある場合は、DPS **グループ登録** を作成する手順に従います。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

Device Provisioning Service での登録の詳細については、[デバイス登録の管理方法](../iot-dps/how-to-manage-enrollments.md)に関するページを参照してください。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 個別登録を作成する

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[メカニズム]** で **[対称キー]** を選択します。

   1. デバイスの一意の **登録 ID** を指定します。

   1. 必要に応じて、デバイスの **IoT Hub デバイス ID** を指定します。 デバイス ID を使用して、個々のデバイスをモジュール展開のターゲットにすることができます。 デバイス ID を指定しなかった場合は、登録 ID が使用されます。

   1. **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 次に例を示します。

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. **[保存]** を選択します。

1. IoT Edge ランタイムのインストール時に使用する個別登録の **主キー** の値をコピーします。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS グループ登録を作成する

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用してグループ登録を作成することもできます。 詳細については、[az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[グループ名]** を指定します。

   1. 構成証明の種類として **[対称キー]** を選択します。

   1. **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 次に例を示します。

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. **[保存]** を選択します。

1. グループ登録で使用するデバイス キーを作成するときに使用する、登録グループの **主キー** の値をコピーします。

これで、登録グループが存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

#### <a name="derive-a-device-key"></a>デバイス キーを派生させる

グループ登録の一部としてプロビジョニングされる各デバイスには、プロビジョニング中に登録で対称キー構成証明を実行するために、派生デバイス キーが必要です。

デバイス キーを生成するには、DPS 登録グループからコピーしたキーを使用して、デバイスに対する一意の登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算し、結果を Base64 形式に変換します。

> [!IMPORTANT]
> デバイス コードには、登録の主キーやセカンダリ キーを含めないでください。

Windows では、次の例に示すように、PowerShell を使用して派生デバイス キーを生成することができます。

**KEY** の値を、前に書き留めた **主キー** で置き換えます。

**REG_ID** の値をデバイスの登録 ID に置き換えます。

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

派生デバイス キーの出力例を次に示します。

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

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

   ![デプロイが成功すると、メッセージの最後に "デプロイが成功しました" と表示される (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/successful-powershell-deployment.png)

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

   ![ターゲット デバイスの一覧が表示されている Windows Admin Center の最初のダッシュボード (PNG)](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/windows-admin-center-initial-dashboard.png)

   ローカル デバイスではなくリモート ターゲット デバイスにデプロイしようとして、目的のターゲット デバイスが一覧に表示されない場合は、[指示に従ってデバイスを追加します](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)。

1. **[+ 追加]** を選択してデプロイの作成を開始します。 このデプロイによって Linux 仮想マシンが作成され、IoT Edge ランタイムが自動的にインストールされます。

1. **[Add or create resources]\(リソースの追加または作成\)** ペインで、**Azure IoT Edge** のタイルを見つけます。 **[Create new]\(新規作成\)** を選択して、Azure IoT Edge for Linux on Windows の新しいインスタンスをデバイスにインストールします。

   IoT Edge for Linux on Windows がデバイスで既に実行されている場合は、 **[Add]\(追加\)** を選択して既存の IoT Edge デバイスに接続し、Windows Admin Center でそれを管理できます。

   ![Windows Admin Center の Azure IoT Edge のタイルで [Create New]\(新規作成\) を選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/resource-creation-tiles.png)

1. **[Create an Azure IoT Edge for Linux on Windows deployment]\(Azure IoT Edge for Linux on Windows のデプロイを作成する\)** ペインが開きます。 **[1. 作業の開始]** タブで、最小要件を満たしていることを確認し、 **[次へ]** を選択します。

1. ライセンス条項を確認し、 **[同意する]** をオンにして、 **[次へ]** を選択します。

1. 必要に応じて、 **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えることができます。

1. **Next:デプロイ**」に進みます。

   ![[Next: Deploy]\(次へ: デプロイ\) ボタンを、オプションの診断データの設定を切り替えた後で選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/select-next-deploy.png)

1. **[2. Deploy]\(2. デプロイ\)** タブの **[Select a target device]\(ターゲット デバイスの選択\)** で、一覧表示されているデバイスをクリックし、最小要件を満たしていることを確認します。 状態がサポート対象であることを確認した後、 **[次へ]** を選択します。

   ![デバイスを選択し、サポートされていることを確認する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/evaluate-supported-device.png)

1. **[2.2 設定]** タブで、デプロイの構成設定を確認します。

   >[!NOTE]
   >IoT Edge linux on Windows では、Linux 仮想マシンに内部 IP アドレスを割り当てる既定のスイッチが使用されます。 Windows マシンの外部からは、この内部 IP アドレスに到達できません。 Windows マシンにログオンしている間は、ローカルで仮想マシンに接続できます。
   >
   >Windows Server を使おうとしている場合は、IoT Edge for Linux on Windows をデプロイする前に[既定のスイッチを設定](how-to-create-virtual-switch.md)します。

   GPU をデプロイに割り当てて、GPU アクセラレーション対応の Linux モジュールを有効にできます。 これらの機能にアクセスするには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](gpu-acceleration.md)」に詳述されている前提条件をインストールする必要があります。 デプロイ プロセスのこの時点でこれらの前提条件のみをインストールする場合は、最初からもう一度開始する必要があります。

   GPU パススルーでは、デプロイに割り当てる GPU アダプターに応じて、**デバイス直接割り当て (DDA)** と **GPU 準仮想化 (GPU-PV)** の 2 つのオプションを使用できます。 それぞれの方法の例を下に示します。

   方法がデバイス直接割り当てでの場合は、Linux 仮想マシンに割り当てる GPU プロセッサの数を選択します。

   ![デバイス直接割り当ての GPU が有効になっている構成設定 (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/gpu-passthrough-direct-device-assignment.png)

   準仮想化の方法の場合、追加の設定は必要はありません。

   ![準仮想化の GPU が有効になっている構成設定 (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

   設定に問題がなければ、 **[次へ]** を選択します。

1. **[2.3 Deployment]\(2.3 デプロイ\)** タブで、デプロイの進行状況を確認できます。 完全なプロセスには、Azure IoT Edge for Linux on Windows パッケージのダウンロード、パッケージのインストール、ホスト デバイスの構成、Linux 仮想マシンの設定が含まれます。 このプロセスの完了には数分かかる場合があります。 デプロイが成功したときの表示を次に示します。

   ![デプロイが成功すると、各ステップに緑色のチェックマークと [Complete]\(完了\) ラベルが表示される (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/successful-deployment.png)

デプロイが完了すると、デバイスをプロビジョニングする準備が整います。 **Next:Connect\(次へ: 接続\)** を選択して **3. Connect\(3. 接続\)** タブに進み、Azure IoT Edge デバイスのプロビジョニングを処理します。

---

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するために使用される情報でデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値
* 作成したデバイス **登録 ID**
* 個別登録の **主キー**、またはグループ登録を使用するデバイスの [派生キー](#derive-a-device-key)のいずれか。

PowerShell または Windows Admin Center を使用して、IoT Edge デバイスをプロビジョニングできます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell の場合は、プレースホルダーの値をご自身の値で更新して、次のコマンドを実行します。

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center の場合は、次の手順に従います。

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[Symmetric Key (DPS)]\(対称キー (DPS)\)** を選択します。

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの DPS インスタンスに移動します。

1. DPS スコープ ID、デバイス登録 ID、および登録の主キーまたは派生キーを Windows Admin Center の各フィールドに入力します。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニング\)** を選択します。

   ![対称キーのプロビジョニングに必要なフィールドを入力した後、選択した方法でのプロビジョニングを選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/provisioning-with-selected-method-symmetric-key.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されます。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

---

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

Device Provisioning Service で作成したグループ登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成したグループ登録に関する登録の詳細を開きます。 **[登録レコード]** タブにアクセスして、そのグループに登録されているすべてのデバイスを表示します。

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

    1. サービスのログを取得します。

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

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。

次のこともできます。

* [IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
* [IoT Edge for Linux on Windows 仮想マシンで証明書を管理](how-to-manage-device-certificates.md)し、ホスト OS から Linux 仮想マシンにファイルを転送する方法について確認します。
* [プロキシ サーバー経由で通信するように IoT Edge デバイスを構成](how-to-configure-proxy-support.md)する方法について確認します。
