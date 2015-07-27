<properties
   pageTitle="SQL Data Warehouse での Azure Machine Learning の使用 | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse での Azure Machine Learning の使用に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="sahajs"/>

# SQL Data Warehouse での Azure Machine Learning の使用

Azure Machine Learning は、完全に管理された予測分析サービスです。このサービスを使用して、SQL Data Warehouse のデータに対して予測モデルを作成し、いつでも利用できる Web サービスとして発行することができます。予測分析と機械学習の基礎については、「[Introduction to Machine Learning on Azure (Azure の機械学習の概要)][]」をご覧ください。機械学習モデルの作成、トレーニング、スコア付け、およびテストの方法については、[実験作成チュートリアル][]のページをご覧ください。

この記事では、[Azure Machine Learning Studio][] を使用して、次のことを行う方法について説明します。- データベースからデータを読み取り、予測モデルの作成、トレーニング、スコア付けを行う。- データベースにデータを書き込む。


## SQL Data Warehouse からのデータの読み取り

AdventureWorksDW データベースの Product テーブルからデータを読み取ります。

### 手順 1.
Machine Learning Studio ウィンドウの下部にある [+新規] をクリックして新しい実験を開始し、[実験] を選択してから「空の実験」を選択します。キャンバスの上部にある既定の実験名を選択し、自動車価格の予測など、わかりやすい名前に変更します。

### 手順 2.
実験キャンバスの左側にあるデータセットとモジュールのパレットで、リーダー モジュールを探します。モジュールを実験キャンバスにドラッグします。![][drag_reader]

### 手順 3.
リーダー モジュールを選択し、プロパティ ウィンドウに情報を入力します。1. [データ ソース] として [Azure SQL Database] を選択します。2. [データベース サーバー名]: サーバー名を入力します。これは、[Azure ポータル][]を使用して見つけることができます。![][server_name] 3.[データベース名]: 指定したサーバー上のデータベースの名前を入力します。4.[サーバー ユーザー アカウント名]: データベースに対するアクセス許可を持つアカウントのユーザー名を入力します。5.[サーバー ユーザー アカウントのパスワード]: 指定したユーザー アカウントのパスワードを入力します。6.[すべてのサーバー証明書を受け入れる]: データを読み取る前に、サイト証明書の確認をスキップする場合は、このオプションを使用します (セキュリティが低下します)。7.[データベース クエリ]: 読み取るデータを記述した SQL ステートメントを入力します。この例では、次のクエリを使用して Product テーブルからデータを読み取ります。


```
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### 手順 4.
1. 実験キャンバスの下にある [実行] をクリックして、実験を実行します。
2. 実験が終了すると、リーダー モジュールに緑色のチェック マークが付けられ、正常に完了したことが示されます。右上隅にも [実行が完了しました] というステータスが表示されます。![][run]
3. インポートしたデータを確認するには、自動車データセットの下部にある出力ポートをクリックし、[視覚化] を選択します。

## モデルの作成、トレーニング、スコア付け
このデータセットを使用して、次のことを行います。- モデルの作成: データを処理し、機能を定義します。- モデルのトレーニング: 学習アルゴリズムを選択して適用します。- モデルのスコア付けとテスト: 新しい自転車の価格を予測します。
	
![][model]

機械学習モデルの作成、トレーニング、スコア付け、およびテストの方法の詳細については、[実験作成チュートリアル][]のページをご覧ください。

## Azure SQL Data Warehouse へのデータの書き込み

AdventureWorksDW データベースの ProductPriceForecast テーブルに結果セットを書き込みます。

### 手順 1.
実験キャンバスの左側にあるデータセットとモジュールのパレットで、ライター モジュールを探します。モジュールを実験キャンバスにドラッグします。![][drag_writer]

### 手順 2.
ライター モジュールを選択し、プロパティ ウィンドウに情報を入力します。1.[データ ソース] として [Azure SQL Database] を選択します。2.[データベース サーバー名]: サーバー名を入力します。これは、[Azure ポータル][]を使用して見つけることができます。3.[データベース名]: 指定したサーバー上のデータベースの名前を入力します。4.[サーバー ユーザー アカウント名]: データベースに対する書き込みアクセス許可を持つアカウントのユーザー名を入力します。5.[サーバー ユーザー アカウントのパスワード]: 指定したユーザー アカウントのパスワードを入力します。6.[すべてのサーバー証明書を受け入れる (安全ではありません)]: 証明書を表示しない場合は、このオプションを選択します。7.[保存する列のコンマ区切りのリスト]: 出力するデータセットまたは結果の列のリストを指定します。8.[データ テーブル名]: データ テーブルの名前を指定します。9.[データ テーブル列のコンマ区切りのリスト]: 新しいテーブルで使用する列名を指定します。ソース データセットとは異なる列名を指定できますが、出力テーブル用に定義した列数と同じ列数をここで示す必要があります。10.[SQL Azure 操作ごとの書き込み行数]: 1 つの操作で SQL Database に書き込む行数を設定できます。

![][writer_properties]

### 手順 3.
1. 実験キャンバスの下にある [実行] をクリックして、実験を実行します。
2. 実験が終了すると、すべてのモジュールに緑色のチェック マークが付けられ、正常に完了したことが示されます。 



## 次のステップ
統合の概要については、「[SQL Data Warehouse integration overview (SQL Data Warehouse の統合の概要)][]」をご覧ください。開発に関するその他のヒントについては、「[SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)][]」をご覧ください。

<!--Image references-->
[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png


<!--Article references-->

[SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)]: ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview (SQL Data Warehouse の統合の概要)]: ./sql-data-warehouse-overview-integration/
[実験作成チュートリアル]: ./machine-learning-create-experiment/
[Introduction to Machine Learning on Azure (Azure の機械学習の概要)]: ./machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure ポータル]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->
[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

<!---HONumber=July15_HO3-->