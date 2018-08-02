---
title: Linux コンテナーを使用する Windows に Azure IoT Edge をインストールする方法 | Microsoft Docs
description: Linux コンテナーを使用する Windows への Azure IoT Edge のインストール手順
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: a56b2b12143a29637196d2239f648b78f1f8e763
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39307867"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Windows に Azure IoT Edge をインストールして Linux コンテナーと共に使用する

Azure IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 これは 3 つのコンポーネントで構成されます。 **IoT Edge セキュリティ デーモン**は、Edge デバイス上のセキュリティ標準を提供し、維持します。 デーモンはブートのたびに開始し、IoT Edge エージェントを開始することでデバイスをブートストラップします。 **IoT Edge エージェント**は、IoT Edge ハブなど、Edge デバイス上のモジュールの展開と監視を容易にします。 **IoT Edge ハブ**は、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。

この記事では、Windows x64 (AMD/Intel) システムに Azure IoT Edge ランタイムをインストールする手順を示します。 Windows のサポートは現在プレビューの段階です。

>[!NOTE]
Windows システム上での Linux コンテナーの使用は、Azure IoT Edge に対して推奨またはサポートされている実稼働構成ではありません。 ただし、開発とテストの目的には使用できます。

## <a name="supported-windows-versions"></a>サポートされている Windows バージョン
Azure IoT Edge は、Linux コンテナーを使用するときに、Windows の次のバージョンで開発とテストに使用できます。
  * Windows 10 以降のデスクトップ オペレーティング システム。
  * Windows Server 2016 以降のサーバー オペレーティング システム。

## <a name="install-the-container-runtime"></a>コンテナー ランタイムのインストール 

Azure IoT Edge は、[OCI と互換性のある][lnk-oci]コンテナー ランタイム (Docker など) に依存します。 

[Docker for Windows][lnk-docker-for-windows] は、開発とテストに使用できます。 Docker for Windows が [Linux コンテナーを使用するように構成][lnk-docker-config]されていることをご確認ください

## <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンのインストール

>[!NOTE]
>Azure IoT Edge ソフトウェア パッケージには、(LICENSE ディレクトリ内の) パッケージ内にあるライセンス条項が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

### <a name="download-the-edge-daemon-package-and-install"></a>Edge デーモン パッケージのダウンロードおよびインストール

管理者用 PowerShell ウィンドウで、次のコマンドを実行します。

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
$path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
Set-ItemProperty -Path $sysenv -Name Path -Value $path
```

以下を使用して vcruntime をインストールします。

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

*iotedge* サービスを作成して開始します。

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

サービスによって使用されるポートのファイアウォールの例外を追加します。

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

次の内容の **iotedge.reg** ファイルを作成し、ダブルクリックするか `reg import iotedge.reg` コマンドを使用して Windows レジストリにインポートします。

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンの構成

デーモンは、`C:\ProgramData\iotedge\config.yaml` にある構成ファイルを使用して構成できます。

Edge デバイスは、[デバイスの接続文字列][lnk-dcs]を使用して手動で構成することも、[Device Provisioning Service を介して自動的に][lnk-dps]構成することもできます。

* 手動構成の場合は、**manual** プロビジョニング モードのコメントを解除します。 **device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* 自動構成の場合は、**dps** プロビジョニング モードのコメントを解除します。 **scope_id** と **registration_id** の値を、IoT Hub DPS インスタンスと TPM を搭載した IoT Edge デバイスの値で更新します。 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

PowerShell で `hostname` コマンドを使用してエッジ デバイスの名前を取得し、構成 yaml で **hostname:** の値として設定します。 例: 

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

次に、構成の **connect:** および **listen:** セクションで、**workload_uri** と **management_uri** の IP アドレスとポートを指定します。

IP アドレスを取得するには、PowerShell ウィンドウに `ipconfig` と入力します。 次の例に示すように、**vEthernet (DockerNAT)** インターフェイスの IP アドレスをコピーします (ご使用のシステム上の IP アドレスは異なる場合があります)。

![DockerNat][img-docker-nat]

構成ファイルの **connect:** セクションの **workload_uri** と **management_uri** を更新します。 **\<GATEWAY_ADDRESS\>** を、コピーした DockerNAT の IP アドレスに置き換えます。 

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

**listen** セクションに同じアドレスを入力します。

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

PowerShell ウィンドウで、環境変数 **IOTEDGE_HOST** を **management_uri** アドレスで作成します。

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

再起動と再起動の間で環境変数を維持します。

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

最後に、**moby_runtime:** の下の **network:** 設定のコメントが解除され、**azure iot-edge** に設定されていることを確認します

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

構成ファイルを保存し、サービスを再起動します。

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

前のセクションで**手動構成**手順を使用した場合、IoT Edge ランタイムがデバイス上で正常にプロビジョニングおよび実行されている必要があります。 **自動構成**手順を使用した場合は、ランタイムが IoT ハブにデバイスを登録できるように、追加の手順を完了する必要があります。 次の手順については、[Windows でのシミュレートされた TPM Edge デバイスの作成とプロビジョニング](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable)に関するページをご覧ください。


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

## <a name="next-steps"></a>次の手順

Edge ランタイムの正常なインストールに問題がある場合は、[トラブルシューティング][lnk-trouble]のページをご確認ください。


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

