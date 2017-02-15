---
title: "Cloud Services のサイズ | Microsoft Docs"
description: "Azure のクラウド サービスの Web ロールと worker ロールのさまざまな仮想マシンのサイズ (および ID) の一覧を示します。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d6faf73b3fb5f56e42a07dc9c0bbf1a48767c7c8


---
# <a name="sizes-for-cloud-services"></a>Cloud Services のサイズ
このトピックでは、クラウド サービスのロール インスタンス (Web ロールと worker ロール) で使用できるサイズとオプションについて説明します。 また、これらのリソースの使用を計画するときに注意するデプロイメントに関する考慮事項も示します。 それぞれのサイズには、[サービス定義ファイル](cloud-services-model-and-package.md#csdef)に配置する ID があります。

Cloud Services は、Azure が提供する数種類のコンピューティング リソースの 1 つです。 Cloud Services の詳細については、 [こちら](cloud-services-choose-me.md) を参照してください。

> [!NOTE]
> 関連する Azure の制限については、 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)
> 
> 

## <a name="sizes-for-web-and-worker-role-instances"></a>Web ロールと worker ロールのインスタンスのサイズ
Azure では複数の標準的なサイズを選択できます。 これらのサイズに関する考慮事項は次のとおりです。

* D シリーズ VM は、より高いコンピューティング能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。 D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。 詳細については、Azure ブログの投稿「 [新しい D シリーズ仮想マシンのサイズ](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)」をご覧ください。
* オリジナルの D シリーズに続く Dv2 シリーズには、より強力な CPU が備わっています。 Dv2 シリーズの CPU は D シリーズの CPU よりも、およそ 35% 高速です。 これは最新世代の 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサに基づいており、Intel Turbo Boost Technology 2.0 を使用することで、最大 3.1 GHz まで実現できます。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。
* G シリーズ VM は、最も多くのメモリを提供し、Intel Xeon E5 V3 ファミリのプロセッサが搭載されたホスト上で実行されます。
* A シリーズ VM は多様なハードウェアの種類とプロセッサにデプロイできます。 デプロイされるハードウェアに関係なく、実行中のインスタンスに対して一貫したプロセッサ パフォーマンスを提供するため、ハードウェアに基づいてサイズが調整されます。 このサイズがデプロイされる物理ハードウェアを判断するには、仮想マシン内から仮想ハードウェアをクエリします。
* A0 サイズは、物理ハードウェアでオーバーサブスクライブされます。 この特定のサイズの場合のみ、他の顧客デプロイメントは、実行中のワークロードのパフォーマンスに影響することがあります。 下に、予想される基準として相対パフォーマンスを示していますが、約 15% の変動の可能性があります。

仮想マシンのサイズは価格に影響します。 また、サイズは仮想マシンの処理、メモリ、記憶容量にも影響します。 Storage のコストは、ストレージ アカウントで使用されるページに基づいて個別に計算されます。 詳細については、[Virtual Machines 料金の詳細](https://azure.microsoft.com/pricing/details/virtual-machines/)に関するページと「[Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。 

サイズを決定する際に役立つ考慮事項は次のとおりです。

* A8 ～ A11 と H シリーズのサイズは、 *コンピューティング集中型インスタンス*とも呼ばれます。 これらのサイズを実行するハードウェアは、ハイ パフォーマンス コンピューティング (HPC) クラスター アプリケーション、モデリング、シミュレーションなど、コンピューティング集中型およびネットワーク集中型アプリケーション用に設計および最適化されています。 A8 ～ A11 シリーズは Intel Xeon E5-2670 @ 2.6 GHZ を使用し、H シリーズは Intel Xeon E5-2667 v3 @ 3.2 GHz を使用します。 これらのサイズの使用に関する詳細な情報と考慮事項については、「 [H シリーズとコンピューティング集中型 A シリーズの VM について](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。 
* Dv2 シリーズ、D シリーズ、G シリーズは、より高速の CPU やより高いローカル ディスク パフォーマンスが必要なアプリケーション、またはメモリー要求がより高いアプリケーションに最適です。  多数のエンタープライズ レベルのアプリケーションに、強力な組み合わせで対処します。
* Azure データ センターの物理ホストの一部では、A5 ～ A11 などの大きな仮想マシンのサイズをサポートしていない場合があります。 その結果、既存の仮想マシンのサイズを新しいサイズに変更した場合、2013 年 4 月 16 日よりも前に作成された仮想ネットワーク内に新しい仮想マシンを作成した場合、または既存のクラウド サービスに新しい仮想マシンを追加した場合に、**"仮想マシン {マシン名} を構成できませんでした"** または **"仮想マシン {マシン名} を作成できませんでした"** というエラー メッセージが表示されることがあります。 各デプロイ シナリオの回避策については、サポート フォーラムで、[エラー: "仮想マシンを構成できませんでした"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) というトピックを参照してください。  
* お客様のサブスクリプションによっては、特定のサイズ ファミリにデプロイできるコア数が制限されることがあります。 コア クォータを増やすには、Azure サポートにお問い合わせください。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項
Azure SKU 間で計算 (CPU) パフォーマンスを比較する手段を提供するため、Azure Compute Unit (ACU) の概念が作成されています。 これは、パフォーマンス ニーズを満たす可能性が最も高い SKU を簡単に見つけるのに役立ちます。  現在、ACU は小さい (Standard_A1) VM を 100 として標準化されており、他のすべての SKU についてはその SKU が標準ベンチマークをそれよりどれくらい速く実行できるかが表されます。 

> [!IMPORTANT]
> ACU はガイドラインに過ぎません。  ワークロードの結果は異なる場合があります。 
> 
> 

<br>

| SKU ファミリ | ACU/コア |
| --- | --- |
| [Standard_A0](#a-series) |50 |
| [Standard_A1 ～ 4](#a-series) |100 |
| [Standard_A5 ～ 7](#a-series) |100 |
| [A8 ～ A11](#a-series) |225* |
| [D1 ～ 14](#d-series) |160 |
| [D1 ～ 15v2](#dv2-series) |210 ～ 250* |
| [G1 ～ 5](#g-series) |180 ～ 240* |
| [H](#h-series) |290 ～ 300* |

* が付いている ACU は、Intel® Turbo テクノロジを使用して CPU 周波数を上げ、パフォーマンスを増強します。  増強量は、VM のサイズ、ワークロード、および同じホストで実行されている他のワークロードによって変化します。

## <a name="size-tables"></a>サイズ一覧表
次の表に、サイズとそのサイズで提供される容量を示します。

* ストレージ容量は GiB (1024^3 バイト) 単位で示されています。 GB (1000^3 バイト) 単位のディスクと GiB (1024^3 バイト) 単位のディスクを比較する場合は、GiB 単位の方が容量の数値が小さく見えることに注意してください。 たとえば、1023 GiB = 1098.4 GB です。
* ディスク スループットの測定単位は、1 秒あたりの入力/出力操作数 (IOPS) および MBps です (MBps = 10^6 バイト/秒)。
* データ ディスクは、キャッシュを有効にしたモードでも無効化したモードでも動作します。 キャッシュを有効にしたデータ ディスクの操作では、ホスト キャッシュ モードは **ReadOnly** または **ReadWrite** に設定されています。  キャッシュを無効にしたデータ ディスクの操作では、ホスト キャッシュ モードは **None**に設定されています。
* 最大ネットワーク帯域幅は、VM の種類ごとに割り当てられた最大集約帯域です。 最大帯域幅は、適切なネットワーク容量を確保するための適切な VM の種類を選択するためのガイダンスを提供します。 低、中、高、非常に高の順でスループットが増加します。 実際のネットワークのパフォーマンスは、ネットワークおよびアプリケーションの負荷、アプリケーションのネットワーク設定など、多くの要因に左右されます。

## <a name="a-series"></a>A シリーズ
| サイズ | CPU コア数 | メモリ: GiB | ローカル HDD: GiB | 最大データ ディスク数 | 最大データ ディスク スループット: IOPS | 最大 NIC/ネットワーク帯域幅 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0.768 |20 |1 |1 x 500 |1/低 |
| Standard_A1 |1 |1.75 |70 |2 |2 x 500 |1/中 |
| Standard_A2 |2 |3.5 GB |135 |4 |4 x 500 |1/中 |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2/高 |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4/高 |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |1/中 |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2/高 |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4/高 |

## <a name="a-series---compute-intensive-instances"></a>A シリーズ - コンピューティング集中型インスタンス
これらのサイズの使用に関する情報と考慮事項については、「 [H シリーズとコンピューティング集中型 A シリーズの VM について](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

| サイズ | CPU コア数 | メモリ: GiB | ローカル HDD: GiB | 最大データ ディスク数 | 最大データ ディスク スループット: IOPS | 最大 NIC/ネットワーク帯域幅 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16 x 500 |2/高 |
| Standard_A9* |16 |112 |382 |16 |16 x 500 |4/非常に高 |
| Standard_A10 |8 |56 |382 |16 |16 x 500 |2/高 |
| Standard_A11 |16 |112 |382 |16 |16 x 500 |4/非常に高 |

* RDMA 対応

## <a name="d-series"></a>D シリーズ
| サイズ | CPU コア数 | メモリ: GiB | ローカル SSD: GiB | 最大データ ディスク数 | 最大データ ディスク スループット: IOPS | 最大 NIC/ネットワーク帯域幅 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1 |1 |3.5 |50 |2 |2 x 500 |1/中 |
| Standard_D2 |2 |7 |100 |4 |4 x 500 |2/高 |
| Standard_D3 |4 |14 |200 |8 |8 x 500 |4/高 |
| Standard_D4 |8 |28 |400 |16 |16 x 500 |8/高 |
| Standard_D11 |2 |14 |100 |4 |4 x 500 |2/高 |
| Standard_D12 |4 |28 |200 |8 |8 x 500 |4/高 |
| Standard_D13 |8 |56 |400 |16 |16 x 500 |8/高 |
| Standard_D14 |16 |112 |800 |32 |32 x 500 |8/非常に高 |

## <a name="dv2-series"></a>Dv2 シリーズ
| サイズ | CPU コア数 | メモリ: GiB | ローカル SSD: GiB | 最大データ ディスク数 | 最大データ ディスク スループット: IOPS | 最大 NIC/ネットワーク帯域幅 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3.5 |50 |2 |2 x 500 |1/中 |
| Standard_D2_v2 |2 |7 |100 |4 |4 x 500 |2/高 |
| Standard_D3_v2 |4 |14 |200 |8 |8 x 500 |4/高 |
| Standard_D4_v2 |8 |28 |400 |16 |16 x 500 |8/高 |
| Standard_D5_v2 |16 |56 |800 |32 |32 x 500 |8/極めて高 |
| Standard_D11_v2 |2 |14 |100 |4 |4 x 500 |2/高 |
| Standard_D12_v2 |4 |28 |200 |8 |8 x 500 |4/高 |
| Standard_D13_v2 |8 |56 |400 |16 |16 x 500 |8/高 |
| Standard_D14_v2 |16 |112 |800 |32 |32 x 500 |8/極めて高 |
| Standard_D15_v2 |20 |140 |1,000 |40 |40 x 500 |8/極めて高 |

## <a name="g-series"></a>G シリーズ
| サイズ | CPU コア数 | メモリ: GiB | ローカル SSD: GiB | 最大データ ディスク数 | 最大ディスク スループット: IOPS | 最大 NIC/ネットワーク帯域幅 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |28 |384 |4 |4 x 500 |1/高 |
| Standard_G2 |4 |56 |768 |8 |8 x 500 |2/高 |
| Standard_G3 |8 |112 |1,536 |16 |16 x 500 |4/非常に高 |
| Standard_G4 |16 |224 |3,072 |32 |32 x 500 |8/極めて高 |
| Standard_G5 |32 |448 |6,144 |64 |64 x 500 |8/極めて高 |

## <a name="h-series"></a>H シリーズ
Azure H シリーズの仮想マシンは、分子モデリングや流体力学などのハイエンド コンピューティングのニーズを目的とした、次世代型のハイ パフォーマンス コンピューティング VM です。 これらの 8 および 16 コアの VM は、DDR4 メモリとローカル SSD ベースの記憶域を備えた Intel Haswell E5-2667 V3 プロセッサ テクノロジをベースに構築されています。 

H シリーズのラインナップは強力な CPU パワーに加えて、FDR InfiniBand を使用した低待機時間 RDMA ネットワークのためのさまざまなオプションと、複数のメモリ構成を備えており、メモリ集中型のコンピューティング要件にも対応しています。

| サイズ | CPU コア数 | メモリ: GiB | ローカル SSD: GiB | 最大データ ディスク数 | 最大ディスク スループット: IOPS | 最大 NIC/ネットワーク帯域幅 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1,000 |16 |16 x 500 |8/高 |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |8/非常に高 |
| Standard_H8m |8 |112 |1,000 |16 |16 x 500 |8/高 |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |8/非常に高 |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |8/非常に高 |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |8/非常に高 |

* RDMA 対応

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>注意: Standard A0 ～ A4 は CLI および PowerShell を使用します
クラシック デプロイ モデルでは、一部の VM サイズが CLI と PowerShell で若干異なります。

* Standard_A0: ExtraSmall 
* Standard_A1: Small
* Standard_A2: Medium
* Standard_A3: Large
* Standard_A4: ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Cloud Services のサイズの構成
ロール インスタンスの仮想マシンのサイズを、 [サービス定義ファイル](cloud-services-model-and-package.md#csdef)で記述するサービス モデルの一部として指定できます。 ロールのサイズによって、CPU コアの数、メモリ容量、および実行中のインスタンスに割り当てられるローカル ファイル システムのサイズが決まります。 ロールのサイズは、アプリケーションのリソース要件に基づいて選択します。

Web ロール インスタンスのロール サイズを [Standard_D2](#general-purpose-d) に設定する例を次に示します。

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="next-steps"></a>次のステップ
* [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)について学習してください。
* 高性能コンピューティング (HPC) などのワークロードに対する [H シリーズとコンピューティング集中型 A シリーズ VM](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) について学習してください。




<!--HONumber=Nov16_HO3-->


