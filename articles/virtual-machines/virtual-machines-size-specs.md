<properties
 pageTitle="仮想マシン サイズ"
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
 ms.date="09/02/2015"
 ms.author="cynthn"/>

# 仮想マシンのサイズ

## 概要

この記事では、アプリとワークロードの実行に使用できる仮想マシン ベースのコンピューティング リソースの利用可能なサイズとオプションについて説明します。また、これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項も示します。

Azure Virtual Machines と Cloud Services は、Azure が提供する数種類のコンピューティング リソースの中の 2 つです。詳細については、「[Azure が提供するコンピューティング ホスティング オプション](http://go.microsoft.com/fwlink/p/?LinkID=311926)」を参照してください。

>[AZURE.NOTE]関連する Azure の制限については、[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)に関するページを参照してください。

## 計画に関する考慮事項

仮想マシンは、Basic と Standard の 2 つのレベルで利用できます。どちらの種類でもサイズを選択できますが、Basic レベルでは、負荷分散や自動スケールなど、Standard レベルで利用できる機能の一部が提供されていません。Standard レベルのサイズには、A、D、DS、G、GS などのさまざまなシリーズがあります。これらのサイズに関する考慮事項は次のとおりです。

*   D シリーズ VM は、より高いコンピューティング能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。詳細については、Azure ブログの[新しい D シリーズ仮想マシンのサイズ](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)に関する記事を参照してください。

*   オリジナルの D シリーズに続く Dv2 シリーズには、より強力な CPU が備わっています。Dv2 シリーズの CPU は D シリーズの CPU よりも、およそ 35% 高速です。これは最新世代の 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサに基づいており、Intel Turbo Boost Technology 2.0 を使用することで、最大 3.2 GHz まで実現できます。Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。詳細については、Azure ブログの投稿「[New Dv2-Series Virtual Machine Sizes (新しい Dv2 シリーズ仮想マシンのサイズ)](http://azure.microsoft.com/blog/2015/10/01/new-dv2-series-virtual-machine-sizes/)」をご覧ください。

*   G シリーズ VM は、最大のサイズと最高のパフォーマンスを提供し、Intel Xeon E5 V3 ファミリのプロセッサが搭載されたホスト上で実行されます。

*   DS シリーズと GS シリーズの VM では、Premium Storage を使用できます。これは、高負荷の I/O ワークロードのための、高パフォーマンスで待機時間の少ない記憶域を提供します。これらの VM では、仮想マシンのディスクをホストするためのソリッド ステート ドライブ (SSD) が使用されており、ローカル SSD ディスク キャッシュも提供されます。Premium Storage は特定のリージョンで使用できます。詳細については、[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage-premium-storage-preview-portal.md)に関するページをご覧ください。

仮想マシンのサイズは価格に影響します。また、サイズは仮想マシンの処理、メモリ、記憶容量にも影響します。ストレージのコストは、ストレージ アカウントで使用されるページに基づいて個別に計算されます。詳細については、[Virtual Machines 料金の詳細](http://azure.microsoft.com/pricing/details/virtual-machines/)に関するページと「[Azure Storage 料金](http://azure.microsoft.com/pricing/details/storage/)」を参照してください。VM のストレージの詳細については、[仮想マシン用のディスクと VHD](virtual-machines-disks-vhds.md)に関するページをご覧ください。

サイズを決定する際に役立つ考慮事項は次のとおりです。

*   A0\\Basic\_A0 サイズを利用できるのは、Azure SDK Version 1.3 以降を使用している場合のみです。  

*   運用環境のワークロードで推奨される最小サイズは A1\\Basic\_A1 です。

*   SQL Server Enterprise Edition を使用する場合は、4 基または 8 基の CPU コアを搭載した仮想マシンを選択します。

*   Azure データ センターの物理ホストの一部では、A5 ～ A11 などの大きな仮想マシンのサイズをサポートしていない場合があります。その結果、既存の仮想マシンのサイズを新しいサイズに変更した場合、2013 年 4 月 16 日よりも前に作成された仮想ネットワーク内に新しい仮想マシンを作成した場合、または既存のクラウド サービスに新しい仮想マシンを追加した場合に、"**仮想マシン <machine name> を構成できませんでした**" または "**仮想マシン <machine name> を作成できませんでした**" というエラー メッセージが表示されることがあります。各デプロイ シナリオの回避策については、サポート フォーラムで、[エラー: "仮想マシンを構成できませんでした"](https://social.msdn.microsoft.com/Forums/ja-JP/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) というトピックを参照してください。

*   A8/A10 と A9/A11 の仮想マシンのサイズは、同じ容量です。A8 と A9 の仮想マシンのインスタンスには、仮想マシン間の高速通信用のリモート ダイレクト メモリ アクセス (RDMA) ネットワークに接続された追加のネットワーク アダプターが含まれています。A8 と A9 のインスタンスは、実行中にノード間で持続的で待ち時間の少ない通信を必要とする高パフォーマンス コンピューティング アプリケーション (Message Passing Interface (MPI) を使用するアプリケーションなど) 向けに設計されています。A10 と A11 の仮想マシン インスタンスには、追加のネットワーク アダプターが含まれていません。A10 と A11 のインスタンスは、ノード間で一定した待ち時間の少ない通信を必要としない高パフォーマンス コンピューティング アプリケーション (パラメトリックまたは Embarrassingly Parallel アプリケーションとも呼ばれる) 向けに設計されています。

*   Dv2 シリーズと D シリーズは、より高速の CPU やより高いローカル ディスク パフォーマンスが必要なアプリケーション、またはメモリー要求がより高いアプリケーションに最適です。多数のエンタープライズ レベルのアプリケーションに、強力な組み合わせで対処します。

## 一般的な制限事項

次の表は、サービス管理デプロイ モデルを使用して作成された仮想マシンについて、仮想マシンのサイズに関係なく適用される制限事項を示したものです。

[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

次の表は、リソース マネージャー デプロイ モデルを使用して作成された仮想マシンについて、仮想マシンのサイズに関係なく適用される制限事項を示したものです。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## サイズ一覧表

次の表に、サイズとそのサイズで提供される容量を示します。

>[AZURE.NOTE]ストレージの容量は、GB の測定単位として 1024^3 バイトを使用して表されます。これはギガバイト、または 2 進数定義とも呼ばれます。使用する進法が異なるサイズを比較する場合、2 進数のサイズが 10 進数よりも小さく見える場合でも、1024^3 は 1000^3 より大きいため、特定のサイズ (1 GB など) では、2 進法の方が 10 進法よりも提供される容量が大きいことに注意してください。

### Basic レベル

|サイズ - Azure ポータル\\コマンドレットと API|CPU コア数|メモリ|最大ディスク サイズ – 仮想マシン|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 300)|
|---|---|---|---|---|---|
|A0\\Basic\_A0|1|768 MB|<p>OS = 1,023 GB</p><p>一時ディスク = 20 GB</p>|1|1 x 300|
|A1\\Basic\_A1|1|1\.75 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 40 GB</p>|2|2 x 300|
|A2\\Basic\_A2|2|3\.5 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 60 GB</p>|4|4 x 300|
|A3\\Basic\_A3|4|7 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 120 GB</p>|8|8 x 300|
|A4\\Basic\_A4|8|14 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 240 GB</p>|16|16 x 300|

### Standard レベル: A シリーズ

|サイズ - Azure ポータル\\コマンドレットと API|CPU コア数|メモリ|最大ディスク サイズ – 仮想マシン|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|
|A0\\極小|1|768 MB|<p>OS = 1,023 GB</p><p>一時ディスク = 20 GB</p>|1|1 x 500|
|A1\\小|1|1\.75 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 70 GB</p>|2|2 x 500|
|A2\\中|2|3\.5 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 135 GB</p>|4|4 x 500|
|A3\\大|4|7 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 285 GB</p>|8|8 x 500|
|A4\\極大|8|14 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 605 GB</p>|16|16 x 500|
|A5\\同|2|14 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 135 GB</p>|4|4 x 500|
|A6\\同|4|28 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 285 GB</p>|8|8 x 500|
|A7\\同|8|56 GB|<p>OS = 1,023 GB</p><p>一時ディスク = 605 GB</p>|16|16 x 500|
|A8\\同|8|56 GB|<p><p>OS = 1,023 GB</p><p>一時ディスク = 382 GB</p><blockquote><p>注: このサイズの使用に関する情報と考慮事項については、「<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンスについて</a>」をご覧ください。</p></blockquote>|16|16 x 500|
|A9\\同|16|112 GB|<p><p>OS = 1,023 GB</p><p>一時ディスク = 382 GB</p><blockquote><p>注: このサイズの使用に関する情報と考慮事項については、「<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンスについて</a>」をご覧ください。</p></blockquote>|16|16 x 500|
|A10\\同|8|56 GB|<p><p>OS = 1,023 GB</p><p>一時ディスク = 382 GB</p><blockquote><p>注: このサイズの使用に関する情報と考慮事項については、「<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンスについて</a>」をご覧ください。</p></blockquote>|16|16 x 500|
|A11\\同|16|112 GB|<p><p>OS = 1,023 GB</p><p>一時ディスク = 382 GB</p><blockquote><p>注: このサイズの使用に関する情報と考慮事項については、「<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンスについて</a>」をご覧ください。</p></blockquote>|16|16 x 500|

### Standard レベル: D シリーズ

|サイズ - Azure ポータル\\コマンドレットと API|CPU コア数|メモリ|最大ディスク サイズ – 仮想マシン|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|
|Standard\_D1\\同|1|3\.5 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 50 GB</p>|2|2 x 500|
|Standard\_D2\\同|2|7 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 100 GB</p>|4|4 x 500|
|Standard\_D3\\同|4|14 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 200 GB</p>|8|8 x 500|
|Standard\_D4\\同|8|28 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 400 GB</p>|16|16 x 500|
|Standard\_D11\\同|2|14 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 100 GB</p>|4|4 x 500|
|Standard\_D12\\同|4|28 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 200 GB</p>|8|8 x 500|
|Standard\_D13\\同|8|56 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 400 GB</p>|16|16 x 500|
|Standard\_D14\\同|16|112 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 800 GB</p>|32|32 x 500|

### Standard レベル: Dv2 シリーズ

|サイズ - Azure ポータル\\コマンドレットと API|CPU コア数|メモリ|最大ディスク サイズ – 仮想マシン|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|
|Standard\_D1\_v2\\同|1|3\.5 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 50 GB</p>|2|2 x 500|
|Standard\_D2\_v2\\同|2|7 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 100 GB</p>|4|4 x 500|
|Standard\_D3\_v2\\同|4|14 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 200 GB</p>|8|8 x 500|
|Standard\_D4\_v2\\同|8|28 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 400 GB</p>|16|16 x 500|
|Standard\_D5\_v2\\同|16|56 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 800 GB</p>|32|32 x 500|
|Standard\_D11\_v2\\同|2|14 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 100 GB</p>|4|4 x 500|
|Standard\_D12\_v2\\同|4|28 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 200 GB</p>|8|8 x 500|
|Standard\_D13\_v2\\同|8|56 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 400 GB</p>|16|16 x 500|
|Standard\_D14\_v2\\同|16|112 GB|<p>OS = 1,023 GB</p><p>一時的 (SSD) = 800 GB</p>|32|32 x 500|

### Standard レベル: DS シリーズ*

|サイズ - Azure ポータル\\コマンドレットと API|CPU コア数|メモリ|最大ディスク サイズ – 仮想マシン|最大データ ディスク数 (各ディスク 1,023 GB)|キャッシュ サイズ (GB)|最大ディスク IOPS および帯域幅|
|---|---|---|---|---|---|---|
|Standard\_DS1\\同|1|3\.5|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 7 GB</p>|2|43|<p>3,200</p><p>32 MB/秒</p>|
|Standard\_DS2\\同|2|7|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 14 GB</p>|4|86|<p>6,400</p><p>64 MB/秒</p>|
|Standard\_DS3\\同|4|14|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 28 GB</p>|8|172|<p>12,800</p><p>128 MB/秒</p>|
|Standard\_DS4\\同|8|28|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 56 GB</p>|16|344|<p>25,600</p><p>256 MB/秒</p>|
|Standard\_DS11\\同|2|14|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 28 GB</p>|4|72|<p>6,400</p><p>64 MB/秒</p>|
|Standard\_DS12\\同|4|28|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 56 GB</p>|8|144|<p>12,800</p><p>128 MB/秒</p>|
|Standard\_DS13\\同|8|56|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 112 GB</p>|16|288|<p>25,600</p><p>256 MB/秒</p>|
|Standard\_DS14\\同|16|112|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 224 GB</p>|32|576|<p>50,000</p><p>512 MB/秒</p>|

**DS シリーズ VM で可能な 1 秒あたりの入力/出力操作 (IOPS) とスループット (帯域幅) の最大値は、ディスクのサイズによる影響を受けます。詳細については、[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage-premium-storage-preview-portal.md)に関するページをご覧ください。

### Standard レベル: G シリーズ

|サイズ – Azure ポータル\\コマンドレットと API|CPU コア数|メモリ|最大ディスク サイズ – 仮想マシン|最大データ ディスク数 (各ディスク 1,023 GB)|最大IOPS (各ディスク 500)|
|---|---|---|---|---|---|
|Standard\_G1\\同|2|28 GB|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 384 GB</p>|4|4 x 500|
|Standard\_G2\\同|4|56 GB|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 768 GB</p>|8|8 x 500|
|Standard\_G3\\同|8|112 GB|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 1,536 GB</p>|16|16 x 500|
|Standard\_G4\\同|16|224 GB|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 3,072 GB</p>|32|32 x 500|
|Standard\_G5\\同|32|448 GB|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 6,144 GB</p>|64|<p>64 x 500</p>|

### Standard シリーズ: GS シリーズ

|サイズ - Azure ポータル\\コマンドレットと API|CPU コア数|メモリ|最大ディスク サイズ – 仮想マシン|最大データ ディスク数 (各ディスク 1,023 GB)|キャッシュ サイズ (GB)|最大ディスク IOPS および帯域幅|
|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 56 GB</p>|4|264|<p>5,000</p><p>125 MB/秒</p>|
|Standard\_GS2|4|56|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 112 GB</p>|8|528|<p>10,000</p><p>250 MB/秒</p>|
|Standard\_GS3|8|112|<p>OS = 1,023 GB</p><p>ローカル SSD ディスク = 224 GB</p>|16|1056|<p>20,000</p><p>500 MB/秒</p>|
|Standard\_GS4|16|224|<p>OS = 1023 GB</p><p>ローカル SSD ディスク = 448 GB</p>|32|2112|<p>40,000</p><p>1,000 MB/秒</p>|
|Standard\_GS5|32|448|<p>OS = 1023 GB</p><p>ローカル SSD ディスク = 896 GB</p>|64|4224|<p>80,000</p><p>2,000 MB/秒</p>|


### 関連項目

[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[A8、A9、A10、A11 コンピューティング集中型インスタンスについて](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=Oct15_HO1-->