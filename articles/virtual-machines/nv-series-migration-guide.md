---
title: NV シリーズの移行ガイド
description: NV シリーズの移行ガイド
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: e91da8b052ba9ecce4a345c610b2299de22de1b3
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254264"
---
#  <a name="nv-series-migration-guide"></a>NV シリーズの移行ガイド 

Microsoft Azure データセンターでより強力な GPU VM が利用可能になったため、ワークロードを評価して NV および NV_Promo シリーズの仮想マシン (VM) を移行することをお勧めします。 これらの従来の VM は、NVsv3 および NVasv4 シリーズなどの新しい VM シリーズに移行して、コストを抑えながらパフォーマンスを改善できます。 NVsv3 VM シリーズは Nvidia M60 GPU、NVasv4 シリーズは AMD Radeon Instinct MI25 GPU を搭載しています。  新しい NVsv3 と NVasv4 は主に、パフォーマンスの向上、Premium Storage のサポート、分割 GPU サイズからマルチ GPU 構成まで選択できるオプションという点で、NV および NV_Promo シリーズと異なります。 NVsv3 と NVasv4 のどちらのシリーズも、コアがより新しくなり、容量が増しています。  

次のセクションでは、従来の NV シリーズと NVsv3 および NVv4 シリーズの違いについて要約しています。
 
 ## <a name="nvsv3-series"></a>NVsv3 シリーズ 

NVv3 シリーズの仮想マシンは、Intel E5-2690 v4 (Broadwell) CPU を使用した NVIDIA Tesla M60 GPU および NVIDIA GRID テクノロジと Intel ハイパースレッディング テクノロジを搭載しています。 これらの仮想マシンは、お客様がデータを視覚化したり、表示する結果をシミュレートしたり、CAD で作業したり、コンテンツをレンダリングおよびストリーミングしたりしたいと考える、GPU で高速化されたグラフィックス アプリケーションや仮想デスクトップを対象にしています。 さらに、これらの仮想マシンは、エンコーディングやレンダリングなどの単精度のワークロードを実行できます。 NVv3 仮想マシンは Premium Storage をサポートしており、NV シリーズと比較して 2 倍のシステム メモリ (RAM) を備えています。 最新の仕様については、[GPU 高速コンピューティング VM サイズ: NVsv3 シリーズ](nvv3-series.md)に関する記事を参照してください

| 現在の VM サイズ | ターゲットの VM サイズ | 仕様の違い  |
|---|---|---|
|Standard_NV6 <br> Standard NV6_Promo |Standard_NV12s_v3  | vCPU: 12 (+6) <br> メモリ: GiB 112 (+56) <br> 一時ストレージ (SSD) GiB: 320 (-20) <br> 最大データ ディスク数: 12 (-12) <br> 高速ネットワーク: あり <br> Premium Storage: あり  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU: 24 (+12) <br>メモリ: GiB 224 (+112) <br>一時ストレージ (SSD) GiB: 640 (-40)<br>最大データ ディスク数: 24 (-24)<br>高速ネットワーク: あり <br>Premium Storage: あり   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU: 48 (+24) <br>メモリ: GiB 448 (+224) <br>一時ストレージ (SSD) GiB: 1,280 (-160) <br>最大データ ディスク数: 32 (-32) <br>高速ネットワーク: あり <br>Premium Storage: あり   |

## <a name="nvsv4-series"></a>NVsv4 シリーズ 

NVv4 シリーズ仮想マシンは AMD Radeon Instinct MI25 GPU および AMD EPYC 7V12 (Rome) CPU を搭載しています。 NVv4 シリーズの場合、Azure では、部分的な GPU を備えた仮想マシンを導入しています。 16 GiB フレーム バッファーを備えた完全な GPU に対して 2 GiB フレーム バッファーを備えた 8 分の 1 の GPU を下限として、GPU で強化されたグラフィックス アプリケーションと仮想デスクトップに対して適正なサイズの仮想マシンを選択します。 NVv4 仮想マシンでは現在、Windows ゲスト オペレーティング システムのみがサポートされています。 最新の仕様については、[GPU 高速コンピューティング VM サイズ: NVsv4 シリーズ](nvv4-series.md)に関する記事を参照してください

| 現在の VM サイズ | ターゲットの VM サイズ | 仕様の違い  |
|---|---|---|
|Standard_NV6 <br> Standard NV6_Promo |Standard_NV16as_v4  | vCPU: 16 (+10) <br>メモリ: GiB 56  <br>一時ストレージ (SSD) GiB: 352 (+12) <br>最大データ ディスク数: 16 (-8) <br>高速ネットワーク: あり <br>Premium Storage: あり   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU: 32 (+20) <br>メモリ: GiB 112 <br>一時ストレージ (SSD) GiB: 704 (+24) <br>最大データ ディスク数: 32 (+16)<br>高速ネットワーク: あり <br>Premium Storage: あり   |
|Standard_NV24 <br> Standard_NV24_Promo |該当なし  | 該当なし  |

## <a name="migration-steps"></a>移行の手順 
 

一般的な変更 

1. 移行用のシリーズとサイズを選択します。 

2. ターゲット VM シリーズのクォータを取得します 

3. 現在の NV シリーズの VM サイズをターゲット サイズに変更します 

  ターゲット サイズが NVv4 の場合は、Nvidia GPU ドライバーを削除して AMD GPU ドライバーをインストールしてください 
  
## <a name="breaking-changes"></a>重大な変更 

## <a name="select-target-size-for-migration"></a>移行用のターゲット サイズを選択する 
現在の使用状況を評価した後、必要な GPU VM の種類を決定します。 ワークロードの要件に応じて、いくつかの異なる選択肢があります。 選択する方法は次のとおりです。  

ワークロードがグラフィックスや視覚化で、Nvidia GPU の使用に大きく依存している場合は、NVsv3 シリーズへの移行をお勧めします。  

ワークロードがグラフィックスや視覚化で、特定の種類の GPU に大きく依存していない場合は、NVsv3 または NVVasv4 シリーズをお勧めします。 
> [!Note]
>コストとパフォーマンスの両方に基づいて VM サイズを選択するのがベスト プラクティスです。 このガイドの推奨は、パフォーマンス メトリックについて NV および NV_Promo サイズと別の VM シリーズの最も合致するものを 1 対 1 で比較した結果に基づいています。
>適切なサイズを決定する前に、Azure 料金ツールを使用してコストの比較を得るようにしてください。

## <a name="get-quota-for-the-target-vm-family"></a>ターゲット VM ファミリのクォータを取得する 

ガイドに従って、[VM ファミリによる vCPU クォータの増加をリクエストします](../azure-portal/supportability/per-vm-quota-requests.md)。 移行用に選択したターゲット VMサイズに応じて、VM ファミリとして NVSv3 シリーズまたは NVv4 シリーズを選択します。
## <a name="resize-the-current-virtual-machine"></a>現在の仮想マシンをサイズ変更する
[Azure portal または PowerShell を通じて仮想マシンをサイズ変更](./windows/resize-vm.md)できます。 また、[Azure CLI を使用して仮想マシンをサイズ変更する](./linux/change-vm-size.md)こともできます。 

## <a name="faq"></a>よく寄せられる質問
**Q:** ターゲット VM サイズにはどの GPU ドライバーを使用する必要がありますか? 

**A:** NVsv3 シリーズには、[Nvidia GRID ドライバー](./windows/n-series-driver-setup.md)を使用します。 NVv4 には、[AMD GPU ドライバー](./windows/n-series-amd-driver-setup.md)を使用します。 

**Q:** 現在、Nvidia GPU ドライバー拡張機能を使用しています。 これはターゲット VM サイズで動作しますか? 

**A:** 現在の [Nvidia ドライバー拡張機能](./extensions/hpccompute-gpu-windows.md)は NVsv3 では動作します。 ターゲット VM サイズが NVv4 の場合は、[AMD GPU ドライバー拡張機能](./extensions/hpccompute-amd-gpu-windows.md)を使用してください。 
       
**Q:** CUDA に依存関係がある場合は、どの VM シリーズを使用すべきですか? 

 **A:** NVv3 では CUDA をサポートしています。 AMD GPU を搭載した NVv4 VM シリーズでは CUDA をサポートしていません。  
