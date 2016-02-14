<properties
 pageTitle="仮想マシンのサイズ | Microsoft Azure"
 description="仮想マシンのさまざまなサイズとその容量を一覧を示します。"
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="01/05/2016"
 ms.author="cynthn"/>

# 仮想マシンのサイズ

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 概要

この記事では、アプリとワークロードの実行に使用できる仮想マシン ベースのコンピューティング リソースの利用可能なサイズとオプションについて説明します。また、これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項も示します。さまざまなサイズの価格について詳しくは、「[Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/)」を参照してください。

Azure VM の一般的な制限事項については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

標準のサイズは、複数のシリーズ (A、D、DS、G、および GS) で構成されます。これらのサイズに関する考慮事項は次のとおりです。

*   D シリーズ VM は、より高いコンピューティング能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。詳細については、Azure ブログの投稿「[新しい D シリーズ仮想マシンのサイズ](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)」をご覧ください。

*   オリジナルの D シリーズに続く Dv2 シリーズには、より強力な CPU が備わっています。Dv2 シリーズの CPU は D シリーズの CPU よりも、およそ 35% 高速です。これは最新世代の 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサに基づいており、Intel Turbo Boost Technology 2.0 を使用することで、最大 3.2 GHz まで実現できます。Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

*   G シリーズ VM は、最も多くのメモリを提供し、Intel Xeon E5 V3 ファミリのプロセッサが搭載されたホスト上で実行されます。

*   DS シリーズと GS シリーズの VM では、Premium Storage を使用できます。これは、高負荷の I/O ワークロードのための、高パフォーマンスで待機時間の少ない記憶域を提供します。これらの VM では、仮想マシンのディスクをホストするためのソリッド ステート ドライブ (SSD) が使用されており、ローカル SSD ディスク キャッシュも提供されます。Premium Storage は特定のリージョンで使用できます。詳細については、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage-premium-storage-preview-portal.md)」を参照してください。

仮想マシンのサイズは価格に影響します。また、サイズは仮想マシンの処理、メモリ、記憶容量にも影響します。Storage のコストは、ストレージ アカウントで使用されるページに基づいて個別に計算されます。詳細については、[Virtual Machines 料金の詳細](https://azure.microsoft.com/pricing/details/virtual-machines/)に関するページと「[Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。VM のストレージの詳細については、「[仮想マシン用のディスクと VHD について](virtual-machines-disks-vhds.md)」を参照してください。

サイズを決定する際に役立つ考慮事項は次のとおりです。


* A8 ～ A11 のサイズは、*コンピューティング集中型インスタンス*とも呼ばれます。これらのサイズを実行するハードウェアは、ハイ パフォーマンス コンピューティング (HPC) クラスター アプリケーション、モデリング、シミュレーションなど、コンピューティング集中型およびネットワーク集中型アプリケーション用に設計および最適化されています。このサイズの使用に関する詳細な情報と考慮事項については、「[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](virtual-machines-a8-a9-a10-a11-specs.md)」をご覧ください。


*	Dv2 シリーズ、D シリーズ、G シリーズ、および対応する DS/GS は、より高速の CPU やより高いローカル ディスク パフォーマンスが必要なアプリケーション、またはメモリー要求がより高いアプリケーションに最適です。多数のエンタープライズ レベルのアプリケーションに、強力な組み合わせで対処します。

*   Azure データ センターの物理ホストの一部では、A5 ～ A11 などの大きな仮想マシンのサイズをサポートしていない場合があります。その結果、既存の仮想マシンのサイズを新しいサイズに変更した場合、2013 年 4 月 16 日よりも前に作成された仮想ネットワーク内に新しい仮想マシンを作成した場合、または既存のクラウド サービスに新しい仮想マシンを追加した場合に、"**仮想マシン <machine name> を構成できませんでした**" または "**仮想マシン <machine name> を作成できませんでした**" というエラー メッセージが表示されることがあります。各デプロイ シナリオの回避策については、サポート フォーラムで、[エラー: "仮想マシンを構成できませんでした"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) というトピックを参照してください。


## パフォーマンスに関する考慮事項

Azure SKU 間で計算 (CPU) パフォーマンスを比較する手段を提供するため、Azure Compute Unit (ACU) の概念が作成されています。これは、パフォーマンス ニーズを満たす可能性が最も高い SKU を簡単に見つけるのに役立ちます。現在、ACU は小さい (Standard\_A1) VM を 100 として標準化されており、他のすべての SKU についてはその SKU が標準ベンチマークをそれよりどれくらい速く実行できるかが表されます。

>[AZURE.IMPORTANT] ACU はガイドラインに過ぎません。ワークロードの結果は異なる場合があります。

<br>

|SKU ファミリ |ACU/コア |
|---|---|
|[Standard\_A0 (極小)](#standard-tier-a-series) |50 |
|[Standard\_A1 ～ 4 (小～大)](#standard-tier-a-series) |100 |
|[Standard\_A5 ～ 7](#standard-tier-a-series) |100 |
|[A8 ～ A11](#standard-tier-a-series) |225 *| |[D1 ～ 14](#standard-tier-d-series) |160 | |[D1 ～ 14v2](#standard-tier-dv2-series) |210 ～ 250 *| |[DS1 ～ 14](#standard-tier-ds-series) |160 | |[G1 ～ 5](#standard-tier-g-series) |180 ～ 240 *| |[GS1 ～ 5](#standard-tier-gs-series) |180 ～ 240 *|


* が付いている ACU は、Intel® Turbo テクノロジを使用して CPU 周波数を上げ、パフォーマンスを増強します。増強量は、VM のサイズ、ワークロード、および同じホストで実行されている他のワークロードによって変化します。



## サイズ一覧表

次の表に、サイズとそのサイズで提供される容量を示します。

>[AZURE.NOTE] Storage の容量は、GB の測定単位として 1024^3 バイトを使用して表されます。これはギガバイト、または 2 進数定義とも呼ばれます。使用する進法が異なるサイズを比較する場合、2 進数のサイズが 10 進数よりも小さく見える場合でも、1024^3 は 1000^3 より大きいため、特定のサイズ (1 GB など) では、2 進法の方が 10 進法よりも提供される容量が大きいことに注意してください。

<br>



## Standard レベル: A シリーズ

クラシック デプロイ モデルでは、一部の VM サイズが Powershell および CLI で若干異なります。

* Standard\_A0: ExtraSmall 
* Standard\_A1: Small
* Standard\_A2: Medium
* Standard\_A3: Large
* Standard\_A4: ExtraLarge

<br>

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_A0\\ExtraSmall |1|768 MB|1| 一時ディスク = 20 GB |1|1 x 500|
|Standard\_A1\\Small|1|1\.75 GB|1|一時ディスク = 70 GB |2|2 x 500|
|Standard\_A2\\Medium|2|3\.5 GB|1|一時ディスク = 135 GB |4|4 x 500|
|Standard\_A3\\Large|4|7 GB|2|一時ディスク = 285 GB |8|8 x 500|
|Standard\_A4\\ExtraLarge|8|14 GB|4|一時ディスク = 605 GB |16|16 x 500|
|Standard\_A5|2|14 GB|1|一時ディスク = 135 GB |4|4 x 500|
|Standard\_A6|4|28 GB|2|一時ディスク = 285 GB |8|8 x 500|
|Standard\_A7|8|56 GB|4|一時ディスク = 605 GB |16|16 x 500|


## Standard レベル: A シリーズ - コンピューティング集中型インスタンス

注: このサイズの使用に関する詳細な情報と考慮事項については、「[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](virtual-machines-a8-a9-a10-a11-specs.md)」をご覧ください。

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_A8|8|56 GB|2| 一時ディスク = 382 GB |16|16 x 500|
|Standard\_A9|16|112 GB|4| 一時ディスク = 382 GB |16|16 x 500|
|Standard\_A10|8|56 GB|2| 一時ディスク = 382 GB |16|16 x 500|
|Standard\_A11|16|112 GB|4| 一時ディスク = 382 GB |16|16 x 500|

## Standard レベル: D シリーズ

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_D1 |1|3\.5 GB|1|一時的 (SSD) = 50 GB |2|2 x 500|
|Standard\_D2 |2|7 GB|2|一時的 (SSD) = 100 GB |4|4 x 500|
|Standard\_D3 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D4 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D11 |2|14 GB|2|一時的 (SSD) = 100 GB |4|4 x 500|
|Standard\_D12 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D13 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D14 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|

## Standard レベル: Dv2 シリーズ

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_D1\_v2 |1|3\.5 GB|1|一時的 (SSD) = 50 GB |2|2 x 500|
|Standard\_D2\_v2 |2|7 GB|2|一時的 (SSD) = 100 GB |4|4 x 500|
|Standard\_D3\_v2 |4|14 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D4\_v2 |8|28 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D5\_v2 |16|56 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|
|Standard\_D11\_v2 |2|14 GB|2|一時的 (SSD) = 100 GB |4|4 x 500|
|Standard\_D12\_v2 |4|28 GB|4|一時的 (SSD) = 200 GB |8|8 x 500|
|Standard\_D13\_v2 |8|56 GB|8|一時的 (SSD) = 400 GB |16|16 x 500|
|Standard\_D14\_v2 |16|112 GB|8|一時的 (SSD) = 800 GB |32|32 x 500|

## Standard レベル: DS シリーズ*

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|キャッシュ サイズ (GB)|最大ディスク IOPS および帯域幅|
|---|---|---|---|---|---|---|---|
|Standard\_DS1 |1|3\.5|1|ローカル SSD ディスク = 7 GB |2|43| 3,200 32 MB/秒 |
|Standard\_DS2 |2|7|2|ローカル SSD ディスク = 14 GB |4|86| 6,400 64 MB/秒 |
|Standard\_DS3 |4|14|4|ローカル SSD ディスク = 28 GB |8|172| 12,800 128 MB/秒 |
|Standard\_DS4 |8|28|8|ローカル SSD ディスク = 56 GB |16|344| 25,600 256 MB/秒 |
|Standard\_DS11 |2|14|2|ローカル SSD ディスク = 28 GB |4|72| 6,400 64 MB/秒 |
|Standard\_DS12 |4|28|4|ローカル SSD ディスク = 56 GB |8|144| 12,800 128 MB/秒 |
|Standard\_DS13 |8|56|8|ローカル SSD ディスク = 112 GB |16|288| 25,600 256 MB/秒 |
|Standard\_DS14 |16|112|8|ローカル SSD ディスク = 224 GB |32|576| 50,000 512 MB/秒 |

**DS シリーズ VM で可能な 1 秒あたりの入力/出力操作 (IOPS) とスループット (帯域幅) の最大値は、ディスクのサイズによる影響を受けます。詳細については、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal.md)」を参照してください。

## Standard レベル: G シリーズ

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|---|
|Standard\_G1 |2|28 GB|1|ローカル SSD ディスク = 384 GB |4|4 x 500|
|Standard\_G2 |4|56 GB|2|ローカル SSD ディスク = 768 GB |8|8 x 500|
|Standard\_G3 |8|112 GB|4|ローカル SSD ディスク = 1,536 GB |16|16 x 500|
|Standard\_G4 |16|224 GB|8|ローカル SSD ディスク = 3,072 GB |32|32 x 500|
|Standard\_G5 |32|448 GB|8|ローカル SSD ディスク = 6,144 GB |64| 64 x 500 |

## Standard シリーズ: GS シリーズ

|サイズ |CPU コア数|メモリ|NIC (最大)|最大ディスク サイズ|最大データ ディスク数 (各ディスク 1,023 GB)|キャッシュ サイズ (GB)|最大ディスク IOPS および帯域幅|
|---|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|1|ローカル SSD ディスク = 56 GB |4|264| 5,000 125 MB/秒 |
|Standard\_GS2|4|56|2|ローカル SSD ディスク = 112 GB |8|528| 10,000 250 MB/秒 |
|Standard\_GS3|8|112|4|ローカル SSD ディスク = 224 GB |16|1056| 20,000 500 MB/秒 |
|Standard\_GS4|16|224|8|ローカル SSD ディスク = 448 GB |32|2112| 40,000 1,000 MB/秒 |
|Standard\_GS5|32|448|8|ローカル SSD ディスク = 896 GB |64|4224| 80,000 2,000 MB/秒 |


### 関連項目

[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=AcomDC_0204_2016-->