<properties 
	pageTitle="保存データをクラウドに転送する場合のオプション | Azure" 
	description="高度な分析を行うためにオンプレミスまたは他のクラウドのソースから Microsoft Azure にデータを転送する場合の最適なオプションを選択する方法について説明します。" 
	services="data-factory, hdinsight, machine-learning, storage, sql-database" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jeffgoll" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="01/07/2014" 
	ms.author="spelluru"/>

# 保存データを Azure のクラウドに転送する場合のオプション

オンプレミスのデプロイメントや他のクラウドのソースからデータを Microsoft Azure に転送し、高度なデータ分析を行う場合があります。この記事では、データを転送する際のオプションを適切に選択する方法を説明します。大量のデータの転送には長い時間がかかる場合があり、適切なセキュリティ対策も必要です。

この記事の内容:

* [BLOB ストレージ用の Azure Import/Export サービス](#blob)
* [AZCopy ユーティリティ](#azcopy-utility)
* [Azure PowerShell](#ps)
* [Azure Data Factory (プレビュー)](#data-factory)
* [Azure SQL Database 移行ツール](#tools)
* [Azure SQL データ同期 (プレビュー)](#data-sync)
* [Azure Event Hubs](#event-hubs)
* [データ転送時の他のオプション](#other)
* [適切なデータ転送オプションの選択](#choose)


## BLOB ストレージ用の Azure Import/Export サービス

ネットワーク経由でのアップロード/ダウンロードが不可能か、または非常にコストがかかる場合には、Azure Import/Export サービスを使用すると、大量のファイル データを Azure BLOB ストレージに転送できます。ネットワーク経由で大量のデータ セットをアップロード/ダウンロードすると、非常に長い時間がかかります。たとえば、T3 (44.7 Mbps) 回線の場合、10 TB の転送を行うと、1 か月もかかります。Microsoft Azure Import/Export サービスは、データのアップロード/ダウンロードにかかる時間を短縮するため、お客様にハード ドライブを発送していただくサービスです。発送の日数も含めると数日かかる可能性があります。

ファイルの大量のデータを BLOB ストレージに転送するために、そのデータを含む 1 台以上のハード ドライブを Azure データ センターに発送することができます。このデータ センターでデータがストレージ アカウントにアップロードされます。同様に、BLOB ストレージからデータをエクスポートするために、空のハード ドライブを Azure データ センターに発送することができます。このデータ センターで、ストレージ アカウントの BLOB データがハード ドライブにコピーされ、返送されます。データを含むドライブを発送する前に、ドライブにあるデータを暗号化してください。インポート/エクスポート サービスで、返送するデータをエクスポートするときにも、データは発送前に暗号化されます。

詳細については、「[Microsoft Azure Import/Export サービスを使用した BLOB ストレージへのデータの転送][import-export]」を参照してください。


## AZCopy ユーティリティ

AzCopy は、Microsoft Azure の BLOB、ファイル、およびテーブル ストレージ間のデータのアップロード、ダウンロード、コピーにおいて高いパフォーマンスを実現するために設計されたコマンド ライン ユーティリティです。このユーティリティは、ネットワーク経由でデータ転送が実用的な場合、Azure のストレージとオンプレミスとの間でデータを一括移動する場合に適しています。詳細については、「[AzCopy コマンド ライン ユーティリティの概要][azcopy]」を参照してください。

> [AZURE.NOTE]Linux ユーザーは、[Linux 用の ACP、AzCopy ](http://www.paratools.com/acp)をダウンロードしてください。


## Azure PowerShell

Azure PowerShell は、Azure のワークロードのデプロイと管理を制御し自動化するために使用できる強力なスクリプティング環境です。Azure PowerShell を使用して、BLOB ストレージにデータをアップロードできます。これにより、高度な分析や MapReduce ジョブによる処理ができるようになります。

次のページも参照してください。

* [Azure PowerShell を使用して BLOB ストレージにデータをアップロードする][upload]
* [Azure PowerShell のインストールおよび構成方法][install]


## Azure Data Factory (プレビュー)

Azure Data Factory は、データの保存、処理、移動の各サービスを効率的で拡張性が高く信頼性も高いデータ生成パイプラインとして構成する十分に管理されたサービスです。

開発者は、オンプレミス、クラウドベース、インターネット サービスからデータ管理ゲートウェイ経由で取得したデータを結合、集計、変換するデータ駆動型のワークフローを構築し、シンプルな JSON スクリプトを使用して複雑なデータ処理を設定できます。その結果として得られたデータは、高度な分析を行うために Azure Storage や Azure SQL Database に保存できます。

具体的には、開発者は、「[Azure Data Factory を使用してデータをコピーする](data-factory-copy-activity.md)」の「[サポートされているソースとシンク](data-factory-copy-activity.md#SupportedSourcesAndSinks)」のセクションで示されている、さまざまなソースとコピー先間の通常のコピー アクティビティを調整できます。これには、さまざまな種類のデータ ストア、列マッピング、シリアル化形式、データ型処理のプロパティも含まれています。

このサービスは、エラーに対して自動再起動で処理できます。また、ある形式から他の形式にデータを移動する場合の形式変換が可能です。コピー アクティビティを定義する方法の詳細については、「[Data Factory を使ってみる][start]」を参照してください。データ ストアの登録とゲートウェイのインストール方法については、「[パイプラインがオンプレミスのデータを扱えるようにする][pipelines]」を参照してください。

次のページも参照してください。

* [Data Factory の概要][intro]

## Azure SQL Database 移行ツール

オンプレミスの SQL サーバーおよび非 SQL サーバーのデータベースを Azure SQL Database に移行する場合には、数多くのツールを利用することができます。最適なツールは、移行するデータベースの種類、サイズ、複雑さなど個々の状況によって異なります。

* 既存の Azure SQL Database のデータとスキーマは、いずれもデータベースをエクスポートした上でエクスポート ファイルを Azure BLOB ストレージ アカウントに保存し、さらに、新しい Azure SQL Database としてインポートすることによって移行できます。エクスポート時に作成されるファイルは、BACPAC ファイルと呼ばれます。詳細については、「[方法: Azure SQL データベースでインポート/エクスポート サービスを使用する][sql-import]」を参照してください。
* データベースのコピー機能により、Azure 内で、既存の Azure SQL Database の複製である新しいデータベースを作成できます。このデータベースはトランザクション上の一貫性が維持されます。詳細については、「[Azure SQL Database でのデータベースのコピー][sql-copy]」を参照してください。
* 複雑なデータ変換が必要な場合は、SQL Server Integration Services (SSIS) を使用できます。SSIS は、Azure SQL Database に対してデータの入出力を行う場合に使用できます。詳細については、「[方法: Integration Services を使用して Azure SQL Database にデータベースを移行する方法][integrate]」および「[SSIS を使用した Azure とハイブリッドのデータの移動][SSIS]」を参照してください。
* SQL Server のインポート/エクスポート ウィザードは、データを移行するために SSIS パッケージを簡単に作成する簡単な方法です。移行元と移行先を構成した後で、基本的なデータ変換を指定できます。このパッケージは、保存、変更、実行が可能で、ジョブとしてスケジュール設定することもできます。詳細については、「[方法: インポート/エクスポート ウィザードを使用した Azure SQL Database へのデータベースの移行][wizard]」を参照してください。
* SQL Database 移行ウィザードは、複数の Azure SQL Database サーバーの間だけでなく、オンプレミスの SQL サーバーと Azure SQL Database サーバーの間でも、スキーマとデータの両方の移行を支援するツールです。このツールは、Azure SQL Database の互換性問題に対応するために、トレース ファイルとスクリプトの分析も行います。詳細については、「[方法: SQL Database 移行ウィザードの使用方法][use-wizard]」を参照してください。
* SQL Server のテーブルに多数の新しい行をインポートする、またはテーブルからデータ ファイルにデータをエクスポートする場合には、BCP ユーティリティを使用できます。詳細については、「[方法: BCP を使用した Azure SQL Database へのデータベースの移行方法][bcp]」を参照してください。

次のページも参照してください。

* [Azure SQL Database へのデータベースの移行][migrate]
 

## Azure SQL データ同期 (プレビュー)

SQL データ同期 (プレビュー) は、Azure SQL Database と、SQL サーバーまたは Azure SQL Database でホストされているデータベースとの間で、定期的な同期機能を作成してスケジュール設定することができます。

SQL データ同期は、オンプレミスのデータベースとクラウドの Azure データベースの間でデータの差分変更を同期する開発者に適しています。「[SQL データ同期][sync]」を参照してください。

##	Azure Event Hubs

Microsoft Azure Event Hubs は、クラウドに対し、短い待機時間と高い信頼性を確保しながらイベントや製品利用統計情報を大規模に取得できるようにするイベント取り込みサービスです。このサービスは、特に、他のダウンストリーム サービスと共に使用した場合、アプリケーション インストルメンテーション、ユーザー エクスペリエンス、ワークフロー処理、モノのインターネットなどのシナリオで役立ちます。

開発者は、イベント ハブにデータを送信して処理し、次の処理のために Azure BLOB または Azure SQL Database に保存できます。

または、十分に管理されたサービスである Azure Stream Analytics を利用し、出力されたストリーミング データに対して拡張性の高い複雑なイベント処理を行うことができます。さらに、Azure Event Hubs からストリームを選択して受信イベントのシリアル化に使用する形式 (JSON、CSV、Avro など) を指定し、Azure BLOB、Azure SQL Database などの出力場所を追加することもできます。

参照:

* [Event Hubs サービスに関する情報](/services/event-hubs/)
* [Event Hubs の概要][overview]
* [Azure Stream Analytics の概要][stream]

## データ転送時の他のオプション

ハイブリッド接続は、Azure Web サイトおよび Azure モバイル サービスを内部設置型リソースに簡単に接続する便利な方法を提供します。開発者は、オンプレミスから Azure にデータを移動するための Web サイトを構築できます。詳細については、「[ハイブリッド接続の概要][hybrid]」を参照してください。

[仮想ネットワーク](/services/virtual-network/)では、Azure の仮想マシンで実行されるデータ統合ツールを使用して、オンサイトのデータ センター内にあるオンプレミス SQL Server のデータベースに安全に接続できます。同じ仮想ネットワーク内にある仮想マシンとサービスだけが識別され、相互に接続できます。場合によっては、ネットワーク サービス プロバイダーまたは Exchange プロバイダーを介して Azure への [ExpressRoute](/services/expressroute/) 直接接続を構築することによって、パブリックなインターネットを完全にバイパスできます。

[Azure Marketplace](?../source=datamarket.md) には、Storm Managed File Transfer など、Azure にデータを移動できるようにするパートナー ソリューションが用意されています。

## 適切なデータ転送オプションの選択

### デシジョン ツリー

![選択するクラウド データ転送オプションを決定する助けになります。][decision]

デシジョン ツリーについて:

* SQL データ同期 (プレビュー) は、変更されたデータから同期をトリガーします。
* Data Factory (プレビュー) は、Azure Storage、Azure SQL Database、オンプレミス SQL Server の間で行われるデータのコピーをサポートします。 
* Data Factory の使用に加え、既存の SSIS パッケージを拡張することにより、クラウドへのデータ移動スケジュールを作成できます。

### GB 規模のデータの移動

<table border="1">
<tr>
<th>データの移動</th>
<th>1 回限り</th>
<th>スケジュール設定</th>
<th>考慮事項</th>
</tr>

<tr>
<td><p>ファイルから Azure Storage</p>
</td>
<td><ul>
<li><a href="/documentation/articles/storage-use-azcopy/">AzCopy</a></li>
<li><a href="http://www.paratools.com/acp" target="_blank">acp</a></li>
<li><a href="/documentation/articles/install-configure-powershell/">Azure PowerShell</a></li>
<li><a href="/documentation/articles/storage-import-export-service/">Azure Import/Export</a></li>
</ul>
</td>
<td><p>該当なし</p>
</td>
<td><p>ネットワーク経由でのデータ転送が実用的でない場合は、AzCopy、acp、Azure PowerShell ではなく、Azure Import/Export を使用してください。このサービスを利用する場合、ディスクをデータ センターに発送する日数も含め数日かかる可能性があります。</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server から Azure SQL Database</li>
<li>Azure SQL Database から SQL Server</li>
</ul>
</td>
<td><ul>
<li><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></li>
<li><a href="http://msdn.microsoft.com/library/azure/ee730904.aspx">SQL Database 移行ツール</a></li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p>例外的なケースで形式変換とジョブの自動復旧ができる移行ツールを使用する場合がありますが、一般的には Azure Data Factory (プレビュー) の使用をお勧めします。オンプレミスの SQL Server に接続する場合には、データ管理ゲートウェイが必要です。</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>Azure Storage から Azure SQL Database</li>
<li>Azure SQL Database から Azure Storage</li>
</ul>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p><a href="/documentation/articles/data-factory-introduction/">Azure Data Factory</a></p>
</td>
<td><p>Azure Data Factory (プレビュー) では、例外的なケースで形式変換およびのジョブの自動復旧ができます。オンプレミスの SQL Server に接続する場合には、データ管理ゲートウェイが必要です。</p>
</td>
</tr>

<tr>
<td>
<ul>
<li>SQL Server から SQL データ同期</li>
<li>SQL データ同期から Azure SQL Database</li>
</ul>
</td>
<td><p>該当なし</p>
</td>
<td><p><a href="http://msdn.microsoft.com/library/azure/hh456371.aspx">SQL データ同期</a></p>
</td>
<td><p>SQL データ同期 (プレビュー) は、同期するデータベース、テーブル、列、同期スケジュールを定義する同期グループによってデータを同期します。</p>
</td>
</tr>

</table>



### TB 規模のデータの移動

<table border="1">
<tr>
<th>データの移動</th>
<th>1 回限り</th>
<th>スケジュール設定</th>
<th>考慮事項</th>
</tr>

<tr>
<td><p>ファイルから Azure Storage</p>
</td>
<td><p><a href="/documentation/articles/storage-import-export-service/">Azure Import/Export</a></p>
</td>
<td><p>該当なし</p>
</td>
<td><p>このサービスは、ディスクをデータ センターに発送する日数も含め数日かかる可能性があります。</p>
</td>
</tr>

</table>
<br>


<!--Anchors-->
[Azure Import/Export service for Blob storage]: #blob
[AZCopy utility]: #azcopy-utility
[Azure PowerShell]: #ps
[Azure Data Factory (preview)]: #data-factory
[Azure SQL Database migration tools]: #tools
[Azure SQL Data Sync (preview)]: #data-sync
[Azure Event Hubs]: #event-hubs
[Other options for data transfer]: #other
[Choose the right data transfer option]: #choose

<!--Image references-->
[decision]: ./media/data-management-options-for-transferring-data/data-transfer-decision-tree.png


<!--Link references-->
[import-export]: ../storage-import-export-service.md
[azcopy]: ../storage-use-azcopy.md
[upload]: ../hdinsight-upload-data.md#powershell
[install]: ../install-configure-powershell.md
[start]: data-factory-get-started.md
[pipelines]: data-factory-use-onpremises-datasources.md
[copy]: data-factory-copy-activity.md
[intro]: data-factory-introduction.md
[sql-import]: http://msdn.microsoft.com/library/azure/hh335292.aspx
[sql-copy]: http://msdn.microsoft.com/library/azure/ff951624.aspx
[integrate]: http://msdn.microsoft.com/library/azure/jj156150.aspx
[SSIS]: http://msdn.microsoft.com/library/jj901708.aspx
[wizard]: http://msdn.microsoft.com/library/azure/jj156152.aspx
[use-wizard]: http://msdn.microsoft.com/library/azure/jj156144.aspx
[bcp]: http://msdn.microsoft.com/library/azure/jj156153.aspx
[migrate]: http://msdn.microsoft.com/library/azure/ee730904.aspx
[overview]: http://msdn.microsoft.com/library/dn836025.aspx
[stream]: ../stream-analytics-introduction.md
[sync]: http://msdn.microsoft.com/library/azure/hh456371.aspx
[hybrid]: ../integration-hybrid-connection-overview.md
 

<!---HONumber=July15_HO4-->