<properties
	pageTitle="Site Recovery Capacity Planner | Microsoft Azure" 
	description="Azure Site Recovery は、オンプレミスに配置されている仮想マシンと物理サーバーの Azure またはセカンダリ オンプレミス サイトへのレプリケーション、フェールオーバー、および復旧を調整します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="11/27/2015" 
	ms.author="pratshar"/>

# Site Recovery Capacity Planner

このドキュメントでは、Microsoft ASR キャパシティ プランニング ツールについて説明しています。このツールは、Site Recovery のシームレスな操作のためにプロビジョニングする必要があるリソースのガイダンスを提供します。キャパシティ プランニング ツールは、ソース環境 (ワークロード)、帯域幅要件、ターゲット側のリソース要件 (VM、ストレージ)、およびソース側 (SC VMM、構成サーバー、プロセス サーバーなど) で必要な任意の追加のサーバー リソースを分析するために使用できます。[Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) ツールをダウンロードします。
 
Capacity Planner は次の 2 つのモードで使用できます。
 
- **クイック プランニング**: VM、ディスク、ストレージ、変更率の数値の平均に基づいて、ネットワークとサーバーの予測を取得します。 
- **詳細なプランニング**: VM レベルで各ワークロードの詳細を提供します。VM レベルで互換性を分析し、ネットワークおよびサーバーの予測も取得します。
     
このドキュメントでは、ユーザーが Azure Site Recovery に習熟していることを前提としています。「[Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

## Getting Started (概要)
###前提条件
使用する Planner のモードによっては、変更を続行するために詳細情報が必要になります。VM、VM あたりのディスク、ディスクあたりの Storage などのインフラストラクチャの詳細の他に、いくつかの詳細が必要になります。キー 1 は、1 日の変更率または解約率です。ソース環境が HYPER-V の場合、[HYPER-V キャパシティ プランニング ツール](https://www.microsoft.com/en-in/download/details.aspx?id=39057)を使用して解約率を取得します。[Hyper-V キャパシティ プランニング ツール](site-recovery-capacity-planning-for-hyper-v-replication.md)の使用方法に関する説明をお読みください。VMWare の場合は、[VMware キャパシティ プランニング アプライアンス ツール](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)を使用します。

##クイック プランナー
1.	**ASR Capacity Planner.xlsm** ファイルを開きます。これには、マクロを実行する必要があります。そのため **[編集を有効にする]** と **[コンテンツの有効化]** が表示されたら、 
1.	リスト ボックスから **[クイック プランナー]** を選択します。これにより**「Capacity Planner」**というタイトルの別のワークシートが開きます。

	![Getting Started (概要)](./media/site-recovery-capacity-planner/getting-started.png)

1.	「Capacity Planner」ワークシートに、必要に応じて入力します。丸で囲まれたフィールドはすべて、必須の入力フィールドです。
	1.	リスト ボックスを使用してシナリオを選択します。リスト ボックスでは、ソース環境が使用するシナリオを「Hyper-V から Azure」と「VMware/物理 から Azure」に切り替えることができます。
	1. 	日次データの平均変更率を測定する必要があります。Hyper-V 環境では、Hyper-V Capacity Planner ツールが使用できます。VMWare の場合は、VMWare Capacity Planner ツールが使用できます。任意のピークをキャプチャして平均するために、通常はこのツールを 1 週間以上実行することをお勧めします。 
	1. 	圧縮 – これは、VMware/物理 から Azure のシナリオで ASR によって提供される圧縮です。Hyper-V から Azure のシナリオでは、これは Riverbed などのサード パーティ製のアプライアンスで実現できます。 
	1. VMware/物理 から Azure のシナリオでは、保持期間を日数で入力します。Hyper-V のシナリオでは、これは時間単位で入力します。 
	1. 最後の 2 つの入力は、初期レプリケーション (IR) の計算に使用されます。ASR セットアップのデプロイ時に、初期データ セット全体をアップロードする必要があります。仮想マシンのバッチの初期レプリケーションを完了させる時間と、初期レプリケーションのバッチあたりの仮想マシンの数を、入力として使用することお勧めします。一方で、これらの数字は既存の帯域幅を調整するために微調整することができます。 

	![入力](./media/site-recovery-capacity-planner/inputs.png)

1. ソース環境の詳細を入力すると、表示される出力に以下のガイダンスが含まれます。
	1.	ネットワーク帯域幅の要件
		1. デルタ レプリケーションに必要な帯域幅の合計 (Mbps)。これは、1 日の平均データ変更率に基づいて計算されます。 
		1. 初期レプリケーションに必要な帯域幅 (Mbps) も表示されます。これは、入力項目 (最後の 2 行) に表示された初期レプリケーションの入力に基づいて計算されます。 
	1.	Azure の要件
		1. 	このセクションでは、Azure で必要なディスク、IOPS、ストレージ アカウント、Storage に関する詳細が示されます。 
	1. 	その他のインフラ要件 
		1. 構成サーバーとプロセス サーバーの要件など、VMware/物理 から Azure のシナリオでの追加の要件。 
		1. 	ソースで必要な追加ストレージなど、Hyper-V から Azure のシナリオでの追加の要件。
			
	![出力](./media/site-recovery-capacity-planner/output.png)
 
##詳細なプランナー

1.	**ASR Capacity Planner.xlsm** ファイルを開きます。これには、マクロを実行する必要があります。そのため **[編集を有効にする]** と **[コンテンツの有効化]** が表示されたら、 
1.	リスト ボックスから **[詳細なプランナー]** を選択します。これにより**「Workload Qualification」**というタイトルの別のワークシートが開きます。

	![Getting Started (概要)](./media/site-recovery-capacity-planner/getting-started-2.png)


1.	Workload Qualification ワークシートに、必要に応じて入力します。赤でマークされている列はすべて、必須フィールドです。その他の列は省略できます。
	1.	プロセス コア: ソース サーバーのコアの総数を指定します。
	1. メモリの割り当て (MB): ソース サーバーの RAM サイズを指定します。
	1.	NIC の数: ソース サーバーの NIC の数を指定します。
	1. Storage の合計 (GB): VM のストレージの合計サイズを指定します。たとえば、ソース サーバーにそれぞれ 500 GB の 3 つのディスクがある場合、ストレージの合計サイズは 1500 GB になります。
	1. 接続されているディスクの数: ソース サーバーのディスクの合計数を指定します。
	1. ディスク容量の使用率: 平均使用率を指定します。 
	1. 1 日の変更率 (%) : ソース サーバーの 1 日のデータ変更率を指定します。
	1. Azure サイズのマッピング: マッピングする Azure VM のサイズを入力することも、[IaaS VM の計算] ボタンを使用して最適な一致を計算することもできます。 

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)
 

1. **[IaaS VM の計算]** ボタンをクリックすると、上記の入力が検証され、Azure VM の最適な一致が提案されます。ソース サーバーに最適なサイズの Azure VM が見つからない場合、サーバーにエラーを示します。たとえば、ソース VM に接続されているディスクの数が 65 の場合、最も大きいサイズの Azure VM に接続できるディスクの最大数は 64 であるとして、エラーを示します。


**[IaaS VM の計算]** は、VM に Azure の標準ストレージ アカウントまたは Azure Premium Storage アカウントのどちらが必要かも計算します。また、ワークロードに必要な標準ストレージ アカウントと Premium Storage アカウントの数も提案します。右下にスクロールして、ソース サーバーに使用できる Azure のストレージ タイプとストレージ アカウントを表示します。
 
**例** : 次の値を持つ 5 つの VM に対し、ツールは最適な Azure VM のサイズを計算して割り当て、その VM に標準ストレージ アカウントと Premium Storage アカウントのどちらが必要かを提案しました。

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)

例では、5 つの VM に標準ストレージ アカウントが 2 つと Premium Storage アカウントが 1 つ必要でした。VM1、VM2 は、1 つ目の標準ストレージ アカウントを使用でき、VM3 は 2 つ目の標準ストレージ アカウントを使用できます。VM4 と VM5 には Premium Storage アカウントが必要で、1 つの Premium Storage アカウントで対応することができます。

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)


>[AZURE.NOTE]IOPS はディスク レベルではなく、VM レベルで計算されます。ソース VM の中の 1 つのディスクの IOPS が 500 を超えていて、VM の合計 IOPS がサポートされる標準的な Azure VM 以内の場合、他のすべての値 (ディスクの数、NIC の数、CPU コアの数、メモリ サイズ) が、標準的な VM の制限に収まり、ツールは Premium Storage の代わりに標準の VM を選択します。ユーザーは適切な DS または GS シリーズ VM を使用して Azure サイズ セルのマッピングを手動で更新する必要があります。


1.	最初の列は、VM、ディスクおよび解約の検証列です。 
1.	すべての詳細を指定したら、上部にある **[プランナー ツールにデータを送信]** ボタンをクリックします。次の図のように、平均値が自動的に入力された **Capacity Planner** ワークシートが開きます。 
1.	この操作により、どのワークロードが保護の対象で、どのワークロードが保護の対象外かも示されます。


###Capacity Planner

1.	**Capacity Planner** ワークシートでは、最初の行のインフラの入力ソース **ワークロード**は、入力情報が **Workload Qualification** ワークシートから取得されることを示しています。  
1.	変更が必要な場合は、必ず **Workload Qualification** ワークシートで必要な変更を行い、**[プランナー ツールにデータを送信]** ボタンをクリックします。 

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1203_2015-->