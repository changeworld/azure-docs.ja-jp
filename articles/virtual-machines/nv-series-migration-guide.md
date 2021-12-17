---
title: NV シリーズ移行ガイド
description: NV シリーズ移行ガイド
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 3f37a9c14a66156b908eb1d046f98301add07c20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562385"
---
# <a name="nv-series-migration-guide"></a>NV シリーズ移行ガイド

Azure データセンターでより強力な GPU VM サイズが利用可能になったため、ワークロードを評価して NV および NV_Promo シリーズの仮想マシン (VM) を移行します。 これらの従来の VM は、NVsv3 および NVasv4 などの新しい VM シリーズに移行して、コストを抑えながらパフォーマンスを改善できます。 NVsv3 VM シリーズは、Nvidia M60 GPU を搭載しています。 NVasv4 シリーズは、AMD Radeon Instinct MI25 GPU を搭載しています。

NV シリーズ、NV_Promo シリーズ、新しい NVsv3 および NVasv4 シリーズの主な違いは次のとおりです。
- パフォーマンスが向上します。
- Premium Storage のサポート。
- 小数部の GPU サイズからマルチ GPU 構成を選択するオプション。

NVsv3 と NVasv4 のどちらのシリーズも、コアがより新しくなり、容量が増しています。

次のセクションでは、従来の NV シリーズと NVsv3 および NVv4 シリーズの違いについて要約しています。
 
 ## <a name="nvsv3-series"></a>NVsv3 シリーズ 

NVv3 シリーズの VM は、Intel E5-2690 v4 (Broadwell) CPU を使用した NVIDIA Tesla M60 GPU および NVIDIA GRID テクノロジと Intel ハイパースレッディング テクノロジを搭載しています。 VM は、次の目的で GPU アクセラレータによるグラフィックス アプリケーションと仮想デスクトップを使用するお客様を対象としています。
- データを視覚化します。
- 表示する結果をシミュレートします。
- CAD を操作します。
- コンテンツをレンダリングおよびストリーム配信します。 

これらの VM は、エンコードやレンダリングなどの単精度のワークロードを実行することもできます。

NVv3 VM は Premium Storage をサポートしており、NV シリーズと比較して 2 倍のシステム メモリ (RAM) を備えています。 最新の仕様については、[GPU 高速コンピューティング VM サイズ: NVsv3 シリーズ](nvv3-series.md)に関する記事を参照してください。

| 現在の VM サイズ | ターゲット VM サイズ | 仕様の違い  |
|---|---|---|
|Standard_NV6 <br> Standard NV6_Promo |Standard_NV12s_v3  | vCPU: 12 (+6) <br> メモリ: GiB 112 (+56) <br> 一時ストレージ (SSD) GiB: 320 (-20) <br> 最大データ ディスク数: 12 (-12) <br> 高速ネットワーク: あり <br> Premium Storage: あり  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU: 24 (+12) <br>メモリ: GiB 224 (+112) <br>一時ストレージ (SSD) GiB: 640 (-40)<br>最大データ ディスク数: 24 (-24)<br>高速ネットワーク: あり <br>Premium Storage: あり   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU: 48 (+24) <br>メモリ: GiB 448 (+224) <br>一時ストレージ (SSD) GiB: 1280 (-160) <br>最大データ ディスク数: 32 (-32) <br>高速ネットワーク: あり <br>Premium Storage: あり   |

## <a name="nvv4-series"></a>NVv4 シリーズ 

NVv4 シリーズ VM は AMD Radeon Instinct MI25 GPU および AMD EPYC 7V12 (Rome) CPU を搭載しています。 NVv4 シリーズでは、部分的な GPU を備えた VM が Azure によって導入されます。 16 GiB フレーム バッファーを備えた完全な GPU に対して 2 GiB フレーム バッファーを備えた 8 分の 1 の GPU を下限として、GPU で強化されたグラフィックス アプリケーションと仮想デスクトップに対して適正なサイズの VM を選択します。

現在 NVv4 VM では、Windows ゲスト オペレーティング システムのみがサポートされています。 最新の仕様については、[GPU 高速コンピューティング VM サイズ: NVv4 シリーズ](nvv4-series.md)に関する記事を参照してください。

| 現在の VM サイズ | ターゲット VM サイズ | 仕様の違い  |
|---|---|---|
|Standard_NV6 <br> Standard NV6_Promo |Standard_NV16as_v4  | vCPU: 16 (+10) <br>メモリ: GiB 56  <br>一時ストレージ (SSD) GiB: 352 (+12) <br>最大データ ディスク数: 16 (-8) <br>高速ネットワーク: あり <br>Premium Storage: あり   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU: 32 (+20) <br>メモリ: GiB 112 <br>一時ストレージ (SSD) GiB: 704 (+24) <br>最大データ ディスク数: 32 (+16)<br>高速ネットワーク: あり <br>Premium Storage: あり   |
|Standard_NV24 <br> Standard_NV24_Promo |該当なし  | 該当なし  |

## <a name="migration-steps-for-general-changes"></a>一般的な変更のための移行手順

一般的な変更に対処するには、次の操作を実行します。

1. 移行用のシリーズとサイズを選択します。 

1. ターゲット VM シリーズのクォータを取得します。

1. 現在の NV シリーズの VM サイズをターゲット サイズに変更します。

  ターゲット サイズが NVv4 の場合は、Nvidia GPU ドライバーを削除し、AMD GPU ドライバーをインストールします。

## <a name="migration-steps-for-breaking-changes"></a>破壊的変更のための移行手順

破壊的変更に対処するには、次のセクションの手順に従います。

### <a name="select-a-target-size-for-migration"></a>移行用のターゲット サイズを選択する

現在の使用状況を評価した後、必要な GPU VM の種類を決定します。 ワークロードの要件に応じて、いくつかの異なる選択肢があります。 選択する方法は次のとおりです。

- ワークロードがグラフィックスまたは視覚化で、Nvidia GPU の使用に大きく依存している場合は、NVsv3 シリーズに移行します。
- ワークロードがグラフィックスまたは視覚化で、特定の種類の GPU に大きく依存していない場合は、NVsv3 または NVVasv4 シリーズに移行します。
 
> [!Note]
>ベスト プラクティスとして、コストとパフォーマンスの両方に基づいて VM サイズを選択することをお勧めします。 この記事の推奨事項は、パフォーマンス メトリックについて NV および NV_Promo サイズと別の VM シリーズの最も合致するものを 1 対 1 で比較した結果に基づいています。
>適切なサイズを決定する前に、Azure 料金ツールを使用してコストの比較を得るようにしてください。

### <a name="get-a-quota-for-the-target-vm-family"></a>ターゲット VM ファミリのクォータを取得する 

ガイドに従って、[VM ファミリによる vCPU クォータの増加をリクエストします](../azure-portal/supportability/per-vm-quota-requests.md)。 移行用に選択したターゲット VM サイズに応じて、VM ファミリ名として NVSv3 シリーズまたは NVv4 シリーズを選択します。

### <a name="resize-the-current-vm"></a>現在の VM のサイズを変更する

[VM はサイズ変更](resize-vm.md)することができます。

## <a name="faq"></a>よく寄せられる質問
**Q:** ターゲット VM サイズにはどの GPU ドライバーを使用する必要がありますか? 

**A:** NVsv3 シリーズには、[Nvidia GRID ドライバー](./windows/n-series-driver-setup.md)を使用します。 NVv4 には、[AMD GPU ドライバー](./windows/n-series-amd-driver-setup.md)を使用します。 

**Q:** 現在、Nvidia GPU ドライバー拡張機能を使用しています。 これはターゲット VM サイズで動作しますか? 

**A:** 現在の [Nvidia ドライバー拡張機能](./extensions/hpccompute-gpu-windows.md)は NVsv3 では動作します。 ターゲット VM サイズが NVv4 の場合は、[AMD GPU ドライバー拡張機能](./extensions/hpccompute-amd-gpu-windows.md)を使用してください。 
 
**Q:** CUDA に依存関係がある場合は、どの VM シリーズを使用すべきですか? 

 **A:** NVv3 では CUDA をサポートしています。 AMD GPU を搭載した NVv4 VM シリーズでは CUDA をサポートしていません。
