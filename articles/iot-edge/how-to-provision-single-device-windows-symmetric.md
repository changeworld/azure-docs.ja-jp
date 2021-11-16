---
title: 対称キーを使用して Windows で IoT Edge デバイスを作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: 対称キーを使用して手動でプロビジョニングするために IoT Hub に 1 つの Windows IoT Edge を作成およびプロビジョニングする
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: v-tcassi
monikerRange: iotedge-2018-06
ms.openlocfilehash: c9ae8eea18225fca6634d55b48b80ec29e713135
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269938"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-symmetric-keys"></a>対称キーを使用して Windows で IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、Windows IoT Edge デバイスを登録およびプロビジョニングするエンドツーエンドの手順について説明します。

>[!NOTE]
>Windows コンテナーを使用する Azure IoT Edge は、Azure IoT Edge のバージョン 1.2 以降サポートされなくなります。
>
>IoT Edge を Windows デバイスで実行するための新しい方法である [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用を検討してください。

IoT ハブに接続するすべてのデバイスには、cloud-to-device または device-to-cloud の通信の追跡に使用されるデバイス ID があります。 デバイスをその接続情報で構成します。これには、IoT ハブ ホスト名、デバイス ID、IoT Hub に対して認証を行うためにデバイスで使用される情報が含まれます。

この記事の手順では、手動プロビジョニングと呼ばれるプロセスについて説明します。ここでは、1 つのデバイスを IoT ハブに接続します。 手動プロビジョニングの場合、IoT Edge デバイスの認証には 2 つのオプションがあります。

* **対称キー**: IoT Hub で新しいデバイス ID を作成すると、サービスによって 2 つのキーが作成されます。 いずれかのキーをデバイスに配置すると、認証時にそのキーが IoT Hub に提供されます。

  この認証方法は比較的すばやく開始できますが、それほど安全ではありません。

* **X.509 自己署名**: 2 つの X.509 ID 証明書を作成し、デバイスに配置します。 IoT Hub で新しいデバイス ID を作成するときは、両方の証明書の拇印を指定します。 デバイスでは IoT Hub に対して認証を行うときに、1 つの証明書が提示され、その証明書がその拇印と一致することが IoT によって検証されます。

  この認証方法はより安全であり、運用環境のシナリオの場合に推奨されます。

この記事では、認証方法として対称キーを使用する方法について説明します。 X.509 証明書を使用する場合は、「[x.509 証明書を使用して Windows で IoT Edge デバイスを作成およびプロビジョニングする](how-to-provision-single-device-windows-x509.md)」を参照してください。

> [!NOTE]
> 設定するデバイスが多数あり、それぞれを手動でプロビジョニングしたくない場合は、次の記事のいずれかを使用して、IoT Edge が IoT Hub Device Provisioning Service でどのように動作するかを確認してください。
>
> * [X.509 証明書を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-windows-x509.md)
> * [TPM を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-auto-provision-simulated-device-windows.md)
> * [対称キーを使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>[前提条件]

この記事では、IoT Edge デバイスを登録し、そこに IoT Edge をインストールする方法について説明します。 これらのタスクには、実行するために使用されるさまざまな前提条件とユーティリティがあります。 次に進む前に、すべての前提条件が満たされていることを確認してください。

### <a name="device-registration"></a>デバイス登録

デバイスを登録する手順には、**Azure portal**、**Visual Studio Code**、または **Azure CLI** を使用できます。 ユーティリティごとに個別の前提条件があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

> [!NOTE]
> 現在、Visual Studio Code 用の Azure IoT 拡張機能では、X.509 証明書を使用したデバイス登録はサポートされていません。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。

---

### <a name="iot-edge-installation"></a>IoT Edge のインストール

Windows デバイス

Windows コンテナーを使用する IoT Edge には、Windows バージョン 1809、ビルド 17763 が必要です。これは、最新の [Windows 長期サポート ビルド](/windows/release-information/)です。 サポートされている SKU の一覧については、[サポートされているシステムの一覧](support.md#operating-systems)を確認してください。

## <a name="register-your-device"></a>デバイスを登録する

デバイスを登録するには、好みに応じて **Azure portal**、**Visual Studio Code**、または **Azure CLI** を使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の IoT Hubで、IoT Edge デバイスは、Edge 対応ではない IoT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/how-to-provision-single-device-windows-symmetric/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。 このデバイス ID は、後で使用するのでメモしておいてください。
   * 認証の種類として **[対称キー]** を選択します。
   * 認証キーを自動生成して、新しいデバイスをハブに接続するために、既定の設定を使用します。

1. **[保存]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT ハブで操作を実行できます。 これらの操作を機能させるには、Azure アカウントにサインインし、ハブを選択する必要があります。

1. Visual Studio Code で **エクスプローラー** ビューを開きます。
1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

   ![[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) セクションを展開する](./media/how-to-provision-single-device-windows-symmetric/azure-iot-hub-devices.png)

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。
1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。
1. Azure サブスクリプションを選択します。
1. IoT ハブを選択します。

### <a name="register-a-new-device-with-visual-studio-code"></a>Visual Studio Code を使用して新しいデバイスを登録する

1. Visual Studio Code エクスプローラーで、 **[Azure IoT Hub]** セクションを展開します。
1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。
1. 表示されたテキスト ボックスにデバイス ID を指定します。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity create](/cli/azure/iot/hub/device-identity) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled
   ```

このコマンドには、3 つのパラメーターが含まれます。

* `--device-id` または `-d`: IoT ハブ内で一意のわかりやすい名前を指定します。
* `--hub-name` または `-n`: IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`:デバイスが IoT Edge デバイスであることを宣言します。

   ![az iot hub device-identity create output](./media/how-to-provision-single-device-windows-symmetric/create-edge-device-cli.png)

---

デバイスが IoT Hub に登録されたので、IoT Edge ランタイムのインストールとプロビジョニングを完了するために使用する情報を取得します。

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>登録済みデバイスを表示し、プロビジョニング情報を取得する

対称キー認証を使用するデバイスでは、IoT Edge ランタイムのインストールとプロビジョニングを完了するために接続文字列が必要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![Azure portal を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-provision-single-device-windows-symmetric/portal-view-devices.png)

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

対称キーを使用して認証を行うデバイスでは、接続文字列をポータルでコピーできます。

1. ポータルの **IoT Edge** ページで、IoT Edge デバイスの一覧からデバイス ID をクリックします。
2. **[プライマリ接続文字列]** または **[セカンダリ接続文字列]** のどちらかの値をコピーします。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できるほか、各 IoT Edge デバイスには **$edgeAgent** モジュールと **$edgeHub** モジュールがデプロイされています。

![VS Code を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-provision-single-device-windows-symmetric/view-devices.png)

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. **[Azure IoT Hub]** セクションでデバイスの ID を右クリックします。
1. **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

   接続文字列がクリップボードにコピーされます。

右クリック メニューから **[Get Device Info]\(デバイス情報の取得\)** を選択して、接続文字列を含むすべてのデバイス情報を出力ウィンドウに表示することもできます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。 [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) コマンドを使用すると、単一のデバイスの接続文字列が返されます。

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device_id] --hub-name [hub_name]
   ```

>[!TIP]
>`connection-string show` コマンドは、Azure IoT 拡張機能のバージョン 0.9.8 で導入され、これによって、非推奨の `show-connection-string` コマンドが置き換えられました。 このコマンドの実行中にエラーが発生した場合は、拡張機能のバージョンが 0.9.8 以降に更新されていることを確認してください。 詳細情報と最新の更新プログラムについては、[Azure CLI 用の Microsoft Azure IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)に関するページを参照してください。

`device-id` パラメーターの値は、大文字と小文字が区別されます。

デバイスで使用する接続文字列をコピーする場合は、接続文字列を囲んでいる引用符を含めないでください。

---

## <a name="install-iot-edge"></a>IoT Edge をインストールする

このセクションでは、Windows デバイスを IoT Edge 用に準備します。 次に、IoT Edge をインストールします。

Azure IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 [Moby](https://github.com/moby/moby) (Moby ベースのエンジン) はインストール スクリプトに含まれているので、エンジンをインストールする追加手順はありません。

IoT Edge ランタイムをインストールするには:

1. PowerShell を管理者として実行します。

   PowerShell(x86) ではなく、PowerShell の AMD64 セッションを使用します。 使用しているセッションの種類がわからない場合は、次のコマンドを実行します。

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

デバイスに IoT Edge をインストールするときは、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* オフライン インストール用にローカル ディレクトリをインストーラーに指定する。

これらの追加パラメーターの詳細については、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

これでコンテナー エンジンと IoT Edge ランタイムがデバイスにインストールされたので、次のステップに進み、クラウド ID と認証情報を使用してデバイスを設定できます。

1. IoT Edge デバイスで、PowerShell を管理者として実行します。

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * オフラインまたは特定のバージョンのインストール用に IoTEdgeSecurityDaemon.ps1 スクリプトをデバイスにダウンロードした場合は、スクリプトのローカル コピーを必ず参照してください。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
      ```

3. 入力を求められたら、前のセクションで取得したデバイス接続文字列を指定します。 このデバイス接続文字列により、物理デバイスと IoT Hub のデバイス ID が関連付けられ、認証情報が提供されます。

   デバイス接続文字列は次の形式をとります。疑問符は入れないでください。`HostName={IoT_hub_name}.azure-devices.net;DeviceId={device_name};SharedAccessKey={key}`

デバイスを手動でプロビジョニングする場合は、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* 特定の edgeAgent コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらの追加パラメーターの詳細については、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

## <a name="offline-or-specific-version-installation-optional"></a>オフラインまたは特定のバージョンのインストール (省略可能)

このセクションの手順は、標準のインストール手順では説明されていないシナリオを対象としています。 次のような場合が含まれます。

* オフラインの間に IoT Edge をインストールする
* リリース候補バージョンをインストールする
* 最新以外のバージョンをインストールする

インストール中に、次の 3 つのファイルがダウンロードされます。

* PowerShell スクリプト。これには、インストール手順が含まれています。
* Microsoft Azure IoT Edge cab。これには、IoT Edge セキュリティ デーモン (iotedged)、Moby コンテナー エンジン、および Moby CLI が含まれています。
* Visual C++ 再頒布可能パッケージ (VC ランタイム) インストーラー

インストール中にデバイスがオフラインになる予定の場合、または特定のバージョンの IoT Edge をインストールする場合は、これらのファイルを事前にデバイスにダウンロードできます。 インストール時には、インストール スクリプトによって、ダウンロード済みのファイルを含むディレクトリを指定します。 インストーラーでは、最初にそのディレクトリをチェックし、見つからないコンポーネントのみをダウンロードします。 すべてのファイルがオフラインで利用可能な場合、インターネット接続なしでインストールできます。

1. IoT Edge の最新のインストール ファイルとその以前のバージョンについては、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を参照してください。

2. インストールするバージョンを見つけ、リリース ノートの **[アセット]** セクションから次のファイルを IoT デバイスにダウンロードします。

   * IoTEdgeSecurityDaemon.ps1
   * 1\.1 リリース チャネルからの Microsoft-Azure-IoTEdge-amd64.cab。

   各リリースの機能をサポートするために機能が変更されるため、使用する .cab ファイルと同じリリースの PowerShell スクリプトを使用することが重要です。

3. ダウンロードした .cab ファイルにアーキテクチャのサフィックスが付いている場合は、そのファイルの名前を **Microsoft-Azure-IoTEdge.cab** のみに変更します。

4. 必要に応じて、Visual C++ 再頒布可能パッケージ インストーラーをダウンロードします。 たとえば、PowerShell スクリプトでは、バージョン [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe) を使用します。 IoT デバイスの IoT Edge ファイルと同じフォルダーにインストーラーを保存します。

5. オフライン コンポーネントを使用してインストールするには、PowerShell スクリプトのローカル コピーを[ドット ソース](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)で実行します。

6. `-OfflineInstallationPath` パラメーターを指定して [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) コマンドを実行します。 ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   デプロイ コマンドでは、指定されたローカル ファイル ディレクトリにあるコンポーネントが使用されます。 .cab ファイルまたは Visual C++ インストーラーが見つからない場合は、ダウンロードが試行されます。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

デバイスから IoT Edge のインストールを削除する必要がある場合は、次のコマンドを使用します。

IoT Edge のインストールを Windows デバイスから削除する場合は、管理用 PowerShell ウィンドウから [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) コマンドを使用します。 このコマンドは、IoT Edge ランタイムを既存の構成と Moby エンジン データと一緒に削除します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

アンインストール オプションの詳細については、`Get-Help Uninstall-IoTEdge -full` コマンドを使用してください。

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
