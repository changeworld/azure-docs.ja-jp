---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: e925dba3805ec8994aeba730e325c407468a5c87
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2018
---
## <a name="supported-distributions-and-drivers"></a>サポートされているディストリビューションとドライバー

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC、NCv2、NCv3、および ND シリーズ - NVIDIA CUDA ドライバー

次の表にある CUDA ドライバーの情報は、公開された時点のものです。 最新の CUDA ドライバーについては、[NVIDIA](https://developer.nvidia.com/cuda-zone) の Web サイトを参照してください。 ディストリビューションには最新の CUDA ドライバーをインストールまたはアップグレードしてください。 

> [!TIP]
> CUDA ドライバーを手動で Linux VM にインストールする代わりに、Azure [データ サイエンス仮想マシン](../articles/machine-learning/data-science-virtual-machine/overview.md) イメージをデプロイすることもできます。 Ubuntu 16.04 LTS または CentOS 7.4 用の DSVM エディションでは、NVIDIA CUDA ドライバーや CUDA Deep Neural Network ライブラリなどのツールが事前にインストールされています。

| ディストリビューション | ドライバー |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 または 7.4<br/><br/> CentOS 7.3 または 7.4、CentOS ベースの 7.4 HPC | NVIDIA CUDA 9.1、ドライバー ブランチ R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>NV シリーズ - NVIDIA GRID ドライバー

Microsoft では、NV VM 用の NVIDIA GRID ドライバー インストーラーを再配布しています。 Azure NV VM にはこれらの GRID ドライバーのみをインストールしてください。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。

| ディストリビューション | ドライバー |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS-based 7.3 | NVIDIA GRID 6.0、ドライバー ブランチ R390|



> [!WARNING] 
> サード パーティ製ソフトウェアを Red Hat 製品にインストールすると、Red Hat サポート条件に影響を与えることがあります。 [Red Hat のサポート技術情報記事](https://access.redhat.com/articles/1067)を参照してください。
>
