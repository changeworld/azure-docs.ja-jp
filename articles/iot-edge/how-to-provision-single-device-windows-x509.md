---
title: X.509 証明書を使用して Windows で IoT Edge デバイスを作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: X.509 証明書を使用して手動でプロビジョニングするために IoT Hub に 1 つの Windows IoT Edge デバイスを作成およびプロビジョニングする
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: v-tcassi
monikerRange: iotedge-2018-06
ms.openlocfilehash: b9b8a1e3c17ce441dd7469547da83791815dc330
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269898"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-x509-certificates"></a>X.509 証明書を使用して Windows で IoT Edge デバイスを作成およびプロビジョニングする

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

この記事では、認証方法として X.509 証明書を使用する方法について説明します。 対称キーを使用する場合は、「[対称キーを使用して Windows で IoT Edge デバイスを作成およびプロビジョニングする](how-to-provision-single-device-windows-symmetric.md)」を参照してください。

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

## <a name="generate-device-identity-certificates"></a>デバイス ID 証明書を生成する

X.509 証明書を使用した手動プロビジョニングには、バージョン 1.0.10 以降の IoT Edge が必要です。

X.509 証明書を使用して IoT Edge デバイスをプロビジョニングする場合は、"*デバイス ID 証明書*" と呼ばれるものを使用します。 この証明書は、IoT Edge デバイスのプロビジョニングと、Azure IoT Hub でのデバイス認証を行うためにのみ使用されます。 これは、他の証明書には署名しないリーフ証明書です。 デバイス ID 証明書は、IoT Edge デバイスによって検証のためにモジュールやダウンストリーム デバイスに提示される証明機関 (CA) 証明書とは別のものです。

X.509 証明書認証の場合、各デバイスの認証情報は、デバイス ID 証明書から取得した "*拇印*" の形式で提供されます。 これらの拇印は、サービスが接続時にデバイスを認識できるように、デバイスの登録時に IoT Hub に付与されます。

IoT Edge デバイスでの CA 証明書の使用方法については、「[Azure IoT Edge での証明書の使用方法について理解する](iot-edge-certs.md)」を参照してください。

X.509 を使用して手動でプロビジョニングするには、次のファイルが必要です。

* 2 つのデバイス ID 証明書と、それに対応する .cer または pem 形式の秘密キー証明書。

  IoT Edge ランタイムには、証明書/キー ファイルのセットが 1 つ用意されています。 デバイス ID 証明書を作成するとき、IoT ハブ内のデバイスで使用するデバイス ID を使って証明書の共通名 (CN) を設定します。

* 両方のデバイス ID 証明書から取得された拇印。

  拇印の値は SHA-1 ハッシュの場合は 40 桁の 16 進数、SHA-256 ハッシュの場合は 64 桁の 16 進数です。 両方の拇印は、デバイスの登録時に IoT Hub に提供されます。

使用できる証明書がない場合は、[デモ証明書を作成して IoT Edge デバイスの機能をテスト](how-to-create-test-certificates.md)できます。 この記事に記載されている手順に従って、証明書作成スクリプトを設定し、ルート CA 証明書を作成してから、2 つの IoT Edge デバイス ID 証明書を作成します。

証明書から拇印を取得する方法の 1 つは、次の openssl コマンドを使用することです。

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-your-device"></a>デバイスを登録する

デバイスを登録するには、好みに応じて **Azure portal**、**Visual Studio Code**、または **Azure CLI** を使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の IoT Hubで、IoT Edge デバイスは、Edge 対応ではない IoT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/how-to-provision-single-device-windows-x509/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。 このデバイス ID は、後で使用するのでメモしておいてください。
   * 認証の種類として **[X.509 自己署名済み]** を選択します。
   * プライマリとセカンダリの ID 証明書の拇印を指定します。 拇印の値は SHA-1 ハッシュの場合は 40 桁の 16 進数、SHA-256 ハッシュの場合は 64 桁の 16 進数です。

1. **[保存]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

現在、Visual Studio Code 用の Azure IoT 拡張機能では、X.509 証明書を使用したデバイス登録はサポートされていません。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity create](/cli/azure/iot/hub/device-identity) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id [device_id] --hub-name [hub_name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA_thumbprint] --secondary-thumbprint [SHA_thumbprint]
   ```

このコマンドには、いくつかのパラメーターが含まれています。

* `--device-id` または `-d`: IoT ハブに対して一意のわかりやすい名前を指定します。 このデバイス ID は、次のセクションで使用するのでメモしておいてください。
* `hub-name` または `-n`: IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`: デバイスが IoT Edge デバイスであることを宣言します。
* `--auth-method` または `--am`: デバイスが使用する承認の種類を宣言します。 この例では、X.509 証明書の拇印を使用しています。
* `--primary-thumbprint` または `--ptp`: 主キーとして使用する X.509 証明書の拇印を指定します。
* `--secondary-thumbprint` または `--stp`: セカンダリ キーとして使用する X.509 証明書の拇印を指定します。

---

デバイスが IoT Hub に登録されたので、IoT Edge ランタイムのインストールとプロビジョニングを完了するために使用する情報を取得します。

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>登録済みデバイスを表示し、プロビジョニング情報を取得する

X.509 証明書認証を使用するデバイスでは、IoT Edge ランタイムのインストールとプロビジョニングを完了するために、IoT Hub 名、デバイス名、証明書ファイルが必要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![Azure portal を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-provision-single-device-windows-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code による X.509 証明書へのデバイスの登録はサポートされていませんが、必要に応じて IoT Edge デバイスを表示することはできます。

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できるほか、各 IoT Edge デバイスには **$edgeAgent** モジュールと **$edgeHub** モジュールがデプロイされています。

![VS Code を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-provision-single-device-windows-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name [hub_name]
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

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

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * オフラインまたは特定のバージョンのインストール用に IoTEdgeSecurityDaemon.ps1 スクリプトをデバイスにダウンロードした場合は、スクリプトのローカル コピーを必ず参照してください。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 入力を求められたら、次の情報を指定します。

   * **IotHubHostName**: デバイスの接続先となる IoT ハブのホスト名。 たとえば、`{IoT_hub_name}.azure-devices.net` のようにします。
   * **DeviceId**: デバイスを登録したときに指定した ID。
   * **X509IdentityCertificate**: デバイス上の ID 証明書の絶対パス。 たとえば、`C:\path\identity_certificate.pem` のようにします。
   * **X509IdentityPrivateKey**: 指定された ID 証明書の秘密キー ファイルの絶対パス。 たとえば、`C:\path\identity_key.pem` のようにします。

デバイスを手動でプロビジョニングする場合は、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* 特定の edgeAgent コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらの追加パラメーターの詳細については、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

---

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
