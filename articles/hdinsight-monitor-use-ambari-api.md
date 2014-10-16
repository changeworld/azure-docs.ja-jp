<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitor Hadoop clusters in HDInsight using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Ambari API を使用した HDInsight の Hadoop クラスターの監視

Ambari API を使用して HDInsight クラスター Version 2.1 を監視する方法について説明します。

**所要時間:** 15 分

## この記事の内容

-   [Ambari とは][]
-   [前提条件][]
-   [ジャンプ スタート][]
-   [Ambari での API の監視][]
-   [次のステップ][]

## <span id="whatisambari"></span></a>Ambari とは

[Apache Ambari][] は、Apache Hadoop クラスターのプロビジョニング、管理、および監視を目的としています。演算子ツールの直観的なコレクションおよび Hadoop の複雑さが見えないようにする信頼性の高い、一連の API が含まれており、クラスターの操作を単純化しています。API の詳細については、「[Ambari API リファレンス][]」を参照してください。

HDInsight は現在、Ambari の監視機能のみをサポートしています。Ambari API v1.0 は HDInsight クラスター Version 2.1 および 3.0 によってサポートされています。この記事では、HDInsight クラスター Version 2.1 での Ambari API の実行のみを取り上げます。

## <span id="prerequisites"></span></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   **コンピューター**。Azure PowerShell がインストールされ構成されている必要があります。手順については、[Azure PowerShell のインストールおよび構成に関するページ][]を参照してください。PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「[Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)][]」を参照してください。

    [Curl][] は省略可能です。[ここ][]からインストールできます。

    > [WACOM.NOTE] Windows で curl コマンドを使用する場合、オプション値には一重引用符の代わりに二重引用符を使用します。

-   **Azure HDInsight クラスター**。クラスターのプロビジョニングの手順については、「[Azure HDInsight の概要][]」または「[HDInsight クラスターのプロビジョニング][]」を参照してください。このチュートリアルを読み進めるには、次のデータが必要です。

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <tr class="header">
    <th align="left">クラスター プロパティ</th>
    <th align="left">PowerShell 変数名</th>
    <th align="left">値</th>
    <th align="left">説明</th>
    </tr>
    <tr class="odd">
    <td align="left">HDInsight クラスター名</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">HDInsight クラスターの名前です。</td>
    </tr>
    <tr class="even">
    <td align="left">クラスター ユーザー名</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">プロビジョニングで指定されたクラスター ユーザー名です。</td>
    </tr>
    <tr class="odd">
    <td align="left">クラスター パスワード</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">クラスター ユーザー パスワードです。</td></tr>
    </table>

    > [WACOM.NOTE] テーブルに値を入力します。そうしておくと、このチュートリアルを読み進める際に役に立ちます。

## <span id="jumpstart"></span></a>ジャンプ スタート

いくつかの方法で、Ambari を使用して HDInsight クラスターを監視することができます。

**Azure PowerShell の使用**

MapReduce jobtracker 情報を取得するための PowerShell スクリプトを以下に示します。

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'mapred.JobTracker'

出力は次のようになります。

![Jobtracker の出力][]

**Curl の使用**

Curl を使用してクラスター情報を取得する例を以下に示します。

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

出力は次のようになります。

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"workernode0"}}]}

## <span id="monitor"></span></a>Ambari での API の監視

以下のテーブルに最も一般的な Ambari での API 呼び出しの監視の一部を示します。API の詳細については、「[Ambari API リファレンス][]」を参照してください。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<tr class="header">
<th align="left">API 呼び出しの監視</th>
<th align="left">URI</th>
<th align="left">説明</th>
</tr>
<tr class="odd">
<td align="left">クラスターの取得</td>
<td align="left">/api/v1/clusters</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">クラスター情報の取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</td>
<td align="left">クラスター、サービス、ホスト</td>
</tr>
<tr class="odd">
<td align="left">サービスの取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</td>
<td align="left">含まれるサービス: hdfs、mapreduce</td>
</tr>
<tr class="even">
<td align="left">サービス情報の取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">サービス コンポーネントの取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</td>
<td align="left">HDFS: namenode、datanode<br />MapReduce: jobtracker、tasktracker</td>
</tr>
<tr class="even">
<td align="left">コンポーネント情報の取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</td>
<td align="left">ServiceComponentInfo、host-components、metrics</td>
</tr>
<tr class="odd">
<td align="left">ホストの取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</td>
<td align="left">headnode0、workernode0</td>
</tr>
<tr class="even">
<td align="left">ホスト情報の取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">ホスト コンポーネントの取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components</td>
<td align="left">namenode、resourcemanager</td>
</tr>
<tr class="even">
<td align="left">ホスト コンポーネント情報の取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;</td>
<td align="left">HostRoles、component、host、metrics</td>
</tr>
<tr class="odd">
<td align="left">構成の取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations</td>
<td align="left">構成の種類: core-site、hdfs-site、mapred-site、hive-site</td>
</tr>
<tr class="even">
<td align="left">構成情報の取得</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&amp;tag=&lt;VersionName&gt;</td>
<td align="left">構成の種類: core-site、hdfs-site、mapred-site、hive-site</td>
</tr>
</table>

## <span id="nextsteps"></span></a> 次のステップ

ここでは Ambari での API 呼び出しの監視の使用方法を説明しました。詳細については、次を参照してください。

-   [管理ポータルを使用した HDInsight クラスターの管理][]
-   [Azure PowerShell を使用した HDInsight クラスターの管理][]
-   [コマンド ライン インターフェイスを使用した HDInsight クラスターの管理][]
-   [HDInsight のドキュメント][]
-   [HDInsight の使用][Azure HDInsight の概要]

  [Ambari とは]: #whatisambari
  [前提条件]: #prerequisites
  [ジャンプ スタート]: #jumpstart
  [Ambari での API の監視]: #monitor
  [次のステップ]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [Ambari API リファレンス]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell/
  [Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [ここ]: http://curl.haxx.se/download.html
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Jobtracker の出力]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [管理ポータルを使用した HDInsight クラスターの管理]: ../hdinsight-administer-use-management-portal/
  [Azure PowerShell を使用した HDInsight クラスターの管理]: ../hdinsight-administer-use-powershell/
  [コマンド ライン インターフェイスを使用した HDInsight クラスターの管理]: ../hdinsight-administer-use-command-line/
  [HDInsight のドキュメント]: /ja-jp/documentation/services/hdinsight/
