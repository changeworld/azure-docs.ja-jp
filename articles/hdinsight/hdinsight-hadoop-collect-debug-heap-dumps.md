<properties
	pageTitle="ヒープ ダンプによる Hadoop サービスのデバッグおよび分析 | Microsoft Azure"
	description="デバッグおよび分析に利用できるように、自動的に Hadoop サービスのヒープ ダンプを収集して Azure BLOB ストレージ アカウント内に配置します。"
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
	ms.date="07/09/2015"
	ms.author="jgao"/>


# BLOB ストレージのヒープ ダンプを収集して Hadoop サービスをデバッグおよび分析する

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Hadoop サービスのヒープ ダンプを自動的に収集し、HDInsightHeapDumps 下のユーザーの Azure BLOB ストレージ アカウント内に置くことができます。サービスのヒープ ダンプ ファイルはアプリケーションのメモリのスナップショットを含みます。これには、ダンプの作成時の変数の値が含まれます。

さまざまなサービスに対するヒープ ダンプの収集は、各クラスター上のサービスに対して、個別に有効にする必要があります。既定では、クラスターに対してこの機能はオフになっています。これらのヒープ ダンプのサイズは大きくなる可能性があるため、収集を有効にした後はヒープ ダンプの保存先である BLOB ストレージ アカウントを定期的に確認してください。

> [AZURE.NOTE]この記事の情報は、Windows ベースの HDInsight にのみ適用されます。Linux ベースの HDInsight については、「[Linux ベースの HDInsight で Hadoop サービスのヒープ ダンプを有効にする](hdinsight-hadoop-collect-debug-heap-dump-linux.md)」を参照してください。

## <a name="whichServices"></a>ヒープ ダンプの対象サービス

要求に応じてヒープ ダンプを有効にすることができるサービスは次のとおりです。

*  **hcatalog** - tempelton
*  **hive** - hiveserver2、metastore、derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager、nodemanager、timelineserver
*  **hdfs** - datanode、secondarynamenode、namenode

## <a name="configuration"></a>ヒープ ダンプを有効にする構成要素

サービスのヒープ ダンプを有効にするには、そのサービスのセクション内に適切な構成要素を設定する必要があります。これは **service\_name** で指定します。

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

**service\_name** の値には、上記のように、tempelton、hiveserver2、metastore、derbyserver、jobhistoryserver、resourcemanager、nodemanager、timelineserver、datanode、secondarynamenode、namenode のいずれかを指定できます。

## <a name="powershell"></a>Azure PowerShell を使用してヒープ ダンプを有効にする方法

たとえば、Azure PowerShell を使用して jobhistoryserver のヒープ ダンプを有効にするには、次の操作を実行します。

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Azure HDInsight .NET SDK でヒープ ダンプを有効にする方法

たとえば、Azure HDInsight .NET SDK を使用して jobhistoryserver のヒープ ダンプを有効にするには、次の操作を実行します。

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=August15_HO6-->