---
title: "インクルード ファイル"
description: "インクルード ファイル"
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>サポートされているディストリビューションとドライバー

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC、NCv2、NCv3、および ND シリーズ - NVIDIA CUDA ドライバー
| ディストリビューション | ドライバー |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 または 7.4<br/><br/> CentOS 7.3 または 7.4 | NVIDIA CUDA 9.1、ドライバー ブランチ R390 |

> [!IMPORTANT]
> ディストリビューションには最新の CUDA ドライバーをインストールまたはアップグレードしてください。 R390 より前のバージョンのドライバーでは、更新された Linux カーネルに関して問題が発生する場合があります。
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV シリーズ - NVIDIA GRID ドライバー

| ディストリビューション | ドライバー |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS-based 7.3 | NVIDIA GRID 5.2、ドライバー ブランチ R384|

> [!NOTE]
> Microsoft では、NV VM 用の NVIDIA GRID ドライバー インストーラーを再配布しています。 Azure NV VM にはこれらの GRID ドライバーのみをインストールしてください。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。
>

> [!WARNING] 
> サード パーティ製ソフトウェアを Red Hat 製品にインストールすると、Red Hat サポート条件に影響を与えることがあります。 [Red Hat のサポート技術情報記事](https://access.redhat.com/articles/1067)を参照してください。
>
