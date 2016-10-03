<properties 
	pageTitle="Team Data Science Process 用に Hadoop クラスターをカスタマイズ | Microsoft Azure" 
	description="一般的な Python モジュールは、カスタムの Azure HDInsight Hadoop クラスターで利用できます。"
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="hangzh;bradsev" />

# Team Data Science Process 用に Azure HDInsight Hadoop クラスターをカスタマイズ 

この記事では、HDInsight サービスでクラスターをプロビジョニングするときに各ノードで 64 ビット Anaconda (Python 2.7) をインストールして、HDInsight Hadoop クラスターをカスタマイズする方法について説明します。また、クラスターにカスタム ジョブを送信するためにヘッドノードにアクセスする方法も示します。このカスタマイズでは、便宜上 Anaconda に含まれる多くの一般的な Python モジュールを、クラスター内のハイブ レコードを処理するように設計されたユーザー定義関数 (UDF) で使用できるようにします。このシナリオで用いている手順については、「[Hive クエリを送信する方法](machine-learning-data-science-move-hive-tables.md#submit)」を参照してください。

次のメニューは、[Team Data Science Process (TDSP)](data-science-process-overview.md) で使用されるさまざまなデータ サイエンス環境の設定方法を説明するトピックにリンクしています。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]


## <a name="customize"></a>Azure HDInsight Hadoop クラスターのカスタマイズ

カスタマイズされた HDInsight Hadoop クラスターをユーザーが作成するには、[**Azure クラシック ポータル**](https://manage.windowsazure.com/)にログオンし、左下隅にある **[新規]** をクリックした後、[DATA SERVICES] -> [HDINSIGHT] -> **[カスタム作成]** を選択して **[クラスターの詳細]** ウィンドウを表示する必要があります。

![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

構成ページ 1 で作成したクラスターの名前を入力し、他のフィールドの既定値をそのまま使用します。矢印をクリックして、次の構成ページに移動します。

![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

構成ページ 2 で **[データ ノード]** の番号を入力し、**[リージョン/仮想ネットワーク]** を選択して、**[ヘッド ノード]** と **[データ ノード]** のサイズを選択します。矢印をクリックして、次の構成ページに進みます。

>[AZURE.NOTE] **[リージョン/仮想ネットワーク]** は、HDInsight Hadoop クラスターに使用するストレージ アカウントのリージョンと同じにする必要があります。同じにしないと、4 番目の構成ページで、ユーザーが使用するストレージ アカウントが **[アカウント名]** ドロップダウン リストに表示されません。

![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png)

構成ページ 3 では、HDInsight Hadoop クラスターのユーザー名とパスワードを指定します。**[Hive/Oozie メタストアの入力]** は選択しないでください。矢印をクリックして、次の構成ページに進みます。

![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png)

構成ページ 4 では、ストレージ アカウント名 (HDInsight Hadoop クラスターの既定のコンテナー) を指定します。ユーザーが **[既定のコンテナー]** ドロップダウン リストで _[既定のコンテナーの作成]_ を選択した場合、クラスターと同じ名前のコンテナーが作成されます。矢印をクリックして、最後の構成ページに進みます。

![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png)

最後の **[スクリプトのアクション]** 構成ページで **[スクリプト アクションの追加]** ボタンをクリックし、テキスト フィールドに次の値を入力します。
 
* **[名前]** - このスクリプト アクションの名前として任意の文字列を入力します。
* **[ノード タイプ]** - **[すべてのノード]** を選択します。
* **スクリプト URI** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1*
	* *publicscripts* は、ストレージ アカウントのパブリック コンテナーです。
	* *getgoing* は、Azure でユーザーが作業しやすくなるように、PowerShell スクリプト ファイルを共有するために使用します。
* **[パラメーター]** - (空白のままにします)

最後に、チェック マークをクリックして、カスタマイズされた HDInsight Hadoop クラスターの作成を開始します。

![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a>Hadoop クラスターのヘッド ノードへのアクセス

ユーザーが RDP を介して Hadoop クラスターのヘッド ノードにアクセスするには、その前に Azure での Hadoop クラスターへのリモート アクセスを有効にする必要があります。

1. [**Azure クラシック ポータル**](https://manage.windowsazure.com/)にログインし、左側で **[HDInsight]** を選択して、クラスターのリストから Hadoop クラスターを選択します。次に、**[構成]** タブをクリックし、ページの下部にある **[リモートを有効にする]** アイコンをクリックします。
	
	![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png)

2. **[リモート デスクトップの構成]** ウィンドウで、[ユーザー名]、[パスワード] の各フィールドに入力し、リモート アクセスの有効期限を選択します。チェック マークをクリックして Hadoop クラスターのヘッド ノードへのリモート アクセスを有効にします。

	![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png)
	
>[AZURE.NOTE] リモート アクセスのユーザー名とパスワードは、Hadoop クラスターを作成したときに使用するユーザー名とパスワードではありません。これらは別個の資格情報のセットです。また、リモート アクセスの有効期限は、現在の日付から 7 日以内にする必要があります。

リモート アクセスを有効にした後、ページ下部の **[接続]** をクリックして、ヘッド ノードにリモートで接続します。以前に指定したリモート アクセス ユーザーの資格情報を入力して、Hadoop クラスターのヘッド ノードにログオンします。

![Create workspace](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png)

高度な分析プロセスの次のステップは、「[Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)」に示されています。HDInsight にデータを移動し、Azure Machine Learning でデータの情報を取得する準備としてデータを処理してサンプリングする手順などがあります。

Hive レコードを処理するために使用されるクラスターに格納されたユーザー定義関数 (UDF) で、クラスターのヘッド ノードから Anaconda に含まれる Python モジュールにアクセスする方法については、「[Hive クエリを送信する方法](machine-learning-data-science-move-hive-tables.md#submit)」をご覧ください。

 

<!---HONumber=AcomDC_0921_2016-->