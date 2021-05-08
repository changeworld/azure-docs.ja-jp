---
title: Azure Stack Edge Pro GPU デバイス上の仮想マシン コンソールに接続する
description: Azure Stack Edge Pro GPU デバイスで実行されている VM で、仮想マシン コンソールに接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962443"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイス上の仮想マシン コンソールに接続する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU ソリューションは、仮想マシンを介して、コンテナー化されていないワークロードを実行します。 この記事では、デバイスにデプロイされている仮想マシンのコンソールに接続する方法について説明します。 

仮想マシン コンソールを使用すると、一般的に利用可能なリモート デスクトップ ツールを使用して、キーボード、マウス、および画面機能で VM にアクセスできます。 コンソールにアクセスし、デバイスに仮想マシンをデプロイするときに発生する問題のトラブルシューティングを行うことができます。 VM がプロビジョニングに失敗した場合でも、仮想マシン コンソールに接続できます。


## <a name="workflow"></a>ワークフロー

大まかなワークフローには、次の手順があります。

1. デバイスの PowerShell インターフェイスに接続します。
1. VM へのコンソール アクセスを有効にします。
1. リモート デスクトップ プロトコル (RDP) を使用して VM に接続します。
1. VM へのコンソール アクセスを取り消します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の前提条件が満たされていることを確認します。

#### <a name="for-your-device"></a>デバイス

アクティブ化されている Azure Stack Edge Pro GPU デバイスにアクセスできる必要があります。 デバイスには、1つまたは複数の VM がデプロイされている必要があります。 VM は、Azure PowerShell、テンプレート、または Azure portal を使用してデプロイできます。

#### <a name="for-client-accessing-the-device"></a>デバイスにアクセスするクライアントでは

次のようなクライアント システムにアクセスできることを確認します。

- デバイスの PowerShell インターフェイスにアクセスできる。 クライアントが、[サポートされているオペレーティング システム](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)を実行している。
- クライアントが、PowerShell 7.0 以降を実行している。 このバージョンの PowerShell は、Windows、Mac、および Linux のクライアントで動作します。 [PowerShell 7.0 をインストール](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true)する手順を参照してください。
- リモート デスクトップ機能がある。 Windows、macOS、Linux のどれを使用しているかに応じて、これらの[リモート デスクトップ クライアント](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)のいずれかをインストールする必要があります。 この記事では、 [Windows リモート デスクトップ](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client)と [FreeRDP](https://www.freerdp.com/) での手順について説明します。 <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>VM コンソールに接続する

次の手順に従い、デバイス上の仮想マシン コンソールに接続します。

### <a name="connect-to-the-powershell-interface-on-your-device"></a>デバイスの PowerShell インターフェイスに接続する

最初のステップは、デバイスの [PowerShell インターフェイスに接続](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)することです。 

### <a name="enable-console-access-to-the-vm"></a>VM へのコンソール アクセスを有効にする

1.  PowerShell インターフェイスで次のコマンドを実行して、VM コンソールへのアクセスを有効にします。

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. サンプル出力で、仮想マシン ID をメモしておきます。 これは後で必要になります。

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>VM に接続します

これで、リモート デスクトップ クライアントを使用して、仮想マシン コンソールに接続できるようになりました。

#### <a name="use-windows-remote-desktop"></a>Windows リモート デスクトップを使用する

1. 新しいテキスト ファイルを作成し、次のテキストを入力します。

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. クライアント システムに * *.rdp* としてファイルを保存します。 このプロファイルを使用して VM に接続します。
1. プロファイルをダブルクリックして、VM に接続します。 次の資格情報を入力します。

    - **ユーザー名**: EdgeARMUser としてサインインします。
    - **パスワード**: デバイスのローカル Azure Resource Manager パスワードを入力します。 パスワードを忘れた場合は、[Azure portal を使用して Azure Resource Manager パスワードをリセット](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal)します。 

#### <a name="use-freerdp"></a>FreeRDP を使用する

Linux クライアントで FreeRDP を使用する場合は、次のコマンドを実行します。 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>VM コンソールへのアクセスを取り消す

VM コンソールへのアクセスを取り消すには、デバイスの PowerShell インターフェイスに戻ります。 次のコマンドを実行します。

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
出力例を次に示します。

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> VM コンソールの使用が終わったら、アクセスを取り消すか PowerShell ウィンドウを閉じて、セッションを終了することをお勧めします。 

## <a name="next-steps"></a>次のステップ

- Azure portal で [Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) をトラブルシューティングします。