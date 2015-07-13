<properties
   pageTitle="HDInsight での Hadoop クラスターのカスタム プロビジョニング | Microsoft Azure"
   description="Azure ポータル、Azure PowerShell、コマンド ライン、.NET SDK を使用して Azure HDInsight のクラスターのカスタム プロビジョニングを行う方法について説明します。"
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
   ms.date="04/21/2015"
   ms.author="nitinme"/>

#カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング

Azure ポータル、Azure PowerShell、Azure CLI、HDInsight .NET SDK を使用した、Azure HDInsight での Hadoop クラスターのカスタム プロビジョニングについて説明します。HBase クラスターと Storm クラスターのプロビジョニングの手順については、「[Provision HBase cluster in HDInsight (HDInsight での HBase クラスターのプロビジョニング](../hdinsight-hbase-get-started.md)」と「[Getting started using Storm on HDInsight (HDInsight での Storm の使用方法)](../hdinsight-storm-getting-started.md)」をご覧ください。2 つのうちのどちらかを選ぶ理由については、「<a href="http://go.microsoft.com/fwlink/?LinkId=510237">What Is The Difference Between HBase and Hadoop/HDFS? (HBase と Hadoop/HDFS の違い)</a>」をご覧ください。

## HDInsight クラスターについて

Hadoop またはビッグ データを説明するときに、クラスターについて必ず触れるのはなぜでしょうか。 その理由は、Hadoop がクラスターのさまざまなノードにまたがる大規模データの分散処理を可能にするためです。クラスターは、1 つのマスター (ヘッドノードまたは名前ノードともいいます) と任意の数のスレーブ (データ ノードともいいます) から成るマスター/スレーブ アーキテクチャを構成しています。詳細については、[Apache Hadoop に関する Web ページ][apache-hadoop]を参照してください。

![HDInsight Cluster][img-hdi-cluster]

HDInsight クラスターでは、Hadoop の実装の詳細を抽象化しているため、クラスターの別のノードとの通信方法を心配する必要はありません。HDInsight クラスターをプロビジョニングすると、Hadoop と関連アプリケーションを含む Azure コンピューティング リソースがプロビジョニングされます。詳細については、「[HDInsight での Hadoop 入門](hdinsight-hadoop-introduction.md)」を参照してください。変更されるデータは、Azure Blob ストレージに格納されます。詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」を参照してください。

この記事では、クラスターをプロビジョニングするさまざまな手順を示します。クラスターをすばやくプロビジョニングする方法を調べる場合は、「[Azure HDInsight の概要](../hdinsight-get-started.md)」をご覧ください。

**前提条件:**

この記事の手順を開始する前に、次の項目を用意する必要があります。

- Azure サブスクリプション。Azure はサブスクリプション方式のプラットフォームです。HDInsight Windows PowerShell コマンドレットはサブスクリプションを使ってタスクを実行します。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。

##<a id="configuration"></a>構成オプション

###Linux 上のクラスター

HDInsight には、Azure で Linux クラスターを構成するためのオプションが用意されています。Linux または Unix に詳しい場合や、Linux 向けに構築された Hadoop エコシステム コンポーネントとの簡単な統合が必要な場合は、既存の Linux ベースの Hadoop ソリューションから移行することで Linux クラスターを構成します。詳細については、「[Get started with Hadoop on Linux in HDInsight (HDInsight の Linux での Hadoop の使用)](../hdinsight-hadoop-linux-get-started.md)」をご覧ください。

###その他のストレージ

構成中に、Azure BLOB ストレージ アカウントと既定のコンテナーを指定する必要があります。このコンテナーは、既定の保存先としてクラスターで使用されます。必要に応じて、クラスターに関連付ける追加の Azure ストレージ アカウントBLOB も指定できます。

>[AZURE.NOTE]1 つの Blob ストレージ コンテナーを複数のクラスターで共有しないでください。これはサポートされていません。

従属的な BLOB ストアの使用の詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」をご覧ください。

###メタストア

メタストアには、Hive テーブル、パーティション、スキーマ、列などについての Hive と Oozie メタデータが格納されます。メタストアを使用すると Hive と Oozie メタデータを保持できるため、新しいクラスターをプロビジョニングするときに、Hive テーブルまたは Oozie ジョブを再作成する必要はありません。既定では、Hive は組み込みデータベースを使用してこの情報を格納します。組み込みデータベースは、クラスターが削除されるとメタデータを保持できません。

### クラスターのカスタマイズ

追加コンポーネントをインストールするか、プロビジョニング中にスクリプトを使用してクラスターの構成をカスタマイズできます。このようなスクリプトは、**スクリプト操作**を使用して実行します。これは Azure ポータル、HDInsight Windows PowerShell コマンドレット、HDInsight .NET SDK で使用できる構成オプションです。詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。


###仮想ネットワーク

[Azure 仮想ネットワーク](http://azure.microsoft.com/documentation/services/virtual-network/)によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。仮想ネットワークでは、次のことが可能になります。

* プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* 仮想プライベート ネットワーク (VPN) を使用したローカル データセンター ネットワークへのクラウド リソースの接続 (サイト間またはポイント対サイト)

	サイト間構成では、ハードウェア VPN を使用するか、ルーティングとリモート アクセス サービスを使用して、データセンターから複数のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure 仮想ネットワークに接続できます。

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

仮想ネットワークの機能、利点の詳細については、「[Azure 仮想ネットワークの概要](http://msdn.microsoft.com/library/azure/jj156007.aspx)」を参照してください。

> [AZURE.NOTE]HDInsight クラスターをプロビジョニングする前に、Azure 仮想ネットワークを作成する必要があります。詳細については、「[仮想ネットワークの構成タスク](http://msdn.microsoft.com/library/azure/jj156206.aspx)」を参照してください。
>
> Azure HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在取り扱っていません。
>
> 1 つのクラスターには単一のサブネットを指定することを強くお勧めします。

##<a id="portal"></a>Azure ポータルの使用

HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには、同じデータ センターにある Azure ストレージ アカウントが必要です。詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-use-blob-storage.md)」を参照してください。Azure ストレージ アカウントの作成の詳細については、「[ストレージ アカウントの作成方法](../storage-create-storage-account.md)」をご覧ください。


> [AZURE.NOTE]現在、HDInsight クラスターをホストできるリージョンは、**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、および**米国中南部**のみです。

**カスタム作成オプションを使用して HDInsight クラスターを作成するには**

1. [Azure ポータル][azure-management-portal]にサインインします。
2. ページ下部の **[+ 新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]**、**[カスタム作成]** の順にクリックします。
3. **[クラスターの詳細]** ページで、次の値を入力または選択します。

	![Hadoop HDInsight クラスターの詳細を提供][image-customprovision-page1]

    <table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>クラスター名</td>
		<td><p>クラスターの名前を指定します。</p>
			<ul>
			<li>Domain Name System (DNS) 名の最初と最後の文字は英数字にする必要があります。それ以外ではダッシュ (-) を使用できます。</li>
			<li>3 ～ 63 文字のクラスター名を入力できます。</li>
			</ul></td></tr>
	<tr><td>クラスターの種類</td>
		<td>クラスターの種類には、<strong>[Hadoop]</strong> を選択します。</td></tr>
	<tr><td>オペレーティング システム</td>
		<td><b>Windows Server 2012 R2 Data Center</b> を選択して、Windows クラスターをプロビジョニングします。Linux クラスターをプロビジョニングするには、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">HDInsight での Hadoop Linux クラスターのプロビジョニング</a>」をご覧ください。</td></tr>
	<tr><td>HDInsight のバージョン</td>
		<td>バージョンを選択します。Hadoop の場合、既定では HDInsight Version 3.1 で、これは Hadoop 2.4 を使用します。</td></tr>
	</table>表に示されている値を入力または選択し、右矢印をクリックします。

4. **[クラスターの構成]** ページで、次の値を入力または選択します。

	![Hadoop HDInsight クラスターの詳細を提供](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>データ ノード</td><td>デプロイするデータ ノードの数です。テストでは、単一ノード クラスターを作成します。<br />クラスター サイズの制限は、Azure サブスクリプションによって変わります。制限値を上げるには、Azure の課金サポートにお問い合わせください。</td></tr>
<tr><td>リージョン/仮想ネットワーク</td><td><p>最後の手順で作成したストレージ アカウントと同じリージョンを選択します。HDInsight は、同じリージョンに配置されたストレージ アカウントを必要とします。これ以後の構成作業では、ここで指定した地域と同じリージョンにあるストレージ アカウントしか選択できません。</p><p>利用可能なリージョンは、<strong>東アジア</strong>、<strong>東南アジア</strong>、<strong>北ヨーロッパ</strong>、<strong>西ヨーロッパ</strong>、<strong>米国東部</strong>、<strong>米国西部</strong>、<strong>米国中北部</strong>、<strong>米国中南部</strong>です。<br/>Azure 仮想ネットワークを作成した場合は、HDInsight クラスターで使用するよう構成するネットワークを選択できます。</p><p>Azure 仮想ネットワークの作成の詳細については、「<a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">仮想ネットワークの構成タスク</a>」をご覧ください。</p></td></tr>
<tr><td>ヘッド ノード サイズ</td><td><p>ヘッド ノードの仮想マシン (VM) サイズを選択します。</p></td></tr>
<tr><td>データ ノード サイズ</td><td><p>データ ノードの VM サイズを選択します。</p></td></tr>
</table>>[AZURE.NOTE]VM の選択に基づき、料金が異なる場合があります。HDInsight は、クラスター ノードにすべて Standard レベルの VM を使用します。VM サイズに応じた料金の詳細については、「<a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight 料金</a>」をご覧ください。


5. **[クラスター ユーザーの構成]** ページで、次の情報を指定します。

    ![Hadoop HDInsight クラスターのユーザーとメタストアの詳細を提供](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>HTTP ユーザー名</td>
		<td>HDInsight クラスターのユーザー名を指定します。</td></tr>
	<tr><td>HTTP パスワード / パスワードの確認</td>
		<td>HDInsight クラスターのユーザー パスワードを指定します。</td></tr>
	<tr><td>Enable remote desktop for cluster (クラスターのリモート デスクトップ アクセスを有効にする)</td>
		<td>このチェック ボックスをオンにして、プロビジョニングされるとクラスター ノードにリモートにできるリモート デスクトップ ユーザーのユーザー名、パスワード、有効期限を指定します。また、クラスターがプロビジョニングされた後にリモート デスクトップを有効にすることもできます。手順については、「<a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">RDP を使用した HDInsight クラスターへの接続</a>」をご覧ください。</td></tr>
	<tr><td>Hive/Oozie メタストアの入力</td>
		<td>このチェックボックスをオンにして、クラスターと同じデータ センターにある Hive/Oozie メタストアとして使用する SQL データベースを指定します。このチェック ボックスを選択した場合、ウィザードの後続のページで Azure SQL データベースの詳細を指定する必要があります。これは、クラスターが削除された後でも Hive/Oozie ジョブに関するメタデータを保持する場合に便利です。</td></tr>
	</td></tr>
</table>右矢印をクリックします。

6. **[Configure Hive/Oozie Metastore]** ページで、次の情報を指定します。

    ![Hadoop HDInsight クラスターのユーザーを指定](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	Hive/OOzie のメタストアとして使用する Azure SQL データベースを指定します。Hive メタストアと Oozie メタストアの両方に同じデータベースを指定できます。この SQL データベースは、HDInsight クラスターと同じデータ センターにある必要があります。リスト ボックスの一覧には、<strong>[クラスターの詳細]</strong> ページで指定したデータ センターにある SQL データベースしか表示されません。また、ユーザー名とパスワードを指定し、選択した Azure SQL データベースに接続します。

    >[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

    右矢印をクリックします。


7. **[ストレージ アカウント]** ページで、次の値を指定します。

    ![HDInsight Hadoop クラスターに対してストレージ アカウントを指定](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

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
		<td>HDInsight は複数のストレージ アカウントをサポートします。クラスターで使用できる追加ストレージ アカウントの数に制限はありません。ただし、Azure ポータルを使用してクラスターを作成する場合は、UI の制約により 7 が上限になります。ここで指定した追加のストレージ アカウントのそれぞれに対する [**ストレージ アカウント**] ページがウィザードに追加され、そこでアカウント情報を指定できます。たとえば、上のスクリーンショットでは、追加のストレージ アカウントが 1 つ選択されたため、ページ 5 がダイアログに追加されています。</td></tr>
</table>右矢印をクリックします。

7. クラスターの追加の記憶域を構成する場合、**[ストレージ アカウント]** ページで、追加のストレージ アカウントのアカウント情報を入力します。

	![HDInsight クラスターに追加のストレージ詳細を提供](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    ここでも、既存のストレージを使用する、新しいストレージを作成する、別の Azure サブスクリプションのストレージを使用する、のいずれかを選択できます。値を指定するための手順は、前の手順と同様です。

    > [AZURE.NOTE]HDInsight クラスター用の Azure ストレージ アカウントを選択した後は、そのアカウントを削除することも、別のアカウントに変更することもできません。

8. **[スクリプトのアクション]** ページで　**[スクリプト アクションの追加]** をクリックし、クラスターが作成される際に、クラスターのカスタマイズを実行するカスタム スクリプトに関する詳細を指定します。詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。

	![HDInsight クラスターをカスタマイズするスクリプト操作を構成](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターをカスタマイズするために呼び出すスクリプトの Uniform Resource Identifier (URI) を指定します。</td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。</td></tr>
</table>複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトを追加した後、チェック マークをオンにして、クラスターのプロビジョニングを開始します。

##<a id="powershell"></a>Azure PowerShell の使用
Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。このセクションでは、Azure PowerShell を使用して HDInsight クラスターをプロビジョニングする方法について説明します。ワークステーションを構成して HDInsight Windows Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成](../install-configure-powershell.md)」をご覧ください。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)」をご覧ください。HDInsight Windows PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス][hdinsight-powershell-reference]」をご覧ください。

> [AZURE.NOTE]このセクションのスクリプトは、Azure 仮想ネットワークの HDInsight クラスターの構成に使用できますが、Azure 仮想ネットワークは作成されません。Azure 仮想ネットワークの作成の詳細については、「[仮想ネットワークの構成タスク](http://msdn.microsoft.com/library/azure/jj156206.aspx)」をご覧ください。

Azure PowerShell を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- Azure のストレージ アカウントの作成
- Azure BLOB コンテナーの作成
- HDInsight クラスターの作成

Windows PowerShell コンソールまたは Windows PowerShell Integrated Scripting Environment (ISE) のいずれかを使用して、スクリプトを実行できます。

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントとストレージ コンテナーが必要です。ストレージ アカウントは、HDInsight クラスターと同じデータ センターに置く必要があります。

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

> [AZURE.NOTE]Azure PowerShell コマンドレットは、HDInsight クラスターの構成変数を変更する方法として唯一お勧めする方法です。リモート デスクトップ経由でクラスターに接続している間に Hadoop 構成ファイルに加えられた変更は、クラスター パッチの際に上書きされる可能性があります。Azure PowerShell 経由で設定された構成値は、クラスターにパッチが適用された場合でも保持されます。

- 次のコマンドを Azure PowerShell コンソール ウィンドウから実行します。

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
        $hadoopUserPassword = "<HadoopUserPassword>"

        $secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]$hadoopUserName コマンドと $hadoopUserPassword コマンドは、クラスター用 Hadoop ユーザー アカウントの作成に使用されます。このアカウントを使用してクラスターに接続し、ジョブを実行します。Azure ポータルから [簡易作成] オプションを使用してクラスターをプロビジョニングする場合は、既定の Hadoop ユーザー名は "admin" になります。このアカウントと、リモート デスクトップ プロトコル (RDP) のユーザー アカウントとを混同しないでください。RDP ユーザー アカウントは Hadoop ユーザー アカウントとは異なっている必要があります。詳細については、「[Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理][hdinsight-admin-portal]」をご覧ください。

	クラスターのプロビジョニングが完了するまでに数分かかる場合があります。

	![HDI.CLI.Provision][image-hdi-ps-provision]



**カスタム構成オプションを使用して HDInsight クラスターをプロビジョニングするには**

クラスターをプロビジョニングするときに、複数の Azure BLOB ストレージ コンテナーへの接続、仮想ネットワークの使用、Hive メタストアおよび Oozie メタストア用の Azure SQL データベースの使用など、その他の構成オプションを使用できます。これにより、データとメタデータの寿命を、クラスターの寿命と切り離すことができます。

> [AZURE.NOTE]Windows PowerShell コマンドレットは、HDInsight クラスターの構成変数を変更する方法として唯一お勧めする方法です。リモート デスクトップ経由でクラスターに接続している間に Hadoop 構成ファイルに加えられた変更は、クラスター パッチの際に上書きされる可能性があります。Azure PowerShell 経由で設定された構成値は、クラスターにパッチが適用された場合でも保持されます。

- 次のコマンドを Windows PowerShell ウィンドウから実行します。

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。

**HDInsight クラスターの一覧を表示するには**

- 次のコマンドを Azure PowerShell コンソール ウィンドウから実行して HDInsight クラスターを一覧表示し、クラスターが正常にプロビジョニングされたことを確認します。

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a>Azure CLI の使用

> [AZURE.NOTE]2014 年 8 月 29 日の時点では、クラスターと Azure 仮想ネットワーク間の関連付けに Azure CLI を使用することはできません。

HDInsight クラスターをプロビジョニングするもう 1 つの方法は、Azure CLI を使用する方法です。Azure CLI は Node.js で実装されます。Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。CLI は、次の場所からインストールできます。

- **Node.js SDK** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Azure CLI** - <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Azure CLI の一般的な使用方法については、[Mac、Linux、Windows 用の Azure CLI](../xplat-cli.md) に関するページを参照してください。

ここでは、Azure CLI を Linux および Windows にインストールする手順と、コマンド ラインを使用してクラスターをプロビジョニングする手順について説明します。

- [Linux 向け Azure CLI の設定](#clilin)
- [Windows 向け Azure CLI の設定](#cliwin)
- [Azure CLI を使用した HDInsight クラスターのプロビジョニング](#cliprovision)

#### <a id="clilin"></a>Linux 向け Azure CLI の設定

次の手順に従って、Azure コマンド ライン インターフェイス (Azure CLI) を使用できるように Linux コンピューターをセットアップします。

- Node.js パッケージ マネージャー (NPM) を使用して Azure CLI をインストールする
- Azure サブスクリプションへの接続

**NPM を使用して Azure CLI をインストールするには**

1.	Linux コンピューターでターミナル ウィンドウを開き、次のコマンドを実行します。

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	次のコマンドを実行してインストールを確認します。

		azure hdinsight -h

	*-h* スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Azure サブスクリプションに接続するには**

Azure CLI を使用する前に、ワークステーションと Azure の間の接続を構成する必要があります。Azure CLI は、Azure のサブスクリプション情報を使用してアカウントに接続します。この情報は、Azure から発行設定ファイルとして入手できます。発行設定ファイルは、永続的なローカル構成設定としてインポートできます。インポートすると、Azure CLI のそれ以降の操作に使用されるようになります。発行設定のインポートは 1 回だけ行う必要があります。

> [AZURE.NOTE]発行設定ファイルには、機密情報が含まれます。Microsoft では、このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、BitLocker Drive Encryption を使用します。


1.	ターミナル ウィンドウを開きます。
2.	次のコマンドを実行して、Azure サブスクリプションにログインします。

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	このコマンドは、発行設定ファイルのダウンロード元の Web ページを起動します。Web ページが開いていない場合は、ターミナル ウィンドウのリンクをクリックしてブラウザー ページを開き、ポータルにログインします。

3.	発行設定ファイルをコンピューターにダウンロードします。
5.	コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Windows 向け Azure CLI の設定

次の手順に従って、Azure CLI を使用するように Windows ベースのコンピューターを設定します。

- Azure CLI のインストール (NPM または Windows インストーラーを使用)
- Azure アカウントの発行設定のダウンロードとインポート


Azure CLI は NPM または Windows インストーラーを使用してインストールできます。Microsoft では、この 2 つのオプションのいずれかを使用してインストールすることをお勧めします。

**NPM を使用して Azure CLI をインストールするには**

1.	ブラウザーで **www.nodejs.org** を開きます。
2.	**[INSTALL]** をクリックし、指示に従います。設定は既定の設定を使います。
3.	コンピューターから**コマンド プロンプト** (または *Azure コマンド プロンプト*、または *VS2012 の開発者コマンド プロンプト*) を開きます。
4.	コマンド プロンプト ウィンドウで次のコマンドを実行します。

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]NPM コマンドが見つからないというエラーが表示される場合、次のパスが PATH 環境変数の中にあることを確認します。<i>C:\Program Files (x86)\nodejs;C:\Users[ユーザー名]\AppData\Roaming\npm</i> または <i>C:\Program Files\nodejs;C:\Users[ユーザー名]\AppData\Roaming\npm</i>

5.	次のコマンドを実行してインストールを確認します。

		azure hdinsight -h

	*-h* スイッチを使うと、さまざまなレベルでヘルプ情報を表示できます。次に例を示します。

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Windows インストーラーを使用して Azure CLI をインストールするには**

1.	ブラウザーで **http://azure.microsoft.com/downloads/** を開きます。2.	下へスクロールして、**[コマンド ライン ツール]** セクションの **[Azure コマンド ライン インターフェイス]** をクリックし、Web プラットフォーム インストーラーのウィザードの指示に従います。

**発行設定をダウンロードしてインポートするには**

Azure CLI を使用する前に、ワークステーションと Azure の間の接続を構成する必要があります。Azure CLI は、Azure のサブスクリプション情報を使用してアカウントに接続します。この情報は、Azure から発行設定ファイルとして入手できます。発行設定ファイルは、永続的なローカル構成設定としてインポートできます。インポートすると、Azure CLI のそれ以降の操作に使用されるようになります。発行設定のインポートは 1 回だけ行う必要があります。

> [AZURE.NOTE]発行設定ファイルには、機密情報が含まれます。Microsoft では、このファイルを削除するか、追加の作業を行ってファイルのある user フォルダーを暗号化することをお勧めします。Windows の場合、フォルダー プロパティを変更するか、または BitLocker を使用します。


1.	**コマンド プロンプト**を開きます。
2.	次のコマンドを実行して、発行設定ファイルをダウンロードします。

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	このコマンドは、発行設定ファイルのダウンロード元の Web ページを起動します。

3.	ファイルを保存するためのプロンプトで、**[保存]** をクリックし、ファイルの保存先となる場所を指定します。
5.	コマンド プロンプト ウィンドウで次のコマンドを実行して、発行設定ファイルをインポートします。

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Azure CLI を使用した HDInsight クラスターのプロビジョニング

Azure CLI を使用して HDInsight クラスターをプロビジョニングするには、以下の手順を実行する必要があります。

- Azure のストレージ アカウントの作成
- クラスターのプロビジョニング

**Azure ストレージ アカウントを作成するには**

HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントが必要です。ストレージ アカウントは、クラスターと同じデータ センターに配置する必要があります。

- コマンド プロンプト ウィンドウで次のコマンドを実行し、Azure ストレージ アカウントを作成します。

		azure storage account create [options] <StorageAccountName>

	場所を指定するよう求められたら、HDInsight クラスターをプロビジョニングできる場所を選択します。このストレージは、HDInsight クラスターと同じ場所にある必要があります。現在、HDInsight クラスターをホストできるリージョンは、**東アジア**、**東南アジア**、**北ヨーロッパ**、**西ヨーロッパ**、**米国東部**、**米国西部**、**米国中北部**、および**米国中南部**のみです。

Azure ポータルを使った Azure ストレージ アカウントの作成については、「[ストレージ アカウントの作成、管理、削除](../storage-create-storage-account.md)」をご覧ください

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

管理ポータルを使用して情報を取得する方法の詳細については、「[ストレージ アカウントの作成、管理、削除](../storage-create-storage-account.md)」の「*方法: ストレージ アクセス キーを表示、コピー、再生成する*」をご覧ください。

HDInsight クラスターでは、ストレージ アカウント内にコンテナーも必要です。指定するストレージ アカウントにまだコンテナーがない場合は、*azure hdinsight cluster create* コマンドにより、コンテナー名を指定するよう求められ、コンテナーも作成されます。ただし、コンテナーを事前に作成する場合は、次のコマンドを使用できます。

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。

**HDInsight クラスターをプロビジョニングするには**

- コマンド プロンプト ウィンドウで次のコマンドを実行します。

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**構成ファイルを使用して HDInsight クラスターをプロビジョニングするには**

通常は、HDInsight クラスターをプロビジョニングして、ジョブを実行した後、クラスターを削除してコストを削減します。Azure CLI を使用すると、構成をファイルに保存し、クラスターをプロビジョニングするたびにその構成を再利用することができます。

- コマンド プロンプト ウィンドウで次のコマンドを実行します。


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE]メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。これは、SQL データベース インスタンスが実行されているサーバーです。サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**クラスターの一覧と詳細を表示するには**

- クラスターの一覧と詳細を表示するには、次のコマンドを使用します。

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**クラスターを削除するには**

- クラスターを削除するには、次のコマンドを使用します。

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a>HDInsight .NET SDK を使用する場合
HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。

SDK を使用して HDInsight クラスターをプロビジョニングするには、以下の手順を実行する必要があります。

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


9. Main() 関数で、次のコードをコピーしてファイルに貼り付けます。

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Main() 関数の先頭にある変数を置き換えます。

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。HDInsight クラスターの作成は数分かかる場合があります。



##<a id="nextsteps"></a>次のステップ
この記事では、HDInsight クラスターをプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要](../hdinsight-get-started.md) - HDInsight クラスターの使用方法について説明しています。
* [HDInsight の Hadoop での Sqoop の使用](hdinsight-use-sqoop.md) - HDInsight と SQL Database または SQL Server 間でデータをコピーする方法について説明しています。
* [Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-powershell.md) - HDInsight で Azure PowerShell を使用する方法について説明しています。
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md) - プログラムを使用して HDInsight にジョブを送信する方法について説明しています。
* [HDInsight][hdinsight-sdk-documentation] - HDInsight SDK について説明しています。


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli.md

[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Hadoop HDInsight クラスターの詳細を提供"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Hadoop HDInsight クラスターのユーザーを指定"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "HDInsight Hadoop クラスターに対してストレージ アカウントの詳細を提供"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "HDInsight Hadoop クラスターに対して追加のストレージ アカウントの詳細を提供"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "HDInsight クラスターをカスタマイズするスクリプト操作を構成"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "クラスターの一覧と表示"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: hdinsight-use-sqoop.md "HDInsight での Sqoop の使用"
 

<!---HONumber=62-->