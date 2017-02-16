---
title: "Linux ベースの HDInsight で Hadoop を使用するためのヒント | Microsoft Docs"
description: "ここには、Azure クラウドで稼動する使い慣れた Linux 環境で、Linux ベースの HDInsight (Hadoop) クラスターを使用するための実装上のヒントを記載します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 854df55045d4d9a062ade3905b9be1daad0a7d8c
ms.openlocfilehash: 3ce0444427ba10e7247aec500017ea4bae3af161


---
# <a name="information-about-using-hdinsight-on-linux"></a>Linux での HDInsight の使用方法

Linux ベースの Azure HDInsight クラスターは、Azure クラウドで実行される使い慣れた Linux 環境での Hadoop を提供します。 使い方は、ほとんどの点で、Linux 環境の他の Hadoop とまったく同じです。 ここでは、知っておく必要がある特定の違いについて説明します。

## <a name="prerequisites"></a>前提条件

このドキュメントの手順の多くでは次のユーティリティを使用するので、これらがシステムにインストールされている必要があります。

* [cURL](https://curl.haxx.se/) - Web ベースのサービスとの通信に使用します
* [jq](https://stedolan.github.io/jq/) - JSON ドキュメントの解析に使用します
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (プレビュー) - Azure サービスをリモート管理するために使用します

## <a name="users"></a>Users

HDInsight は、[ドメイン参加済み](hdinsight-domain-joined-introduction.md)でない限り**シングルユーザー** システムであるとみなされます。 このクラスターでは、管理者レベルのアクセス許可を持つ単一の SSH ユーザー アカウントを作成します。 追加の SSH アカウントを作成することもできますが、これらのアカウントもクラスターに対する管理者アクセスを持ちます。

ドメイン参加済みの HDInsight では、複数のユーザーがサポートされ、アクセス許可とロールの設定を詳細に行うことができます。 詳細については、[ドメイン参加済み HDInsight クラスターの管理](hdinsight-domain-joined-manage.md)に関するページを参照してください。

## <a name="domain-names"></a>ドメイン名

インターネットからクラスターへの接続時に使用する完全修飾ドメイン名 (FQDN) は、**&lt;<clustername>.azurehdinsight.net** または (SSH のみ) **&lt;clustername-ssh>.azurehdinsight.net** です。

内部的には、クラスターの各ノードに、クラスターの構成時に割り当てられた名前が与えられます。 クラスター名を見つけるには、Ambari Web UI の **[Hosts] (ホスト)** ページにアクセスするか、次の方法で Ambari REST API からホストの一覧を返します。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

**PASSWORD** は管理者アカウントのパスワードに、**CLUSTERNAME** はクラスターの名前に置き換えます。 これで返される JSON ドキュメントにクラスター内のホストの一覧が含まれます。jq により、クラスター内の各ホストの `host_name` 要素値が取り出されます。

特定のサービスのノード名を見つける必要がある場合、そのコンポーネントについて Ambari に問い合わせることができます。 たとえば、HDFS 名のノードのホストを見つけるには、次の方法を利用します。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

これでサービスの説明が記載された JSON ドキュメントが返されます。jq により、ホストの `host_name` 値のみが引き出されます。

## <a name="remote-access-to-services"></a>サービスへのリモート アクセス

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

    クラスター管理者のユーザー名とパスワードを使用して認証した後、Ambari にログインします。 その場合も、クラスター管理者のユーザー名とパスワードを使用します。

    認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

    > [!IMPORTANT]
    > クラスター用の Ambari にはインターネットから直接アクセスできますが、一部の機能では、クラスターが使用する内部ドメイン名によってノードにアクセスします。 これは内部ドメイン名で、パブリックではないため、インターネット経由で機能にアクセスしようとすると、サーバーが見つからないことを示すエラーが発生します。
    >
    > Ambari Web UI の全機能を使用するには、SSH トンネルを使用して、クラスター ヘッド ノードに対する Web トラフィックがプロキシ経由になるようにします。 「 [SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

    > [!NOTE]
    > クラスター管理者のユーザー名とパスワードを使用して認証します。
    >
    > 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

    > [!NOTE]
    > クラスター管理者のユーザー名とパスワードを使用して認証します。
    >
    > 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **SSH** -  ポート 22 または 23 上の &lt;clustername>-ssh.azurehdinsight.net。 ポート 22 はプライマリ ヘッドノードへの接続に、23 はセカンダリ ヘッドノードへの接続に使用されます。 ヘッド ノードの詳細については、「 [HDInsight における Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)」を参照してください。

    > [!NOTE]
    > クラスター ヘッド ノードにアクセスするには、クライアント コンピューターから SSH を使用する必要があります。 接続されたら、ヘッドノードから SSH を使用して worker ノードにアクセスできます。

## <a name="file-locations"></a>ファイルの場所

Hadoop 関連ファイルは、 `/usr/hdp`のクラスター ノードにあります。 このディレクトリには、次のサブディレクトリが含まれます。

* **2.2.4.9-1**: このディレクトリは HDInsight が使用する Hortonworks Data Platform のバージョンから名前が付けられるため、クラスター上の番号はここに記載されたものと異なる場合があります。
* **current**: このディレクトリには、**2.2.4.9-1** ディレクトリ下のディレクトリへのリンクが含まれており、(変わる可能性がある) バージョン番号を、ファイルへのアクセスのたびに入力する手間を省くために存在します。

サンプル データ ファイルと JAR ファイルは、Hadoop 分散ファイル システム (HDFS) または Azure BLOB ストレージの '/example' または 'wasbs:///example' にあります。

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS、Azure BLOB ストレージ、ストレージのベスト プラクティス

ほとんどの Hadoop ディストリビューションでは、HDFS はクラスター内のコンピューターのローカル ストレージによって支えられています。 これは効率的である一方、コンピューティング リソースが時間または分単位で課金されるクラウド ベースのソリューションでは、コストが高くなります。

HDInsight は Azure BLOB ストレージを既定の保管場所として使用します。これには次の利点があります。

* 低コストの長期ストレージ
* Websites、ファイル アップロード/ダウンロード ユーティリティ、さまざまな言語の SDK、Web ブラウザーなどの外部サービスからアクセスできます。

HDInsight の既定の保管場所であるため、通常は何もしなくても使用できます。 たとえば、次のコマンドは、Azure BLOB ストレージに保存されている **/example/data** フォルダー内のファイルの一覧を表示します。

    hdfs dfs -ls /example/data

一部のコマンドでは、BLOB ストレージを使用することを指定する必要があります。 その場合、コマンドにプレフィックスとして **wasb://** または **wasbs://** を付けます。

HDInsight では、クラスターに複数の BLOB ストレージ アカウントを関連付けることもできます。 既定以外の Blob Storage アカウントのデータにアクセスするには、**wasbs://&lt;container-name>@&lt;account-name>.blob.core.windows.net/** という形式を使用します。 たとえば、次のコマンドは、指定したコンテナーと BLOB ストレージ アカウントについて、 **/example/data** ディレクトリの内容を表示します。

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>クラスターで使用している BLOB ストレージ

クラスター作成時、既存の Azure ストレージ アカウントとコンテナーを使用するか、新しく作成するかを選択します。 後になって、何を選択したかを忘れることもあります。 Ambari REST API を使用して、既定のストレージ アカウントとコンテナーを調べることができます。

1. 次のコマンドを使用して、curl を使用する HDFS の構成情報を取得し、 [jq](https://stedolan.github.io/jq/)を使用してフィルター処理します。

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'

    > [!NOTE]
    > このコードは、この情報が含まれるサーバーに適用される最初の構成を返します (`service_config_version=1`)。 クラスターの作成後に変更された値を取得する場合は、構成のバージョンを一覧表示した後で最新の構成を取得することが必要になる場合があります。

    このコマンドは、次のような値を返します。ここで、**CONTAINER** は既定のコンテナー、**ACCOUNTNAME** は Azure Storage アカウント名です。

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

2. 次のコマンドを使用して、ストレージ アカウントの一意の ID を取得します。 次のコマンドで、**ACCOUNTNAME** を、Ambari から取得したストレージ アカウント名に置き換えます。

        az storage account list --query "[?name=='ACCOUNTNAME'].id --out list

3. 次のコマンドを使用して、ストレージ アカウントのキーを取得します。 **STORAGEID** には、ストレージ アカウントの ID を指定します。

        az storage account keys list --ids STORAGEID --out list

    アカウントのプライマリ キーが返されます。

次のように Azure ポータルを使用して、ストレージ情報を検索することもできます。

1. [Azure ポータル](https://portal.azure.com/)で HDInsight クラスターを選択します。
2. **[要点]** セクションで、**[すべての設定]** を選択します。
3. **[設定]** で **[Azure Storage キー]** を選択します。
4. **[Azure Storage キー]** で、表示されているストレージ アカウントのいずれかを選択します。 これにより、ストレージ アカウントに関する情報が表示されます。
5. キーのアイコンを選択します。 これにより、このストレージ アカウントのキーが表示されます。

### <a name="how-do-i-access-blob-storage"></a>BLOB ストレージにアクセスする方法

クラスターから Hadoop コマンドを使用する以外にも、BLOB にアクセスするさまざまな方法があります。

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): Azure を使用するためのコマンド ライン インターフェイス コマンド。 インストール後、ストレージの使用方法については `az storage`、BLOB 特有のコマンドについては `az storage blob` をご覧ください。
* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Azure Storage で BLOB を使用するための Python スクリプト。
* さまざまな SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.JS](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [ストレージ REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## <a name="a-namescalingascaling-your-cluster"></a><a name="scaling"></a>クラスターのスケーリング

クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用されるデータ ノードの数を、クラスターを削除して再作成することなく、変更できます。

クラスターで他のジョブまたはプロセスを実行している間にスケーリング操作を実行できます。

別のクラスターの種類は、次のようにスケーリングすることによって影響を受けます。

* **Hadoop**: クラスター内のノードの数をスケール ダウンすると、クラスター内のサービスの一部が再起動されます。 これにより、スケーリング操作の完了時に、実行中および保留中のジョブが失敗する可能性があります。 ただし、操作が完了した時点で、ジョブを再送信できます。
* **HBase**: リージョンのサーバーは、スケーリング操作の完了から数分以内に自動的に調整されます。 リージョンのサーバーを手動で調整するには、次の手順を使用します。

    1. SSH を使用して HDInsight クラスターに接続します。 HDInsight での SSH の使用方法の詳細については、次のいずれかのドキュメントを参照してください。

        * [Linux、Unix、Mac OS X から HDInsight で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Windows から HDInsight で SSH (PuTTY) を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

    2. HBase シェルを開始するには、次のコマンドを使用します。

            hbase shell
    
    3. HBase シェルが読み込まれたら、次のコマンドを使用してリージョンのサーバーを手動で調整します。

            balancer

* **Storm**: スケーリング操作が実行された後に、実行中の Storm トポロジをすべて再調整する必要があります。 これにより、トポロジはクラスター内のノードの新しい数に基づいて並列処理の設定を再調整できます。 実行中のトポロジを再調整するには、次のオプションのいずれかを使用します。

    * **SSH**: サーバーに接続し、次のコマンドを使用してトポロジを再調整します。

            storm rebalance TOPOLOGYNAME

        また、トポロジによって最初に提供された並列処理のヒントを上書きするパラメーターも指定できます。 たとえば、 `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` は、トポロジを再構成して、5 個の worker プロセス、blue-spout コンポーネント用の 3 個の実行プログラム、yellow-bolt コンポーネント用の 10 個の実行プログラムにします。

    * **Storm UI**: 次の手順により、Storm UI を使用してトポロジを再調整します。

        1. Web ブラウザーで **https://CLUSTERNAME.azurehdinsight.net/stormui** にアクセスします。CLUSTERNAME は実際の Storm クラスターの名前です。 メッセージが表示されたら、HDInsight クラスター管理者 (admin) の名前と、クラスターの作成時に指定したパスワードを入力します。
        2. 再調整するトポロジを選択し、 **[Rebalance] (再調整)** ボタンをクリックします。 再調整の操作が実行されるまでの待ち時間を入力します。

HDInsight クラスターのスケーリングに関する具体的な情報については、以下を参照してください。

* [Azure ポータルを使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hue (またはその他の Hadoop コンポーネント) のインストール方法

HDInsight は、管理されたサービスです。つまり、問題が検出された場合、Azure によってクラスター内のノードは自動的に破棄されて、再プロビジョニングされる可能性があります。 そのため、クラスター ノードにコンポーネントを直接、手動でインストールすることは推奨されません。 代わりに、次をインストールする必要がある場合は [HDInsight スクリプト アクション](hdinsight-hadoop-customize-cluster.md)を使用します。

* Spark や Hue など、サービスや Web サイト。
* クラスターの複数のノードで構成変更を必要とするコンポーネント。 たとえば、必要な環境変数、ログ ディレクトリの作成、構成ファイルの作成。

スクリプト アクションとは、クラスターのプロビジョニング中に実行される Bash スクリプトで、クラスター上に追加のコンポーネントをインストールし、構成するために使用できます。 次のコンポーネントをインストールするスクリプトの例が用意されています。

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

独自のスクリプト アクションを開発する方法の詳細については、「 [HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」を参照してください。

### <a name="jar-files"></a>Jar ファイル

一部の Hadoop 技術は自己完結型の jar ファイルで提供されます。このファイルには MapReduce ジョブの一環として、あるいは Pig または Hive 内から使用される関数が含まれています。 ファイルはスクリプト アクションでインストールできますが、多くの場合、セットアップを必要とせず、プロビジョニング後にクラスターにアップロードし、直接使用できます。 クラスターの再イメージ化の後もコンポーネントが残っていることを確認するには、WASB に jar ファイルを保存します。

たとえば、最新版の [DataFu](http://datafu.incubator.apache.org/)を使用する場合、プロジェクトを含む jar をダウンロードし、それを HDInsight クラスターにアップロードできます。 その後、Pig や Hive からそれを使用する方法については、DataFu のドキュメントに従います。

> [!IMPORTANT]
> スタンドアロン jar ファイルである一部のコンポーネントは HDInsight で提供され、パスにはありません。 特定のコンポーネントを探している場合、次を利用し、クラスターでそれを検索できます。
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 一致する jar ファイルがあれば、そのパスが返されます。

クラスターがスタンドアロン jar ファイルとしてあるバージョンのコンポーネントを提供するが、別のバージョンを希望する場合、クラスターに新しいバージョンのコンポーネントをアップロードし、ジョブでそれを試すことができます。

> [!WARNING]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) や [Spark](http://spark.apache.org/) などのプロジェクト サイトがあります。

## <a name="next-steps"></a>次のステップ

* [Windows ベースの HDInsight から Linux ベースへの移行](hdinsight-migrate-from-windows-to-linux.md)
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)



<!--HONumber=Dec16_HO1-->


