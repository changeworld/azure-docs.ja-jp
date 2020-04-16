---
title: Azure VM のサイズ - HPC | Microsoft Docs
description: Azure のハイ パフォーマンス コンピューティング仮想マシンで使用できるさまざまなサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: df22c857571e51bb886ff1d25db185a306999540
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420864"
---
# <a name="high-performance-computing-vm-sizes"></a>ハイ パフォーマンス コンピューティング VM のサイズ

Azure H シリーズ仮想マシン (VM) は、実環境のさまざまな HPC ワークロードに対するリーダー クラスのパフォーマンス、MPI スケーラビリティ、およびコスト効率を提供するように設計されています。

[HBv2 シリーズ](hbv2-series.md) VM は 200 Gb/秒の Mellanox HDR InfiniBand を、HB および HC シリーズ VM は、どちらも 100 Gb/秒の Mellanox EDR InfiniBand を備えています。 これらの VM の種類は、最適化された一貫性のある RDMA パフォーマンスを確保するために、ノンブロッキング ファット ツリー構造で接続されています。 HBv2 VM では、アダプティブ ルーティングと動的接続トランスポート (DCT、標準 RC トランスポートと UD トランスポートに加えて) をサポートしています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、それらを使用することを強くお勧めします。

[HB シリーズ](hb-series.md) VM は、流体力学、陽解法有限要素解析、気象モデリングなどの、メモリ帯域幅に基づいたアプリケーション向けに最適化されています。 HB VM は 60 個の AMD EPYC 7551 プロセッサ コア、CPU コアあたり 4 GB の RAM を搭載し、ハイパースレッディングはありません。 AMD EPYC プラットフォームは、260 GB/秒を超えるメモリ帯域幅を提供します。

[HC シリーズ](hc-series.md) VM は、陰解法有限要素解析、分子力学、計算化学などの、高密度計算に基づいたアプリケーション向けに最適化されています。 HC VM は 44 個の Intel Xeon Platinum 8168 プロセッサ コア、CPU コアあたり 8 GB の RAM を搭載し、ハイパースレッディングはありません。 Intel Xeon Platinum プラットフォームは、Intel Math Kernel Library などの Intel の豊富なソフトウェア ツールのエコシステムをサポートしています。

[H シリーズ](h-series.md) VM は、高い CPU 周波数またはコアあたり大容量メモリの要件に基づいたアプリケーション向けに最適化されています。 H シリーズ VM は 8 または 16 個の Intel Xeon E5 2667 v3 プロセッサ コア、CPU コアあたり 7 または 14 GB の RAM を搭載し、ハイパースレッディングはありません。 H シリーズは、一貫した RDMA パフォーマンスを得るために、非ブロッキングのファット ツリー構成内に 56 Gb/秒の Mellanox FDR InfiniBand を搭載しています。 H シリーズ VM は、Intel MPI 5.x と MS-MPI をサポートしています。

> [!NOTE]
> A8 - A11 VM は、2021 年 3 月で廃止される予定です。 詳細については、「[HPC マイグレーション ガイド](https://azure.microsoft.com/resources/hpc-migration-guide/)」を参照してください。

## <a name="rdma-capable-instances"></a>RDMA 対応のインスタンス

ほとんどの HPC VM サイズ (HBv2、HB、HC、H16r、H16mr、A8 および A9) には、リモート ダイレクト メモリ アクセス (RDMA) 接続のためのネットワーク インターフェイスが備わっています。 NC24rs 構成 (NC24rs_v3、NC24rs_v2、および NC24r) など、'r' で指定された、選択された [N シリーズ] (https://docs.microsoft.com/azure/virtual-machines/nc-series) サイズも RDMA に対応します。 このインターフェイスは、標準の Azure ネットワーク インターフェイスに加えて、他の VM サイズでも利用可能です。

このインターフェイスにより、RDMA 対応インスタンスは InfiniBand (IB) ネットワークを介して通信することができ、HBv2 では HDR のレートで、HB、HC では EDR のレートで、H16r、H16mr、および RDMA 対応 N シリーズの仮想マシンでは FDR のレートで、A8 と A9 の VM では QDR のレートで動作します。 これらの RDMA 機能により、特定の Message Passing Interface (MPI) アプリケーションのスケーラビリティとパフォーマンスが向上します。 速度の詳細については、このページの表の詳細情報を参照してください。

> [!NOTE]
> Azure HPC では、InfiniBand に対して SR-IOV 対応であるかどうかに応じて、2 つのクラスの VM があります。 現在のところ、InfiniBand の SR-IOV 対応の VM は、HBv2、HB、HC、NCv3 です。 残りの InfiniBand 対応 VM は SR-IOV 対応ではありません。
> RDMA over IB は、RDMA 対応のすべての VM でサポートされています。
> IP over IB は、SR-IOV 対応の VM のみでサポートされています。

- **オペレーティングシステム** - Linux は、HPC VM で適切にサポートされており、CentOS、RHEL、Ubuntu、SUSE などのディストリビューションが一般的です。 Windows のサポートに関しては、すべての HPC シリーズ VM で Windows Server 2016 がサポートされています。 また、Windows Server 2012 R2、Windows Server 2012 は、SR-IOV に対応していない VM でもサポートされています。

- **MPI** - Azure の SR-IOV 対応 VM サイズ (HBv2、HB、HC、NCv3) では、ほぼすべてのフレーバーの MPI を Mellanox OFED と一緒に使用できます。
SR-IOV に対応していない VM の場合、サポートされている MPI 実装では、VM 間の通信に Microsoft Network Direct (ND) インターフェイスが使用されます。 そのため、Microsoft MPI (MS MPI) 2012 R2 以降と Intel MPI 5.x バージョンのみがサポートされています。 Intel MPI ランタイム ライブラリの以降のバージョン (2017、2018) は、Azure RDMA ドライバーと互換性がある場合とない場合があります。

- **InfiniBandDriver<Linux|Windows> VM 拡張** - RDMA 対応の VM で、InfiniBandDriver<Linux|Windows> 拡張機能を追加して InfiniBand を有効にします。 Linux では、InfiniBandDriverLinux VM 拡張機能は、RDMA 接続用に Mellanox OFED ドライバーを (SR-IOV VM 上に) インストールします。 Windows では、InfiniBandDriverWindows VM 拡張機能は、RDMA 接続用に Windows Network Direct ドライバーを (SR-IOV に非対応の VM 上に) インストールするか、または Mellanox OFED ドライバーを (SR-IOV VM 上に) インストールします。
A8 インスタンスと A9 インスタンスの特定のデプロイでは、HpcVmDrivers 拡張機能は自動的に追加されます。 HpcVmDrivers VM 拡張機能は廃止される予定であり、更新されないことに注意してください。
VM に VM 拡張機能を追加するには、[Azure PowerShell](/powershell/azure/overview) コマンドレットを使用できます。 

  次のコマンドは、*West US* リージョン内の *myResourceGroup* という名前のリソース グループにデプロイされた *myVM* という名前の既存の RDMA 対応 VM に、最新バージョン 1.0 の InfiniBandDriverWindows 拡張機能をインストールします。

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  また、次の JSON 要素を使用して、簡単にデプロイするために VM 拡張機能を Azure Resource Manager テンプレートに含めることができます。

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  次のコマンドでは、*myResourceGroup* という名前のリソース グループにデプロイされた *myVMSS* という名前の既存の仮想マシン スケール セットのすべての RDMA 対応 VM に、最新バージョンの 1.0 の InfiniBandDriverWindows 拡張機能をインストールします。

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  詳しくは、[仮想マシン拡張機能とその機能](./extensions/overview.md)に関する記事をご覧ください。 [クラシック デプロイ モデル](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)にデプロイされている VM にも拡張機能を使用できます。

- **RDMA ネットワーク アドレス空間** - Azure の RDMA ネットワークでは、アドレス空間 172.16.0.0/16 は予約済みです。 Azure 仮想ネットワークにデプロイ済みのインスタンスで MPI アプリケーションを実行する場合、仮想ネットワークのアドレス空間が RDMA ネットワークと重複しないようにしてください。

## <a name="cluster-configuration-options"></a>クラスター構成オプション

Azure では、次のような、RDMA ネットワークを使用して通信できる Windows HPC VM のクラスターを作成するためのオプションがいくつか提供されます。 

- **仮想マシン** - 同じスケール セットまたは可用性セット内に RDMA 対応の HPC VM をデプロイします (Azure Resource Manager デプロイ モデルを使用する場合)。 クラシック デプロイ モデルを使用する場合は、同じクラウド サービス内に VM をデプロイします。

- **Virtual Machine Scale Sets** - 仮想マシン スケール セットで、単一の配置グループにデプロイを制限するようにします。 たとえば、Resource Manager テンプレートでは、`singlePlacementGroup` プロパティを `true` に設定します。 `singlePlacementGroup` プロパティを `true` にして起動できる VMSS の最大サイズは、既定で 100 VM に制限されることに注意してください。 HPC ジョブ スケールのニーズが、1 つの VMSS テナントでの 100 VM よりも大きい場合は、増加を要求できます。[オンライン カスタマー サポートに申請](../azure-supportability/how-to-create-azure-support-request.md) (無料) してください。

- **仮想マシン間の MPI** - 仮想マシン (VM) 間で RDMA (MPI 通信の使用など) が必要な場合は、VM が同じ仮想マシン スケール セットまたは可用性セットに含まれていることを確認します。

- **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/) 内に HPC クラスターを作成して、MPI ジョブを実行します。

- **Azure Batch** - [Azure Batch](/azure/batch/) プールを作成して、MPI ワークロードを実行します。 Azure Batch で MPI アプリケーションを実行するときにコンピューティング集中型インスタンスを使用するには、「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../batch/batch-mpi.md)」をご覧ください。

- **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) には、RDMA 対応の Linux VM 上にデプロイした場合に Azure RDMA ネットワークを使用する MS-MPI 用のランタイム環境が含まれています。 デプロイの例については、[HPC Pack を使用して Linux RDMA クラスターをセットアップして MPI アプリケーションを実行する](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)方法に関するページを参照してください。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

- **Azure サブスクリプション** – 多数のコンピューティング集中型インスタンスをデプロイするには、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

- **価格と可用性** - これらの VM サイズは、Standard 価格レベルでのみ提供されます。 各 Azure リージョンで利用できるかどうかについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/) 」を参照してください。

- **コア クォータ** – 場合によっては、Azure サブスクリプションのコア クォータを既定値から増やす必要があります。 サブスクリプションによっては、H シリーズを含む特定の VM サイズ ファミリにデプロイできるコア数が制限されることがあります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../azure-supportability/how-to-create-azure-support-request.md) (無料) してください。 (既定の制限は、サブスクリプション カテゴリによって異なる場合があります。)

  > [!NOTE]
  > 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。 Azure のクォータは容量保証ではなくクレジット制限です。 クォータに関係なく、使用したコアに対してのみ課金されます。
  
- **仮想ネットワーク** – コンピューティング集中型インスタンスを使用するために、Azure [Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) は不要です。 ただし、多くのデプロイでは、少なくともクラウド ベースの Azure Virtual Network またはサイト間接続 (オンプレミス リソースへのアクセスが必要な場合) が必要になります。 必要に応じて、新しい仮想ネットワークを作成して、インスタンスをデプロイします。 アフィニティ グループ内の仮想ネットワークにコンピューティング集中型の VM を追加することはできません。

- **サイズ変更** – 特殊なハードウェアが使用されるため、コンピューティング集中型インスタンスのサイズ変更は同じサイズ ファミリ (H シリーズまたはコンピューティング集中型 A シリーズ) 内でのみ可能です。 たとえば、H シリーズの VM は H シリーズのあるサイズから別のサイズにのみ変更できます。 さらに、コンピューティング集中型以外のサイズからコンピューティング集中型サイズへのサイズ変更はサポートされていません。  


## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

- [HPC ワークロード] で Azure 用に HPC アプリケーションを最適化することに関する詳細といくつかの例を確認する (https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
