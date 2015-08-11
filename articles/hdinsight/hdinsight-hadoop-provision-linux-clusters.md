<properties
   pageTitle="HDInsight での Hadoop クラスター (Linux ベース) のプロビジョニング | Microsoft Azure"
   description="管理ポータル、コマンド ライン、.NET SDK を使用して、HDInsight 向けに Linux で Hadoop クラスターをプロビジョニングする方法を説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/17/2015"
   ms.author="nitinme"/>


#カスタム オプションを使用した HDInsight での Hadoop Linux クラスターのプロビジョニング (プレビュー)

この記事では、Azure ポータル、Azure PowerShell、Azure CLI、または HDInsight .NET SDK を使用して、Azure HDInsight での Hadoop Linux クラスターのプロビジョニングをカスタマイズするさまざまな方法について説明します。

## HDInsight クラスターについて

Hadoop またはビッグ データを説明するときに、クラスターについて必ず触れるのはなぜでしょうか。 その理由は、Hadoop がクラスターのさまざまなノードにまたがる大規模データの分散処理を可能にするためです。クラスターは、1 つのマスター (ヘッドノードまたは名前ノードともいいます) と任意の数のワーカー ノード (データ ノードともいいます) から成るマスター / ワーカー アーキテクチャを構成しています。詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop に関する Web ページ</a>」をご覧ください。

![HDInsight Cluster][img-hdi-cluster]


HDInsight クラスターでは、Hadoop の実装の詳細を抽象化しているため、クラスターの別のノードとの通信方法を心配する必要はありません。HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。詳細については、「[HDInsight での Hadoop 入門](hdinsight-hadoop-introduction.md)」を参照してください。変更されるデータは、Azure Blob ストレージに格納されます。詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」をご覧ください。


この記事では、クラスターをプロビジョニングするさまざまな手順を示します。クラスターをすばやくプロビジョニングする方法を調べる場合は、「[Azure HDInsight (Linux) の概要](../hdinsight-hadoop-linux-get-started.md)」をご覧ください。

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- **Secure Shell (SSH) キー**。パスワードの代わりに SSH とキーを使用して、Linux クラスターにリモート接続する場合に必要です。キーはより安全性が高いことから、キーを使用する方法をお勧めします。SSH キーを生成する手順については、次の記事をご覧ください。
	-  Linux コンピューターの場合 - [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
	-  Windows コンピューターの場合 - [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a id="configuration"></a>構成オプション

### Linux 上のクラスター

HDInsight には、Azure で Linux クラスターをプロビジョニングするためのオプションが用意されています。Linux または Unix に詳しい場合や、Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、既存の Linux ベースの Hadoop ソリューションから移行することで Linux クラスターをプロビジョニングします。Linux での Azure HDInsight の詳細については、「[HDInsight での Hadoop 入門](hdinsight-hadoop-introduction.md)」をご覧ください


### その他のストレージ

構成中に、Azure BLOB ストレージ アカウントと既定のコンテナーを指定する必要があります。このコンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターに関連付ける追加の BLOB も指定することができます。


従属的な BLOB ストアの使用の詳細については、「[HDInsight の Hadoop での BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」をご覧ください。


### メタストア

メタストアには、Hive テーブル、パーティション、スキーマ、列などについての情報が格納されます。この情報は、データが Hadoop 分散ファイル システム (HDFS)、または HDInsight の Azure Blob ストレージのどこに格納されているかを見つけるために、Hive によって使用されます。既定では、Hive は組み込みデータベースを使用してこの情報を格納します。

HDInsight クラスターをプロビジョニングするとき、Hive のメタストアを格納する SQL データベースを指定します。これにより、クラスターを削除するとき、SQL データベースに外付けで格納されているメタデータ情報を保持できます。


> [AZURE.NOTE]現在、メタストアを使用するオプションは、.NET SDK を使用して Linux 向け HDInsight をプロビジョニングする場合にのみ使用できます。手順については、[.NET SDK を使用した HDInsight クラスター (Linux) のプロビジョニング](#sdk)をご覧ください



## <a id="options"></a>HDInsight Linux クラスターのプロビジョニング オプション

HDInsight Hadoop Linux クラスターは、Linux コンピューターと Windows ベースのコンピューターからプロビジョニングできます。次の表は、さまざまな OS から使用できるプロビジョニング オプションと、その各手順を記載したリンクを示しています。

この OS を実行するコンピューターからの Linux クラスターのプロビジョニング| Azure ポータルの使用 | Azure コマンド ライン インターフェイスの使用 | .NET SDK の使用 | Azure PowerShell の使用
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| [ここ](#portal)をクリック | [ここ](#cli)をクリック| 適用不可 | 適用不可
Windows | [ここ](#portal)をクリック | [ここ](#cli)をクリック | [ここ](#sdk)をクリック | [ここ](#powershell)をクリック

### <a id="portal"></a>Azure ポータルの使用

HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには、同じデータ センターにある Azure ストレージ アカウントが必要です。詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」をご覧ください。Azure ストレージ アカウントの作成の詳細については、「[ストレージ アカウントの作成方法](../storage-create-storage-account.md)」をご覧ください。


> [AZURE.NOTE]現在、HDInsight Linux クラスターをホストできるリージョンは、**東南アジア**、**北ヨーロッパ**、**米国東部**、**米国中南部**のみです。

**カスタム作成オプションを使用して HDInsight クラスターを作成するには**

1. [Azure ポータル][azure-management-portal]にサインインします。
2. ページ下部の **[+ 新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]**、**[カスタム作成]** の順にクリックします。
3. **[クラスターの詳細]** ページで、次の値を入力または選択します。

	![Hadoop HDInsight クラスターの詳細を提供](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>クラスター名</td>
			<td><p>クラスターの名前を指定します。</p>
				<ul>
				<li>Domain Name System (DNS) 名の最初と最後の文字は英数字にする必要があります。それ以外ではダッシュ (-) を使用できます。</li>
				<li>3 ～ 63 文字のクラスター名を入力できます。</li>
				</ul></td></tr>
		<tr><td>クラスターの種類</td>
			<td><strong>[Hadoop]</strong> を選択 します。</td></tr>
		<tr><td>オペレーティング システム</td>
			<td>Linux で HDInsight クラスターをプロビジョニングするには、<b>[Ubuntu 12.04 LTS Preview]</b> を選択します。Windows クラスターをプロビジョニングするには、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング</a>」をご覧ください。</td></tr>
		<tr><td>HDInsight のバージョン</td>
			<td>バージョンを選択します。Linux での HDInsight の場合、既定では HDInsight Version 3.2 で、これは Hadoop 2.6 を使用します。</td></tr>
		</table>

	表に示されている値を入力または選択し、右矢印をクリックします。

4. **[クラスターの構成]** ページで、次の値を入力または選択します。

	![Hadoop HDInsight クラスターの詳細を提供](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
	<tr><th>名前</th><th>値</th></tr>
	<tr><td>データ ノード</td><td>デプロイするデータ ノードの数です。テストでは、単一ノード クラスターを作成します。<br />クラスター サイズの制限は、Azure サブスクリプションによって変わります。制限値を上げるには、Azure の課金サポートにお問い合わせください。</td></tr>
	<tr><td>リージョン/仮想ネットワーク</td><td><p>作成済みのストレージ アカウントと同じリージョンを選択します。HDInsight クラスターは、同じリージョンに配置されたストレージ アカウントを必要とします。これ以後の構成作業では、ここで指定した地域と同じリージョンにあるストレージ アカウントしか選択できません。</p></td></tr>
	<tr><td>ヘッド ノード サイズ</td><td><p>ヘッド ノードの仮想マシン (VM) サイズを選択します。</p></td></tr>
	<tr><td>データ ノード サイズ</td><td><p>データ ノードの VM サイズを選択します。</p></td></tr>
	</table>

	>[AZURE.NOTE]VM の選択に基づき、料金が異なる場合があります。HDInsight は、クラスター ノードにすべて Standard レベルの VM を使用します。VM サイズに応じた料金の詳細については、「<a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 料金</a>」をご覧ください。


5. **[クラスター ユーザーの構成]** ページで、次の情報を指定します。

    ![Hadoop HDInsight クラスターのユーザーを指定](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>HTTP パスワード</td>
			<td>既定の HTTP ユーザーである <strong>admin</strong> のパスワードを指定します。</td></tr>
		<tr><td>SSH ユーザー名</td>
			<td>SSH ユーザー名を指定します。このユーザー名を使用して、HDInsight クラスター ノードでリモート SSH セッションを開始します。</td></tr>
		<tr><td>SSH 認証の種類</td>
			<td>SSH ユーザーの認証に、パスワードと SSH キーのどちらを使用するかを指定します。</td></tr>
		<tr><td>SSH パスワード</td>
			<td>認証の種類にパスワードを選択した場合、SSH ユーザーを認証するための SSH パスワードを指定します。リモート Linux マシンで SSH セッションを開始するときに、このパスワードの入力を求められます。</td></tr>
		<tr><td>SSH パブリック キー</td>
			<td>認証の種類にキーを選択した場合は、既に生成済みの SSH パブリック キーを指定します。Linux クラスターのノードで SSH セッションを開始した場合は、この公開キーに関連付けられた秘密キーを使用します。<br>
			Linux コンピューターで SSH キーを生成する手順については、<a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">こちら</a>をご覧ください。Windows コンピューターで SSH キーを生成する手順については、<a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">こちら</a>をご覧ください。
		</td></tr>
		<tr><td>Hive/Oozie メタストアの入力</td>
			<td>このチェックボックスをオンにして、クラスターと同じデータ センターにある Hive/Oozie メタストアとして使用する SQL データベースを指定します。このチェック ボックスを選択した場合、ウィザードの後続のページで Azure SQL データベースの詳細を指定する必要があります。これは、クラスターが削除された後でも Hive/Oozie ジョブに関するメタデータを保持する場合に便利です。</td></tr>
		</td></tr>
		</table>


	> [AZURE.NOTE]パスワード認証より安全であるため、SSH による SSH パブリック キー認証を使用することをお勧めします。

	右矢印をクリックします。

6. **[Configure Hive/Oozie Metastore]** ページで、次の情報を指定します。

    ![Hadoop HDInsight クラスターのユーザーを指定](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	Hive/OOzie のメタストアとして使用する Azure SQL データベースを指定します。Hive メタストアと Oozie メタストアの両方に同じデータベースを指定できます。この SQL データベースは、HDInsight クラスターと同じデータ センターにある必要があります。リスト ボックスの一覧には、<strong>[クラスターの詳細]</strong> ページで指定したデータ センターにある SQL データベースしか表示されません。また、ユーザー名とパスワードを指定し、選択した Azure SQL データベースに接続します。

    >[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

    右矢印をクリックします。


6. **[ストレージ アカウント]** ページで、次の値を指定します。


    ![HDInsight Hadoop クラスターに対してストレージ アカウントを指定](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>ストレージ アカウント</td>
			<td>HDInsight クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを指定します。次の 3 つのオプションのいずれかを選択できます。
			<ul>
				<li><strong>既存のストレージを使用する</strong></li>
				<li><strong>新しいストレージを作成する</strong></li>
				<li><strong>別のサブスクリプションのストレージを使用する</strong></li>
			</ul>
			</td></tr>
		<tr><td>アカウント名</td>
			<td><ul>
				<li>既存のストレージを使用する場合は、<strong>[アカウント名]</strong> で既存のストレージ アカウントを選択します。ドロップダウンの一覧には、クラスターをプロビジョニングする対象として選択したデータ センターにあるストレージ アカウントのみが表示されます。</li>
				<li><strong>[新しいストレージを作成する]</strong> または <strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合は、ストレージ アカウント名を指定する必要があります。</li>
			</ul></td></tr>
		<tr><td>アカウント キー</td>
			<td><strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合は、そのストレージ アカウントのアカウント キーを指定します。</td></tr>
		<tr><td>既定のコンテナー</td>
			<td><p>ストレージ アカウントの既定のコンテナーを指定します。既定のコンテナーは、HDInsight クラスターの既定のファイル システムとして使用されます。<strong>[ストレージ アカウント]</strong> フィールドで <strong>[既存のストレージを使用する]</strong> を選択したにもかかわらずそのアカウントに既存のコンテナーがない場合は、既定でクラスターと同じ名前のコンテナーが作成されます。クラスター名と同じ名前のコンテナーが既に存在する場合は、コンテナー名に連番が付加されます。たとえば、mycontainer1、mycontainer2、などとなります。ただし、既存のストレージ アカウントに指定したクラスター名と異なる名前のコンテナーがある場合は、そのコンテナーを使用できます。</p>
	        <p>新しいストレージの作成または別の Azure サブスクリプションのストレージの使用を選択した場合は、既定のコンテナー名を指定する必要があります。</p>
	    </td></tr>
		<tr><td>追加のストレージ アカウント</td>
			<td>HDInsight は複数のストレージ アカウントをサポートします。クラスターで使用できる追加ストレージ アカウントの数に制限はありません。ただし、Azure ポータルを使用してクラスターを作成する場合は、UI の制約により 7 が上限になります。ここで指定した追加のストレージ アカウントのそれぞれに対する <strong>[ストレージ アカウント]</strong> ページがウィザードに追加され、そこでアカウント情報を指定できます。たとえば、上のスクリーンショットでは、追加のストレージ アカウントが 1 つ選択されたため、ページ 5 がダイアログに追加されています。</td></tr>
	</table>

	右矢印をクリックします。

7. クラスターの追加の記憶域を構成する場合、**ストレージ アカウントページ**で、追加のストレージ アカウントのアカウント情報を入力します。

	![HDInsight クラスターに追加のストレージ詳細を提供](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page6.png)

    ここでも、既存のストレージを使用する、新しいストレージを作成する、別の Azure サブスクリプションのストレージを使用する、のいずれかを選択できます。値を指定するための手順は、前の手順と同様です。


    > [AZURE.NOTE]HDInsight クラスター用の Azure ストレージ アカウントを選択した後は、そのアカウントを削除することも、別のクラスターに変更することもできません。


 	追加のストレージ アカウントを指定した後、チェック マークをオンにして、クラスターのプロビジョニングを開始します。

###<a id="cli"></a> Azure コマンド ライン インターフェイス (Azure CLI) の使用

HDInsight クラスターをプロビジョニングするもう 1 つの方法は、Azure CLI を使用する方法です。Azure CLI は Node.js で実装されます。Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。Azure CLI は、次の場所からインストールできます。

- **Node.js SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Mac、Linux、Windows 用の Azure CLI** - <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Azure CLI の一般的な使用方法については、「[Mac、Linux、Windows 用の Azure CLI](../xplat-cli.md)」を参照してください。

ここでは、Azure CLI を Linux および Windows にインストールする手順と、コマンド ラインを使用してクラスターをプロビジョニングする手順について説明します。

- [Linux 向け Azure CLI の設定](#clilin)
- [Windows 向け Azure CLI の設定](#cliwin)
- [Azure CLI を使用した HDInsight クラスターのプロビジョニング](#cliprovision)

#### <a id="clilin"></a>Azure CLI のセットアップ (Linux の場合)

次の手順に従って、Azure コマンド ライン インターフェイス (Azure CLI) を使用できるように Linux コンピューターをセットアップします。

- Node.js パッケージ マネージャー (NPM) を使用して Azure CLI をインストールする
- Azure サブスクリプションへの接続

**NPM を使用して Azure CLI をインストールするには**

1.	Linux コンピューターでターミナル ウィンドウを開き、次のコマンドを実行します。

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	次のコマンドを実行してインストールを確認します。

		azure hdinsight -h

	**-h** スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Azure サブスクリプションに接続するには**

Azure CLI を使用する前に、ワークステーションと Azure の間の接続を構成する必要があります。Azure CLI は、Azure のサブスクリプション情報を使用してアカウントに接続します。この情報は、Azure から発行設定ファイルとして入手できます。入手した発行設定ファイルは、永続的なローカル構成設定としてインポートできます。インポートすると、Azure CLI のそれ以降の操作に使用されるようになります。発行設定のインポートは 1 回だけ行う必要があります。


> [AZURE.NOTE]発行設定ファイルには、機密情報が含まれます。Microsoft では、このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、BitLocker Drive Encryption を使用します。



1.	ターミナル ウィンドウを開きます。
2.	次のコマンドを実行して、Azure サブスクリプションにログインします。

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	このコマンドは、発行設定ファイルのダウンロード元の Web ページを起動します。Web ページが開いていない場合は、ターミナル ウィンドウのリンクをクリックしてブラウザー ページを開き、ポータルにログインします。

3.	発行設定ファイルをコンピューターにダウンロードします。
4.	コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Azure CLI のセットアップ (Windows の場合)

次の手順に従って、Azure コマンド ライン インターフェイス (Azure CLI) を使用できるように Windows コンピューターをセットアップします。

- NPM または Windows インストーラーを使用して Azure CLI をインストールする
- Azure アカウントの発行設定のダウンロードとインポート


Azure CLI は NPM または Windows インストーラーを使用してインストールできます。Microsoft では、この 2 つのオプションのいずれかを使用してインストールすることをお勧めします。

**NPM を使用して Azure をインストールするには**

1.	ブラウザーで **www.nodejs.org** を開きます。
2.	**[INSTALL]** をクリックし、指示に従います。設定は既定の設定を使います。
3.	コンピューターから**コマンド プロンプト** (または **Azure コマンド プロンプト**、または **VS2012 の開発者コマンド プロンプト**) を開きます。
4.	コマンド プロンプト ウィンドウで次のコマンドを実行します。

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]NPM コマンドが見つからないというエラーが表示される場合、次のパスが **PATH** 環境変数の中にあることを確認します。<i>C:\Program Files (x86)\nodejs;C:\Users[ユーザー名]\AppData\Roaming\npm</i> または <i>C:\Program Files\nodejs;C:\Users[ユーザー名]\AppData\Roaming\npm</i>


5.	次のコマンドを実行してインストールを確認します。

		azure hdinsight -h

	**-h** スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Windows インストーラーを使用して Azure CLI をインストールするには**

1.	ブラウザーで **http://azure.microsoft.com/downloads/** を開きます。2.	下へスクロールして、**[コマンド ライン ツール]** セクションの **[Azure コマンド ライン インターフェイス]** をクリックし、Web プラットフォーム インストーラーのウィザードの指示に従います。

**発行設定をダウンロードしてインポートするには**

Azure CLI を使用する前に、ワークステーションと Azure の間の接続を構成する必要があります。Azure CLI は、Azure のサブスクリプション情報を使用してアカウントに接続します。この情報は、Azure から発行設定ファイルとして入手できます。入手した発行設定ファイルは、永続的なローカル構成設定としてインポートできます。インポートすると、Azure CLI のそれ以降の操作に使用されるようになります。発行設定のインポートは 1 回だけ行う必要があります。


> [AZURE.NOTE]発行設定ファイルには、機密情報が含まれます。Microsoft では、このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、または BitLocker を使用します。



1.	コマンド プロンプトを開きます。
2.	次のコマンドを実行して、発行設定ファイルをダウンロードします。

		azure account download


	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	このコマンドは、発行設定ファイルのダウンロード元の Web ページを起動します。


3.	ファイルを保存するためのプロンプトで、**[保存]** をクリックし、ファイルの保存先となる場所を指定します。
5.	コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

		azure account import <path/to/the/file>

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]


#### <a id="cliprovision"></a>Azure CLI を使用した HDInsight クラスターのプロビジョニング

Azure CLI を使用して HDInsight クラスターをプロビジョニングするには、以下の手順を実行する必要があります。

- Azure のストレージ アカウントの作成
- クラスターのプロビジョニング


**Azure ストレージ アカウントを作成するには**

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントが必要です。ストレージ アカウントは、クラスターと同じデータ センターに配置する必要があります。

- コマンド プロンプト ウィンドウで次のコマンドを実行し、Azure ストレージ アカウントを作成します。

		azure storage account create [options] <StorageAccountName>


	場所を指定するよう求められたら、HDInsight Linux クラスターをプロビジョニングできる場所を選択します。このストレージは、HDInsight クラスターと同じ場所にある必要があります。


Azure ポータルを使った Azure ストレージ アカウントの作成については、「[ストレージ アカウントの作成、管理、削除](../storage-create-storage-account.md)」をご覧ください

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

管理ポータルを使用して情報を取得する方法の詳細については、「[ストレージ アカウントの作成、管理、削除](../storage-create-storage-account.md)」の「方法: ストレージ アクセス キーを表示、コピー、再生成する」をご覧ください。

HDInsight クラスターでは、ストレージ アカウント内にコンテナーも必要です。指定するストレージ アカウントにまだコンテナーがない場合は、**azure hdinsight cluster create** プロンプトにより、コンテナー名を指定するよう求められ、コンテナーも作成されます。ただし、コンテナーを事前に作成する場合は、次のコマンドを使用できます。

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターをプロビジョニングするには**

- コマンド プロンプト ウィンドウで次のコマンドを実行します。

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

	>[AZURE.NOTE]**--userName** と **--password** に指定する値は、Hadoop ユーザー用です。Hadoop ユーザーには常に --userName を "admin" として指定する必要があります。

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**構成ファイルを使用して HDInsight クラスターをプロビジョニングするには**

通常は、HDInsight クラスターをプロビジョニングして、ジョブを実行した後、クラスターを削除してコストを削減します。Azure CLI には構成をファイルに保存するオプションがあり、クラスターをプロビジョニングするたびにその構成を再利用することができます。

- コマンド プロンプト ウィンドウで次のコマンドを実行します。


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster. Make sure you specify --userName as "admin"
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>


	>[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。



	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**クラスターの一覧と詳細を表示するには**

- クラスターの一覧と詳細を表示するには、次のコマンドを使用します。

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**クラスターを削除するには**

- クラスターを削除するには、次のコマンドを使用します。

		azure hdinsight cluster delete <ClusterName>

###<a id="powershell"></a>Azure PowerShell の使用
Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。このセクションでは、Azure PowerShell を使用して HDInsight クラスターをプロビジョニングする方法について説明します。コンピューターを構成して HDInsight Windows Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../install-configure-powershell.md)」をご覧ください。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)」をご覧ください。HDInsight Windows PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス][hdinsight-powershell-reference]」をご覧ください。

Azure PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure のストレージ アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成

Windows PowerShell コンソールまたは Windows PowerShell Integrated Scripting Environment (ISE) のいずれかを使用して、スクリプトを実行できます。

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントとストレージ コンテナーが必要です。ストレージ アカウントは、HDInsight クラスターと同じデータ センターに置く必要があります。現在、HDInsight Linux クラスターをホストできるリージョンは、**東南アジア**、**北ヨーロッパ**、**米国東部**、**米国中南部**のみです。

**Azure アカウントに接続するには**

		Add-AzureAccount

Azure アカウント資格情報の入力を求められます。

**Azure ストレージ アカウントを作成するには**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次の Windows PowerShell コマンドを使ってその情報を取得できます。

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Azure BLOB ストレージ コンテナーを作成するには**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターをプロビジョニングするには**

Linux クラスターをプロビジョニングするために設定する必要がある最も重要な 2 つのパラメーターは、OS の種類と SSH ユーザーの詳細を指定するパラメーターです。

- **-OSType** パラメーターを **Linux** として指定していることを確認します。
- クラスターのリモート セッションで SSH を使用するには、SSH ユーザー パスワードまたは SSH 公開キーのいずれかを指定します。SSH ユーザー パスワードと SSH 公開キーを両方とも指定すると、キーが無視されます。リモート セッションで SSH キーを使用する場合は、入力を求められたら、空の SSH パスワードを指定する必要があります。


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $containerName = $clusterName					  # Azure Blob container that is used as the default file system for the HDInsight cluster


		# Get the credentials for HTTP and SSH users for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster.
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $clusterCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey


>[AZURE.NOTE]**$clusterCredentials** に指定する値は、クラスターの Hadoop ユーザー アカウントの作成に使用されます。このアカウントを使用してクラスターに接続します。**$sshCredentials** に指定する値は、クラスターの SSH ユーザーの作成に使用されます。このアカウントを使用してクラスターでリモート SSH セッションを開始し、ジョブを実行します。Azure ポータルから [簡易作成] オプションを使用してクラスターをプロビジョニングする場合、既定の Hadoop ユーザー名は "admin"、既定の SSH ユーザー名は "hdiuser" になります。

クラスターのプロビジョニングが完了するまでに数分かかる場合があります。

![HDI.CLI.Provision][image-hdi-ps-provision]

**カスタム構成オプションを使用して HDInsight クラスターをプロビジョニングするには**

クラスターをプロビジョニングするときに、複数の Azure BLOB ストレージ コンテナーに接続するか、Hive メタストアと Oozie メタストア用の Azure SQL データベースを使用するなどの他の構成オプションを使用できます。これにより、データとメタデータの寿命を、クラスターの寿命と切り離すことができます。

Linux クラスターをプロビジョニングするために設定する必要がある最も重要な 2 つのパラメーターは、OS の種類と SSH ユーザーの詳細を指定するパラメーターです。

- **-OSType** パラメーターを **Linux** として指定していることを確認します。
- クラスターのリモート セッションで SSH を使用するには、SSH ユーザー パスワードまたは SSH 公開キーのいずれかを指定します。SSH ユーザー パスワードと SSH 公開キーを両方とも指定すると、キーが無視されます。リモート セッションで SSH キーを使用する場合は、入力を求められたら、空の SSH パスワードを指定する必要があります。


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster

		# Get the credentials for HTTP user, SSH user, and Hive/Oozie metastore databases for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster.
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}

		# Create a cluster configuration file
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore

		# Create the cluster
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $clusterLocation -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey

>[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

クラスターのプロビジョニングが完了するまでに数分かかる場合があります。

![HDI.CLI.Provision][image-hdi-ps-config-provision]

###<a id="sdk"></a>HDInsight .NET SDK を使用する場合
HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。

SDK を使用して Linux で HDInsight クラスターをプロビジョニングするには、以下の手順を実行する必要があります。

- HDInsight .NET SDK のインストール
- 自己署名証明書の作成
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**

公開されている SDK の最新のビルドを [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**自己署名証明書を取得するには**

自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[Create a self-signed certificate (自己署名証明書の作成)](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。


**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2013 を開きます。

2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. **[新しいプロジェクト]** で、次の値を入力するか、選択します。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">プロパティ</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">値</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カテゴリ</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">テンプレート/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">テンプレート</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">コンソール アプリケーション</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

7. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8. 次の using ステートメントをファイルの先頭に追加します。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. **Main()** 関数で、次のコードをコピーしてファイルに貼り付けます。

	> [AZURE.NOTE]HDInsight Linux クラスターをホストできるリージョンは、**東南アジア**、**北ヨーロッパ**、**米国東部**、**米国中南部**のみです。指定するストレージ アカウントは同じリージョンにする必要があります。

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";		# Make sure you specify this username as "admin"
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;


		// If required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores

		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
			Version = version,
            OSType = OSType.Linux,
            SshUserName = sshusername,
            SshPublicKey = sshpublickey,
			HiveMetastore = hiveMetastore,		//Only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//Only if you created an ooziemetastore object earlier
        };

		// Configure Hive and Oozie if you opted for the metastores earlier
		clusterInfo.HiveConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("datanucleus.connectionPoolingType", "none"));
		clusterInfo.OozieConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("oozie.service.AuthorizationService.security.enabled", "false"));


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. **Main()** 関数の先頭にある変数を置き換えます。

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。HDInsight クラスターの作成は数分かかる場合があります。



##<a id="nextsteps"></a>次のステップ
この記事では、HDInsight Hadoop クラスターを Linux でプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

- [Linux での HDInsight の使用](hdinsight-hadoop-linux-information.md): Linux で HDInsight クラスターを操作するとはどういったことかを把握できます。
- [Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md) - Ambari Web または Ambari REST API を使用して、HDInsight クラスターで Linux ベースの Hadoop を監視し、管理する方法を説明します。
- [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md): さまざまな方法によるクラスターでの MapReduce ジョブの実行について説明します。
- [HDInsight での Hive の使用](hdinsight-use-hive.md): クラスターで Hive クエリを実行するその他の方法を説明します。
- [HDInsight での Pig の使用](hdinsight-use-pig.md): さまざまな方法によるクラスターでの Pig ジョブの実行について説明します。
- [HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md): HDInsight で Azure BLOB ストレージを使用して HDInsight クラスター用にデータを格納する方法を説明します。
- [HDInsight へのデータのアップロード](hdinsight-upload-data.md): HDInsight クラスター用に Azure BLOB ストレージに格納されたデータの操作方法を説明します。


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "クラスターの一覧と表示"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "HDInsight での Sqoop の使用"
 

<!----HONumber=July15_HO4-->