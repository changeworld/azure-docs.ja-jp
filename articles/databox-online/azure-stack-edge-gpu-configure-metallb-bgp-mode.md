---
title: Azure Stack Edge 上で BGP を使用して MetalLB を構成する
description: ご利用の Azure Stack Edge デバイス上で、負荷分散を行うために Border Gateway Protocol を使用して MetalLB を構成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/24/2021
ms.author: alkohli
ms.openlocfilehash: df4f0170f6dde0995576b0244733dea4ab01515e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017518"
---
# <a name="configure-load-balancing-with-metallb-on-your-azure-stack-edge"></a>Azure Stack Edge 上で MetalLB を使用して負荷分散を構成する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge デバイス上で Border Gateway Protocol (BGP) を介して MetalLB を使用して負荷分散を構成する方法について説明します。 

## <a name="about-metallb-and-load-balancing"></a>MetalLB と負荷分散について

MetalLB とは、ベア メタル Kubernetes クラスター用のロード バランサー実装です。 MetalLB には次の 2 つの機能があります: 構成済みの IP アドレス プールから Kubernetes ロード バランサー サービスに IP アドレスを割り当てます。次にその IP を外部ネットワークにアナウンスします。 MetalLB では、これらの機能を実現するために、アドレス解決プロトコル (ARP)、近隣探索プロトコル (NDP)、または Border Gateway Protocol (BGP) などの標準のルーティング プロトコルが使用されます。 

詳細については、[MetalLB の BGP モード](https://metallb.universe.tf/configuration/#bgp-configuratioN)に関するページを参照してください。

## <a name="metallb-on-azure-stack-edge"></a>Azure Stack Edge での MetalLB

Azure Stack Edge デバイスには、Calico、MetalLB、Core DNS などの複数のネットワーク コンポーネントがインストールされています。 MetalLB は Azure Stack Edge デバイス上で実行されている Kubernetes クラスターにフックされます。これにより、お客様はクラスター内に `LoadBalancer` 型の Kubernetes サービスを作成できるようになります。

BGP モードでは、クラスター内のすべてのコンピューターが、お客様が制御する近くのルーターと BGP ピアリング セッションを確立し、トラフィックをサービス IP に転送する方法をそれらのルーターに知らせます。MetalLB と Border Gateway Protocol (BGP) の組み合わせは、ご利用のデバイスで実行されている Kubernetes クラスターの既定のネットワーク モードではありません。 BGP を使用して MetalLB を構成するには、Top-of-Rack (ToR) スイッチをロード バランサーとして指定し、ピア セッションを設定します。 

2 ノード デバイスを使用している場合は、MetalLB を BGP モードで構成することで、フェールオーバー時間を短縮できます。 この構成は、Top-of-Rack スイッチにアクセスできない可能性があるため、標準の構成よりも複雑になります。

## <a name="configure-metallb"></a>MetalLB を構成する

MetalLB を BGP モードで構成するには、デバイスの PowerShell インターフェイスに接続してから特定のコマンドレットを実行します。

### <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。
- デバイスの 1 つのポートでコンピューティングが有効になっている。 これにより、そのポートに仮想スイッチが作成されます。 
    - コンピューティングを有効にするには、ご利用のデバイスのローカル UI で、 **[高度なネットワーク]** ページにアクセスし、コンピューティングを有効にしたいポートを選択します。 
    - **[ネットワーク設定]** ページで、コンピューティング用のポートを有効にします。 設定を **適用** します。
- ご利用のデバイス上でコンピューティングを有効にしたポートと同じサブネット内に使用可能な IP がある。 

### <a name="configuration"></a>構成

BGP セッションを使用した MetalLB の基本的な構成には、次の情報が必要です。

- MetalLB の接続先とするピア IP アドレス。
- ピアの自律システム番号 (ASN)。 BGP では、ピア セッションの ASN を使用してルートをアナウンスする必要があります。
- MetalLB で使用される ASN。 ASN は 1 から 65534 の 16 ビット番号と 131072 から 4294967294 の 32 ビット番号です。

> [!IMPORTANT]
> MetalLB を BGP モードで動作させるには、ピアを指定する必要があります。 BGP ピアが指定されていない場合、MetalLB は既定のレイヤー 2 モードで動作します。 詳細については、[MetalLB のレイヤー 2 モード](https://metallb.universe.tf/concepts/layer2/)に関するページを参照してください。 


MetalLB を BGP モードで構成するには、次の手順に従ってください。

1. デバイスの [PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
 
1. `Get-HcsExternalVirtualSwitch` コマンドレットを実行して、BGP モードで使用する外部仮想スイッチの名前を取得します。 この仮想スイッチは、コンピューティング用のポートを有効にしたときに作成されています。

    ```powershell
    Get-HcsExternalVirtualSwitch
    ```
1. `Set-HcsBGPPeer` コマンドレットを実行して、BGP ピア セッションを確立します。

    ```powershell
    Set-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -PeerAsn <ASN for the peer> -SelfAsn <Your ASN> -SwitchName <Name of virtual switch on the port enabled for compute> -HoldTimeInSeconds <Optional hold time in seconds> 
    ```
1. そのセッションを確立したら、`Get-HcsBGPPeers` コマンドレットを実行して、仮想スイッチ上に存在するピア セッションを取得します。

    ```powershell
    Get-HcsBGPPeers -SwitchName <Name of virtual switch that you enabled for compute>
    ```
1. `Remove-HcsBGPPeer` コマンドレットを実行して、ピア セッションを削除します。 

    ```powershell
    Remove-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -SwitchName <Name of virtual switch on the port enabled for compute>
    ```
1. `Get-HcsBGPPeers` を実行して、ピア セッションが削除されていることを確認します。

出力例を次に示します。 

```powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $Name = "dbe-1csphq2.microsoftdatabox.com"
PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
PS C:\WINDOWS\system32> $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions
WARNING: The Windows PowerShell interface of your device is intended to
be used only for the initial network configuration. Please
engage Microsoft Support if you need to access this interface
to troubleshoot any potential issues you may be experiencing.
Changes made through this interface without involving Microsoft
Support could result in an unsupported configuration.
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : False
EnabledForMgmt                : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 3cb2d0ae-6a7b-44cc-8a5d-8eac2d1c0436
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

Name                          : vSwitch2
InterfaceAlias                : {Port3, Port4}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : True
EnabledForMgmt                : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 8dd480c0-8f22-42b1-8621-d2a43f70690d
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

[dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsBGPPeer -PeerAddress 10.126.77.125 -PeerAsn 64512 -SelfAsn 64513 -SwitchName vSwitch1 -HoldTimeInSeconds 15
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1

PeerAddress   PeerAsn SelfAsn HoldTime
-----------   ------- ------- --------
10.126.77.125 64512   64513         15

[dbe-1csphq2.microsoftdatabox.com]: PS>Remove-HcsBGPPeer -PeerAddress 10.126.77.125 -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>
```

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge デバイス上の Kubernetes クラスターのネットワーク](azure-stack-edge-gpu-kubernetes-networking.md)について説明します。