<properties
	pageTitle="Script Action を使った HDInsight クラスターのカスタマイズ | Microsoft Azure"
	description="Script Action を使って HDInsight クラスターをカスタマイズする方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/07/2015"
	ms.author="nitinme"/>

# Script Action を使って HDInsight クラスターをカスタマイズする

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-v1.md)

HDInsight には、カスタム スクリプトを呼び出す **Script Action** という構成オプションがあります。Script Action は、プロビジョニング プロセス中にクラスター上で実行されるカスタマイズを定義します。これらのスクリプトを使用して、クラスター上に追加のソフトウェアをインストールしたり、クラスター上のアプリケーションの構成を変更したりできます。


> [AZURE.NOTE]この記事の情報は、Windows ベースの HDInsight クラスターに固有のものです。この記事の Linux ベースのクラスターに固有のバージョンについては、「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする (Linux)](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

HDInsight クラスターは、その他さまざまな方法でカスタマイズできます。たとえば、Azure ストレージ アカウントの追加、Hadoop 構成ファイルの変更 (core-site.xml、hive-site.xml など)、クラスター内の一般的な場所への共有ライブラリの追加 (Hive、Oozie など) といった方法があります。これらのカスタマイズは、Azure PowerShell、Azure HDInsight .NET SDK、または Azure プレビュー ポータルから実行できます。詳細については、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision-cluster]」をご覧ください。

## クラスターのプロビジョニング処理での Script Action

Script Action は、クラスターが作成中にのみ使用されます。次の図は、プロビジョニング処理中に Script Action が実行された場合を示しています。

![クラスター プロビジョニング時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトの実行中、クラスターは **ClusterCustomization** 段階に入ります。この段階でスクリプトは、システム管理者アカウントで、ノードで完全な管理者権限を持ち、クラスター内のすべての指定したノードで並列的に実行されます。

> [AZURE.NOTE]**ClusterCustomization** 段階ではクラスター ノードで管理者権限を持っているため、スクリプトを使用して、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。そのため、スクリプトの一部として、スクリプトの完了前に Ambari サービスや他の Hadoop 関連のサービスが動作していることを確認する必要があります。これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているか確認する必要があります。これらのサービスに影響するクラスター上の構成を変更する場合は、用意されているヘルパー関数を使用する必要があります。ヘルパー関数の詳細については、「[HDInsight 用の Script Action スクリプトの開発][hdinsight-write-script]」を参照してください。

スクリプトの出力とエラー ログは、クラスター用に指定した既定のストレージ アカウントに格納されます。ログは、**u<\\cluster-name-fragment><\\time-stamp>setuplog** という名前のテーブルに格納されます。これらは、クラスター内のすべてのノード (ヘッドノードとワーカー ノード) で実行されるスクリプトから取得される集計ログです。


各クラスターは、指定された順序で呼び出される複数の Script Action を受け取ることができます。スクリプトはヘッド ノード、ワーカー ノード、またはその両方で実行できます。

## Script Action のスクリプトを呼び出す

Script Action のスクリプトは、Azure プレビュー ポータル、Azure PowerShell、または HDInsight .NET SDK から使用できます。この記事では、ポータルから Script Action を使用する方法を示します。PowerShell と .NET SDK で Script Action を使用する方法については、次の表に示されている例を参照してください。

HDInsight は、HDInsight クラスターで、次のコンポーネントをインストールするためのいくつかのスクリプトを提供します。

名前 | スクリプト
----- | -----
**Spark のインストール** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1「[HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]」をご覧ください。
**R のインストール** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1「[HDInsight クラスターに R をインストールして使用する][hdinsight-install-r]」をご覧ください。
**Solr のインストール** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1「[HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)」をご覧ください。
- **Giraph のインストール** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1「[HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)」をご覧ください。



**Azure プレビュー ポータルから呼び出す**

1. [カスタム オプションを使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)に関するページの説明に従って、クラスターのプロビジョニングを開始します。
2. [オプションの構成] の **[スクリプト アクション]** ブレードで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Script Action を使ってクラスターをカスタマイズする")

	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。</td></tr>
	<tr><td>ヘッド/ワーカー</td>
		<td>カスタマイズ スクリプトが実行されるノード (**[ヘッド]** または **[ワーカー]**) を指定します。</b>
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。</td></tr>
</table>クラスターに複数のコンポーネントをインストールするには、Enter キーを押して複数のスクリプト アクションを追加します。

3. **[選択]** をクリックしてスクリプト アクションの構成を保存し、クラスターのプロビジョニングを続行します。


## HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート
Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。<a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure サポート FAQ Web サイト</a>の**サポート範囲**のセクションでも説明しているように、Microsoft Azure では、オープン ソース テクノロジについて一般的なレベルのサポートを提供しています。HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

- **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。クラスタ コンポーネントの完全な一覧は、「<a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">HDInsight で提供される Hadoop クラスター バージョンの新機能</a>」から入手できます。
- **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

組み込みコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。

> [AZURE.WARNING]HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、[MSDN の HDInsight フォーラム](https://social.msdn.microsoft.com/Forums/azure/ja-JP/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) や [Spark](http://spark.apache.org/) などのプロジェクト サイトがあります。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。
2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。
3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。これらのサンプルはサポートなしで提供されます。

## Script Action のスクリプトを開発する

「[HDInsight 用の Script Action スクリプトの開発][hdinsight-write-script]」を参照してください。


## 関連項目

- 「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision-cluster]」では、その他のカスタム オプションを使用して HDInsight クラスターをプロビジョニングする方法について説明しています。
- [HDInsight 用の Script Action スクリプトの開発][hdinsight-write-script]
- [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]
- [HDInsight クラスターに R をインストールして使用する][hdinsight-install-r]
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "クラスター プロビジョニング時の段階"

<!---HONumber=Oct15_HO1-->