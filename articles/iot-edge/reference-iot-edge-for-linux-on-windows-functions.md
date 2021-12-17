---
title: Azure IoT Edge for Linux on Windows 用の PowerShell 関数 | Microsoft Docs
description: IoT Edge for Linux on Windows 仮想マシンのデプロイ、プロビジョニング、状態取得のための Azure IoT Edge for Linux on Windows PowerShell 関数のリファレンス情報。
author: v-tcassi
ms.author: fcabrera
ms.date: 10/15/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 9038f8b99728b2808cb1d4cf6b23a7673fa5d92e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085907"
---
# <a name="powershell-functions-for-iot-edge-for-linux-on-windows"></a>IoT Edge for Linux on Windows 用の PowerShell 関数

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge for Linux on Windows (EFLOW) 仮想マシンのデプロイ、プロビジョニング、状態取得のための PowerShell 関数について説明します。

## <a name="prerequisites"></a>前提条件

この記事で説明されているコマンドは、`AzureEFLOW.psm1` ファイルからのものです。これは、ご使用のシステムの `C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW` の `WindowsPowerShell` ディレクトリー内にあります。

PowerShell ディレクトリに **AzureEflow** フォルダーがない場合は、次の手順を使用して、Azure IoT Edge for Linux on Windows をダウンロードしてインストールします。 

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

1. ターゲット デバイスの実行ポリシーがまだ `AllSigned` に設定されていない場合、これを設定します。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

## <a name="connect-eflowvm"></a>Connect-EflowVM

**Connect-EflowVm** コマンドは、SSH を使用して仮想マシンに接続します。 仮想マシンへの SSH 接続が許可されるアカウントは、それを作成したユーザーだけです。

このコマンドは、ホスト デバイスで実行されている PowerShell セッションでのみ機能します。 Windows Admin Center または PowerShell ISE を使用する場合は機能しません。

詳細については、コマンド `Get-Help Connect-EflowVm -full` を使用してください。

## <a name="copy-eflowvmfile"></a>Copy-EflowVmFile

**Copy-EflowVmFile** コマンドは、SCP を使用して、仮想マシンとの間でファイルをコピーします。 省略可能なパラメーターを使用して、コピー元およびコピー先のファイル パスとコピーの方向を指定します。

ユーザー **iotedge-user** は、仮想マシン上の元のディレクトリすべてに対する読み取りアクセス許可を持っているか、コピー先ディレクトリすべてに対する書き込みアクセス許可を持っている必要があります。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| fromFile | ファイルへのパスを表す文字列 | 読み取り元のファイルを定義します。 |
| toFile | ファイルへのパスを表す文字列 |  書き込み先のファイルを定義します。 |
| pushFile | なし | このフラグは、コピーの方向を示します。 指定した場合、このコマンドにより、仮想マシンにファイルがプッシュされます。 指定しない場合、このコマンドにより、仮想マシンからファイルがプルされます。 |

詳細については、コマンド `Get-Help Copy-EflowVMFile -full` を使用してください。

## <a name="deploy-eflow"></a>Deploy-Eflow

**Deploy-Eflow** コマンドは、メインのデプロイ方法です。 このデプロイ コマンドによって仮想マシンが作成され、ファイルがプロビジョニングされ、IoT Edge エージェント モジュールがデプロイされます。 どのパラメーターも必須ではありませんが、作成時に仮想マシンの設定を変更するために使用できます。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| acceptEula | **Yes** または **No** | EULA を受け入れまたは拒否し、EULA プロンプトをバイパスするためのショートカット。 |
| acceptOptionalTelemetry | **Yes** または **No** |  省略可能なテレメトリを受け入れまたは拒否し、テレメトリのプロンプトをバイパスするためのショートカット。 |
| cpuCount | 1 からデバイスの CPU コア数の範囲の整数値 |  VM の CPU コア数。<br><br>**既定値**: 1 仮想コア。 |
| memoryInMB | 1024 からデバイスの空きメモリの最大容量の範囲の整数値 |VM に割り当てられたメモリ。<br><br>**既定値**: 1024 MB。 |
| vmDiskSize | 8 GB から 256 GB | 容量可変の仮想ハード ディスクの最大ディスク サイズ。<br><br>**既定値**: 10 GB。 |
| vswitchName | 仮想スイッチの名前 |  EFLOW VM に割り当てられた仮想スイッチの名前。 |
| vswitchType | **内部** または **外部** | EFLOW VM に割り当てられた仮想スイッチの種類。 |
| ip4Address | DHCP サーバー スコープの範囲内の IPv4 アドレス | EFLOW VM の静的 IPv4 アドレス。 |
| ip4PrefixLength | サブネットの IPv4 プレフィックス長 | IPv4 サブネット プレフィックス長。静的 IPv4 アドレスが指定されている場合にのみ有効です。 |
| ip4GatewayAddress | サブネット ゲートウェイの IPv4 アドレス | ゲートウェイ IPv4 アドレス。静的 IPv4 アドレスが指定されている場合にのみ有効です。 |
| gpuName | GPU デバイス名 |  パススルーに使用される GPU デバイスの名前。 |
| gpuPassthroughType | **DirectDeviceAssignment**、**ParaVirtualization**、または none (CPU のみ) |  GPU パススルーの種類 |
| gpuCount | 1 からデバイスの GPU コア数の範囲の整数値 | VM の GPU デバイスの数。 <br><br>**注**: ParaVirtualization を使用する場合は、必ず gpuCount = 1 を設定してください |

詳細については、コマンド `Get-Help Deploy-Eflow -full` を使用してください。  

## <a name="get-eflowhostconfiguration"></a>Get-EflowHostConfiguration

**Get-EflowHostConfiguration** コマンドを実行すると、ホスト構成が返されます。 このコマンドはパラメーターを受け取りません。 これは、次の 4 つのプロパティを含むオブジェクトを返します。

* FreePhysicalMemoryInMB
* NumberOfLogicalProcessors
* DiskInfo
* GpuInfo

詳細については、コマンド `Get-Help Get-EflowHostConfiguration -full` を使用してください。

## <a name="get-eflowlogs"></a>Get-EflowLogs

**Get-EflowLogs** コマンドを実行すると、IoT Edge for Linux on Windows のデプロイとインストールからログが収集され、バンドルされます。 バンドルされたログは、`.zip` フォルダーの形式で出力されます。

詳細については、コマンド `Get-Help Get-EflowLogs -full` を使用してください。

## <a name="get-eflowvm"></a>Get-EflowVm

**Get-EflowVm** コマンドを実行すると、仮想マシンの現在の構成が返されます。 このコマンドはパラメーターを受け取りません。 これは、次の 4 つのプロパティを含むオブジェクトを返します。

* VmConfiguration
* EdgeRuntimeVersion
* EdgeRuntimeStatus
* SystemStatistics

読み取り可能なリスト内の特定のプロパティを表示するには、そのプロパティを展開して `Get-EflowVM` コマンドを実行します。 次に例を示します。

```powershell
Get-EflowVM | Select -ExpandProperty VmConfiguration | Format-List
```

詳細については、コマンド `Get-Help Get-EflowVm -full` を使用してください。

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

**Get-EflowVmAddr** コマンドは、仮想マシンの現在の IP と MAC アドレスに対してクエリを実行するために使用されます。 このコマンドは、IP と MAC アドレスが時間の経過と共に変わる場合があるという事実を考慮して存在しています。

追加情報については、`Get-Help Get-EflowVmAddr -full` コマンドを使用してください。

## <a name="get-eflowvmfeature"></a>Get-EflowVmFeature

**Get-EflowVmFeature** コマンドを実行すると、IoT Edge for Linux on Windows の機能の有効化の状態が返されます。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| の機能 | **DpsTpm** | クエリを実行する機能名。 |

詳細については、コマンド `Get-Help Get-EflowVmFeature -full` を使用してください。

## <a name="get-eflowvmname"></a>Get-EflowVmName

**Get-EflowVmName** コマンドを実行すると、仮想マシンの現在のホスト名が返されます。 このコマンドは、Windows ホスト名が時間の経過と共に変わる場合があるという事実を考慮して存在しています。

詳細については、コマンド `Get-Help Get-EflowVmName -full` を使用してください。

## <a name="get-eflowvmtelemetryoption"></a>Get-EflowVmTelemetryOption

**Get-EflowVmTelemetryOption** コマンドを実行すると、仮想マシン内のテレメトリ (**Optional** または **Required**) の状態が表示されます。

詳細については、コマンド `Get-Help Get-EflowVmTelemetryOption -full` を使用してください。

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

**Get-EflowVmTpmProvisioningInfo** コマンドを実行すると、TPM プロビジョニング情報が返されます。 このコマンドはパラメーターを受け取りません。 これは、次の 2 つのプロパティを含むオブジェクトを返します。

* 保証キー
* 登録 ID

詳細については、コマンド `Get-Help Get-EflowVmTpmProvisioningInfo -full` を使用してください。

## <a name="invoke-eflowvmcommand"></a>Invoke-EflowVmCommand

**Invoke-EflowVMCommand** コマンドを実行すると、仮想マシン内で Linux コマンドが実行され、出力が返されます。 このコマンドは、有限の出力を返す Linux コマンドに対してのみ機能します。 ユーザー操作が必要、または無期限に実行される Linux コマンドには使用できません。

次の省略可能なパラメーターを使用して、コマンドを事前に指定できます。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| command | String | VM で実行されるコマンド。 |
| ignoreError | なし |  このフラグを指定した場合、コマンドからのエラーが無視されます。 |

詳細については、コマンド `Get-Help Invoke-EflowVmCommand -full` を使用してください。

## <a name="provision-eflowvm"></a>Provision-EflowVm

**Provision-EflowVm** コマンドを実行すると、IoT Edge デバイスのプロビジョニング情報が仮想マシンの IoT Edge の `config.yaml` ファイルに追加されます。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| provisioningType | **ManualConnectionString**、**ManualX509**、**DpsTPM**、**DpsX509**、または **DpsSymmetricKey** |  IoT Edge デバイスに使用するプロビジョニングの種類を定義します。 |
| devConnString | 既存の IoT Edge デバイスのデバイス接続文字列 | IoT Edge デバイスを手動でプロビジョニングするためのデバイス接続文字列 (**ManualConnectionString**)。 |
| iotHubHostname | 既存の IoT ハブのホスト名 | IoT Edge デバイスをプロビジョニングするための Azure IoT Hub ホスト名 (**ManualX509**)。 |
| deviceId | 既存の IoT Edge デバイスのデバイス ID | IoT Edge デバイスをプロビジョニングするためのデバイス ID (**ManualX509**)。 |
| scopeId | 既存の DPS インスタンスのスコープ ID。 | IoT Edge デバイスをプロビジョニングするためのスコープ ID (**DpsTPM**、**DpsX509**、または **DpsSymmetricKey**)。 |
| symmKey | 既存の DPS 登録の主キー、または対称キーを使用して登録された既存の IoT Edge デバイスの主キー | IoT Edge デバイスをプロビジョニングするための対称キー (**DpsSymmetricKey**)。 |
| registrationId | 既存の IoT Edge デバイスの登録 ID | IoT Edge デバイスをプロビジョニングするための登録 ID (**DpsSymmetricKey**)。 |
| identityCertPath | ディレクトリ パス | Windows ホスト マシン上の ID 証明書の絶対宛先パス (**ManualX509**、**DpsX509**)。 |
| identityPrivKeyPath | ディレクトリ パス | Windows ホスト マシン上の ID 秘密キーの絶対ソース パス (**ManualX509**、**DpsX509**)。 |

詳細については、コマンド `Get-Help Provision-EflowVm -full` を使用してください。

## <a name="set-eflowvm"></a>Set-EflowVM

**Set-EflowVM** コマンドを実行すると、要求されたプロパティで仮想マシンの構成が更新されます。 仮想マシンの特定の構成を定義するには、省略可能なパラメーターを使用します。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| cpuCount | 1 からデバイスの CPU コア数の範囲の整数値 | VM の CPU コア数。 |
| memoryInMB | 1024 からデバイスの空きメモリの最大容量の範囲の整数値 | VM に割り当てられたメモリ。 |
| gpuName | GPU デバイス名 |  パススルーに使用される GPU デバイスの名前。 |
| gpuPassthroughType | **DirectDeviceAssignment**、**ParaVirtualization**、または none (パススルーなし) |  GPU パススルーの種類 |
| gpuCount | 1 からデバイスの GPU コア数の範囲の整数値 | VM の GPU デバイスの数。**注**: DirectDeviceAssignment を使用する場合にのみ有効です |
| headless | なし | このフラグを指定した場合、これにより、セキュリティ警告が発行された場合にユーザーが確認する必要があるかどうかが決まります。 |

詳細については、コマンド `Get-Help Set-EflowVM -full` を使用してください。

## <a name="set-eflowvmfeature"></a>Set-EflowVmFeature

**Set-EflowVmFeature** コマンドを実行すると、IoT Edge for Linux on Windows の機能の状態が有効または無効になります。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| の機能 | **DpsTpm** | 切り替える機能の名前。 |
| 有効化 (enable) | なし | このフラグを指定した場合、コマンドによって機能が有効になります。 |

詳細については、コマンド `Get-Help Set-EflowVmFeature -full` を使用してください。

## <a name="set-eflowvmtelemetryoption"></a>Set-EflowVmTelemetryOption

**Set-EflowVmTelemetryOption** コマンドを実行すると、仮想マシン内の省略可能なテレメトリが有効または無効になります。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| optionalTelemetry | "**True**" または "**False**" | 省略可能なテレメトリが選択されるかどうか。 |

詳細については、コマンド `Get-Help Set-EflowVmTelemetryOption -full` を使用してください。

## <a name="start-eflowvm"></a>Start-EflowVm

**Start-EflowVm** コマンドを実行すると、仮想マシンが起動されます。 仮想マシンが既に起動されている場合、何のアクションも実行されません。

詳細については、コマンド `Get-Help Start-EflowVm -full` を使用してください。

## <a name="stop-eflowvm"></a>Stop-EflowVm

**Stop-EflowVm** コマンドを実行すると、仮想マシンが停止されます。 仮想マシンが既に停止されている場合、何のアクションも実行されません。

詳細については、コマンド `Get-Help Stop-EflowVm -full` を使用してください。

## <a name="verify-eflowvm"></a>Verify-EflowVm

**Verify-EflowVm** コマンドは、IoT Edge for Linux on Windows 仮想マシンが作成されたかどうかを確認するための公開された関数です。 これは一般的なパラメーターのみを受け取り、仮想マシンが作成されていた場合は **True**、そうでない場合は **False** を返します。

詳細については、コマンド `Get-Help Verify-EflowVm -full` を使用してください。

## <a name="next-steps"></a>次のステップ

次の記事で、これらのコマンドを使用して IoT Edge for Linux on Windows をインストールおよびプロビジョニングする方法を確認します。

* [Azure IoT Edge for Linux on Windows をインストールする](./how-to-provision-single-device-linux-on-windows-symmetric.md)
