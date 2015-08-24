<properties 
	pageTitle="Azure Machine Learning 用にデータを Azure SQL Database に移動する | Azure" 
	description="SQL テーブルを作成して SQL テーブルにデータを読み込みます"  
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="jacob.spoelstra" 
	editor=""  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="fashah;bradsev" />

# Azure Machine Learning 用にデータを Azure SQL Database に移動する


このトピックでは、フラット ファイル (CSV 形式または TSV 形式) のデータまたはオンプレミスの SQL Server に格納されているデータを、Azure SQL Database に移動するためのオプションについて説明します。クラウドにデータを移動するこれらのタスクは、Azure Machine Learning が提供する Advanced Analytics Process and Technology (ADAPT) の一部です。

Machine Learning 用にオンプレミスの SQL Server にデータを移動するためのオプションについては、「[Azure Virtual Machine 上の SQL Server にデータを移動する](machine-learning-data-science-move-sql-server-virtual-machine.md)」を参照してください。

次の表は、Azure SQL Database にデータを移動するためのオプションをまとめたものです。<table>

<tr>
<td><b>ソース</b></td>
<td colspan="2"><b>移動先: Azure SQL Database</b></td>
</tr>

<tr>
  <td><b>フラット ファイル (CSV または TSV 形式)</b></td>  

  <td>
    1.<a href="#bulk-insert-sql-query">一括挿入 SQL クエリ
  </td>
</tr>

<tr>
  <td><b>オンプレミスの SQL Server</b></td>

  <td>
    1.<a href="#export-flat-file">フラット ファイルへのエクスポート<br>
    2.<a href="#insert-tables-bcp">SQL Database 移行ウィザード<br>
    3.<a href="#db-migration">データベースのバックアップと復元<br>
    4.<a href="#adf">Azure Data Factory
  </td>
</tr>

</table>


## <a name="prereqs"></a>前提条件
ここに記載されているこの手順には次のものが必要です。

* **Azure サブスクリプション**。サブスクリプションがない場合でも、[無料評価版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* **Azure ストレージ アカウント**。このチュートリアルのデータを格納するには、Azure ストレージ アカウントを使用します。Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](storage-create-storage-account.md#create-a-storage-account)」を参照してください。ストレージ アカウントを作成した後は、ストレージにアクセスするために使用するアカウント キーを取得する必要があります。「[ストレージ アクセス キーの表示、コピーおよび再生成](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。
* **Azure SQL Database** へのアクセス権。Azure SQL Database を設定する必要がある場合は、「[Microsoft Azure SQL データベースの概要](sql-database-get-started.md)」に説明されている、Azure SQL Database の新しいインスタンスをプロビジョニングする方法を参照してください。
* **Azure PowerShell** がローカルにインストールされ構成されていること。手順については、「[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)」を参照してください。

**データ**: 移行プロセスは、[NYC タクシー データセット](http://chriswhong.com/open-data/foil_nyc_taxi/)を使用して説明されています。NYC タクシー データセットには移動データと料金についての情報が含まれ、投稿で説明されているように[この](http://www.andresmh.com/nyctaxitrips/) Azure BLOB ストレージで使用できます。これらのファイルのサンプルと説明は、「[NYC タクシー乗車データセットの説明](machine-learning-data-science-process-sql-walkthrough.md#dataset)」にあります。
 
ここで説明されている手順は、自身のデータに適用することも、NYC タクシー データセットを使用してこの手順に従って行うこともできます。NYC タクシー データセットを自身のオンプレミスの SQL Server データベースにアップロードするには、「[SQL Server データベースにデータを一括インポートする](machine-learning-data-science-process-sql-walkthrough.md#dbload)」に記載されている手順に従います。これらは Azure Virtual Machine 上の SQL Server にアップロードする手順ですが、オンプレミスの SQL Server へのアップロード手順も同じです。

## <a name="file-to-azure-sql-database"></a>フラット ファイル ソースから Azure SQL Database へのデータの移動

フラット ファイル (CSV 形式または TSV 形式) のデータは、一括挿入 SQL クエリを使用して Azure SQL Database に移動できます。

### <a name="bulk-insert-sql-query"></a> 一括挿入 SQL クエリ

一括挿入 SQL クエリを使用する手順は、フラット ファイル ソースから Azure VM 上の SQL Server にデータを移動する手順と似ています。詳細については、「[一括挿入 SQL クエリ](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery)」を参照してください。

##<a name="sql-on-prem-to-sazure-sql-database"></a>オンプレミスの SQL Server から Azure SQL Database へのデータの移動

ソース データがオンプレミスの SQL Server に保存されている場合は、さまざまな方法で Azure SQL Database にデータを移動できます。

1. [フラット ファイルへのエクスポート](#export-flat-file) 
2. [SQL Database 移行ウィザード](#insert-tables-bcp)
3. [データベースのバックアップと復元](#db-migration)
4. [Azure Data Factory](#adf)

最初の 3 つの手順は、「[Azure Virtual Machine 上の SQL Server にデータを移動する](machine-learning-data-science-move-sql-server-virtual-machine.md)」の対応する手順とよく似ています。そのトピックの該当するセクションへのリンクを以下に示します。

###<a name="export-flat-file"></a>フラット ファイルへのエクスポート

フラット ファイルにエクスポートする手順は、「[フラット ファイルへのエクスポート](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file)」の手順と似ています。

###<a name="insert-tables-bcp">SQL Database 移行ウィザード</a>

SQL Database 移行ウィザードを使用する手順は、「[SQL データベースの移行ウィザード](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration)」の手順と似ています。

###<a name="db-migration"></a>データベースのバックアップと復元

データベースのバックアップと復元を使用する手順は、「[データベースのバックアップと復元](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup)」の手順と似ています。

###<a name="adf"></a>Azure Data Factory

Azure Data Factory (ADF) を使用して Azure SQL Database にデータを移動する手順は、トピック「[Azure Data Factory を使用してオンプレミスの SQL Server から SQL Azure にデータを移動する](machine-learning-data-science-move-sql-azure-adf.md)」で説明されています。このトピックでは、ADF を使用してオンプレミスの SQL Server データベースから Azure BLOB ストレージ経由で Azure SQL Database にデータを移動する方法が示されています。

オンプレミスとクラウドの両方のリソースにアクセスするハイブリッド シナリオで、継続的にデータを移行する必要がある場合、および移行の過程で、データを処理する場合や、データに変更を加えたりビジネス ロジックを付加したりする必要がある場合には、ADF の使用を検討してください。ADF では、定期的にデータの移動を管理するシンプルな JSON スクリプトを使用して、ジョブのスケジュールと監視ができます。ADF には他にも、複雑な操作のサポートなどの機能があります。

<!---HONumber=August15_HO7-->