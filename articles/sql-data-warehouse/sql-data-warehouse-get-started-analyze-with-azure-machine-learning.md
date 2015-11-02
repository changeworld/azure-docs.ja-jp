<properties
   pageTitle="Azure Machine Learning を使用したデータの分析 | Microsoft Azure"
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
   ms.date="10/21/2015"
   ms.author="sahajs"/>

# Azure Machine Learning を使用したデータの分析
このチュートリアルでは、Azure SQL Data Warehouse のデータを使用して Azure Machine Learning の機械学習予測モデルを作成する方法を使用します。チュートリアル内では、対象を絞ったマーケティング キャンペーンを作成するために、AdventureWorksDW データベースを使用して、Adventure Works 自転車店の顧客を自転車購入の見込みがあるかないかに分類します。


## 前提条件
このチュートリアルを進めるには、次のものが必要です。

- AdventureWorksDW サンプル データベースが含まれた SQL Data Warehouse

[SQL Data Warehouse の作成][]に関するページに、サンプル データを設定したデータベースのプロビジョニング方法が示されています。SQL Data Warehouse データベースは既にあってもサンプル データがない場合は、[サンプル データを手動で読み込む][]ことができます。


## 手順 1. データを取得する 
AdventureWorksDW データベースの dbo.vTargetMail ビューからデータを読み取ります。

1. [Azure Machine Learning Studio][] にサインインし、[実験] をクリックします。
2. **[+ 新規]** をクリックし、**[空の実験]** を選択します。
3. 実験の名前として「対象を絞ったマーケティング」と入力します。
4. [モジュール] ウィンドウから **[リーダー]** モジュールをキャンバスにドラッグします。
5. [プロパティ] ウィンドウで、SQL Data Warehouse データベースの詳細を指定します。 
6. 目的のデータを読み取るためのデータベース クエリを指定します。

```
SELECT [CustomerKey]
      ,[GeographyKey]
      ,[CustomerAlternateKey]
      ,[MaritalStatus]
      ,[Gender]
      ,cast ([YearlyIncome] as int) as SalaryYear
      ,[TotalChildren]
      ,[NumberChildrenAtHome]
      ,[EnglishEducation]
      ,[EnglishOccupation]
      ,[HouseOwnerFlag]
      ,[NumberCarsOwned]
      ,[CommuteDistance]
      ,[Region]
      ,[Age]
      ,[BikeBuyer]
  FROM [dbo].[vTargetMail]
```
7. 実験キャンバスの下にある **[実行]** をクリックして、実験を実行します。
8. [リーダー] モジュールの下部にある出力ポートをクリックし、**[視覚化]** を選択して、インポートしたデータを表示します。



## 手順 2. データをクリーニングする
モデルとの関連性が低い列をいくつか削除します。

1. **[プロジェクト列]** モジュールをキャンバスにドラッグします。
2. [プロパティ] ウィンドウの **[列セレクターの起動]** をクリックし、削除する列を指定します。
3. CustomerAlternateKey と GeographyKey の 2 つの列を除外します。



## 手順 3. モデルを作成する
データを 80 対 20 に分割し、80% を機械学習モデルのトレーニングに、20% をモデルのテストに使用します。今回の二項分類の問題には "2 クラス" アルゴリズムを使用します。

1. **[分割]** モジュールをキャンバスにドラッグします。
2. [プロパティ] ウィンドウの [最初の出力データセットにおける列の割合] に「0.8」を入力します。
3. **[2 クラス ブースト デシジョン ツリー]** モジュールをキャンバスにドラッグします。
4. **[モデルのトレーニング]** モジュールをキャンバスにドラッグし、次のように指定します。1 つ目の入力: ML アルゴリズム。2 つ目の入力: アルゴリズムをトレーニングするためのデータ。
5. [プロパティ] ウィンドウの **[列セレクターの起動]** をクリックし、モデルが予測する列として BikeBuyer を指定します。


## 手順 4. モデルにスコアを付ける
ここでは、テスト データに対するモデルのパフォーマンスをテストします。選択したアルゴリズムを別のアルゴリズムと比較し、どちらのパフォーマンスが優れているかを評価します。

1. **[モデルのスコア付け]** モジュールをキャンバスにドラッグします。1 つ目の入力: トレーニング済みのモデル、2 つ目の入力: テスト データ
2. **[2 クラスのベイズ ポイント マシン]** を実験キャンバスにドラッグします。このアルゴリズムのパフォーマンスを 2 クラスのブースト デシジョン ツリーのパフォーマンスと比較します。
3. [モデルのトレーニング] モジュールと [モデルのスコア付け] モジュールをコピーしてキャンバスに貼り付けます。
4. **[モデルの評価]** モジュールをキャンバスにドラッグし、2 つのアルゴリズムを比較します。
5. 実験を**実行**します。
6. [モデルの評価] モジュールの下部にある出力ポートをクリックし、[視覚化] をクリックします。


指定されているメトリックは、ROC 曲線、精度/再現率図、およびリフト曲線です。これらのメトリックを見ると、最初に実行されたモデルの方が 2 つ目のモデルよりもパフォーマンスが優れていることがわかります。1 つ目のモデルが予測した内容を確認するには、[モデルのスコア付け] の出力ポートをクリックし、[視覚化] をクリックします。


テスト データセットに追加された 2 つの列が表示されます。

- スコア付け確率: 顧客が自転車を購入する可能性
- スコア付けラベル: モデルによって行われた分類 - 自転車を購入する顧客 (1) か、購入しない顧客 (0)ラベル付けの確率のしきい値は 50% に設定されており、調整できます。

[BikeBuyer] 列 (実際) をスコア付けラベル (予測) と比較すると、モデルのパフォーマンスがどの程度優れていたかを評価できます。次のステップとして、このモデルを使用して新規顧客の予測を行い、Web サービスとしてこのモデルを発行したり、SQL Data Warehouse に結果を書き戻したりできます。

詳細については、[Azure での機械学習の概要][]に関するページを参照してください。



<!--Article references-->
[Azure Machine Learning studio]: https://studio.azureml.net/
[Azure での機械学習の概要]: ../machine-learning/machine-learning-what-is-machine-learning.md
[サンプル データを手動で読み込む]: ./sql-data-warehouse-get-started-manually-load-samples.md
[SQL Data Warehouse の作成]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO4-->