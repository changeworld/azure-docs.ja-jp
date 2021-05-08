---
title: NP シリーズ - Azure Virtual Machines
description: NP シリーズ VM の仕様。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: aa67a858d0396badc25a625b23dc2f2fdf1bdff9
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551375"
---
# <a name="np-series"></a>NP シリーズ 
NP シリーズの仮想マシンでは、機械学習の推論、ビデオのトランスコード、データベースの検索と分析など、ワークロードを加速させるために [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA を利用しています。 また、NP シリーズの VM は Intel Xeon 8171M (Skylake) の CPU を搭載し、全コア ターボ クロック速度は 3.2 GHz です。

[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
VM 世代サポート: 第 1 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | FPGA | FPGA メモリ:GiB | 最大データ ディスク数 | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1/7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2/15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4/30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:** 使用すべき Vitis のバージョンを教えてください。 

**A:** Xilinx では [Vitis 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html) を推奨しています。


**Q:** ソリューションを開発するために NP VM を使用する必要がありますか。 

**A:** いいえ。オンプレミスで開発し、クラウドにデプロイできます。 構成証明のドキュメントに従って、NP VM にデプロイしてください。 

**Q:** 使用すべき XRT のバージョンを教えてください。

**A:** xrt_202020.2.8.832 

**Q:** ターゲット デプロイ プラットフォームはどれですか。

**A:** 次のプラットフォームを使用してください。
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**Q:** どのプラットフォームが開発の対象になりますか。

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**Q:** どの OS (オペレーティング システム) がサポートされますか。 

**A:** Xilinx と Microsoft は、Ubuntu 18.04 LTS と CentOS 7.8 を検証しました。

 Xilinx は、これらの VM のデプロイを簡略化するために、次の Marketplace イメージを作成しました。 

[Xilinx Alveo U250 Deployment VM – Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Deployment VM – CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**Q:** 独自の Ubuntu/CentOS VM をデプロイし、XRT/デプロイ ターゲット プラットフォームをインストールすることはできますか。 

**A:** はい。

**Q:** 独自の Ubuntu 18.04 VM をデプロイする場合、どのパッケージと手順が必要になりますか。

**A:** [Xilinx XRT のドキュメント](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)に従ってカーネル 4.1X を使用してださい。
       
次のパッケージをインストールしてください。
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**Q:** Ubuntu で、VM を再起動した後に FPGA が見つかりません。 

**A:** カーネルがアップグレードされていないことを確認してください (uname-a)。 その場合、カーネル 4.1X にダウングレードしてください。 

**Q:** 独自の CentOS 7.8 VM をデプロイする場合、どのパッケージと手順が必要になりますか。

**A:** カーネル バージョン 3.10.0-1160.15.2.el7.x86_64 を使用してください。

 次のパッケージをインストールしてください。
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

**Q:** CentOS で xbutil validate を実行すると、次の警告が表示されます: "WARNING: Kernel version 3.10.0-1160.15.2.el7.x86_64 is not officially supported.(警告: カーネル バージョン 3.10.0-1160.15.2.el7.x86_64 は、公式にはサポートされていません。) 4.18.0-193 is the latest supported version.(4.18.0-193 が、サポートされている最新のバージョンです。)" 

**A:** これは無視しても問題ありません。 

**Q:** XRT に関してオンプレミス と NP の 各VM の違いは何ですか。 

**A:** Azure では、XDMA 2.1 プラットフォームは Host_Mem (SB) および DDR データ保有機能のみをサポートします。 

Host_Mem(SB) (1Gb RAM) を有効にするには: sudo xbutil host_mem --enable --size 1g 

Host_Mem(SB) を無効にするには: sudo xbutil host_mem --disable 

**Q:** xbmgmt コマンドを実行できますか。 

**A:** いいえ。Azure VM では、Azure VM からの直接の管理はサポートされていません。 

 **Q:** PLP を読み込む必要がありますか。 

**A:** いいえ。PLP は自動的に読み込まれるため、xbmgmt コマンドを使用して読み込む必要はありません。 

 
**Q:** Azure ではさまざまな PLP がサポートされていますか。 

**A:** 現時点ではされていません。 デプロイ プラットフォーム パッケージで提供される PLP のみがサポートされます。 

**Q:** PLP 情報を照会するにはどのようにすればよいですか。 

**A:** xbutil クエリを実行し、下の部分を確認する必要があります。 

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
