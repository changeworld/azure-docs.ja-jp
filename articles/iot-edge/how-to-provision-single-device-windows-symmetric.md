---
title: 対称キーを使用して Windows で IoT Edge デバイスを作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: 対称キーを使用して手動でプロビジョニングするために IoT Hub に 1 つの Windows IoT Edge を作成およびプロビジョニングする
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: 4c831e12c7660727c966d1fdc9d0079d8b236fa5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853360"
---
# <a name="create-and-provision-an-iot-edge-device-on-windows-using-symmetric-keys"></a>対称キーを使用して Windows で IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、Windows IoT Edge デバイスを登録およびプロビジョニングするエンドツーエンドの手順について説明します。

>[!NOTE]
>Windows コンテナーを使用する Azure IoT Edge は、Azure IoT Edge のバージョン 1.2 以降サポートされなくなります。
>
>IoT Edge を Windows デバイスで実行するための新しい方法である [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用を検討してください。
>
>Azure IoT Edge for Linux on Windows を使用する場合は、[同等のハウツーガイド](how-to-provision-single-device-linux-on-windows-symmetric.md)に記載されている手順に従います。

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
> * [TPM を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-windows-tpm.md)
> * [対称キーを使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-windows-symmetric.md)

## <a name="prerequisites"></a>前提条件

この記事では、IoT Edge デバイスを登録し、そこに IoT Edge をインストールする方法について説明します。 これらのタスクには、実行するために使用されるさまざまな前提条件とユーティリティがあります。 次に進む前に、すべての前提条件が満たされていることを確認してください。

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

### <a name="iot-edge-installation"></a>IoT Edge のインストール

Windows デバイス。

Windows コンテナーを使用する IoT Edge には、Windows バージョン 1809、ビルド 17763 が必要です。これは、最新の [Windows 長期サポート ビルド](/windows/release-information/)です。 サポートされている SKU の一覧については、[サポートされているシステムの一覧](support.md#operating-systems)を確認してください。

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

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

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge デバイスにランタイムが正常にインストールされ、構成されていることを確認します。

IoT Edge サービスの状態を確認します。

```powershell
Get-Service iotedge
```

サービス ログを調べます。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

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
   . path_to_powershell_module_here\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath path_to_file_directory_here
   ```

   デプロイ コマンドでは、指定されたローカル ファイル ディレクトリにあるコンポーネントが使用されます。 .cab ファイルまたは Visual C++ インストーラーが見つからない場合は、ダウンロードが試行されます。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

IoT Edge のインストールを Windows デバイスから削除する場合は、管理用 PowerShell ウィンドウから [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) コマンドを使用します。 このコマンドは、IoT Edge ランタイムを既存の構成と Moby エンジン データと一緒に削除します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

アンインストール オプションの詳細については、`Get-Help Uninstall-IoTEdge -full` コマンドを使用してください。

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
