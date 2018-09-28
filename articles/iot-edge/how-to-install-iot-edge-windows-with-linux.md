---
title: Linux コンテナーを使用する Windows に Azure IoT Edge をインストールする方法 | Microsoft Docs
description: Linux コンテナーを使用する Windows への Azure IoT Edge のインストール手順
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 86a24c2cdba890046f2cbb0ccf6b73decdac7235
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997524"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Windows に Azure IoT Edge をインストールして Linux コンテナーと共に使用する

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 

IoT Edge ランタイムの動作とランタイムに含まれるコンポーネントについては、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、Linux コンテナーと共に Windows x64 (AMD/Intel) システムに Azure IoT Edge ランタイムをインストールする手順を示します。 Windows のサポートは現在プレビューの段階です。

>[!NOTE]
Windows システム上での Linux コンテナーの使用は、Azure IoT Edge に対して推奨またはサポートされている実稼働構成ではありません。 ただし、開発とテストの目的には使用できます。

## <a name="supported-windows-versions"></a>サポートされている Windows バージョン
Azure IoT Edge は、Linux コンテナーを使用するときに、Windows の次のバージョンで開発とテストに使用できます。
  * Windows 10 以降のデスクトップ オペレーティング システム。
  * Windows Server 2016 以降のサーバー オペレーティング システム。

現在サポートされているオペレーティング システムの詳細については、「[Azure IoT Edge のサポート](support.md#operating-systems)」を参照してください。 

## <a name="install-the-container-runtime"></a>コンテナー ランタイムをインストールする 

Azure IoT Edge は、[OCI と互換性のある][lnk-oci]コンテナー ランタイム (Docker など) に依存します。 

[Docker for Windows][lnk-docker-for-windows] は、開発とテストに使用できます。 [Linux コンテナーを使用するように][lnk-docker-config] Docker for Windows を構成してください

## <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンのインストール

>[!NOTE]
>Azure IoT Edge ソフトウェア パッケージには、(LICENSE ディレクトリ内の) パッケージ内にあるライセンス条項が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

IoT Hub によって提供されるデバイス接続文字列を使用して、1 つの IoT Edge デバイスを手動でプロビジョニングすることもできますし、 Device Provisioning Service を使用して、複数のデバイスを自動的にプロビジョニングすることもできます。これは、プロビジョニングするデバイスが多数ある場合に便利です。 目的にプロビジョニング方法に応じて、適切なインストール スクリプトを選択してください。 

### <a name="option-1-install-and-manually-provision"></a>オプション 1: インストールして手動でプロビジョニングする

1. 「[新しい Azure IoT Edge デバイスを登録する][lnk-dcs]」の手順に従って、デバイスを登録し、デバイス接続文字列を取得します。 

2. IoT Edge デバイスで、PowerShell を管理者として実行します。 

3. IoT Edge ランタイムをダウンロードしてインストールします。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. **DeviceConnectionString** の入力を求められたら、IoT Hub から取得した接続文字列を入力します。 接続文字列の前後に引用符は含めないでください。 

### <a name="option-2-install-and-automatically-provision"></a>オプション 2: インストールして自動的にプロビジョニングする

1. 「[Windows 上のシミュレートされた TPM Edge デバイスの作成とプロビジョニング][lnk-dps]」の手順に従って、Device Provisioning Service を設定し、その**スコープ ID** を取得して、TPM デバイスをシミュレートし、その**登録 ID** を取得した後、個別の登録を作成します。 デバイスが IoT Hub に登録されたら、インストールを続行します。  

   >[!TIP]
   >インストールとテストを行っている間は、TPM シミュレーターを実行しているウィンドウを開いたままにしてください。 

2. IoT Edge デバイスで、PowerShell を管理者として実行します。 

3. IoT Edge ランタイムをダウンロードしてインストールします。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. メッセージが表示されたら、プロビジョニング サービスとデバイスの **ScopeID** と **RegistrationID** を入力します。

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

## <a name="tips-and-suggestions"></a>ヒントと検索候補

ネットワークにプロキシ サーバーが存在する場合は、[プロキシ サーバー経由で通信するように IoT Edge デバイスを構成する](how-to-configure-proxy-support.md)手順に従います。

## <a name="next-steps"></a>次の手順

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ][lnk-modules]できます。

Edge ランタイムを正常にインストールできない場合は、[トラブルシューティング][lnk-trouble]のページを調べてください。


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-modules]: how-to-deploy-modules-portal.md
