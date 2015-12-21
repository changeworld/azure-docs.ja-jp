<properties
	pageTitle="HDInsight で Hadoop サービスのヒープ ダンプを有効にする | Microsoft Azure"
	description="デバッグと分析のために Linux ベースの HDInsight クラスターから Hadoop サービスのヒープ ダンプを有効にする"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/04/2015"
	ms.author="larryfr"/>


#Linux ベースの HDInsight で Hadoop サービスのヒープ ダンプを有効にする (プレビュー)

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

ヒープ ダンプには、ダンプが作成された時点の変数の値を含む、アプリケーションのメモリのスナップショットが含まれています。これらは、実行時に発生する問題を診断するのに便利です。

> [AZURE.NOTE]この記事の情報は、Linux ベースの HDInsight にのみ適用されます。Windows ベースの HDInsight については、[Windows ベースの HDInsight で Hadoop サービスのヒープ ダンプを有効にする](hdinsight-hadoop-collect-debug-heap-dumps.md)に関するページをご覧ください。

## <a name="whichServices"></a>サービス

次のサービスのヒープ ダンプを有効にできます。

*  **hcatalog** - tempelton
*  **hive** - hiveserver2、metastore、derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager、nodemanager、timelineserver
*  **hdfs** - datanode、secondarynamenode、namenode

HDInsight によって実行されるマップと削減のプロセスに対して、ヒープ ダンプを有効にすることもできます。

## <a name="configuration"></a>ヒープ ダンプ構成について

ヒープ ダンプは、サービスの開始時にオプション (opts またはパラメーターとも呼ばれる) を JVM に渡すことによって有効になります。ほとんどの Hadoop サービスでは、サービスを開始するのに使用されるシェル スクリプトを変更することでこれを実現できます。

各スクリプトには、JVM に渡されるオプションを含む ***\_OPTS** のエクスポートが含まれています。たとえば、**hadoop env.sh** スクリプトには、`export HADOOP_NAMENODE_OPTS=` で始まる行に NameNode サービスのオプションが含まれています。

これらが MapReduce サービスの子プロセスである場合は、マップと削減のプロセスが多少異なります。マップや削減のプロセスはそれぞれ子コンテナーで実行され、これらの JVM オプションを含む 2 つのエントリがあります。どちらも **mapred-site.xml** に含まれています。

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [AZURE.NOTE]Ambari がクラスター内のノード間の変更のレプリケーションを処理するように、Ambari を使用してスクリプトと mapred-site.xml 設定の両方を変更することをお勧めします。具体的な手順については、[Ambari の使用](#using-ambari)のセクションを参照してください。

###ヒープ ダンプを有効にする

次のオプションは、OutOfMemoryError が発生したときに、ヒープ ダンプを有効にします。

    -XX:+HeapDumpOnOutOfMemoryError

**+** は、このオプションが有効になっていることを示します。既定では無効になっています。

> [AZURE.WARNING]ヒープ ダンプは、ダンプ ファイルが大きくなる可能性があるため、既定では HDInsight の Hadoop サービスで無効になっています。トラブルシューティングのために有効にした場合は、問題を再現してダンプ ファイルを収集したら、忘れずに無効にしてください。

###ダンプの場所

ダンプ ファイルの既定の場所は、現在の作業ディレクトリです。次のオプションを使用して、ファイルの保存先を制御できます。

    -XX:HeapDumpPath=/path

たとえば、`-XX:HeapDumpPath=/tmp` を使用すると、ダンプは /tmp ディレクトリに保存されます。

###スクリプト

**OutOfMemoryError** が発生したときに、スクリプトをトリガーすることもできます。たとえば、通知をトリガーすると、エラーが発生したことがわかります。これは、次のオプションを使用して制御されます。

    -XX:OnOutOfMemoryError=/path/to/script

> [AZURE.NOTE]Hadoop は分散システムなので、使用するスクリプトは、サービスを実行しているクラスター内のすべてのノードに配置する必要があります。
>
> また、スクリプトはサービスが実行するアカウントからアクセスできる場所にあり、実行権限を提供する必要もあります。たとえば、スクリプトを `/usr/local/bin` に保存し、`chmod go+rx /usr/local/bin/filename.sh` を使用して読み取りと実行権限を付与できます。

##Ambari の使用

サービスの構成を変更するには、次の手順を実行します。

1. クラスターの Ambari Web UI を開きます。URL は https://YOURCLUSTERNAME.azurehdinsight.net です。

    メッセージが表示されたら、HTTP のアカウント名 (既定値: admin) とクラスターのパスワードを使用してサイトを認証します。

    > [AZURE.NOTE]Ambari によって、ユーザー名とパスワードの入力が 2 回求められる場合があります。その場合は、同じアカウント名とパスワードを再入力してください。

2. 左側の一覧を使用して、変更するサービス領域を選択します。たとえば、**[HDFS]** です。中央の領域で、**[Configs]** タブを選択します。

    ![HDFS Configs タブが選択されている Ambari Web の画像](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. **[フィルター]** エントリに「**opts**」と入力します。これにより、このテキストを含むものに対してのみ構成項目の一覧がフィルター処理され、シェル スクリプトや、これらのオプションの設定に使用できる**テンプレート**をすばやく見つけることができます。

    ![フィルター処理された一覧](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. ヒープ ダンプを有効にするサービスの ***\_OPTS** エントリを検索し、有効にするオプションを追加します。次の図では、`-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` を **HADOOP\_NAMENODE\_OPTS** エントリに追加しました。

    ![HADOOP\_NAMENODE\_OPTS with -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

	> [AZURE.NOTE]マップまたは削減の子プロセスのヒープ ダンプが有効になっている場合は、代わりに、**mapreduce.admin.map.child.java.opts** と **mapreduce.admin.reduce.child.java.opts** というフィールドを検索します。

    **[保存]** ボタンを押して、変更を保存します。変更を説明する短いメモを入力できます。

5. 変更が適用されると、**再起動が必要**であることを示すアイコンが 1 つ以上のサービスの横に表示されます。

    ![再起動が必要アイコンと [再起動] ボタン](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. 再起動が必要な各サービスを選択し、**[サービス アクション]** ボタンを使用して **[メンテナンス モードの有効化]** を選択します。これは、再起動したときに、このサービスからアラートが生成されないようにします。

    ![[メンテナンス モードの有効化] メニュー](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. メンテナンス モードを有効にしたら、サービスの **[再起動]** ボタンを使用して **[すべてを再起動]** を選択します。

    ![[すべて再起動] エントリ](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

    > [AZURE.NOTE]**[再起動]** ボタンのエントリは、サービスによって異なる場合があります。

8. サービスが再開したら、**[サービス アクション]** ボタンを使用して **[メンテナンス モードの無効化]** を選択します。これにより、Ambari はサービスのアラートの監視を再開します。

<!---HONumber=AcomDC_1210_2015-->