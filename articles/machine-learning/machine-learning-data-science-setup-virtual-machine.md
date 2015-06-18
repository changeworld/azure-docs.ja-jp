<properties 
	pageTitle="データ サイエンスのための Azure 仮想マシンのセットアップ" 
	description="IPython Server を含むクラウド ベースのデータ サイエンス環境で使用するために、Azure 仮想マシンをセットアップします。" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# データ サイエンスのための Azure 仮想マシンのセットアップ

このトピックでは、クラウド ベースのデータ サイエンス環境の一部として使える Azure 仮想マシンをプロビジョニングおよび構成する方法について説明します。IPython Notebook、Azure Storage Explorer、AzCopy などのサポート ツールに加えて、データ サイエンス プロジェクトに役立つ他のユーティリティのある Windows 仮想マシンを構成します。たとえば Azure Storage Explorer および AzCopy には、ローカル マシンから Azure BLOB ストレージにデータをアップロードしたり、BLOB ストレージからローカル マシンにデータをダウンロードしたりするための便利な機能が備わっています。

## <a name="create-vm"></a>手順 1:汎用の Azure 仮想マシンを作成する

Azure 仮想マシンが既にあり、単に IPython Notebook サーバーをセットアップするだけの場合は、このステップを省略して[手順 2IPython Notebook 用のエンドポイントを既存の仮想マシンに追加する](#add-endpoint)に進むことができます。 
 
Azure で仮想マシンを作成するプロセスを始める前に、プロジェクトのデータ処理に必要なマシンのサイズを決定する必要があります。小さいマシンは大きいマシンに比べてメモリの量と CPU コア数が少ないですが、より安価でもあります。マシンの種類と価格については、「[Virtual Machines 料金](http://azure.microsoft.com/pricing/details/virtual-machines/)」ページを参照してください。 

1. https://manage.windowsazure.com にログインして、左下隅の **[新規]** をクリックします。ウィンドウがポップアップ表示されます。**[コンピューティング]** -> **[仮想マシン]** -> **[ギャラリーから]** の順にクリックします。

	![Create workspace][24]

2. 次のいずれかのイメージを選択します。 

	* Windows Server 2012 R2 Datacenter 
	* Windows Server Essentials エクスペリエンス (Windows Server 2012 R2) 

	その後、右下にある右向きの矢印をクリックして、次の構成ページに進みます。
	
	![Create workspace][25]

3. 作成する仮想マシンの名前を入力し、マシンで処理するデータのサイズおよびマシンの想定機能 (メモリ サイズと CPU コア数) に基づいてマシンのサイズを選択し、マシンのユーザー名とパスワードを入力します。その後、右向きの矢印をクリックして、次の構成ページに進みます。

	![Create workspace][26]

4. この仮想マシンに使用する予定の **[ストレージ アカウント]** を含んでいる **[リージョン/アフィニティ グループ/仮想ネットワーク]** を選択して、そのストレージ アカウントを選択します。下部にある **[エンドポイント]** フィールドで、エンドポイントの名前 (ここでは「IPython」) を入力してエンドポイントを追加します。エンドポイントの **[名前]** として任意の文字列を選択し、**[パブリック ポート]** として**使用可能な** 0 から 65536 までの任意の整数を選択できます。**[プライベート ポート]** は **9999** でなければなりません。ユーザーは、インターネット サービスに既に割り当てられているプライベート ポートの使用を**避ける**必要があります。[インターネット サービス用のポート](http://www.chebucto.ns.ca/~rakerman/port-table.html)には、既に割り当てられている (したがって使用を避けるべき) ポートの一覧が示されます。 

	![Create workspace][27]

	>[AZURE.NOTE] このステップでエンドポイントを追加する場合、「[手順 2: IPython Notebook 用のエンドポイントを既存の仮想マシンに追加する](#add-endpoint)」を省略できます。

5. 仮想マシンのプロビジョニング プロセスを開始するには、チェック マークをクリックします。 

	![Create workspace][28]


仮想マシンのプロビジョニング プロセスが完了するには、15 ～ 25 分かかる場合があります。仮想マシンが作成された後、このマシンの状態が **[実行中]** と表示されるはずです。

![Create workspace][29]
	
## <a name="add-endpoint"></a>手順 2:IPython Notebook 用のエンドポイントを既存の仮想マシンに追加する

「手順 1」の指示に従って仮想マシンを作成した場合、IPython Notebook 用のエンドポイントは既に作成されており、このステップを省略できます。 

仮想マシンが既に存在する場合、下記の「手順 3」でインストールする IPython Notebook 用のエンドポイントを追加する必要があれば、まず Azure 管理ポータルにログインし、仮想マシンを選択して、IPython Notebook サーバー用のエンドポイントを追加します。次の図は、IPython Notebook 用のエンドポイントを Windows 仮想マシンに追加した後のポータルのスクリーン ショットを示しています。 

![Create workspace][17]

## <a name="run-commands"></a>手順 3:IPython Notebook とその他のサポート ツールをインストールする

仮想マシンの作成後、リモート デスクトップ プロトコル (RDP) を使用して Windows 仮想マシンにログオンします。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法](../virtual-machines-log-on-windows-server.md)」を参照してください。管理者として **[コマンド プロンプト]** (**Powershell コマンド ウィンドウではない**) を開き、次のコマンドを実行します。
 
    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

インストールが完了すると、 *C:\Users\&#60;user name>\Documents\IPython Notebooks* ディレクトリ内で IPython Notebook サーバーが自動的に起動します。

メッセージが表示されたら、IPython Notebook のパスワードと、マシン管理者のパスワードを入力します。これにより、IPython Notebook をマシン上のサービスとして実行させることができます。 

## <a name="access"></a>手順 4:Web ブラウザーから IPython Notebook にアクセスする
IPython Notebook サーバーにアクセスするには、Web ブラウザーを開いて URL テキスト ボックスに「 *https://&#60;virtual machine DNS name>:&#60;public port number>*」と入力します。ここでは、*&#60;pパブリック ポート番号>* として、IPython Notebook エンドポイントの追加時に指定したポート番号を入力してください。パブリック ポート番号として *443* を選択した場合、URL テキスト ボックスに明示的にポート番号を指定せずに IPython Notebook にアクセスできます。それ以外の場合は、**&#60;パブリック ポート番号>* が必要です。 

*&#60;仮想マシン DNS 名>* は、Azure 管理ポータルで確認できます。管理ポータルにログインした後、**[仮想マシン]** をクリックし、作成済みのマシンを選択して **[ダッシュ ボード]** を選択すると、DNS 名が次のように表示されます。

![Create workspace][19]

次の図のように、「この Web サイトのセキュリティ証明書には問題があります」(Internet Explorer) または「この接続ではプライバシーが保護されません」 (Chrome) という警告が表示されます。Internet Explorer の場合は **[このサイトの閲覧を続行する (推奨されません)] **、Chrome の場合は **[詳細設定]**、**[&#60;*DNS 名*> にアクセスする (安全ではありません)]** をクリックして続行します。次に、既に指定したパスワードを入力して IPython Notebook にアクセスします。

Internet Explorer:
![Create workspace][20]

Chrome:
![Create workspace][21]

IPython Notebook にログオンした後、ディレクトリ   *DataScienceSamples* がブラウザーに表示されます。このディレクトリには、ユーザーがデータ サイエンス タスクを実行するうえで役立つ、Microsoft が共有するサンプル IPython Notebook が含まれています。これらのサンプル IPython Notebook は、IPython Notebook サーバーのセットアップ プロセス中に [**Github リポジトリ**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)から仮想マシンにチェックアウトされます。Microsoft はこのリポジトリを保守し、頻繁に更新しています。ユーザーは Github リポジトリを参照して、最近更新されたサンプル IPython Notebook を入手することができます。 
![Create workspace][18]

## <a name="upload"></a>手順 5:既存の IPython Notebook をローカル マシンから IPython Notebook サーバーにアップロードする

IPython Notebook を使用すると、ユーザーはローカル マシン上の既存の IPython Notebook を仮想マシン上の IPython Notebook サーバーに簡単にアップロードできます。ユーザーは Web ブラウザーで IPython Notebook にログオンした後、IPython Notebook のアップロード先となる**ディレクトリ**にクリックして移動します。次に、ローカル マシンからアップロードする IPython Notebook の .ipynb ファイルを **[ファイル エクスプ ローラー]** で選択し、Web ブラウザーの IPython Notebook ディレクトリまでドラッグ アンド ドロップします。**[アップロード]** ボタンをクリックすると .ipynb ファイルが IPython Notebook サーバーにアップロードされます。その後、他のユーザーは自分の Web ブラウザーからそれを使用できるようになります。

![Create workspace][22]

![Create workspace][23]


## <a name="shutdown"></a>使用されていないときは、仮想マシンをシャット ダウンして割り当てを解除する

Azure Virtual Machines は、**従量課金制**で課金されます。仮想マシンを使用しないときに課金されないようにするには、非使用時に **[停止 (割り当て解除)]** 状態にする必要があります。

> [AZURE.NOTE] (Windows 電源オプションを使用して) 仮想マシン内部から仮想マシンをシャット ダウンすると、その仮想マシンは停止しますが、割り当てられた状態のままになります。継続的に課金されないようにするには、常に [Azure 管理ポータル](http://manage.windowsazure.com/)から仮想マシンを停止してください。また、Powershell で VM を停止することもできます。その際、**ShutdownRoleOperation** 呼び出しで "PostShutdownAction" を "StoppedDeallocated" にしてください。

仮想マシンをシャット ダウンして割り当て解除するには、次のようにします。

1. アカウントを使用して [Azure 管理ポータル](http://manage.windowsazure.com/) にログインします。  

2. 左側のナビゲーション バーから **[仮想マシン]** を選択します。

3. 仮想マシンの一覧で、 仮想マシンの名前をクリックして **[ダッシュボード]** ページに移動します。

4. ページの下部にある **[シャット ダウン]** をクリックします。 

![VM Shutdown][15]

仮想マシンの割り当てが解除されますが、削除はされません。Azure 管理ポータルから、いつでも仮想マシンを再起動することができます。

## Azure VM を使用する準備ができました。次のステップは ...

これで、仮想マシンをデータ サイエンス演習で使用する準備ができました。また、仮想マシンを IPython Notebook サーバーとして使用し、データの探索と処理など、Azure Machine Learning およびクラウド データ サイエンス プロセスに関連するタスクを行う準備もできました。 

データ サイエンス プロセスにおける次のステップは、「[学習ガイド: Azure での高度なデータ処理](machine-learning-data-science-advanced-data-processing.md)」に示されています。これには、HDInsight にデータを移動して、Azure Machine Learning でデータから知見を得るための準備としてデータを処理してサンプリングするための手順が含まれています。

* Azure BLOB ストレージから HDInsight にデータを移動する方法については、「[データを作成して Azure BLOB ストレージから Hive テーブルに読み込む](machine-learning-data-science-hive-tables.md)」を参照してください。
* Hive クエリを使用して HDInsight でデータを処理する方法については、「[クラウド データ サイエンス プロセスで HDInsight Hadoop クラスターに Hive クエリを送信する](machine-learning-data-science-process-hive-tables.md)」を参照してください。
* HDInsight でのデータのサンプリングについては、「[Azure HDInsight Hive テーブル内のデータのサンプリング](machine-learning-data-science-sample-data-hive.md)」を参照してください。



[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png




<!--HONumber=49--> 