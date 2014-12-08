<properties title="Azure Machine Learning Sample: Prediction of student performance" pageTitle="Machine Learning サンプル: 学生の成績の予測 | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts student performance on tests." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning サンプル: 学生の成績の予測

>[AZURE.NOTE]
>ML Studio には、このモデルに対応した[サンプル実験]と[サンプル データセット]が用意されています。詳細については、以下を参照してください。
[サンプル実験]: #sample-experiment
[サンプル データセット]: #sample-dataset


##データセットと問題の説明
この実験で使用するデータセットは、KDD Cup 2010 の Algebra_2008_2009 トレーニング セットです。このデータセットは、[https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp](https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp) からダウンロードできます。このデータセットには、学生のチュートリアル システムのログ ファイルが含まれています。提供される特徴には、問題 ID とその簡潔な説明、学生 ID、タイムスタンプ、学生が問題を正しい方法で解くまでにかかった回数があります。学習の問題は、学生が出題された問題を 1 回目で解くことができるかどうかを予測することです。KDD Cup のルールに従って、[二乗平均平方根誤差](http://en.wikipedia.org/wiki/Root-mean-square_deviation) (RMSE) を使用して学習アルゴリズムの精度を測定します。元のデータセットには 890 万件のレコードが含まれます。実験の速度を上げるために、データセットを先頭の 100,000 行にダウンサンプリングしました。このデータセットには、数値、カテゴリ、タイムスタンプなど、さまざまな型の列が 23 列あります。列はタブ区切りになっています。
 
##開発ワークフロー
このデータセットには、**"R 操作の適用"** モジュールによって処理できない非 ASCII 文字が含まれています。Passau でデータセットを使用する前に、次の PowerShell コマンドを使って非 ASCII 文字を削除しました。  

	gc algebra_2008_2009_train.txt -head 100000 | sc algebra_train_small.txt
	sc tmp.txt -Encoding ASCII -Value (gc algebra_train_small.txt)
	cat tmp.txt | %{$_ -replace "\?\?sqrt","+sqrt"} | sc algebra_train_small.txt_ascii  

結果のファイル algebra_train_small.txt_ascii はまだ非常に大きく、36 MB もあります。このファイルは Azure BLOB ストレージに格納し、**"リーダー"** モジュールを使用して実験に読み込みます。ファイルを BLOB ストレージに格納するための PowerShell コマンドは次のとおりです。  

	Add-AzureAccount
	$key = Get-AzureStorageKey -StorageAccountName <your storage account name>
	$ctxt = New-AzureStorageContext -StorageAccountName $key.StorageAccountName -StorageAccountKey $key.Primary
	Set-AzureStorageBlobContent -Container <container name in your storage account> -File "algebra_train_small.txt_ascii" -Context $ctxt

![][1]
 
**"リーダー"** モジュールのパラメーターは上記のとおりです。この例では、ストレージ アカウント名は "datascience" で、データセット ファイル algebra_train_small.txt_ascii はコンテナー "sampleexperiments" 内にあります。アカウント キーは Azure Storage アカウントのアクセス キーです。このキーは Azure の管理ポータル ([https://manage.windowsazure.com](https://manage.windowsazure.com)) でアカウントから取得できます。
 
![][2]
 
次のステップでは、上記のとおり、さまざまな変換処理を施してデータセットを学習アルゴリズムに適した形式にします。**メタデータ エディター**を使用して、タイムスタンプ列の "First Transaction Time" を文字列形式に変換します。これは、トレーニング/テストの分割を生成するうえで必要な処理です。次に、**"プロジェクト列"** を使用して、実験で使用しないさまざまな列を削除します。"不足値スクラブ" を使用して、不足値をすべて 0 に置き換えます。次のステップでは、"Unit Name, Section Name" 列を "Unit Name" と "Section Name" の 2 列に分割します。これを行うには、**"R を実行"** モジュールで次の R コードを使用します。  

	dataset <- maml.mapInputPort(1)
	b <- data.frame(do.call(rbind,strsplit(as.vector(dataset[,3]),",")))
	names(b) <- c("Unit Name","Section Name")
	data.set <- cbind(dataset[,1:2],b,dataset[,4:15])
	maml.mapOutputPort("data.set")  

 
データの読み込みと最初の変換を含む実験の最初の手順は、上記のとおりです。データの処理後に実験を 4 つの分岐に分割し、各分岐で異なる特徴のセットをテストします。一部の特徴セットは [1] で使用したものです。以下に示す最初の分岐で使用する特徴のセットは、StudentID と Unit です。  

![][3]  
![][4]
 
 
Name、Section Name、Problem Name、Problem View、Opportunity の各特徴と、問題の説明テキストです。整数以外の特徴は、カテゴリの特徴として表されます。この分岐では、まず **"プロジェクト列"** を使用して Hints 列を削除します。新しい学生の成功を予想する際に、Hints 列は使用できないためです。次に、**"分割"** モジュールを使用して、データセットをトレーニング セットとテスト セットに分割します。どの例にもタイム スタンプがあるため、時間を基に分割します。2008 の First Transaction Time が含まれる行はすべてトレーニング セットに含まれ、2009 のものはテスト セットに含まれます。**"分割"** モジュールのパラメーターは右側に示されています。分割後、ブースト デシジョン ツリーを使用して二項分類モデルを生成し、テスト データのスコアを計算します。**"モデルのトレーニング"** モジュールのラベル列は "Correct First Attempt" です。  

![][5]  

 
上記の **"数値演算の適用"** モジュールと **"基本統計"** モジュールの次のシーケンスでは、未加工のスコアと true ラベルを使用して、モデルの RMSE を計算します。回帰モデルの場合は、このメトリックは **"エバリュエーター"** モジュールで計算します。それ以外のモデルの場合は、このメトリックは手動で計算する必要があります。最初の **"数値演算の適用"** モジュールでは、**"モデルのスコア付け"** モジュールで生成したスコア列とラベル列の誤差を計算します。2 番目の **"数値演算の適用"** モジュールでは、この誤差の 2 乗を計算します。次に、**"基本統計"** モジュールで二乗誤差の平均を計算します。さらに、最後の **"数値演算の適用"** モジュールで平方根を計算します。これら 4 つのモジュールのパラメーターは以下のとおりです。  

![][6] 
![][7]  
![][8]  
![][9] 
 
 
上記の 2 つ目の分岐では、時間ディメンションも活用します。最初の分岐の特徴に加えて、ユーザーが解いている問題の最後の 2 ステップの名前も、その説明と共に使用します。提供されたデータセットでは、すべてのユーザーのアクティビティがタイムスタンプに基づいて昇順で格納されています。また、提供されたデータセットではユーザーのアクティビティはインターリーブされていないので、先頭に最初のユーザーのすべての行が存在し、次に 2 番目のユーザーのすべての行が存在します。それ以降のユーザーについても同様です。したがって、ユーザーの最後のステップを見つけるために、RowID 列を利用します。固定ユーザーの場合は、この列は時間軸として役立ちます。この列に 1 を追加し、"数値演算の適用****" を使用することで、各行を一度に 1 ずつ前へシフトさせます。次に、**"結合"** モジュールを使用して、元のデータセットをシフトしたデータセットと結合します。その際、RowID、StudentID、ProblemName をキーとして使用します。その結果、StudentID と ProblemName は同じで、時刻 t と t-1 のレコードが各行に含まれる拡張データセットができます。*左外部結合*を使用して、StudentID と ProblemID が同じで前のステップを含まない行を保持します。**"数値演算の適用"** と **"結合"** を組み合わせたものをもう一度適用して、2 ステップ前の特徴を取得します。**"数値演算の適用"** と **"結合"** の正確なパラメーターは以下のとおりです。  

![][10]  
![][11]  
![][12]  

 
これらの処理を実行すると、さまざまな列が同じになります。たとえば、"結合" モジュールの使用により、ProblemName 列は 3 回複製されます (ステップ t、t-1、t-2)。重複した列は、**"プロジェクト列"** モジュールを使用して削除します。さらに、*左外部結合*を使用したので、結合演算子によって生成された行の一部に値が存在しない可能性があります。そこで、**"不足値スクラブ"** を使用して、すべての不足値を文字列 "0" に置き換えます。**"不足値スクラブ"** モジュールのパラメーターは上記のとおりです。  

特徴の新しいセットを計算した後、2 番目の分岐のワークフローとして、最初の分岐と同じものを実行します。  

![][13]
 
3 番目の分岐では、2 番目の分岐で使用した特徴に加えて、最初の分岐の元の特徴を連結した二次特徴と三次特徴も使用します。二次特徴と三次特徴を計算するには、前に示した R コードを **"R 操作の実行"** モジュールで使用します。新しい特徴セットを計算したら、最初の 2 つの分岐と同じようにトレーニング、スコア付け、評価を行います。  

4 番目の分岐では、最初の 3 つの分岐とはまったく異なる特徴を使用します。StudentID ごとに、時刻 t までの (t は含まない) "Correct First Attempt" の平均値を求めます。この値は、CFA(StudentID,t) と表記します。同様に、Hints(StudentID,t) は時刻 t までの (t は含まない) 固定 StudentID に対する Hints の平均値を表します。これらの平均値の計算を高速化するために、データセット全体は検討せず、時刻 t までの直近の 10 レコードのみを確認します。CFA(Problem Name,t)、CFA(Step Name,t)、CFA(Description,t)、Hints(Problem Name,t)、Hints(Step Name,t)、Hints(Description,t) の定義も似ています。First Transaction Time が t(x) で、列値が StudentID(x)、Problem Name(x)、Step Name(x)、Description(x) という例 x の場合に、上の 8 つの CFA および Hints の特徴を生成します。  

	CFA(StudentID(x),t(x)), CFA(Problem Name(x),t(x)), CFA(Step Name(x),t(x)), CFA(Description(x),t(x)), 
	Hints(StudentID(x),t(x)), Hints(Problem Name(x),t(x)), Hints(Step Name(x),t(x)), Hints(Description(x),t(x))  

![][14]  

 
同様に、StudentID と Problem Name、Problem Name と Step Name、StudentId と UnitName、StudentID と Problem Description という組み合わせを使用して、さらに 8 つの CFA と Hints の特徴も計算します。特徴は合計で 16 個となり、これらを Correct First Attempt 列の値を予測するために使用します。これら 16 個の特徴は、**"R 操作の実行"** モジュールで R コードを使用して計算します。このコードは長くて冗長ですが、高度に最適化されています。これらの特徴を計算した後で、R コードによって追加されたいくつかの補助的な列を削除します。そのために、**"プロジェクト列"** モジュールを使用します。4 番目の分岐で特徴を計算するための完全なワークフローは、上に示されています。新しい特徴セットを計算したら、最初の 3 つの分岐と同じようにトレーニング、スコア付け、評価を行います。  

4 つの分岐すべてで RMSE 値を計算したら、**"行の追加"** モジュールを使用して結果を収集します。**"R の実行"** モジュールを使用して注釈も生成します。この実験の最後の部分のワークフローは、以下のとおりです。  

![][15]  
 
 
実験の最後の出力を次に示します。1 列目が特徴セットの名前で、2 列目がテストの例で測定した RSME です。  

![][16]
 

4 番目の特徴のセットで RMSE が最も小さくなりました。  

##参照
H.-F. Yu ほか、Feature Engineering and Classifier Ensemble for KDD Cup 2010、KDD Cup 2010 Workshop、2010 年



## サンプル実験

ML Studio には、このモデルに対応した次のサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。

> **サンプル実験 - 学生の成績 - 開発**


## サンプル データセット

ML Studio には、この実験で使用される次のサンプル データセットが用意されています。モジュール パレットの **[保存されたデータセット]** にあります。

###student_performance.txt
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../includes/machine-learning-sample-dataset-student-performance.md)]



[1]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-1.jpg
[2]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-2.jpg
[3]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-3.jpg
[4]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-4.jpg
[5]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-5.jpg
[6]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-6.jpg
[7]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-7.jpg
[8]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-8.jpg
[9]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-9.jpg
[10]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-10.jpg
[11]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-11.jpg
[12]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-12.jpg
[13]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-13.jpg
[14]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-14.jpg
[15]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-15.jpg
[16]: ./media/machine-learning-sample-prediction-of-student-performance/student-performance-16.jpg
