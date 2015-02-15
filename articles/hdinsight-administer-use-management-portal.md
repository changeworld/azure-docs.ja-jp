<properties 
	pageTitle="Azure ポータルを使用した HDInsight Hadoop クラスターの管理 | Azure" 
	description="HDInsight サービスを管理する方法を学習します。HDInsight クラスターを作成し、対話型 JavaScript コンソールを開いて、Hadoop コマンド コンソールを開きます。." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="jgao"/>



# Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理

Azure の管理ポータルを使用すると、HDInsight で Hadoop クラスターをプロビジョニングし、Hadoop ユーザーのパスワードを変更して、クラスターで Hadoop コマンド コンソールにアクセスできるように RDP を有効にすることができます管理ポータル以外にも、HDInsight を管理するツールが用意されています。 

- Azure PowerShell を使用して HDInsight を管理する方法の詳細については、[PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]に関するページを参照してください。

- クロス プラットフォーム コマンド ライン ツールを使用して HDInsight を管理する方法の詳細については、[クロスプラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理][hdinsight-admin-cross-platform]に関するページを参照してください。 

**前提条件:**

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。Azure はサブスクリプション方式のプラットフォームです。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。


##この記事の内容

* [HDInsight クラスターのプロビジョニング](#create)
* [HDInsight クラスターのカスタマイズ](#customize)
* [HDInsight クラスターのユーザー名とパスワードの変更](#password)
* [RDP を使用した HDInsight クラスターへの接続](#rdp)
* [自己署名証明書の作成](#cert)
* [HTTP サービスのアクセス許可の付与/取り消し](#httpservice)
* [Hadoop コマンド コンソールを開く](#hadoopcmd)
* [次のステップ](#nextsteps)

##<a id="create"></a> HDInsight クラスターのプロビジョニング

HDInsight クラスターを作成する方法はいくつかあります。この記事では、Azure 管理ポータルの [簡易作成] オプションを使用する方法だけを説明します。その他のオプションについては、[HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。

HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。Azure BLOB ストレージと HDInsight クラスターのシームレスな統合の詳細については、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。

Azure ストレージ アカウントは、HDInsight クラスターをプロビジョニングする予定のデータ センターと同じ場所に作成する必要があります。現在、HDInsight クラスターをプロビジョニングできるのは次の 5 つのデータ センターです。 

- 東南アジア 
- 北ヨーロッパ
- 西ヨーロッパ 
- 米国東部 
- 米国西部 

Azure ストレージ アカウントの作成の詳細については、[ストレージ アカウントの作成方法][azure-create-storageaccount]に関するページを参照してください。


**HDInsight クラスターをプロビジョニングするには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。
2. ページ下部の **[新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]**、**[簡易作成]** の順にクリックします。

3. **クラスター名**、**クラスターのサイズ**、**クラスター管理用パスワード**、Azure **ストレージ アカウント**を入力し、**[HDInsight クラスターの作成]** をクリックします。クラスターを作成して実行すると、状態が  *Running* になります。

	![HDI.QuickCreate][image-cluster-quickcreate]

	[簡易作成] オプションを使用してクラスターを作成すると、Hadoop ユーザー アカウントの既定のユーザー名は  *admin* になります。アカウントに別のユーザー名を付けるには、[簡易作成] ではなく [カスタム作成] を使用します。または、プロビジョニングした後でアカウント名を変更します。

	[簡易作成] オプションを使用してクラスターを作成すると、指定したストレージ アカウントに、HDInsight クラスターと同じ名前の新しいコンテナーが自動的に作成されます。クラスターが既定で使用するコンテナーの名前を変更する場合は、[カスタム作成] オプションを使用する必要があります。 

	> [AZURE.NOTE] HDInsight クラスター用の Azure Storage アカウントを選択した後で、ストレージ アカウントを変更する唯一の方法は、クラスターを削除して、目的のストレージ アカウントを使って新しいクラスターを作成することです。

4. 新しく作成したクラスターをクリックします。次のようなランディング ページが表示されます。

	![HDI.ClusterLanding][image-cluster-landing]


##<a id="customize"></a> HDInsight クラスターのカスタマイズ

HDInsight は、広範囲の Hadoop コンポーネントで動作します。検証およびサポートされているコンポーネントの一覧については、[Azure HDInsight でサポートされている Hadoop のバージョン][hdinsight-versions]に関するページを参照してください。次のいずれかのオプションを使用して、HDInsight のカスタマイズを行うことができます。

- クラスターのプロビジョニングの間は、HDInsight .NET SDK または Azure PowerShell でクラスター カスタマイズ パラメーターを使用します。そうすることで、これらの構成変更はクラスターの有効期間中は保持され、Azure プラットフォームが定期的に保守を実行するクラスター ノードのイメージ再適用の影響を受けません。クラスター カスタマイズ パラメーターの使用方法の詳細については、[HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。
- Mahout や Cascading などの一部のネイティブな Java コンポーネントは、JAR ファイルとしてクラスター上で実行できます。これらの JAR ファイルは、Azure BLOB ストレージ (WASB) に分散させ、Hadoop ジョブ送信メカニズムを使用して HDInsight クラスターに送信できます。詳細については、[プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]に関するページを参照してください。 


	>[AZURE.NOTE] HDInsight クラスターへの jar ファイルの展開に関する問題、または HDInsight クラスターでの jar ファイルの呼び出しに関する問題がある場合は、[Microsoft サポート][hdinsight-support]にお問い合わせください。
	
	> Cascading は HDInsight ではサポートされておらず、Microsoft サポートの対象でもありません。サポートされているコンポーネントの一覧については、[HDInsight によって提供されるクラスター バージョンの新機能][hdinsight-versions]に関するページを参照してください。


リモート デスクトップ接続を使用したクラスターでのカスタム ソフトウェアのインストールはサポートされていません。クラスターを再作成する必要がある場合は、ヘッド ノードのドライブは失われるため、これらのドライブにファイルを保存しないでください。Azure BLOB ストレージにファイルを保存することをお勧めします。BLOB ストレージは永続的です。

##<a id="password"></a> HDInsight クラスターのユーザー名とパスワードの変更
HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。HDInsight クラスターのユーザー アカウントは、プロビジョニング処理中に作成されます。RDP を使用してクラスターにアクセスするために、RDP ユーザー アカウントを作成することもできます。「[リモート デスクトップを有効にする]」を参照してください。(#enablerdp)。

**HDInsight クラスターのユーザー名とパスワードを変更するには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。
3. ユーザー名とパスワードをリセットする HDInsight クラスターをクリックします。
4. ページの上部にある **[構成]** をクリックします。
5. **[オフ]**   (**[HADOOP サービス]** の横) をクリックします。
6. ページの下部にある **[保存]** をクリックし、無効化処理の完了を待ちます。
7. サービスが無効になった後、**[HADOOP サービス]** の横にある **[オン]** クリックします。
8. **[ユーザー名]** と **[新しいパスワード]** を入力します。  これらが、クラスターの新しいユーザー名とパスワードになります。
8. **[保存]** をクリックします。


##<a id="rdp"></a> RDP を使用した HDInsight クラスターへの接続

クラスターの作成時に指定したクラスターの資格情報を使用するとクラスター上のサービスにアクセスできますが、リモート デスクトップを介してクラスター自身にアクセスすることはできません。リモート デスクトップ アクセスは既定でオフに設定されているため、リモート デスクトップを使用してクラスターに直接アクセスするには、クラスターの作成後に追加の構成をする必要があります。

**リモート デスクトップを有効にするには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。
3. 接続する HDInsight クラスターをクリックします。
4. ページの上部にある **[構成]** をクリックします。
5. ページの下部にある **[リモートを有効にする]** をクリックします。
6. **リモート デスクトップの構成**ウィザードで、リモート デスクトップのユーザー名とパスワードを入力します。クラスターの作成時に使用したユーザー名 ([簡易作成] オプションの場合、既定で *admin*) とは異なるユーザー名を指定する必要があります。**[有効期限]** ボックスに有効期限の日付を入力します。有効期限としては、現在から 1 週間以内の将来の日付を指定する必要があります。有効期限の時刻は、既定で、指定した日付の真夜中と想定されます。チェック マーク アイコンをクリックします。

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]

	有効期限は、今日から 7 日以内の将来の日付である必要があります。時刻は、選択した日付の真夜中です。

> [AZURE.NOTE] クラスターの RDP が有効になった後、クラスターに接続する前にページを更新する必要があります。
 
**RDP を使用してクラスターに接続するには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。
3. 接続する HDInsight クラスターをクリックします。
4. ページの上部にある **[構成]** をクリックします。
5. **[接続]** クリックし、指示に従います。

##<a id="cert"></a>自己署名証明書の作成

クラスターに対し、.NET SDK を使用して何らかの操作を実行するには、ワークステーションで自己署名証明書を作成したうえで、ご利用の Azure サブスクリプションにその証明書をアップロードする必要があります。これは 1 回限りの作業です。証明書が有効である限り、同じ証明書を他のコンピューターにインストールすることができます。

**自己署名証明書を作成するには**

1. 要求を認証するために使用する自己署名証明書を作成します。IIS または [makecert][makecert-info] を使用して証明書を作成できます。
 
2. 証明書の場所を参照して、証明書を右クリックします。**[証明書のインストール]** をクリックし、コンピューターの個人用ストアに証明書をインストールします。証明書のプロパティを編集し、フレンドリ名を割り当てます。

3. Azure 管理ポータルに証明書をインポートします。ポータルで、ページの左下にある **[設定]** をクリックし、**[管理証明書]** をクリックします。ページの下部で **[アップロード]** をクリックして指示に従い、前の手順で作成した .cer ファイルをアップロードします。

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##<a id="httpservice"></a> HTTP サービスのアクセス許可の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

既定では、これらのサービスへのアクセス許可が付与されます。管理ポータルからアクセス許可を取り消す/付与することができます。 

>[AZURE.NOTE] アクセス許可を付与する/取り消すことで、クラスター ユーザーのユーザー名とパスワードがリセットされます。

**HTTP Web サービスのアクセス許可を付与する/取り消すには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。
3. 構成する HDInsight クラスターをクリックします。
4. ページの上部にある **[構成]** をクリックします。
5. **[HADOOP サービス]** の横にある **[オン]** または **[オフ]** をクリックします。  
6. **[ユーザー名]** と **[新しいパスワード]** を入力します。これらが、クラスターの新しいユーザー名とパスワードになります。
7. **[保存]** をクリックします。

この処理は、Azure PowerShell コマンドレットを使用して行うこともできます。

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

[PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]に関するページを参照してください。

##<a id="hadoopcmd"></a> Hadoop コマンド ラインを開く

リモート デスクトップを使用してクラスターに接続して Hadoop コマンド ラインを使用するには、まず、先のセクションで説明したように、クラスターに対するリモート デスクトップ アクセスを有効にする必要があります。 

**Hadoop コマンド ラインを開くには**

1. [Azure の管理ポータル][azure-management-portal]にサインインします。
2. 左側ウィンドウの **[HDINSIGHT]** をクリックします。デプロイした Hadoop クラスターが一覧表示されます。
3. 接続する HDInsight クラスターをクリックします。
3. ページの上部にある **[構成]** をクリックします。
4. ページの下部にある **[接続]** をクリックします。
5. **[開く]** をクリックします。
6. ユーザー名とパスワードを入力して **[OK]** をクリックします。クラスターの作成時に構成したユーザー名とパスワードを使用してください。
7. **[はい]** をクリックします。
8. デスクトップで **[Hadoop コマンド ライン]** をダブルクリックします。
		
	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Hadoop コマンドの詳細については、[Hadoop commands reference (Hadoop コマンド リファレンス)][hadoop-command-reference] に関するページを参照してください。

先のスクリーンショットで、フォルダー名には Hadoop のバージョン番号が埋め込まれています。バージョン番号は、クラスターにインストールされている Hadoop コンポーネントのバージョンに基づいて変更できます。Hadoop 環境変数を使用して、これらのフォルダーを参照できます。  次に例を示します。

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%   
	cd %hcatalog_home%

##<a id="nextsteps"></a> 次のステップ
この記事では、Azure 管理ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。詳細については、次の記事を参照してください。

* [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]に関するページ
* [クロスプラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理][hdinsight-admin-cross-platform]に関するページ
* [HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページ
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]に関するページ
* [Azure HDInsight の概要][hdinsight-get-started]に関するページ
* [Azure HDInsight でサポートされている Hadoop のバージョン][hdinsight-versions]に関するページ

[hdinsight-admin-cross-platform]: ../hdinsight-administer-use-command-line/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-support]: http://azure.microsoft.com/ja-jp/support/options/
[makecert-info]: http://msdn.microsoft.com/ja-jp/library/bfsktky3(v=vs.110).aspx

[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/

[azure-purchase-options]: http://azure.microsoft.com/ja-jp/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ja-jp/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ja-jp/pricing/free-trial/


[hadoop-command-reference]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster landing page"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop command line"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!--HONumber=42-->
