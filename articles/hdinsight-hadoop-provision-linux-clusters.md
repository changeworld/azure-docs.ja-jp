<properties 
   pageTitle="HDInsight での Hadoop クラスター (Linux ベース) のプロビジョニング | Azure" 
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
   ms.date="03/03/2015"
   ms.author="nitinme"/>


#カスタム オプションを使用した HDInsight での Hadoop Linux クラスターのプロビジョニング (プレビュー)

この記事では、Azure 管理ポータル、PowerShell、コマンド ライン ツール、または HDInsight .NET SDK を使用した、Azure HDInsight 上の Hadoop Linux クラスターのさまざまなカスタム プロビジョニング方法について学習します。

## HDInsight クラスターについて

Hadoop またはビッグデータを説明するときに、クラスターについて必ず触れるのはなぜでしょうか。その理由は、Hadoop がクラスターのさまざまなノードにまたがる大規模データの分散処理を可能にするためです。クラスターは、1 つのマスター (ヘッドノードまたは名前ノードともいいます) と任意の数のスレーブ (データ ノードともいいます) から成るマスター/スレーブ アーキテクチャを構成しています。詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a> に関するページを参照してください。

![HDInsight Cluster][img-hdi-cluster]

HDInsight クラスターでは、Hadoop の実装の詳細を抽象化しているため、クラスターの別のノードとの通信方法を心配する必要はありません。HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。詳細については、「[HDInsight での Hadoop 入門](hdinsight-hadoop-introduction.md)」を参照してください。変更されるデータは、HDInsight のコンテキストでは *Azure Storage - BLOB* (または WASB) とも呼ばれる Azure BLOB ストレージに格納されます。詳細については、[HDInsight での Azure BLOB ストレージの使用](hdinsight-use-blob-storage.md)に関するページを参照してください。

この記事では、クラスターをプロビジョニングするさまざまな手順を示します。クラスターをすばやくプロビジョニングする方法を調べる場合は、[Azure HDInsight (Linux) の概要](hdinsight-hadoop-linux-get-started.md)に関するトピックを参照してください。

**前提条件:**

この記事を読み始める前に、次の項目を用意する必要があります。

- Azure サブスクリプション。Azure はサブスクリプション方式のプラットフォームです。HDInsight PowerShell コマンドレットはサブスクリプションを使ってタスクを実行します。サブスクリプションの入手方法の詳細については、<a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">購入オプション</a>に関するページ、「<a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">メンバー プラン</a>」、または<a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">無料評価版</a>に関するページを参照してください。
- SSH キー。パスワードの代わりに SSH とキーを使用して、Linux クラスターにリモートでアクセスする場合に必要です。キーはより安全性が高いことから、キーを使用する方法をお勧めします。SSH キーを生成する手順については、次の記事を参照してください。
	-  Linux コンピューターの場合 - [Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)。
	-  Windows コンピューターの場合 - [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)。

## <a id="configuration"></a>構成オプション

### Linux 上のクラスター

HDInsight には、Azure で Linux クラスターをプロビジョニングするためのオプションが用意されています。Linux または Unix に詳しい場合、または Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、既存の Linux ベースの Hadoop ソリューションから移行することで Linux クラスターをプロビジョニングします。Linux での Azure HDInsight の詳細については、「[HDInsight での Hadoop 入門]」を参照してください(hdinsight-hadoop-introduction.md)。 

### その他のストレージ

構成中に、Azure BLOB ストレージ アカウントと既定のコンテナーを指定する必要があります。このコンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターに関連付ける追加の BLOB も指定することができます。

従属的な BLOB ストアの使用の詳細については、「[HDInsight の Hadoop での BLOB ストレージの使用](http://azure.microsoft.com/documentation/articles/hdinsight-use-blob-storage/)」を参照してください。

### メタストア

メタストアには、Hive テーブル、パーティション、スキーマ、列などについての情報が格納されます。この情報は、データが HDFS (または HDInsight の WASB) のどこに格納されているかを見つけるために、Hive によって使用されます。既定では、Hive は組み込みデータベースを使用してこの情報を格納します。

HDInsight クラスターをプロビジョニングするとき、Hive のメタストアを格納する SQL Database を指定します。これにより、クラスターを削除するとき、SQL Database に外付けで格納されているメタデータ情報を保持できます。

> [WACOM.NOTE] 現在、メタストアを使用するオプションは、.NET SDK を使用して Linux 向け HDInsight をプロビジョニングする場合にのみ使用できます。手順については、[Provision HDInsight clusters on Linux using .NET SDK (.NET SDK を使用した HDInsight クラスター (Linux) のプロビジョニング]」を参照してください(#sdk)。


## <a id="options"></a>HDInsight Linux クラスターのプロビジョニング オプション

HDInsight Hadoop Linux クラスターは、Linux コンピューターおよび Windows コンピューターからプロビジョニングできます。次の表は、さまざまな OS から使用できるプロビジョニング オプションと、その各手順を記載したリンクを示しています。

Linux クラスターをプロビジョニングするコンピューターで実行される OS | Azure の管理ポータルの使用 | クロスプラットフォーム CLI の使用 | .NET SDK の使用 | PowerShell の使用
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| [ここ]をクリック(#portal) | [ここ]をクリック(#cli)|該当なし | 近日利用開始予定
Windows | [ここ]をクリック(#portal) | [ここ]をクリック(#cli) | [ここ]をクリック(#sdk) | 近日利用開始予定

### <a id="portal"></a>Azure の管理ポータルの使用

HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには、同じデータ センターにある Azure ストレージ アカウントが必要です。詳細については、[HDInsight での Azure BLOB ストレージの使用](hdinsight-use-blob-storage.md)に関するページを参照してください。Azure ストレージ アカウントの作成の詳細については、[ストレージ アカウントの作成方法][azure-create-storageaccount]に関するページを参照してください。


> [WACOM.NOTE] 現在、HDInsight クラスターをホストできるリージョンは、**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、および**米国中南部**のみです。

**カスタム作成オプションを使用して HDInsight クラスターを作成するには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。
2. ページ下部の **[+ 新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]**、**[カスタム作成]** の順にクリックします。
3. [**クラスターの詳細**] ページで、次の値を入力または選択します。

	![Provide Hadoop HDInsight cluster details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>クラスター名</td>
			<td><p>クラスターの名前を指定します。 </p>
				<ul>
				<li>DNS 名の最初と最後の文字は英数字にする必要があります。それ以外ではダッシュ (-) を使用できます。</li>
				<li>3 ～ 63 文字のクラスター名を入力できます。</li>
				</ul></td></tr>
		<tr><td>クラスターの種類</td>
			<td>[クラスターの種類] で、<strong>[Hadoop]</strong> を選択します。</td></tr>
		<tr><td>オペレーティング システム</td>
			<td>Linux で HDInsight クラスターをプロビジョニングするには、<b>[Ubuntu 12.04 LTS Preview]</b> を選択します。Windows クラスターをプロビジョニングするには、<a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Windows の HDInsight での Hadoop クラスターのプロビジョニング</a>に関するページを参照してください。</td></tr>
		<tr><td>HDInsight のバージョン</td>
			<td>バージョンを選択します。Linux での HDInsight の場合、既定では HDInsight Version 3.2 で、これは Hadoop 2.5 を使用します。</td></tr>
		</table>

	表に示されている値を入力または選択し、右矢印をクリックします。

4. [**クラスターの構成**] ページで、次の値を入力または選択します。

	<table border="1">
	<tr><th>名前</th><th>値</th></tr>
	<tr><td>データ ノード</td><td>デプロイするデータ ノードの数です。テストでは、単一ノード クラスターを作成します。 <br />クラスター サイズの制限は、Azure サブスクリプションによって変わります。制限値を上げるには、Azure の課金サポートにお問い合わせください。</td></tr>
	<tr><td>リージョン/仮想ネットワーク</td><td><p>最後の手順で作成したストレージ アカウントと同じリージョンを選択します。HDInsight は、同じリージョンに配置されたストレージ アカウントを必要とします。これ以後の構成作業では、ここで指定した地域と同じリージョンにあるストレージ アカウントしか選択できません。</p><p>使用可能なリージョンは、<strong>東アジア</strong>、<strong>東南アジア</strong>、<strong>北ヨーロッパ</strong>、<strong>西ヨーロッパ</strong>、<strong>米国東部</strong>、<strong>米国西部</strong>、<strong>米国中北部</strong>、<strong>米国中南部</strong>です。<br/></p></td></tr>
	</table>



5. [**クラスター ユーザーの構成**] ページで、次の情報を指定します。

    ![Provide Hadoop HDInsight cluster user](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>HTTP パスワード</td>
			<td>既定の HTTP ユーザーである <i>admin</i> のパスワードを指定します。</td></tr>
		<tr><td>SSH ユーザー名</td>
			<td>SSH ユーザー名を指定します。このユーザー名を使用して、HDInsight クラスター ノードでリモート SSH セッションを開始します。</td></tr>
		<tr><td>SSH 認証の種類</td>
			<td>SSH ユーザーの認証に、パスワードと SSH キーのどちらを使用するかを指定します。</td></tr>
		<tr><td>SSH パスワード</td>
			<td>認証の種類にパスワードを選択した場合、SSH ユーザーを認証するための SSH パスワードを指定します。リモート Linux マシンで SSH セッションを開始するときに、このパスワードの入力を求められます。</td></tr>
		<tr><td>SSH パブリック キー</td>
			<td>認証の種類にキーを選択した場合は、既に生成済みの SSH パブリック キーを指定します。Linux クラスターのノードで SSH セッションを開始した場合は、この公開キーに関連付けられた秘密キーを使用します。<br>
			Linux コンピューターで SSH キーを生成する手順については、<a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">こちら</a>を参照してください。Windows コンピューターで SSH キーを生成する手順については、<a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">こちら</a>を参照してください。
		</td></tr>
		</table>

	> [WACOM.NOTE] パスワード認証より安全であるため、SSH による SSH パブリック キー認証を使用することをお勧めします。

	右矢印をクリックします。


6. **[ストレージ アカウント]** ページで、次の情報を指定します。

    ![Provide storage account for Hadoop HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	<table border='1'>
		<tr><th>プロパティ</th><th>値</th></tr>
		<tr><td>ストレージ アカウント</td>
			<td>HDInsight クラスターの既定のファイル システムとして使用する Azure ストレージ アカウントを指定します。次の 3 つのオプションのいずれかを選択できます。
			<ul>
				<li>既存のストレージを使用する</li>
				<li>新しいストレージを作成する</li>
				<li>別のサブスクリプションのストレージを使用する</li>
			</ul>
			</td></tr>
		<tr><td>アカウント名</td>
			<td><ul>
				<li>既存のストレージを使用する場合は、<strong>[アカウント名]</strong> で既存のストレージ アカウントを選択します。ボックスの一覧には、クラスターをプロビジョニングする対象として選択したデータ センターと同じデータ センターにあるストレージ アカウントのみが表示されます。</li>
				<li><strong>[新しいストレージを作成する]</strong> または <strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合は、ストレージ アカウント名を指定する必要があります。</li>
			</ul></td></tr>
		<tr><td>アカウント キー</td>
			<td><strong>[別のサブスクリプションのストレージを使用する]</strong> を選択した場合は、そのストレージ アカウントのアカウント キーを指定します。</td></tr>
		<tr><td>既定のコンテナー</td>
			<td><p>ストレージ アカウントの既定のコンテナーを指定します。既定のコンテナーは、HDInsight クラスターの既定のファイル システムとして使用されます。<strong>[ストレージ アカウント]</strong> で <strong>[既存のストレージを使用する]</strong> を選択したにもかかわらず、そのアカウントに既存のコンテナーがない場合は、既定でクラスターと同じ名前のコンテナーが作成されます。クラスター名と同じ名前のコンテナーが既に存在する場合は、コンテナー名に連番が付加されます。たとえば、mycontainer1、mycontainer2、などとなります。ただし、既存のストレージ アカウントに指定したクラスター名と異なる名前のコンテナーがある場合は、そのコンテナーを使用できます。</p>
            <p>新しいストレージの作成または別の Azure サブスクリプションのストレージの使用を選択した場合は、既定のコンテナー名を指定する必要があります。</p>
        </td></tr>
		<tr><td>追加のストレージ アカウント</td>
			<td>HDInsight は複数のストレージ アカウントをサポートします。クラスターで使用できる追加ストレージ アカウントの数に制限はありません。ただし、管理ポータルを使用してクラスターを作成する場合は、UI の制約により 7 が上限になります。ここで指定した追加のストレージ アカウントのそれぞれに対する [ストレージ アカウント] ページがウィザードに追加され、そこでアカウント情報を指定できます。たとえば、上のスクリーンショットでは、追加のストレージ アカウントが 1 つ選択されたため、ページ 5 がダイアログに追加されています。</td></tr>
	</table>

	右矢印をクリックします。

7. クラスターの追加の記憶域を構成する場合、**ストレージ アカウント**ページで、追加のストレージ アカウントのアカウント情報を入力します。

	![Provide additional storage details for HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

    ここでも、既存のストレージを使用する、新しいストレージを作成する、別の Azure サブスクリプションのストレージを使用する、のいずれかを選択できます。値を指定するための手順は、前の手順と同様です。

    > [WACOM.NOTE] HDInsight クラスター用の Azure ストレージ アカウントを選択した後は、そのアカウントを削除することも、別のアカウントに変更することもできません。

 	追加のストレージ アカウントを指定した後、チェック マークをオンにして、クラスターのプロビジョニングを開始します。 

###<a id="cli"></a>クロスプラットフォーム コマンド ラインの使用

HDInsight クラスターをプロビジョニングするもう 1 つの方法は、クロスプラットフォーム コマンド ライン インターフェイスです。コマンド ライン ツールは Node.js で実装されます。Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。CLI は、次の場所からインストールできます。

- **Node.JS SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **クロスプラットフォーム CLI** - <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

コマンド ライン インターフェイスの使用方法の一般的なガイドについては、[Mac および Linux 用 Azure コマンド ライン ツール][azure-command-line-tools]に関するページを参照してください。

以下の手順は、Linux および Windows にクロスプラットフォーム コマンド ラインをインストールする方法と、そのコマンド ラインを使用してクラスターをプロビジョニングする方法を示しています。

- [Linux 向け Azure クロスプラットフォーム コマンド ラインの設定](#clilin)
- [Windows 向け Azure クロスプラットフォーム コマンド ラインの設定](#cliwin)
- [Azure クロスプラットフォーム コマンド ラインを使用した HDInsight クラスターのプロビジョニング](#cliprovision)

#### <a id="clilin"></a>Linux 向けクロスプラットフォーム コマンド ラインの設定

次の手順に従って、Azure コマンド ライン ツールを使用するように Linux コンピューターを設定します。

- NPM を使用したクロスプラットフォーム コマンド ラインのインストール
- Azure サブスクリプションへの接続

**NPM を使用してコマンド ライン インターフェイスをインストールするには**

1.	Linux コンピューターでターミナル ウィンドウを開き、次のコマンドを実行します。

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	次のコマンドを実行してインストールを確認します。

		azure hdinsight -h

	*-h* スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Azure サブスクリプションに接続するには**

コマンド ライン インターフェイスを使用する前に、自分のコンピューターと Azure との接続を構成する必要があります。Azure のサブスクリプション情報は、コマンド ライン インターフェイスがアカウントにアクセスする際に使用されます。この情報は、Azure から発行設定ファイルとして入手できます。発行設定ファイルは永続的なローカル構成設定としてインポートすることができます。インポートすると、コマンド ライン インターフェイスの以降の操作にはこのファイルが使用されます。発行設定のインポートは 1 回だけ行う必要があります。

> [WACOM.NOTE] 発行設定ファイルには、機密情報が含まれます。Microsoft では、このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、または BitLocker を使用します。 


1.	ターミナル ウィンドウを開きます。
2.	次のコマンドを実行して、Azure サブスクリプションにログインします。

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	このコマンドは、発行設定ファイルのダウンロード元の Web ページを起動します。Web ページが開いていない場合は、ターミナル ウィンドウのリンクをクリックしてブラウザー ページを開き、ポータルにログインします。 

3.	発行設定ファイルをコンピューターにダウンロードします。
5.	コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Windows 向けクロスプラットフォーム コマンド ラインの設定

次の手順に従って、Azure コマンド ライン ツールを使用するように Windows コンピューターを設定します。

- (NPM または Windows インストーラーを使用した) クロスプラットフォーム コマンド ラインのインストール
- Azure アカウントの発行設定のダウンロードとインポート


コマンド ライン インターフェイスは、 *Node.js パッケージ マネージャー (NPM)* または Windows インストーラーを使用してインストールできます。Microsoft では、この 2 つのオプションのいずれかを使用してインストールすることをお勧めします。

**NPM を使用してコマンド ライン インターフェイスをインストールするには**

1.	ブラウザーで **www.nodejs.org** を開きます。
2.	**[INSTALL]** をクリックし、指示に従います。設定は既定の設定を使います。
3.	コンピューターから**コマンド プロンプト** (または  *Azure コマンド プロンプト*、または  *VS2012* の開発者プロンプト) を開きます。
4.	コマンド プロンプト ウィンドウで次のコマンドを実行します。

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [WACOM.NOTE] NPM コマンドが見つからないというエラー メッセージが表示された場合は、次のパスが PATH 環境変数にあるか確認します。<i>C:\Program Files (x86)\nodejs;C:\Users\[username]\AppData\Roaming\npm</i> または <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>

5.	次のコマンドを実行してインストールを確認します。

		azure hdinsight -h

	*-h* スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Windows インストーラーを使用してコマンド ライン インターフェイスをインストールするには**

1.	**http://azure.microsoft.com/downloads/** を参照します。
2.	下へスクロールして、**[コマンド ライン ツール]** セクションの **[クロスプラットフォーム コマンド ライン インターフェイス]** をクリックし、Web プラットフォーム インストーラー ウィザードの指示に従います。

**発行設定をダウンロードしてインポートするには**

コマンド ライン インターフェイスを使用する前に、自分のコンピューターと Azure との接続を構成する必要があります。Azure のサブスクリプション情報は、コマンド ライン インターフェイスがアカウントにアクセスする際に使用されます。この情報は、Azure から発行設定ファイルとして入手できます。発行設定ファイルは永続的なローカル構成設定としてインポートすることができます。インポートすると、コマンド ライン インターフェイスの以降の操作にはこのファイルが使用されます。発行設定のインポートは 1 回だけ行う必要があります。

> [WACOM.NOTE] 発行設定ファイルには、機密情報が含まれます。Microsoft では、このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、または BitLocker を使用します。


1.	**コマンド プロンプト**を開きます。
2.	次のコマンドを実行して、発行設定ファイルをダウンロードします。

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	このコマンドは、発行設定ファイルのダウンロード元の Web ページを起動します。

3.	ファイルを保存するためのプロンプトで、**[保存]** をクリックし、ファイルの保存先となる場所を指定します。
5.	コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

		azure account import <path/to/the/file>

	
#### <a id="cliprovision"></a>Azure クロスプラットフォーム コマンド ラインを使用した HDInsight クラスターのプロビジョニング

クロスプラットフォーム コマンド ラインを使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure のストレージ アカウントの作成
- クラスターのプロビジョニング


**Azure Storage アカウントを作成するには**

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントが必要です。ストレージ アカウントは、クラスターと同じデータ センターに配置する必要があります。

- コマンド プロンプト ウィンドウで次のコマンドを実行し、Azure ストレージ アカウントを作成します。

		azure storage account create [options] <StorageAccountName>

	場所を指定するよう求められたら、HDINsight クラスターをプロビジョニングできる場所を選択します。このストレージは、HDInsight クラスターと同じ場所にある必要があります。現在、HDInsight クラスターをホストできるリージョンは、**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、および**米国中南部**のみです。  

Azure の管理ポータルを使った Azure ストレージ アカウントの作成については、[ストレージ アカウントの作成、管理、削除方法][azure-create-storageaccount]に関するページを参照してください。

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

管理ポータルを使用して情報を取得する方法の詳細については、 **[ストレージ アカウントの作成、管理、または削除][azure-create-storageaccount]に関するページのストレージ アクセス キーを表示、コピー、および再生成する方法を参照してください。

HDInsight クラスターでは、ストレージ アカウント内にコンテナーも必要です。指定するストレージ アカウントにまだコンテナーがない場合は、 *azure hdinsight cluster create* でコンテナー名を指定するよう求められ、コンテナーも作成されます。ただし、コンテナーを事前に作成する場合は、次のコマンドを使用できます。

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターをプロビジョニングするには**

- コマンド プロンプト ウィンドウで次のコマンドを実行します。

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>		

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**構成ファイルを使用して HDInsight クラスターをプロビジョニングするには**

通常は、HDInsight クラスターをプロビジョニングして、ジョブを実行した後、クラスターを削除してコストを削減します。コマンド ライン インターフェイスを使うと、構成をファイルに保存して、クラスターをプロビジョニングするたびにその構成を再利用することができます。

- コマンド プロンプト ウィンドウで次のコマンドを実行します。


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Windows Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**クラスターの一覧と詳細を表示するには**

- クラスターの一覧と詳細を表示するには、次のコマンドを使用します。

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**クラスターを削除するには**

- クラスターを削除するには、次のコマンドを使用します。

		azure hdinsight cluster delete <ClusterName>



###<a id="sdk"></a>HDInsight .NET SDK の使用
HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。

SDK を使用して Linux で HDInsight クラスターをプロビジョニングするには、以下の手順を実行する必要があります。

- HDInsight .NET SDK のインストール
- 自己署名証明書の作成
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**

公開されている SDK の最新のビルドを [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**自己署名証明書を作成するには**

自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[自己署名証明書の作成](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。


**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2013 を開きます。

2. [ファイル] メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. [新しいプロジェクト] で、次の値を入力または選択します。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">プロパティ</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">値</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カテゴリ</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">テンプレート/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">コンソール アプリケーション</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューの **[Nuget パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

7. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8. 次の using ステートメントをファイルの先頭に追加します。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Main() 関数で、次のコードをコピーしてファイルに貼り付けます。

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		
		//if required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores
		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
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
			HiveMetastore = hiveMetastore,		//only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//only if you created an ooziemetastore object earlier
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

10. main() 関数の先頭にある変数を置き換えます。

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。HDInsight クラスターの作成は数分かかる場合があります。



##<a id="nextsteps"></a>次のステップ
この記事では、HDInsight Hadoop クラスターを Linux でプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

- [Linux での HDInsight の使用](hdinsight-hadoop-linux-information.md)に関するページ。Linux で HDInsight クラスターを操作するとはどういったことかを把握できます。
- [Manage HDInsight clusters using Ambari (Ambari を使用した HDInsight クラスターの管理)](hdinsight-hadoop-manage-ambari.md)。Ambari Web または Ambari REST API を使用して、HDInsight クラスターで Linux ベースの Hadoop を監視および管理する方法を説明します。 
- [HDInsight での MapReduce の使用][hdinsight-use-mapreduce]に関するページ。さまざまな方法によるクラスターでの MapReduce ジョブの実行について説明します。
- [HDInsight での Hive の使用][hdinsight-use-hive]に関するページ。クラスターで Hive クエリを実行するその他の方法を説明します。
- [HDInsight での Pig の使用][hdinsight-use-pig]に関するページ。さまざまな方法を使用したクラスターでの Pig ジョブの実行について説明します。
- [HDInsight での Azure BLOB ストレージの使用](hdinsight-use-blob-storage.md)に関するページ。HDInsight で Azure BLOB ストレージを使用して HDInsight クラスター用にデータを格納する方法を説明します。
- [HDInsight へのデータのアップロード][hdinsight-upload-data]に関するページ。HDInsight クラスター用に Azure BLOB ストレージに格納されたデータの操作方法を説明します。

[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

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
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=47-->
