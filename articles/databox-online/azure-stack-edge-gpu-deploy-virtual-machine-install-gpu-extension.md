---
title: Azure Stack Edge Pro GPU デバイスの GPU VM に GPU 拡張機能をインストールする
description: Azure Stack Edge Pro GPU デバイス上の GPU 仮想マシン (VM) に GPU 拡張機能をインストールする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 80337c4953182e903655eddb360fc8b49a88ca59
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732930"
---
# <a name="install-gpu-extension-on-vms-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスの VM に GPU 拡張機能をインストールする

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、GPU ドライバー拡張機能をインストールして、Azure Stack Edge デバイスで実行されている GPU VM に適切な Nvidia ドライバーをインストールする方法について説明します。 この記事では、Windows と Linux VM の両方で Azure Resource Manager テンプレートを使用して GPU 拡張機能をインストールするための手順について説明します。

> [!NOTE]
> Azure portal では、VM の作成時または VM のデプロイ後に GPU 拡張機能をインストールできます。 手順と要件については、[GPU 仮想マシンをデプロイ する](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)に関する記事を参照してください。


## <a name="prerequisites"></a>前提条件

デバイスで実行されている GPU VM に GPU 拡張機能をインストールする前に、次のことを確認してください。

1. 1 つ以上の GPU VM をデプロイした Azure Stack Edge デバイスにアクセスできること。 [デバイスに GPU VM をデプロイする方法](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)に関するページを参照してください。

    - デバイス上のコンピューティング ネットワーク用に有効になっているポートがインターネットに接続されていて、アクセス可能であることを確認してください。 GPU ドライバーは、インターネット アクセスを通じてダウンロードされます。

        次の例では、ポート 2 がインターネットに接続され、コンピューティング ネットワークを有効にするために使用されています。 ご使用の環境に Kubernetes がデプロイされていない場合は、Kubernetes ノード IP と外部サービス IP の割り当てをスキップできます。

        ![Azure Stack Edge デバイスの [コンピューティング] ウィンドウのスクリーンショット. ポート 2 の [コンピューティング] の設定が強調表示されています。](media/azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension/enable-compute-network-1.png)
1. クライアント コンピューターに [GPU 拡張テンプレートとパラメーター ファイルをダウンロード](https://aka.ms/ase-vm-templates)します。 それを、作業ディレクトリとして使用するディレクトリに解凍します。
1. デバイスへのアクセスに使用するクライアントが、Azure PowerShell 経由で Azure Resource Manager にまだ接続されていることを確認します。 Azure Stack Edge デバイスが再起動されると、Azure Resource Manager への接続は 1.5 時間ごとに有効期限が切れます。 この場合、実行するすべてのコマンドレットで、Azure に接続されていないことを示すエラー メッセージが返されます。 再度サインインする必要があります。 詳細な手順については、「[Azure Stack Edge デバイスで Azure Resource Manager に接続する](azure-stack-edge-gpu-connect-resource-manager.md)」を参照してください。

## <a name="edit-parameters-file"></a>パラメーター ファイルを編集する

VM のオペレーティング システムに応じて、Windows 用または Linux 用の GPU 拡張機能をインストールできます。


### <a name="windows"></a>[Windows](#tab/windows)

既存の VM 用の Nvidia GPU ドライバーをデプロイするには、`addGPUExtWindowsVM.parameters.json` パラメーター ファイルを編集してから、`addGPUextensiontoVM.json` テンプレートをデプロイします。

`addGPUExtWindowsVM.parameters.json` ファイルでは、次のパラメーターを受け取ります。

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

### <a name="linux"></a>[Linux](#tab/linux)

既存の Linux VM 用の Nvidia GPU ドライバーをデプロイするには、パラメーター ファイルを編集してから、`addGPUextensiontoVM.json` テンプレートをデプロイします。 

Ubuntu または Red Hat Enterprise Linux (RHEL) を使用している場合、`addGPUExtLinuxVM.parameters.json` ファイルは次のパラメーターを受け取ります。

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    }
    }
    }
```

この記事で使用されている Ubuntu パラメーター ファイルの例を次に示します。

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
        }
    }
}
```

### <a name="gpu-vms-from-rhel-byos-images"></a>RHEL BYOS イメージからの GPU VM

Red Hat Enterprise Linux Bring Your Own Subscription イメージ (RHEL BYOS) を使用して VM を作成した場合は、次のことを確認してください。

- [RHEL BYOS イメージの使用](azure-stack-edge-gpu-create-virtual-machine-image.md)に関するページに示されている手順に従います。 
- GPU VM を作成した後、Red Hat カスタマー ポータルで VM を登録し、サブスクライブします。 VM が適切に登録されていないと、VM に権限がないため、インストールは続行されません。 [Red Hat Subscription Manager を使用したワンステップで行う登録と自動サブスクライブ](https://access.redhat.com/solutions/253273)に関する記事をご覧ください。 この手順では、インストール スクリプトで GPU ドライバーの関連パッケージをダウンロードできます。
- `vulkan-filesystem` パッケージを手動でインストールするか、CentOS7 リポジトリを yum リポジトリ リストに追加します。 GPU 拡張機能をインストールすると、インストール スクリプトによって CentOS7 リポジトリ上の `vulkan-filesystem` パッケージ (RHEL7 用) が検索されます。 

---

## <a name="deploy-template"></a>テンプレートのデプロイ 

### <a name="windows"></a>[Windows](#tab/windows)

テンプレート `addGPUextensiontoVM.json` をデプロイします。 このテンプレートにより、既存の VM に拡張機能がデプロイされます。 次のコマンドを実行します。

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> 拡張機能のデプロイは長時間実行されるジョブであり、完了するまでに約 10 分かかります。

出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

### <a name="linux"></a>[Linux](#tab/linux)

テンプレート `addGPUextensiontoVM.json` をデプロイします。 このテンプレートにより、既存の VM に拡張機能がデプロイされます。 次のコマンドを実行します。

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> 拡張機能のデプロイは長時間実行されるジョブであり、完了するまでに約 10 分かかります。

出力例を次に示します。

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
---

## <a name="track-deployment"></a>デプロイの追跡

### <a name="windows"></a>[Windows](#tab/windows)

特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

拡張機能の実行の出力は、次のファイルにログ記録されます。 インストールの状態を追跡するには、この `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` ファイルを参照してください。 


正常にインストールされた場合は、`message` によって `Enable Extension` として、および `status` によって `success` として示されます。

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

### <a name="linux"></a>[Linux](#tab/linux)

テンプレートのデプロイは、長時間実行されるジョブです。 特定の VM の拡張機能のデプロイ状態を確認するには、別の PowerShell セッションを開きます (管理者として実行します)。 次のコマンドを実行します。 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
出力例を次に示します。 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> デプロイが完了すると、`ProvisioningState` が `Succeeded` に変わります。

拡張機能の実行の出力は、`/var/log/azure/nvidia-vmext-status` ファイルにログ記録されます。

---

## <a name="verify-driver-installation"></a>ドライバーのインストールの確認

### <a name="windows"></a>[Windows](#tab/windows)

VM にサインインし、ドライバーと共にインストールされる nvidia-smi コマンド ライン ユーティリティを実行します。 `nvidia-smi.exe` は、`C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` にあります。 ファイルが表示されない場合は、ドライバーのインストールがバックグラウンドでまだ実行されている可能性があります。 10 分待ってから、もう一度確認してください。

ドライバーがインストールされている場合は、次の例のような出力が表示されます。 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

詳細については、「[Windows 用の Nvidia GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-gpu-windows.md)」を参照してください。

> [!NOTE]
> GPU ドライバーと GPU 拡張機能のインストールが完了すると、コンピューティングにインターネット アクセスのあるポートを使用する必要がなくなります。

### <a name="linux"></a>[Linux](#tab/linux)

ドライバーのインストールを確認するには、次の手順に従います。

1. GPU VM に接続します。 「[Linux VM への接続](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm)」の手順に従います。 

    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. ドライバーと共にインストールされる nvidia-smi コマンド ライン ユーティリティを実行します。 ドライバーが正常にインストールされている場合は、このユーティリティを実行して次の出力を確認できます。

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

詳細については、「[Linux 用の Nvidia GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-gpu-linux.md)」を参照してください。

> [!NOTE]
> GPU ドライバーと GPU 拡張機能のインストールが完了すると、コンピューティングにインターネット アクセスのあるポートを使用する必要がなくなります。


---


## <a name="remove-gpu-extension"></a>GPU 拡張機能の削除

GPU 拡張機能を削除するには、次のコマンドを使用します。

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

出力例を次に示します。

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```


## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。

- [GPU 拡張機能の問題をトラブルシューティングします](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)。
- [デバイス上の VM アクティビティを監視します](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)。
- [VM ディスクを管理します](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md)。
- [VM ネットワーク インターフェイスを管理します](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)。
- [VM のサイズを管理します](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md)。