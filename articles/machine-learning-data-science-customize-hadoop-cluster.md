<properties 
	pageTitle="Azure HDInsight Hadoop Clusters for Data Science のカスタマイズ | Azure" 
	description="Azure HDInsight Hadoop Clusters for Data Science のカスタマイズ" metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="hangzh;bradsev" />

# Azure HDInsight Hadoop Clusters for Data Science のカスタマイズ

この記事では、HDInsight サービスでクラスターをプロビジョニングするときに各ノードで 64 ビット Anaconda (Python 2.7) をインストールして、HDInsight Hadoop クラスターをカスタマイズする方法について説明します。また、クラスターにカスタム ジョブを送信するためにヘッドノードにアクセスする方法も示します。

このカスタマイズでは、便宜上 Anaconda に含まれる多くの一般的な Python モジュールを、クラスター内のハイブ レコードを処理するように設計されたユーザー定義関数 (UDF) で使用できるようにします。このシナリオで使用される手順の詳細については、[クラウド データの科学プロセスにおける HDInsight Hadoop クラスターへのハイブ クエリの送信](machine-learning-data-science-hive-queries.md)を参照してください。

## <a name="customize"></a>Azure HDInsight Hadoop クラスターのカスタマイズ

カスタマイズされた HDInsight Hadoop クラスターをユーザーが作成するには、[**Azure の管理ポータル**](https://manage.windowsazure.com/) にログオンし、左下隅にある **[新規]** をクリックした後、**[データ サービス] -> [HDINSIGHT] -> [カスタム作成]** を選択して **[クラスターの詳細]** ウィンドウを表示する必要があります。 

![Create workspace][1]

構成ページ 1 で作成したクラスターの名前を入力し、他のフィールドの既定値をそのまま使用します。矢印をクリックして、次の構成ページに移動します。 

![Create workspace][2]

構成ページ 2 で **[データ ノード]** の番号を入力し、**[リージョン/仮想ネットワーク]** を選択して、**[ヘッド ノード]** および **[データ ノード]** のサイズを選択します。矢印をクリックして、次の構成ページに進みます。

>[AZURE.NOTE] **[リージョン/仮想ネットワーク]** は、HDInsight Hadoop クラスターに使用するストレージ アカウントのリージョンと同じにする必要があります。同じにしないと、4 番目の構成ページで、ユーザーが使用するストレージ アカウントが **[アカウント名]** ドロップダウン リストに表示されません。

![Create workspace][3]

構成ページ 3 では、HDInsight Hadoop クラスターのユーザー名とパスワードを指定します。****[Hive/Oozie メタストアの入力] は選択しないでください。矢印をクリックして、次の構成ページに進みます。 

![Create workspace][4]

構成ページ 4 では、ストレージ アカウント名 (HDInsight Hadoop クラスターの既定のコンテナー) を指定します。ユーザーが **[既定のコンテナー]** ドロップダウン リストで [既定のコンテナーの作成] を選択した場合、クラスターと同じ名前でコンテナーが作成されます。矢印をクリックして、最後の構成ページに進みます。

![Create workspace][5]

最後の **[スクリプトのアクション]** 構成ページで **[スクリプト アクションの追加]** ボタンをクリックし、テキスト フィールドに次の値を設定します。
 
* **名前** - このスクリプト アクションの名前として任意の文字列を設定します。 
* **ノード タイプ** - **[すべてのノード]** を選択します。 
* **スクリプト URI** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
	* *publicscripts* はストレージ アカウントのパブリック コンテナーです。 
	* *getgoing* は、Azure でユーザーが作業しやすくなるように、PowerShell スクリプト ファイルを共有するために使用します。 
* **パラメーター** - (空白のままにします)

最後に、チェック マークをクリックして、カスタマイズされた HDInsight Hadoop クラスターの作成を開始します。 

![Create workspace][6]

## <a name="headnode"></a> Hadoop クラスターのヘッド ノードへのアクセス

ユーザーが RDP を介して Hadoop クラスターのヘッド ノードにアクセスするには、その前に Azure での Hadoop クラスターへのリモート アクセスを有効にする必要があります。 

1. [**Azure 管理ポータル**](https://manage.windowsazure.com/) にログインし、左側で **[HDInsight]** を選択して、Hadoop クラスターをクラスターのリストから選択します。その後、**[構成]** タブをクリックして、ページの下部にある **[リモートを有効にする]** アイコンをクリックします。
	
	![Create workspace][7]

2. **[リモート デスクトップの構成]** ウィンドウで、[ユーザー名] と [パスワード] フィールドに入力し、リモート アクセスの有効期限を選択します。チェック マークをクリックして Hadoop クラスターのヘッド ノードへのリモート アクセスを有効にします。
	
	>[AZURE.NOTE] 
	>
	>1. リモート アクセスのユーザー名とパスワードは、Hadoop クラスターを作成したときに使用するユーザー名とパスワードではありません。これらは別個の資格情報のセットです。
	>
	>2. リモート アクセスの有効期限は、現在の日付から 7 日以内にする必要があります。

	![Create workspace][8]

3. リモート アクセスを有効にした後、ページ下部の **[接続]** をクリックして、ヘッド ノードにリモートで接続します。以前に指定したリモート アクセス ユーザーの資格情報を入力して、Hadoop クラスターのヘッド ノードにログオンします。

	 ![Create workspace][9]

次に、ハイブ レコードを処理するために使用されるクラスターに格納されたユーザー定義関数 (UDF) で、クラスターのヘッド ノードから Anaconda に含まれる Python モジュールにアクセスする方法については、[クラウド データの科学プロセスにおける HDInsight Hadoop クラスターへの Hive クエリの送信](machine-learning-data-science-hive-queries.md)を参照してください。

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png


<!--HONumber=49-->