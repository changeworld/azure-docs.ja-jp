<properties 
	pageTitle="デバッグと分析用のヒープ ダンプの収集| Azure" 
	description="デバッグと分析用のヒープ ダンプの収集" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="bradsev"/>

# デバッグと分析用のヒープ ダンプの収集

Hadoop サービスのヒープ ダンプを自動的に収集し、HDInsightHeapDumps 下のユーザーの blob ストレージ アカウント内に置くことができます。  サービスのヒープ ダンプ ファイルはアプリケーションのメモリのスナップショットを含みます。これには、ダンプの作成時の変数の値が含まれます。

さまざまなサービスに対するヒープ ダンプの収集は、各クラスター上のサービスに対して、個別に有効にする必要があります。既定では、クラスターに対してこの機能はオフになっています。これらのヒープ ダンプのサイズは大きくなる可能性があるため、収集を有効にした後はヒープ ダンプの保存先である blob ストレージ アカウントを定期的に確認してください。

## この記事の内容

- [ヒープ ダンプを有効にすることができるサービス](#whichServices)
- [ヒープ ダンプを有効にする構成要素](#configuration)
- [Azure HDInsight PowerShell でヒープ ダンプを有効にする方法](#powershell)
- [HDInsight .NET SDK でヒープ ダンプを有効にする方法](#sdk)


## <a name="whichServices"></a>ヒープ ダンプを有効にすることができるサービス

要求に応じてヒープ ダンプを有効にすることができるサービスは次のとおりです。 

*  **hcatalog**: tempelton
*  **hive**: hiveserver2、metastore、derbyserver 
*  **mapreduce**: jobhistoryserver 
*  **yarn**: resourcemanager、nodemanager、timelineserver 
*  **hdfs**: datanode、secondarynamenode、namenode

## <a name="configuration"></a>ヒープ ダンプを有効にする構成要素

サービスのヒープ ダンプを有効にするために、ユーザーはそのサービスのセクション内に適切な構成要素を設定する必要があります。これは service_name で指定します。

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

<service_name> の値には、上記のように、tempelton、hiveserver2、metastore、derbyserver、jobhistoryserver、resourcemanager、nodemanager、timelineserver、datanode、secondarynamenode、または namenode のいずれかを指定できます。

## <a name="powershell"></a>Azure HDInsight PowerShell でヒープ ダンプを有効にする方法

たとえば、PowerShell を使用して jobhistoryserver のヒープ ダンプを有効にするには ユーザーは次の操作を実行します。

PowerShell SDK を使用:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Azure HDInsight .NET SDK でヒープ ダンプを有効にする方法

たとえば、.NET SDK を使用して jobhistoryserver のヒープ ダンプを有効にするには ユーザーは次の操作を実行します。

C# SDK を使用:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));



<!--HONumber=42-->
