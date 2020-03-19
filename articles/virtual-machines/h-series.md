---
title: H シリーズ - Azure Virtual Machines
description: H シリーズ VM の仕様。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: a71b7b7de6f6039106b43576847675f48de803c8
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088056"
---
# <a name="h-series"></a>H シリーズ

H シリーズ VM は、高い CPU 周波数またはコアあたり大容量メモリの要件に基づいたアプリケーション向けに最適化されています。 H シリーズ VM は、8 個または 16 個の Intel Xeon E5 2667 v3 プロセッサ コア、CPU コアあたり最大 14 GB の RAM を備え、ハイパースレッディングはありません。 H シリーズは、一貫した RDMA パフォーマンスを得るために、非ブロッキングのファット ツリー構成内に 56 Gb/秒の Mellanox FDR InfiniBand を搭載しています。 H シリーズ VM は、Intel MPI 5.x と MS-MPI をサポートしています。

ACU: 290-300

Premium Storage: サポートされていません

Premium Storage Caching: サポートされていません

ライブ マイグレーション:サポートされていません

メモリ保持更新: サポートされていません

| サイズ | vCPU | プロセッサ | メモリ (GB) | メモリ帯域幅 GB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GB/秒) | MPI のサポート | 一時ストレージ (GB) | 最大データ ディスク数 | 最大イーサネット NIC |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x、MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> MPI アプリケーションの場合、専用の RDMA バックエンド ネットワークが FDR InfiniBand ネットワークによって有効になります。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>サポート対象の OS イメージ (Linux)
 
Azure Marketplace には、RDMA 接続をサポートする多くの Linux ディストリビューションがあります。
  
* **CentOS ベースの HPC** - SR-IOV に対応していない VM の場合、CentOS ベースのバージョン 6.5 の HPC またはそれ以降 (7.5 まで) のバージョンが適しています。 H シリーズの VM では、バージョン 7.1 から 7.5 をお勧めします。 RDMA ドライバーおよび Intel MPI 5.1 は、VM にインストールされます。
  SR-IOV 対応の VM では、CentOS HPC 7.6 は最適化され、RDMA ドライバーが事前に読み込まれ、さまざまな MPI パッケージがインストールされています。
  その他の RHEL/CentOS VM イメージの場合、InfiniBandLinux 拡張機能を追加して InfiniBand を有効にします。 この Linux VM 拡張機能は、RDMA 接続用に Mellanox OFED ドライバーを (SR-IOV 対応の VM 上に) インストールします。 次の PowerShell コマンドレットは、InfiniBandDriverLinux 拡張機能の最新バージョン (バージョン 1.0) を既存の RDMA 対応の VM にインストールします。 RDMA 対応の VM は、名前が *myVM* で、次のように *West US* リージョンの *myResourceGroup* という名前のリソース グループにデプロイされます。

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  また、次の JSON 要素を使用して、簡単にデプロイするために VM 拡張機能を Azure Resource Manager テンプレートに含めることができます。
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  次のコマンドでは、*myResourceGroup* という名前のリソース グループにデプロイされた *myVMSS* という名前の既存の仮想マシン スケール セットのすべての RDMA 対応 VM に、最新バージョンの 1.0 の InfiniBandDriverLinux 拡張機能をインストールします。
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > CentOS ベースの HPC イメージでは、 **yum** 構成ファイルでのカーネルの更新は無効にされています。 これは、Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)、SLES 12 SP4 および SLES 15。 RDMA ドライバーがインストールされ、Intel MPI パッケージが VM に配布されます。 次のコマンドを実行して MPI をインストールします。

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS、18.04 LTS。 VM で RDMA ドライバーを構成し、Intel に登録して Intel MPI をダウンロードします。

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  InfiniBand の有効化と MPI の設定の詳細については、[Infiniband の有効化](/workloads/hpc/enable-infiniband.md)に関するページを参照してください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
