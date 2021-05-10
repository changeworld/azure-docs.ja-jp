---
title: PowerShell を使用して Azure Stack Edge に新しい仮想スイッチを作成する
description: PowerShell を使用して Azure Stack Edge デバイスに仮想スイッチを作成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555923"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>PowerShell を使用して Azure Stack Edge Pro GPU に新しい仮想スイッチを作成する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスに新しい仮想スイッチを作成する方法について説明します。 たとえば、仮想マシンが別の物理ネットワーク ポートを使用して接続するようにする場合は、新しい仮想スイッチを作成します。

## <a name="vm-deployment-workflow"></a>VM デプロイのワークフロー

1. デバイスの PowerShell インターフェイスに接続します。
2. 使用可能な物理ネットワーク インターフェイスのクエリを実行します。
3. 仮想スイッチを作成する」で構成した仮想スイッチを指定する必要があります。
4. 自動的に作成された仮想ネットワークとサブネットを確認します。

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

- デバイスの PowerShell インターフェイスにアクセスできるクライアント マシンにアクセスできる。 「[PowerShell インターフェイスに接続する](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)」を参照してください。 

    クライアント マシンは、[サポートされている OS](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) を実行している必要があります。

- ローカル UI を使用して、デバイス上の物理ネットワーク インターフェイスの 1 つでコンピューティングを有効にする。デバイスで[コンピューティング ネットワークを有効にする](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)手順を参照してください。 


## <a name="connect-to-the-powershell-interface"></a>PowerShell インターフェイスに接続する

[デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

## <a name="query-available-network-interfaces"></a>使用可能なネットワーク インターフェイスのクエリを実行する

1. 次のコマンドを使用して、新しい仮想スイッチを作成できる物理ネットワーク インターフェイスの一覧を表示します。 これらのネットワーク インターフェイスのいずれかを選択します。

    ```powershell
    Get-NetAdapter -Physical
    ```
    出力例を次に示します。
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. 次の状態のネットワーク インターフェイスを選択します。

    - **Up** 状態になっている。 
    - 既存の仮想スイッチで使用されていない。 現時点では、ネットワーク インターフェイスごとに構成できる仮想スイッチは 1 つだけです。 
    
    既存の仮想スイッチとネットワーク インターフェイスの関連付けを確認するには、`Get-HcsExternalVirtualSwitch` コマンドを実行します。
 
    出力例を次に示します。

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    このインスタンスでは、ポート 2 は既存の仮想スイッチに関連付けられているため、使用できません。

## <a name="create-a-virtual-switch"></a>仮想スイッチを作成する

次のコマンドレットを使用して、指定したネットワーク インターフェイスに新しい仮想スイッチを作成します。 この操作が完了すると、コンピューティング インスタンスで新しい仮想ネットワークを使用できるようになります。

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

`Get-HcsExternalVirtualSwitch` コマンドを使用して、新しく作成したスイッチを識別します。 新しく作成されるスイッチには、`vswitch-<InterfaceAlias>` という名前が付けられます。 

出力例を次に示します。

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>ネットワーク、サブネットを確認する 

新しい仮想スイッチを作成すると、Azure Stack Edge Pro GPU によって、それに対応する仮想ネットワークとサブネットが自動的に作成されます。 この仮想ネットワークを VM の作成時に使用できます。

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>次の手順

- [Azure PowerShell を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Azure portal を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
