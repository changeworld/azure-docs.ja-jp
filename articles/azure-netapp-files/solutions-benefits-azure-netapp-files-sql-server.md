---
title: SQL Server のデプロイに Azure NetApp Files を使用する利点 | Microsoft Docs
description: SQL Server のデプロイに Azure NetApp Files を使用した場合の詳細なコスト分析とパフォーマンス上の利点について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863906"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>SQL Server のデプロイに Azure NetApp Files を使用する利点

Azure NetApp Files を使用すると、ブロック ストレージ ソリューションと比較して、SQL Server の総保有コスト (TCO) が削減します。  ブロック ストレージでは、仮想マシンのディスク操作で I/O および帯域幅が制限されますが、Azure NetApp Files に適用されるのはネットワーク帯域幅の制限のみです。  つまり、Azure NetApp Files には VM レベルの I/O 制限は適用されません。 これらの I/O 制限がないため、Azure NetApp Files に接続されている小規模の仮想マシンで実行されている SQL Server は、大規模な仮想マシンで実行されている SQL Server 同様のパフォーマンスを発揮できます。 このようにインスタンスのサイズを小さくすると、コンピューティング コストが以前のコストの 25% になります。  "*Azure NetApp Files を使用すると、コンピューティング コストを削減できます。* "  

ただし、コンピューティング コストは、SQL Server ライセンス コストと比べると安価です。  Microsoft SQL Server の[ライセンス](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf)は、物理コア数に関連付けられています。 そのため、インスタンス サイズを小さくすると、ソフトウェア ライセンスのコストをさらに節約できます。 "*Azure NetApp Files を使用すると、ソフトウェア ライセンスのコストを削減できます。* "

ストレージ自体のコストは、データベースの実際のサイズに応じて異なります。 選択したストレージに関係なく、容量には、マネージド ディスクまたはファイル共有のいずれであるかにかかわらず、コストがかかります。  データベースのサイズが増加し、ストレージのコストが増加するにつれて、ストレージは TCO の増加の原因となり、全体的なコストに影響を与えます。  このため、"*Azure NetApp Files を使用すると、SQL Server のデプロイ コストを削減できる*" と言えます。 

この記事では、SQL Server のデプロイに Azure NetApp Files を使用した場合の詳細なコスト分析とパフォーマンス上の利点について説明します。 Azure NetApp Files を使用すれば、小さなインスタンスが、大規模なインスタンスのブロックのみで可能なデータベース作業を実行するために十分な CPU を持つだけでなく、"*多くの場合、小さなインスタンスの方が、大きなディスクベースのものよりも高いパフォーマンスを発揮するようになります。* " 

## <a name="detailed-cost-analysis"></a>Detailed cost analysis (詳細コスト分析) 

このセクションの 2 つのグラフィックのセットは、TCO の例を示しています。  最良の価格と容量のパフォーマンスを実現するように、各シナリオのマネージド ディスクの数と種類、Azure NetApp Files サービス レベル、および容量が選択されています。  各グラフィックは、グループ化されたマシン (例では Azure NetApp Files を使用した D16 とマネージド ディスクを使用した D64 を比較) で構成され、マシンの種類ごとに価格が内訳されています。  

最初のグラフィック セットは、1-TiB のデータベース サイズを使用したソリューションの全体的なコストを示しています。これは、D16s_v3 と D64、D8 と D32、D4 と D16 を比較したものです。 各構成の予想される IOP は、緑または黄色の線によって示され、右側の Y 軸に対応します。

[ ![1-TiB データベース サイズを使用してソリューションの全体的なコストを示すグラフィック。](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


2 番目のグラフィック セットは、50-TiB のデータベースを使用した全体的なコストを示しています。 比較のそれ以外の部分は同じです。例では、Azure NetApp Files を使用した D16 と ブロックを使用した D64 が比較されています。 

[ ![50-TiB のデータベース サイズを使用してソリューションの全体的なコストを示すグラフィック。](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>大きなパフォーマンス  

大幅なコスト削減を実現するには、大きなパフォーマンスが必要になります。一般的な Azure インベントリの最大インスタンスでは、例にある 80,000 ディスク IOPS がサポートされます。 1 つの Azure NetApp Files ボリュームによって、80,000 データベース IOPS を実現でき、D16 などのインスタンスで同様の IOPS 数を使用できます。 D16 は通常、25,600 ディスク IOPS の処理能力を持ちますが、サイズは D64 の 25% です。  D64s_v3 は、80,000 ディスク IOPS の処理能力を持つため、優れた高レベルの比較ポイントとなります。

D16s_v3 は、Azure NetApp Files ボリュームで 80,000 データベース IOPS を実現することができます。 SQL Storage Benchmark (SSB) ベンチマークツールで実証されているように、D16 インスタンスは、D64 インスタンスのディスクで達成可能なワークロードよりも 125% 大きいワークロードを実現します。  ツールの詳細については、「[SSB テスト ツール](#ssb-testing-tool)」セクションを参照してください。

1-TiB の作業セット サイズと 80% の読み取り、20% の更新 SQL Server ワークロードを使用して、D インスタンス クラスのほとんどのインスタンスのパフォーマンス能力を測定しましたが、すべてではありません。D2 と D64 のインスタンス自体はテストから除外されました。 前者は高速ネットワークをサポートしていないため、後者は比較ポイントであるため、除外されました。 D4s_v3、D8s_v3、D16s_v3、および D32s_v3 の制限事項については、次のグラフを参照してください。  マネージド ディスク ストレージのテストはグラフに表示されていません。 比較値は、D クラスのインスタンスの種類の [Azure 仮想マシンの制限の表](../virtual-machines/dv3-dsv3-series.md)から直接得られたものです。

Azure NetApp Files を使用すると、D クラスの各インスタンスで、その 2 倍のサイズのインスタンスのディスク パフォーマンス機能を実現するか、それを上回ることができます。  "*Azure NetApp Files を使用すると、ソフトウェア ライセンスのコストを大幅に削減できます。* "  

* CPU 使用率 75% の D4 が、D16 のディスク処理能力を達成しました。  
    * D16 のレート制限は、25,600 ディスク IOPS です。  
* CPU 使用率 75% の D8 が、D32 のディスク処理能力を達成しました。  
    * D32 のレート制限は、51,200 ディスク IOPS です。  
* CPU 使用率 55% の D16 が、D64 のディスク処理能力を達成しました。  
    * D64 のレート制限は 80,000 ディスク IOPS です。  
* CPU 使用率 15% の D32 も、D64 のディスク処理能力を達成しました。  
    * 前述のように、D64 のレート制限は、80,000 ディスク IOPS です。  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>S3B CPU 制限テスト – パフォーマンスと処理能力

次の図は、S3B CPU 制限テストの概要を示しています。

![Azure NetApp Files に対する単一インスタンス SQL Server の平均 CPU 使用率を示す図。](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

重要なのはスケーラビリティだけではありません。 もう 1 つ挙げられるのは待機時間です。  小さな仮想マシンで大幅に高い I/O 速度を実現できるだけでなく、下に示すように、低い 1 桁の待機時間でこれを達成することができます。  

* D4 は Azure NetApp Files に対し、2.3 ms の待機時間で 26,000 IOPS を実行しました。  
* D8 は Azure NetApp Files に対し、2.0 ms の待機時間で 51,000 IOPS を実行しました。  
* D16 は Azure NetApp Files に対し、2.8 ms の待機時間で 88,000 IOPS を実行しました。
* D32 は Azure NetApp Files に対し、2.4 ms の待機時間で 80,000 IOPS を実行しました。  

### <a name="s3b-per-instance-type-latency-results"></a>インスタンスの種類ごとの S3B の待機時間の結果

次の図は、Azure NetApp Files での単一インスタンス SQL Server の待機時間を示したものです。

![Azure NetApp Files での単一インスタンス SQL Server の待機時間を示す図。](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>SSB テスト ツール 
 
[TPC-E](http://www.tpc.org/tpce/) ベンチマーク ツールは、設計上、"*ストレージ*" よりも "*コンピューティング*" に重点を置いています。 このセクションに示されているテスト結果は、SQL Storage Benchmark (SSB) というストレス テスト ツールに基づいています。  SQL Server Storage Benchmark は、[SLOB2 Oracle ベンチマーク ツール](https://kevinclosson.net/slob/)と同様に、SQL Server データベースに対して大規模な SQL 実行を行い、OLTP ワークロードをシミュレーションできます。 

SSB ツールは、Azure 仮想マシン内で実行されている SQL Server データベースに対して SELECT および UPDATE を直接発行し、これらのステートメントによるワークロードを生成します。  このプロジェクトでは、SSB ワークロードは 1 人から 100 人の SQL Server ユーザーに増加し、ユーザー数当たり 15 分で 10 または 12 の中間点を設定しました。  これらの実行からのパフォーマンス メトリックは、パフォーマンス モニターの観点からのものです。再現性を確認するため、各シナリオ当たり SSB を 3 回実行しました。 

テスト自体は、80% が SELECT、20% が UPDATE ステートメントで、90% がランダムな読み取りです。  SSB が作成したデータベース自体は、サイズが 1000 GB でした。 15 のユーザー テーブルと、ユーザー テーブルごとに 900 万行、行あたり 8192 バイトで構成されています。 

SSB ベンチマークはオープンソースのツールです。  [SQL Storage Benchmark の GitHub ページ](https://github.com/NetApp/SQL_Storage_Benchmark.git)から無料で入手できます。  


## <a name="in-summary"></a>まとめ  

Azure NetApp Files を使用すると、総保有コストを大幅に削減しながら、SQL Server のパフォーマンスを向上させることができます。 

## <a name="next-steps"></a>次の手順

* [Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files を使用したソリューション アーキテクチャ –SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

