---
title: Azure IoT Edge on Windows のインストール | Microsoft Docs
description: Windows 10、Windows Server、Windows IoT Core への Azure IoT Edge のインストール手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: c280410816bfb48f21c68fe5d57b6ae18af0e855
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970663"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Windows に Azure IoT Edge ランタイムをインストールする

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 

IoT Edge ランタイムの動作については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、Windows x64 (AMD/Intel) システムに Azure IoT Edge ランタイムをインストールする手順を示します。 Windows のサポートは現在プレビューの段階です。

>[!NOTE]
Windows システム上での Linux コンテナーの使用は、Azure IoT Edge に対して推奨またはサポートされている実稼働構成ではありません。 ただし、開発とテストの目的には使用できます。

## <a name="prerequisites"></a>前提条件

このセクションを使用して、使用する Windows デバイスが IoT Edge をサポートできるかどうかを確認し、インストールする前に Windows デバイスをコンテナー エンジン用に準備します。 

### <a name="supported-windows-versions"></a>サポートされている Windows バージョン

Azure IoT Edge は、Windows コンテナーまたは Linux コンテナーのどちらを実行しているかによって、サポートする Windows のバージョンが異なります。 

Windows コンテナーを使用する Azure IoT Edge の最新バージョンは、次のバージョンの Windows で実行できます。
* Windows 10 または IoT Core October 2018 Update (ビルド 17763)
* Windows Server 2019 (ビルド 17763)

Linux コンテナーを使用する Azure IoT Edge の最新バージョンは、次のバージョンの Windows で実行できます。 
* Windows 10 Anniversary Update (ビルド 14393) 以降
* Windows Server 2016 以降

現在サポートされているオペレーティング システムの詳細については、[Azure IoT Edge のサポート](support.md#operating-systems)に関する記事を参照してください。 

IoT Edge の最新バージョンの内容の詳細については、 [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に関する記事を参照してください。

### <a name="prepare-for-a-container-engine"></a>コンテナー エンジンを準備する 

Azure IoT Edge は、[OCI と互換性のある](https://www.opencontainers.org/)コンテナー エンジンに依存します。 運用シナリオの場合は、インストール スクリプトに含める Moby エンジンを使用して、Windows デバイスで Windows コンテナーを実行します。 開発およびテストの場合、Windows デバイス上で Linux コンテナーを実行できますが、IoT Edge をインストールする前にコンテナー エンジンをインストールして構成する必要があります。 いずれのシナリオについても、デバイスを準備する前提条件として以下のセクションを参照してください。 

#### <a name="moby-engine-for-windows-containers"></a>Windows コンテナー用の Moby エンジン

運用環境シナリオで IoT Edge を実行する Windows デバイスの場合は、Moby のみが正式にサポートされているコンテナー エンジンです。 インストール スクリプトでは、IoT Edge をインストールする前に Moby エンジンがデバイスに自動的にインストールされます。 コンテナー機能を有効にして、デバイスを準備します。 

1. スタート バーで、**Windows の機能の有効化または無効化**を検索し、コントロール パネル プログラムを開きます。
2. **[コンテナー]** を見つけて選択します。
3. **[OK]** を選択します。 

#### <a name="docker-for-linux-containers"></a>Docker for Linux コンテナー

Linux デバイス用のコンテナーを開発およびテストするために Windows を使用している場合、コンテナー エンジンとして [Docker for Windows](https://www.docker.com/docker-windows) を使用できます。 [Linux コンテナーを使用する](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)ように Docker を構成できます。 IoT Edge をインストールする前に、Docker をインストールして構成する必要があります。 Linux コンテナーは、運用環境の Windows デバイスではサポートされません。 

使用する IoT Edge デバイスが Windows コンピューターの場合は、Hyper-V の[システム要件](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)を満たしていることを確認します。 仮想マシンの場合は、[入れ子になった仮想化](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)を有効にして、少なくとも 2 GB のメモリを割り当てます。

## <a name="install-iot-edge-on-a-new-device"></a>新しいデバイスに IoT Edge をインストールする

>[!NOTE]
>Azure IoT Edge ソフトウェア パッケージには、(LICENSE ディレクトリ内の) パッケージ内にあるライセンス条項が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

PowerShell スクリプトは、Azure IoT Edge セキュリティ デーモンをダウンロードしてインストールします。 セキュリティ デーモンは、2 つのランタイム モジュールのうちの 1 つである IoT Edge エージェントを開始し、これによってもう 1 つモジュールのリモート デプロイが可能になります。 

IoT Edge ランタイムをデバイスに初めてインストールするときは、IoT ハブからの ID を使用してデバイスをプロビジョニングする必要があります。 IoT Hub によって提供されるデバイス接続文字列を使用して、1 つの IoT Edge デバイスを手動でプロビジョニングすることもできますし、 Device Provisioning Service を使用して、複数のデバイスを自動的にプロビジョニングすることもできます。これは、セットアップするデバイスが多数ある場合に便利です。 目的にプロビジョニング方法に応じて、適切なインストール スクリプトを選択してください。 

次のセクションでは、新しいデバイス上で IoT Edge インストール スクリプトを使用する場合の一般的なユース ケースとパラメーターについて説明します。 

### <a name="option-1-install-and-manually-provision"></a>オプション 1:インストールして手動でプロビジョニングする

この最初のオプションでは、IoT Hub で生成されるデバイス接続文字列を指定して、デバイスをプロビジョニングします。 

「[新しい Azure IoT Edge デバイスを登録する](how-to-register-device-portal.md)」の手順に従って、デバイスを登録し、デバイス接続文字列を取得します。 

この例は、Windows コンテナーでの手動インストールを示します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

デバイスを手動でインストールおよびプロビジョニングするとき、追加のパラメーターを使用して、インストールを次のように変更できます。
* プロキシ サーバーを経由するようトラフィックを誘導する
* インストーラーでオフライン ディレクトリを指定する
* 特定のエージェント コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する
* Moby CLI インストールをスキップする

これらのインストール オプションの詳細については、この記事を読み進めるか、[すべてのインストール パラメーター](#all-installation-parameters)にスキップしてください。

### <a name="option-2-install-and-automatically-provision"></a>オプション 2:インストールして自動的にプロビジョニングする

この 2 番目のオプションでは、IoT Hub Device Provisioning Service を使用してデバイスをプロビジョニングします。 Device Provisioning Service インスタンスから**スコープ ID** を指定し デバイスから**登録 ID** を指定します。

「[Windows 上のシミュレートされた TPM Edge デバイスの作成とプロビジョニング](how-to-auto-provision-simulated-device-windows.md)」の手順に従って、Device Provisioning Service を設定し、その**スコープ ID** を取得して、TPM デバイスをシミュレートし、その**登録 ID** を取得した後、個別の登録を作成します。 デバイスが IoT Hub に登録されたら、インストールを続行します。  

   >[!TIP]
   >インストールとテストを行っている間は、TPM シミュレーターを実行しているウィンドウを開いたままにしてください。 

次の例は、Windows コンテナーでの自動インストールを示しています。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

デバイスを手動でインストールおよびプロビジョニングするとき、追加のパラメーターを使用して、インストールを次のように変更できます。
* プロキシ サーバーを経由するようトラフィックを誘導する
* インストーラーでオフライン ディレクトリを指定する
* 特定のエージェント コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する
* Moby CLI インストールをスキップする

これらのインストール オプションの詳細については、この記事を読み進めるか、[すべてのインストール パラメーター](#all-installation-parameters)にスキップしてください。

## <a name="update-an-existing-installation"></a>既存のインストールを更新する

IoT Edge ランタイムを以前デバイスにインストールしたことがあり、IoT Hub からの ID によってデバイスがプロビジョニングされている場合、簡略化されたインストール スクリプトを使用できます。 フラグ `-ExistingConfig` は、IoT Edge の構成ファイルが既にデバイスに存在していることを宣言します。 構成ファイルには、デバイス ID のほかに証明書とネットワーク設定に関する情報が含まれています。 このインストール オプションは、デバイスが手動または自動で最初からプロビジョニングされている場合に使用できます。 

詳細については、[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)を参照してください。

この例では、既存の構成ファイルを指定し、Windows コンテナーを使用するインストールを示します。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

既存の構成ファイルから IoT Edge をインストールするとき、追加のパラメーターを使用して、インストールを次のように変更できます。
* プロキシ サーバーを経由するようトラフィックを誘導する
* インストーラーでオフライン ディレクトリを指定する 
* Moby CLI インストールをスキップする 

IoT Edge エージェント コンテナー イメージは、以前のインストールの構成ファイルですでに設定されているため、スクリプト パラメーターで宣言することはできません。 エージェント コンテナー イメージを変更する場合は、config.yaml ファイルで行います。 

これらのインストール オプションの詳細については、この記事を読み進めるか、[すべてのインストール パラメーター](#all-installation-parameters)にスキップしてください。

## <a name="offline-installation"></a>オフライン インストール

インストール中に、次の 4 つのファイルがダウンロードされます。 
* IoT Edge セキュリティ デーモン (iotedgd) の zip 
* Moby エンジンの zip
* Moby CLI の zip
* Visual C++ 再頒布可能パッケージ (VC ランタイム) の msi

これらのファイルのうち 1 つまたはすべてをデバイスに事前にダウンロードし、ファイルを含むディレクトリをインストール スクリプトで指定することができます。 インストーラーはまずディレクトリをチェックし、見つからないコンポーネントのみダウンロードします。 4 つすべてのファイルがオフラインで利用可能な場合、インターネット接続なしでインストールできます。 また、この機能を使用して、1 つまたは複数のコンポーネントのオンライン バージョンをオーバーライドすることもできます。  

最新のインストール ファイルとその以前のバージョンについては、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を参照してください

オフライン コンポーネントを使用してインストールするには、`-OfflineInstallationPath` パラメーターを使用し、ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>すべてのインストール パラメーター

前のセクションでは、パラメーターを使用してインストール スクリプトを変更する方法の例を用いた一般的なインストール シナリオについて紹介しました。 このセクションでは、IoT Edge をインストールするために使用できる有効なパラメーターの参照テーブルを示します。 詳細については、PowerShell ウィンドウで `get-help Install-SecurityDaemon -full` を実行してください。 

既存の構成を使用して IoT Edge をインストールするには、インストール コマンドで次の一般的なパラメーターを使用できます。 

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **手動** | なし | **スイッチ パラメーター**。 すべてのインストールで、manual、DPS、または existingconfig を宣言する必要があります。<br><br>デバイスをプロビジョニングするためのデバイス接続文字列を手動で指定することを宣言します |
| **Dps** | なし | **スイッチ パラメーター**。 すべてのインストールで、manual、DPS、または existingconfig を宣言する必要があります。<br><br>Device Provisioning Service (DPS) 経由でプロビジョニングするために、DPS のスコープ ID とデバイスの登録 ID を指定することを宣言します。  |
| **ExistingConfig** | なし | **スイッチ パラメーター**。 すべてのインストールで、manual、DPS、または existingconfig を宣言する必要があります。<br><br>プロビジョニング情報を持つ config.yaml ファイルがデバイスに既に存在することを宣言します。 |
| **DeviceConnectionString** | 単一引用符で囲まれた、IoT Hub に登録されている IoT Edge デバイスからの接続文字列 | 手動インストールで**必須**です。 スクリプト パラメーターに接続文字列を指定しなかった場合は、インストール中にこれを指定するよう促されます。 |
| **ScopeId** | IoT Hub に関連付けられた Device Provisioning Service のインスタンスからのスコープ ID。 | DPS インストールで**必須**です。 スクリプト パラメーターにスコープ ID を指定しなかった場合、インストール中にこれを指定するよう促されます。 |
| **RegistrationId** | デバイスによって生成された登録 ID | DPS インストールで**必須**です。 スクリプト パラメーターに登録 ID を指定しなかった場合、インストール中にこれを指定するよう促されます。 |
| **ContainerOs** | **Windows** または **Linux** | コンテナー OS が指定されない場合、Linux が既定値となります。 Windows コンテナーの場合、コンテナー エンジンがインストールに含まれます。 Linux コンテナーの場合、インストールを開始する前にコンテナー エンジンをインストールする必要があります。 Linux コンテナーを Windows 上で実行することは開発シナ リオでは便利ですが、運用環境ではサポートされません。 |
| **Proxy** | プロキシ URL | デバイスがインターネットに接続するためにプロキシ サーバーを経由する必要がある場合は、このパラメーターを含めます。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **InvokeWebRequestParameters** | パラメーターと値のハッシュ テーブル | インストール中には、いくつかの Web 要求が行われます。 それらの Web 要求のパラメーターを設定するにはこのフィールドを使用します。 このパラメーターは、プロキシ サーバーの資格情報を構成するために使用すると便利です。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **OfflineInstallationPath** | ディレクトリ パス | このパラメーターが含まれる場合、インストーラーはインストールに必要な iotedged の zip、Moby エンジンの zip、Moby CLI の zip、および VC ランタイム MSI ファイルのディレクトリをチェックします。 4 つすべてのファイルがディレクトリにある場合、IoT Edge をオフライン中にインストールすることができます。 特定のコンポーネントのオンライン バージョンをオーバーライドするために、このパラメーターを使用することもできます。 |
| **AgentImage** | IoT Edge エージェント イメージの URI | 既定では、新しい IoT Edge のインストールでは、IoT Edge エージェント イメージの最新のローリング タグを使用します。 イメージ バージョンについて特定のタグを設定したり、独自のエージェント イメージを提供したりするには、このパラメーターを使用します。 詳細については、[IoT Edge タグ](how-to-update-iot-edge.md#understand-iot-edge-tags)に関する記事を参照してください。 |
| **ユーザー名** | コンテナー レジストリのユーザー名 | プライベート レジストリ内のコンテナーに - AgentImage パラメーターを設定する場合にのみ、このパラメーターを使用します。 レジストリへのアクセス権を持つユーザー名を指定します。 |
| **パスワード** | セキュリティで保護されたパスワード文字列 | プライベート レジストリ内のコンテナーに - AgentImage パラメーターを設定する場合にのみ、このパラメーターを使用します。 レジストリにアクセスするためのパスワードを指定します。 | 
| **SkipMobyCli** | なし | -ContainerOS が Windows に設定された場合にのみ適用されます。 Moby CLI (docker.exe) を $MobyInstallDirectory にインストールしないでください。 |

## <a name="verify-successful-installation"></a>インストールの成功を確認する

以下によって IoT Edge サービスの状態を確認できます。 

```powershell
Get-Service iotedge
```

以下を使用して、過去 5 分のサービス ログを調べます。

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

また、以下を使用して、実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

IoT Edge のインストールを Windows デバイスから削除する場合は、管理用 PowerShell ウィンドウから次のコマンドを使用します。 このコマンドは、IoT Edge ランタイムを既存の構成と Moby エンジン データと一緒に削除します。 

```PowerShell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

IoT Edge をデバイスに再インストールするつもりの場合は、セキュリティ デーモンを既存の構成情報と一緒に後で再インストールできるように、`-DeleteConfig` と `-DeleteMobyDataRoot` のパラメーターを除外します。 

## <a name="next-steps"></a>次の手順

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge を正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。
