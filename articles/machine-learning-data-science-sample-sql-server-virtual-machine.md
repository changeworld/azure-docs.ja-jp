<properties 
	pageTitle="Azure の SQL Server のデータのサンプリング | Azure" 
	description="Azure の SQL Server のデータのサンプリング" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="fashah,garye" /> 

#<a name="heading"></a>Azure の SQL Server のデータのサンプリング

このドキュメントでは、Azure の SQL Server に格納されたデータを次の方法でサンプリングすることについて説明します。

1. [SQL を使用する](#sql)
2. [Python プログラミング言語を使用する](#python) 

**注**
>このドキュメントにあるサンプルの SQL コードは、データが Azure の SQL Server に存在することを前提としています。存在しない場合は、クラウド データ サイエンス プロセス マップにある、Azure の SQL Server にデータを移動する手順を参照してください。

###<a name="SQL"></a>SQL を使用する

このセクションでは、SQL を使用して、データベース内のデータに対して簡単なランダム サンプリングを実行するいくつかの方法について説明します。データのサイズとその分布に基づいて方法を選択してください。

次の 2 つの項目は、SQL Server の newid を使用してサンプリングを実行する方法を示しています。選択する方法は、サンプルをどの程度ランダムにするかによって変わります (次のサンプル コードで、pk_id は自動生成された主キーであることを想定しています)。

1. 制限が少なめのランダム サンプル

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. よりランダムなサンプル 

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

以下に説明するとおり、Tablesample も同様にサンプリングに使用できます。データのサイズが大きい場合 (異なるページのデータと相関関係がないと仮定すると)、クエリを妥当な時間内に完了するには、これがより適したアプローチになることがあります。

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

**注**
> このサンプリングされたデータを新しいテーブルに格納すると、このデータから探索および特徴の生成を行えます。


####<a name="sql-aml"></a>Azure Machine Learning に接続する

Azure ML リーダー モジュールで上記のサンプル クエリを直接使用し、データをその場でダウンサンプリングして Azure ML の実験で使用できます。リーダー モジュールを使用してサンプリングされたデータを読み取るスクリーン ショットを次に示します。
   
![reader sql][1]

###<a name="python"></a>Python プログラミング言語の使用 

このセクションでは、Python で pyodbc ライブラリを使用した SQL Server データベースへの接続について説明します。データベース接続文字列は、次のとおりです。(サーバー名、データベース名、ユーザー名、およびパスワードを使用する構成に置き換えます)

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python の [Pandas](http://pandas.pydata.org/) ライブラリには、Python プログラミングでのデータ操作用にデータ構造とデータ解析ツールの豊富なセットが用意されています。次のコードは、Azure SQL データベースから 0.1% のデータのサンプルを Pandas のデータに読み込みます。

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

これで、Pandas データ フレームでサンプリングされたデータを操作できるようになりました。 

####<a name="python-aml"></a>Azure Machine Learning に接続する

次のサンプル コードを使用すると、ダウンサンプリングされたデータをファイルに保存し、Azure BLOB にアップロードすることができます。BLOB 内のデータは、 *Reader Module* を使用して直接 Azure ML の実験に読み込むことができます。手順は次のとおりです。 

1. Pandas データ フレームをローカル ファイルに書き込む

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. ローカル ファイルを Azure BLOB にアップロードする

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. 次のスクリーン ショットに示すように、Azure ML *Reader Module* を使用して Azure BLOB からデータを読み取ります。
 
![reader blob][2]

### 実行中の Azure データ サイエンスの例

公開されているデータセットを使用した Azure のデータ サイエンス プロセスのエンドツーエンドのチュートリアルの例については、「[実行中の Azure データ サイエンス プロセス](machine-learning-data-science-process-sql-walkthrough.md)」を参照してください。

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png


<!--HONumber=49-->