---
title: X.509 証明書を使用して Linux on Windows で IoT Edge デバイスを作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: X.509 証明書の構成証明を使用して、Device Provisioning Service による Azure IoT Edge 用デバイスの大規模プロビジョニングをテストします
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 309ac9bf4810d245695ad92c316a23d4d8571ac5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699222"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>X.509 証明書を使用して IoT Edge for Linux on Windows デバイスを大規模に作成してプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、X.509 証明書を使用して 1 つ以上の [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) デバイスを自動プロビジョニングするエンド ツー エンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニング](../iot-dps/about-iot-dps.md#provisioning-process)の概要を読んでから先に進んでください。

タスクは次のとおりです。

1. 証明書とキーを生成する。
1. 単一のデバイスの **個別登録** を作成するか、一連のデバイスに対して **グループ登録** を作成する。
1. IoT Edge ランタイムがインストールされた Linux 仮想マシンをデプロイし、それを IoT Hub に接続する。

X.509 証明書を構成証明メカニズムとして使用することは、実稼働環境を拡張し、デバイスのプロビジョニングを簡素化するための優れた方法です。 通常、X.509 証明書は信頼する証明書チェーンに配置されます。 自己署名証明書または信頼されたルート証明書から開始して、チェーン内の各証明書によって、それよりも 1 つ下位にある証明書が署名されます。 このパターンにより、ルート証明書から各中間証明書を経由して、デバイスにインストールされた最終的な "リーフ" 証明書に至る、信頼する委任チェーンが作成されます。

## <a name="prerequisites"></a>前提条件

* アクティブな IoT Hub。
* [Git](https://git-scm.com/download/) の最新バージョンがインストールされている。
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
* 自分の IoT ハブにリンクした、Azure 内の IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service インスタンスをお持ちでない場合は、IoT Hub Device Provisioning Service クイックスタートの[新しい IoT Hub Device Provisioning Service の作成](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service)および [IoT Hub と Device Provisioning Service のリンク](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service)に関するセクションの手順に従ってください。
  * Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。
* Azure IoT Edge for Linux on Windows のデプロイで **GPU アクセラレーション対応の Linux モジュール** を使用する場合は、いくつかの構成オプションを検討する必要があります。 お使いの GPU のアーキテクチャに応じて適切なドライバーをインストールする必要があり、Windows Insider Program ビルドを入手することが必要な場合があります。 構成上の必要性を特定し、それらの前提条件を満たすには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](gpu-acceleration.md)」を参照してください。

**PowerShell** または **Windows Admin Center** のいずれかを使用して、IoT Edge デバイスをインストールして管理できます。 ユーティリティごとに個別の前提条件があります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用する場合は、次の手順を使用して、Azure IoT Edge for Linux on Windows をダウンロードしてインストールします。

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

   カスタムのインストールおよび VHDX ディレクトリを指定するには、インストール コマンドに `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` および `VHDXDIR="<FULLY_QUALIFIED_PATH>"` パラメーターを追加します。

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

Windows Admin Center を使用する場合は、Windows Admin Center にアクセスできることと、Azure IoT Edge 拡張機能がインストールされていることを確認してください。

   1. [Windows Admin Center のインストーラー](https://aka.ms/wacdownload)をダウンロードして実行します。 インストール ウィザードの指示に従って Windows Admin Center をインストールします。

   1. インストールが完了したら、サポートされているブラウザーを使用して Windows Admin Center を開きます。 Windows 10 上の Microsoft Edge (Windows 10 バージョン 1709 以降)、Google Chrome、Microsoft Edge Insider などのブラウザーがサポートされています。

   1. Windows Admin Center を初めて使用するときに、使用する証明書の選択を求められます。 証明書として **[Windows Admin Center Client]\(Windows Admin Center クライアント\)** を選択します。

   1. Azure IoT Edge 拡張機能をインストールします。 Windows Admin Center ダッシュボードの右上にある歯車アイコンを選択します。

      ![ダッシュボードの右上にある歯車アイコンを選択して、設定にアクセスする (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. **[Settings]\(設定\)** メニューの **[Gateway]\(ゲートウェイ\)** で **[Extensions]\(拡張機能\)** を選択します。

   1. **[Available extensions]\(利用できる拡張\)** タブの拡張機能の一覧で、**Azure IoT Edge** を見つけます。 それを選択し、拡張機能の一覧の上にある **[Install]\(インストール\)** プロンプトを選択します。

   1. インストールが完了すると、 **[Installed extensions]\(インストールされている拡張機能\)** タブのインストールされている拡張機能の一覧に Azure IoT Edge が表示されます。

---

## <a name="generate-device-identity-certificates"></a>デバイス ID 証明書を生成する

デバイス ID 証明書は、信頼する証明書チェーンを介して最上位の X.509 証明機関 (CA) 証明書につながるリーフ証明書です。 デバイス ID 証明書の共通名 (CN) は、IoT ハブ内のデバイスに付けるデバイス ID に設定されている必要があります。

デバイス ID 証明書は、IoT Edge デバイスのプロビジョニングと、Azure IoT Hub でのデバイス認証を行うためにのみ使用されます。 証明書に署名するためのものではありません (IoT Edge デバイスから確認のためにモジュールまたはリーフ デバイスに提示される CA 証明書とは異なります)。 詳細については、[Azure IoT Edge 証明書の使用方法に関する詳細](iot-edge-certs.md)のページを参照してください。

デバイス ID 証明書を作成すると、2 つのファイルができます。証明書の公開部分を含む .cer または pem ファイルと、証明書の秘密キーを含む .cer または pem ファイルです。 DPS でグループ登録を使用する予定がある場合は、同じ信頼する証明書チェーン内にある中間証明書またはルート CA 証明書の公開部分も必要です。

X.509 を使用して自動プロビジョニングを設定するには、次のファイルが必要です。

* デバイス ID 証明書とその秘密キー証明書。 個別登録を作成すると、デバイス ID 証明書が DPS にアップロードされます。 秘密キーは IoT Edge ランタイムに渡されます。
* 完全なチェーン証明書。少なくともデバイス ID と中間証明書が含まれている必要があります。 完全なチェーン証明書は IoT Edge ランタイムに渡されます。
* 信頼する証明書チェーンからの中間証明書またはルート CA 証明書。 グループ登録を作成すると、この証明書は DPS にアップロードされます。

> [!NOTE]
> 現時点では、libiothsm の制限により、2038 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

### <a name="use-test-certificates-optional"></a>テスト証明書を使用する (省略可能)

新しい ID 証明書を作成するために使用できる証明機関がない場合、このシナリオを試すには、Azure IoT Edge の Git リポジトリにあるスクリプトを使用してテスト証明書を生成できます。 それらの証明書は開発テスト専用に設計されているため、運用環境では使用できません。

テスト証明書を作成するには、「[IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する](how-to-create-test-certificates.md)」の手順に従います。 証明書生成スクリプトを設定してルート CA 証明書を作成するために必要とされる、2 つのセクションを完了します。 次に、手順に従ってデバイス ID 証明書を作成します。 完了すると、次の証明書チェーンとキーの組が作成されます。

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

これらの証明書の両方が IoT Edge デバイス上に必要です。 DPS で個別登録を使用する場合は、.cert.pem ファイルをアップロードします。 DPS でグループ登録を使用する場合は、同じ信頼する証明書チェーン内に、アップロードする中間証明書またはルート CA 証明書も必要です。 デモ用の証明書を使用している場合は、グループ登録用に `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 証明書を使用します。

## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

生成された証明書とキーを使用して、DPS で 1 つまたは複数の IoT Edge デバイスの登録を作成します。

単一の IoT Edge デバイスをプロビジョニングする方法を求めている場合は、**個別の登録** を作成します。 複数のデバイスをプロビジョニングする必要がある場合は、DPS **グループ登録** を作成する手順に従います。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

Device Provisioning Service での登録の詳細については、[デバイス登録の管理方法](../iot-dps/how-to-manage-enrollments.md)に関するページを参照してください。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 個別登録を作成する

個別登録では、デバイスの ID 証明書の公開部分が渡され、デバイス上の証明書と照合されます。

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   * **メカニズム**: **[X.509]** を選択します。

   * **[プライマリ証明書の .pem ファイルまたは .cer ファイル]** : デバイス ID 証明書からパブリック ファイルをアップロードします。 スクリプトを使用してテスト証明書を生成した場合は、次のファイルを選択します。

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

   * **[IoT Hub のデバイス ID]** : 必要に応じて、デバイス ID を指定します。 デバイス ID を使用して、個々のデバイスをモジュール展開のターゲットにすることができます。 デバイス ID を指定しない場合は、X.509 証明書内の共通名 (CN) が使用されます。

   * **[IoT Edge デバイス]** : **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。

   * **[このデバイスを割り当てることができる IoT ハブを選択する]** : デバイスの接続先になるリンクされた IoT ハブを選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   * **[デバイス ツインの初期状態]** : 必要に応じて、デバイス ツインに追加するタグ値を追加します。 タグを使用して、デバイス グループを自動展開のターゲットにすることができます。 次に例を示します。

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

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS グループ登録を作成する

グループ登録では、個々のデバイス ID 証明書の生成に使用された信頼する証明書チェーンから、中間証明書またはルート CA 証明書を使用します。

#### <a name="verify-your-root-certificate"></a>ルート証明書を確認する

登録グループを作成するときに、確認済みの証明書を使用することもできます。 ルート証明書の所有権があることを示すことによって、DPS で証明書を確認できます。 詳細については、[X.509 CA 証明書の所有証明を行う方法](../iot-dps/how-to-verify-certificates.md)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. 左側のメニューから **[証明書]** を選択します。

1. **[追加]** を選択して新しい証明書を追加します。

1. 証明書のフレンドリ名を入力し、X.509 証明書の公開部分を表す .cer または .pem ファイルを参照します。

   デモ用の証明書を使用している場合は、`<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem` 証明書をアップロードします。

1. **[保存]** を選択します。

1. これで、その証明書が **[証明書]** ページに表示されます。 それを選択して証明書の詳細を開きます。

1. **[確認コードを生成します]** を選択した後、生成されたコードをコピーします。

1. 独自の CA 証明書を入手したか、デモ用の証明書を使用しているかにかかわらず、IoT Edge リポジトリに用意されている検証ツールを使用して、所有証明を確認することができます。 検証ツールでは、CA 証明書を使用して、指定された確認コードをサブジェクト名として持つ新しい証明書に署名します。

   ```powershell
   New-CACertsVerificationCert "<verification code>"
   ```

1. Azure portal の同じ証明書詳細ページで、新しく生成された検証証明書をアップロードします。

1. **[認証]** を選択します。

#### <a name="create-enrollment-group"></a>登録グループを作成する

Device Provisioning Service での登録の詳細については、[デバイス登録の管理方法](../iot-dps/how-to-manage-enrollments.md)に関するページを参照してください。

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用してグループ登録を作成することもできます。 詳細については、[az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[登録グループの追加]** を選択し、登録を構成する以下の手順を完了します。

   * **[グループ名]** : このグループ登録の覚えやすい名前を入力します。

   * **[構成証明の種類]** : **[Certificate]** を選択します。

   * **[IoT Edge デバイス]** : **[True]** を選択します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

   * **[証明書の種類]** : **[CA 証明書]** を選択します。

   * **[プライマリ証明書]** : ドロップダウン リストからご利用の証明書を選択します。

   * **[このデバイスを割り当てることができる IoT ハブを選択する]** : デバイスの接続先になるリンクされた IoT ハブを選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   * **[デバイス ツインの初期状態]** : 必要に応じて、デバイス ツインに追加するタグ値を追加します。 タグを使用して、デバイス グループを自動展開のターゲットにすることができます。 次に例を示します。

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

これで、これらのデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

---

以上で、次のセクションに進む準備が整いました。

## <a name="create-a-new-iot-edge-for-linux-on-windows-deployment"></a>新しい IoT Edge for Linux on Windows のデプロイを作成する

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

1. ターゲット デバイスの実行ポリシーがまだ `AllSigned` に設定されていない場合、これを設定します。 最新の実行ポリシーをチェックしたり実行ポリシーを `AllSigned` に設定したりするためのコマンドについては、PowerShell の前提条件を参照してください。

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

   ![デプロイが成功すると、メッセージの最後に "デプロイが成功しました" と表示される (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-powershell-deployment.png)

デプロイが完了すると、デバイスをプロビジョニングする準備が整います。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center 用の Azure IoT Edge 拡張機能は、現在[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階にあります。 インストールと管理のプロセスは、一般公開される機能とは異なることがあります。

Windows Admin Center 開始ページにある接続の一覧に、Windows Admin Center を実行している PC を表すローカル ホスト接続が表示されます。 その他の管理対象のサーバー、PC、またはクラスターもここに表示されます。

Windows Admin Center を使用して、ローカル デバイスまたはリモート マネージド デバイスのいずれかに、Azure IoT Edge for Linux on Windows をインストールして管理できます。 このガイドでは、Azure IoT Edge for Linux on Windows のデプロイのターゲット デバイスとしてローカル ホストを使用します。

ローカル デバイスではなくリモート ターゲット デバイスにデプロイしようとして、目的のターゲット デバイスが一覧に表示されない場合は、[指示に従ってデバイスを追加します](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)。

   ![ターゲット デバイスの一覧が表示されている Windows Admin Center の最初のダッシュボード (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/windows-admin-center-initial-dashboard.png)

1. **[追加]** を選択します。

1. **[Add or create resources]\(リソースの追加または作成\)** ペインで、**Azure IoT Edge** のタイルを見つけます。 **[Create new]\(新規作成\)** を選択して、Azure IoT Edge for Linux on Windows の新しいインスタンスをデバイスにインストールします。

   IoT Edge for Linux on Windows がデバイスで既に実行されている場合は、 **[Add]\(追加\)** を選択して既存の IoT Edge デバイスに接続し、Windows Admin Center でそれを管理できます。

   ![Windows Admin Center の Azure IoT Edge のタイルで [Create New]\(新規作成\) を選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/resource-creation-tiles.png)

1. **[Create an Azure IoT Edge for Linux on Windows deployment]\(Azure IoT Edge for Linux on Windows のデプロイを作成する\)** ペインが開きます。 **[1. 作業の開始]** タブで、最小要件を満たしていることを確認し、 **[次へ]** を選択します。

1. ライセンス条項を確認し、 **[同意する]** をオンにして、 **[次へ]** を選択します。

1. 必要に応じて、 **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えることができます。

1. **Next:デプロイ**」に進みます。

   ![[Next: Deploy]\(次へ: デプロイ\) ボタンを、オプションの診断データの設定を切り替えた後で選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-next-deploy.png)

1. **[2. Deploy]\(2. デプロイ\)** タブの **[Select a target device]\(ターゲット デバイスの選択\)** で、一覧表示されているデバイスをクリックし、最小要件を満たしていることを確認します。 状態がサポート対象であることを確認した後、 **[次へ]** を選択します。

   ![デバイスを選択し、サポートされていることを確認する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/evaluate-supported-device.png)

1. **[2.2 設定]** タブで、デプロイの構成設定を確認します。

   >[!NOTE]
   >IoT Edge linux on Windows では、Linux 仮想マシンに内部 IP アドレスを割り当てる既定のスイッチが使用されます。 Windows マシンの外部からは、この内部 IP アドレスに到達できません。 Windows マシンにログオンしている間は、ローカルで仮想マシンに接続できます。
   >
   >Windows Server を使おうとしている場合は、IoT Edge for Linux on Windows をデプロイする前に既定のスイッチを設定します。

   GPU をデプロイに割り当てて、GPU アクセラレーション対応の Linux モジュールを有効にできます。 これらの機能にアクセスするには、「[Azure IoT Edge for Linux on Windows 用の GPU アクセラレーション](gpu-acceleration.md)」に詳述されている前提条件をインストールする必要があります。 デプロイ プロセスのこの時点でこれらの前提条件のみをインストールする場合は、最初からもう一度開始する必要があります。

   GPU パススルーでは、デプロイに割り当てる GPU アダプターに応じて、**デバイス直接割り当て (DDA)** と **GPU 準仮想化 (GPU-PV)** の 2 つのオプションを使用できます。 それぞれの方法の例を下に示します。

   方法がデバイス直接割り当てでの場合は、Linux 仮想マシンに割り当てる GPU プロセッサの数を選択します。

   ![デバイス直接割り当ての GPU が有効になっている構成設定 (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-direct-device-assignment.png)

   準仮想化の方法の場合、追加の設定は必要はありません。

   ![準仮想化の GPU が有効になっている構成設定 (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >ハードウェア デバイスのパススルーを有効にすると、セキュリティ上のリスクが増える可能性があります。 該当する場合は、GPU ベンダーが提供する、デバイスのリスク軽減ドライバーをお勧めします。 詳細については、「[Discrete Device Assignment を使用したグラフィックス デバイスのデプロイ](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)」を参照してください。

   設定に問題がなければ、 **[次へ]** を選択します。

1. **[2.3 Deployment]\(2.3 デプロイ\)** タブで、デプロイの進行状況を確認できます。 完全なプロセスには、Azure IoT Edge for Linux on Windows パッケージのダウンロード、パッケージのインストール、ホスト デバイスの構成、Linux 仮想マシンの設定が含まれます。 このプロセスの完了には数分かかる場合があります。 デプロイが成功したときの表示を次に示します。

   ![デプロイが成功すると、各ステップに緑色のチェックマークと [Complete]\(完了\) ラベルが表示される (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-deployment.png)

デプロイが完了すると、デバイスをプロビジョニングする準備が整います。 **Next:Connect\(次へ: 接続\)** を選択して **3. Connect\(3. 接続\)** タブに進み、Azure IoT Edge デバイスのプロビジョニングを処理します。

---

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するために使用される情報でデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値。 Azure portal で、使用している DPS インスタンスの概要ページから、この値を取得できます。
* デバイス上のデバイス ID 証明書チェーン ファイル。
* デバイス上のデバイス ID キー ファイル。

PowerShell または Windows Admin Center を使用して、IoT Edge デバイスをプロビジョニングできます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell の場合は、プレースホルダーの値をご自身の値で更新して、次のコマンドを実行します。

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center の場合は、次の手順に従います。

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[X509 Certificate (DPS)]\(X509 証明書 (DPS)\)** を選択します。

1. DPS スコープ ID、デバイス登録 ID、および登録の主キーまたは派生キーを Windows Admin Center の各フィールドに入力します。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニング\)** を選択します。

   ![対称キーのプロビジョニングに必要なフィールドを入力した後、選択した方法でのプロビジョニングを選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されます。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

---

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

Device Provisioning Service で作成したグループ登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成したグループ登録の詳細を開きます。 **[登録レコード]** タブにアクセスして、そのグループに登録されているすべてのデバイスを表示します。

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

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。

次のこともできます。

* [IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
* [IoT Edge for Linux on Windows 仮想マシンで証明書を管理](how-to-manage-device-certificates.md)し、ホスト OS から Linux 仮想マシンにファイルを転送する方法について確認します。
* [プロキシ サーバー経由で通信するように IoT Edge デバイスを構成](how-to-configure-proxy-support.md)する方法について確認します。
