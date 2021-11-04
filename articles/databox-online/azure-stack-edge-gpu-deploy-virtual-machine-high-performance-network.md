---
title: Azure Stack Edge Pro GPU にハイ パフォーマンスのネットワーク VM をデプロイする
description: Azure Stack Edge Pro GPU にハイ パフォーマンスのネットワーク VM をデプロイする方法を確認してください。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/29/2021
ms.author: alkohli
ms.openlocfilehash: 4b3f2a2b232bec60edbce204008e245bcabee09c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092582"
---
# <a name="deploy-high-performance-network-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスにハイ パフォーマンスのネットワーク VM をデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure portal、テンプレート、Azure PowerShell コマンドレット、および Azure CLI または Python のスクリプトを使用して、Azure Stack Edge Pro GPU デバイス上に仮想マシン (VM) を作成および管理できます。 この記事では、Azure Stack Edge Pro GPU デバイスでハイ パフォーマンスのネットワーク (HPN) VM を作成および管理する方法について説明します。 

## <a name="about-hpn-vms"></a>HPN VM について

Non-uniform memory access (NUMA) アーキテクチャは、プロセッサの速度を向上させるために使用されます。 NUMA システムで、CPU はノードと呼ばれる小さいシステムに配置されます。 各ノードには、独自のプロセッサとメモリがあります。 通常、プロセッサにはアクセスが高速になるように、近接しているメモリが割り当てられます。 詳細については、「[NUMA サポート](/windows/win32/procthread/numa-support)」を参照してください。  

Azure Stack Edge デバイスで、論理プロセッサは NUMA ノードで配布され、高速ネットワーク インターフェイスはこれらのノードに接続できます。 HPN VM には、専用の論理プロセッサセットがあります。 これらのプロセッサは、まず高速ネットワーク インターフェイスが接続されている NUMA ノードから選択され、次に他のノードから選択されます。 HPN VM は、プロセッサに割り当てられている NUMA ノードのメモリのみを使用できます。  

デバイスに展開されている HPN VM で、低待機時間と高スループットのネットワーク アプリケーションを実行するには、NUMA ノード0に存在する vCPU を予約してください。 このノードには、Mellanox 高速ネットワーク インターフェイス、ポート5、およびポート6がアタッチされています。   

        
## <a name="hpn-vm-deployment-workflow"></a>HPN VM デプロイのワークフロー

HPN デプロイの大まかなワークフローは次のとおりです。

1. お使いの Azure Stack Edge デバイスでコンピューティング用のネットワーク インターフェイスを有効にします。 この手順により、指定されたネットワーク インターフェイスに仮想スイッチが作成されます。
1. Azure portal からの VM のクラウド管理を有効にします。
1. Azure Storage Explorer を使用して Azure Storage アカウントに VHD をアップロードします。 
1. アップロードした VHD を使用して、VHD をデバイスにダウンロードし、VHD から VM イメージを作成します。 
1. HPN VM 用に vCPU をデバイス上に予約します。
1. 前の手順で作成したリソースを使用します。
    1. 作成した VM イメージ。
    1. コンピューティングを有効にしたネットワーク インターフェイスに関連付けられている仮想スイッチ。
    1. 仮想スイッチに関連付けられているサブネット。

    次のリソースをインラインで作成または指定します。
    1. VM 名、サポートされている HPN VM サイズ、VM のサインイン資格情報を選択します。 
    1. 新しいデータ ディスクを作成するか、既存のデータ ディスクを接続します。
    1. VM の静的 IP または動的 IP を構成します。 静的 IP を指定する場合は、コンピューティング用に有効化されているネットワーク インターフェイスのサブネット範囲内の空き IP アドレスから選択します。

    前のリソースを使用して HPN VM を作成します。

## <a name="prerequisites"></a>[前提条件]

Azure portal を使用してデバイスで VM の作成と管理を開始する前に、次のことを確認してください。

- 「[手順 1: Azure Stack Edge Pro GPU デバイスを構成する](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device)」の説明に従って Azure Stack Edge Pro GPU デバイスでネットワーク設定を完了していること。

    1. コンピューティング用のネットワーク インターフェイスを有効にしていること。 このネットワーク インターフェイスの IP を使用して、VM デプロイ用の仮想スイッチを作成します。 デバイスのローカル UI で、 **[Compute]\(コンピューティング\)** に移動します。 仮想スイッチの作成に使用するネットワーク インターフェイスを選択します。

        > [!IMPORTANT] 
        > コンピューティング用に構成できるポートは 1 つだけです。

    1. そのネットワーク インターフェイスでコンピューティングを有効にします。 そのネットワーク インターフェイスに対応する仮想スイッチが Azure Stack Edge Pro GPU によって作成、管理されます。

-  作成する仮想マシンの VM イメージを作成するために使用する Windows または Linux VHD にアクセスできること。

VM の作成に使用される上記の前提条件に加えて、次の前提条件を HPN VM 専用に構成する必要もあります。

- Mellanox インターフェイスで HPN VM の vCPU を予約します。 次の手順に従います。

    1. [デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

    1. デバイスで実行されているすべての VM を識別します。 これには、Kubernetes VM、またはデプロイされている可能性のある VM ワークロードが含まれます。

        ```powershell
        get-vm -force
        ```
    1. 実行中のすべての VM を停止します。
    
        ```powershell
        stop-vm -force
        ```
    1. デバイスの `hostname` を取得します。 これにより、デバイスのホスト名に対応する文字列が返されます。

        ```powershell
        hostname
        ```
    1. HPN VM 用に予約する論理プロセッサインデックスを取得します。
    
        ```powershell
        Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName <Output of hostname command>
        ```
        出力例を次に示します。
    
        ```powershell    
        [dbe-1csphq2.microsoftdatabox.com]: PS>hostname
        1CSPHQ2
        [dbe-1csphq2.microsoftdatabox.com]: P> Get-HcsNumaLpMapping -MapType HighPerformanceCapable -NodeName 1CSPHQ2
         { Numa Node #0 : CPUs [4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19] }
         { Numa Node #1 : CPUs [24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39] }
        
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. HPN VM 用に vCPU を予約します。 ここで予約されている vCPU の数によって、HPN VM に割り当て可能な vCPU が決まります。 各 HPN VM サイズで使用されるコアの数については、「[サポートされている HPN VM サイズ](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)」を参照してください。 デバイスで、Mellanox ポート5と6は NUMA ノード0にあります。
    
        ```powershell
        Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated <Logical indexes from the Get-HcsNumaLpMapping cmdlet> -AssignAllCpusToRoot $false
        ```
    
        このコマンドを実行すると、デバイスが自動的に再起動されます。

        出力例を次に示します。 
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsNumaLpMapping -CpusForHighPerfVmsCommaSeperated "4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39" -AssignAllCpusToRoot $false
        Requested Configuration requires a reboot...
        Machine will reboot in some time. Please be patient.
        [dbe-1csphq2.microsoftdatabox.com]: PS>    
        ```
    
        > [!NOTE]
        > - この例に示されている両方の NUMA ノードから、またはインデックスのサブセットのすべての論理インデックスを予約することを選択できます。 インデックスのサブセットを予約する場合は、最適なパフォーマンスを得るために、Mellanox ネットワーク インターフェイスが接続されているデバイスノードからインデックスを選択します。 Azure Stack Edge Pro GPU の場合、Mellanox ネットワーク インターフェイスを使用した NUMA ノードは #0 です。  
        > - 論理インデックスの一覧には、奇数と偶数のペアのシーケンスが含まれている必要があります。 ((4, 5) (6, 7) (10, 11)) など。 `5,6,7`や、など`4,6` の数値リストを設定しようとしても機能しません。
        > - `Set-HcsNuma`vCPU を割り当てるために連続して2つのコマンドを使用すると、構成がリセットされます。 また、HPN VM をデプロイしている場合は、Set-HcsNuma コマンドレットを使用して CPU を解放しないでください。
  
    1. デバイスの再起動が完了するまで待ちます。 デバイスが実行されたら、新しい PowerShell セッションを開きます。 [デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
    
    1. vCPU 予約を検証します。
    
        ```powershell
        Get-HcsNumaLpMapping -MapType MinRootAware -NodeName <Output of hostname command> 
        ```
        出力には、設定したインデックスは表示されません。 出力に設定したインデックスが表示された場合、`Set` コマンドは正常に完了しませんでした。 コマンドを再試行してください。問題が解決しない場合は、Microsoft サポートにお問い合わせください。 
    
        出力例を次に示します。
    
        ```powershell
        [dbe-1csphq2.microsoftdatabox.com]: PS> Get-HcsNumaLpMapping -MapType MinRootAware -NodeName 1CSPHQ2
        { Numa Node #0 : CPUs [0, 1, 2, 3] }
        { Numa Node #1 : CPUs [20, 21, 22, 23] }
        [dbe-1csphq2.microsoftdatabox.com]: PS>
        ```
    
    1. 前の手順で停止した VM を再起動します。
    
        ```powershell
        start-vm 
        ```
    <!-- Start-vm doesn't seem to work alone. Get-vm alone doesn't seem to return my running VM"VmId"--> 



## <a name="deploy-a-vm"></a>VM をデプロイする

デバイスに HPN VM を作成するには、次の手順に従います。

1. Azure Stack Edge リソースの Azure portal で、[VM イメージを追加](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm-image)します。 次の手順で VM を作成するには、この VM イメージを使用します。

1. この構成要件で [VM を追加する](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#add-a-vm) のすべての手順に従います。 

    [基本] タブで、[DSv2 から、または HPN でサポートされている F シリーズ](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)の VM サイズを選択します。

    ![Azure Stack Edge の "仮想マシンの追加" ウィザードの [基本] タブを示すスクリーンショット。 [基本] タブと [次へ: ディスク] ボタンが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-1.png)

1. VM 作成の残りの手順を完了します。 VM の作成には約30分かかります。 

    ![Azure Stack Edge の仮想マシンの追加ウィザードの [確認と作成] タブを示すスクリーンショット。 [作成] ボタンが強調表示されています。](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-2.png)

1. VM が正常に作成されると、 **[概要]** ペインに新しい VM が表示されます。 新しく作成された VM を選択し、 **[仮想マシン]** にアクセスします。

    ![Azure Stack Edge デバイスの [仮想マシン] ペインを示すスクリーンショット。 [仮想マシン] ラベルと [仮想マシン] エントリが強調表示されている。](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-3.png)

    VM を選択すると、詳細が表示されます。

    ![Azure Stack Edge の仮想マシンの [概要] ペインの [詳細] タブを示すスクリーンショット。 VM サイズとネットワーク内の IP アドレスが強調表示されています。](media/azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network/add-high-performance-network-virtual-machine-4.png)

    VM に接続するネットワーク インターフェイスの IP アドレスを使用します。

    > [!NOTE]
    > デプロイ前の HPN VM 用に vCPU が予約されていない場合、デプロイは失敗し、`FabricVmPlacementErrorInsufficientNumaNodeCapacity`エラーが表示されます。
                                                                                                                                                                         
## <a name="next-steps"></a>次のステップ

- [VM のデプロイのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [デバイスの VM アクティビティの監視](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [VM の CPU とメモリの監視](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

