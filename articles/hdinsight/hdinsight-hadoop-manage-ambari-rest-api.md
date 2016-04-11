<properties
   pageTitle="Apache Ambari REST API を使用して HDInsight クラスターを監視および管理する |Microsoft Azure"
   description="Ambari を使用して Linux ベースの HDInsight クラスターを監視および管理する方法を説明します。このドキュメントでは、HDInsight クラスターに含まれている Ambari REST API を使用する方法について説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/29/2016"
   ms.author="larryfr"/>

#Ambari REST API を使用した HDInsight クラスターの管理

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari には使いやすい Web UI と REST API が用意されているため、Hadoop クラスターを簡単に管理および監視できます。Linux ベースの HDInsight クラスターに含まれている Ambari は、クラスターの監視と構成の変更を行うために使用します。このドキュメントでは、クラスター ノードの完全修飾ドメイン名の検索、クラスターによって使用されている既定のストレージ アカウントの検索などの一般的なタスクを実行することによって、Ambari REST API の操作の基本を学習します。

> [AZURE.NOTE] この記事の情報は、Linux ベースの HDInsight クラスターにのみ適用されます。Windows ベースの HDInsight クラスターでは、Ambari REST API で利用できる監視機能の一部のみを利用できます。「[Ambari API を使用した HDInsight の Windows ベースの Hadoop の監視](hdinsight-monitor-use-ambari-api.md)」をご覧ください。

##前提条件

* [cURL](http://curl.haxx.se/): cURL は、コマンドラインから REST API を操作するためのクロスプラットフォーム ユーティリティです。このドキュメントでは、Ambari REST API との通信にこれを使用しています。
* [jq](https://stedolan.github.io/jq/): jq は、JSON ドキュメントを操作するためのクロスプラットフォームのコマンドライン ユーティリティです。このドキュメントでは、Ambari REST API から返される JSON ドキュメントの解析にこれを使用しています。

##<a id="whatis"></a>Ambari とは

[Apache Ambari](http://ambari.apache.org) は、Hadoop クラスターのプロビジョニング、管理、監視に使用する Web UI を簡単に使用できる方法を提供することで Hadoop の管理を簡略化します。開発者は、[Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) を使用して、これらの機能をアプリケーションに統合することができます。

Ambari は既定で Linux ベースの HDInsight クラスターに付属しています。

##REST API

HDInsight の Ambari REST API のベース URI は https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME です。ここで、__CLUSTERNAME__ はクラスターの名前です。

> [AZURE.IMPORTANT] URI (CLUSTERNAME.azurehdinsight.net,) の FQDN (完全修飾ドメイン名) 部分のクラスター名では大文字と小文字が区別されませんが、他に URI が出現する部分では大文字と小文字が区別されます。たとえば、クラスター名が MyCluster であれば、有効な URI は次のようになります。
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> 名前の 2 番目の出現で大文字/小文字の指定が正しくないため、次の URI はエラーを返します。
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

HDInsight の Ambari に接続するには、HTTPS が必要です。クラスターの作成時に指定した管理者アカウント名 (既定値は __admin__) とパスワードを使用して、Ambari を認証する必要もあります。

cURL を使用して REST API に対する GET 要求を実行する例を次に示します。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
このコードを実行する際は、__PASSWORD__ をクラスターの管理者パスワードに置き換え、__CLUSTERNAME__ をクラスター名に置き換えてください。実行すると、次のような情報で始まる JSON ドキュメントが返されます。

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

これは JSON であるため、一般に、データを取得するには、JSON パーサーを使用するのが簡単です。たとえば、(__"Host/host\_status/ALERT"__ 要素に含まれている) アラートの数を取得するには、次のコードを使用して値に直接アクセスすることができます。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
このコードを実行すると、JSON ドキュメントが取得され、その出力がパイプを介して jq に渡されます。`'.Clusters.health_report."Host/host_status/ALERT"'` は、取得する JSON ドキュメント内の要素を示します。

> [AZURE.NOTE] __Host/host\_status/ALERT__ 要素が引用符で囲まれているのは、"/" が要素名の一部であることを示すためです。jq の使い方の詳細については、[jq の Web サイト](https://stedolan.github.io/jq/)を参照してください。

##例: クラスター ノードの FQDN を取得する

HDInsight を使用する際は、クラスター ノードの完全修飾ドメイン名 (FQDN) を知ることが必要になる場合があります。次のコードを使用して、クラスター内のさまざまなノードの FQDN を簡単に取得できます。

* __ヘッド ノード__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __ワーカー ノード__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Zookeeper ノード__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

いずれのコードも同じパターンに従っています。つまり、これらのノードで実行されていることがわかっているコンポーネントを照会し、これらのノードの FQDN が含まれている `host_name` 要素を取得しています。

返されるドキュメントの `host_components` 要素には、複数の項目が含まれています。`.host_components[]` を使用して、要素内のパスを指定すると、各項目に対してループ処理が実行され、特定のパスから値が抽出されます。最初の FQDN エントリなど、1 つの値のみが必要な場合は、項目をコレクションとして返し、特定のエントリを選択することができます。

    jq '[.host_components[].HostRoles.host_name][0]'

このコードは、最初の FQDN をコレクションから返します。

##例: 既定のストレージ アカウントとコンテナーを取得する

HDInsight クラスターを作成する場合は、Azure ストレージ アカウントと、クラスターの既定のストレージとして BLOB コンテナーを使用する必要があります。Ambari を使用すると、クラスターが作成された後にこの情報を取得できます。たとえば、プログラムでコンテナーに直接データを書き込むとします。

次のコードは、クラスターの既定のストレージの WASB URI を取得します。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] このコードは、この情報が含まれるサーバーに適用される最初の構成を返します (`service_config_version=1`)。クラスターの作成後に変更された値を取得する場合は、構成のバージョンを一覧表示した後で最新の構成を取得することが必要になる場合があります。

このコードは、次のような値を返します。ここで、__CONTAINER__ は既定のコンテナー、__ACCOUNTNAME__ は Azure ストレージ アカウント名です。

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

この情報を [Azure CLI](../xplat-cli-install.md) で使用して、コンテナーとの間でデータのアップロードまたはダウンロードを行うことができます。次に例を示します。

1. ストレージ アカウントのリソース グループを取得します。__ACCOUNTNAME__ を、Ambari から取得したストレージ アカウント名に置き換えてください。

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    アカウントのリソース グループ名が返されます。
    
    > [AZURE.NOTE] このコマンドから何も返されない場合は、Azure CLI を Azure リソース マネージャー モードに変更し、コマンドを再度実行することが必要になる場合があります。Azure リソース マネージャー モードに切り替えて、次のコマンドを実行します。
    >
    > `azure config mode arm`
    
2. ストレージ アカウントのキーを取得します。__GROUPNAME__ を、前の手順で取得したリソース グループ名に置き換えてください。__ACCOUNTNAME__ を、ストレージ アカウント名に置き換えてください。

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    アカウントのプライマリ キーが返されます。
    
3. upload コマンドを使用してファイルをコンテナーに保存します。

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    __ACCOUNTNAME__ を、ストレージ アカウント名に置き換えてください。__ACCOUNTKEY__ を、以前に取得したキーに置き換えてください。__FILEPATH__ は、アップロードするファイルへのパスです。__BLOBPATH__ は、コンテナー内のパスです。

    たとえば、wasb://example/data/filename.txt のように HDInsight にファイルが表示されるようにするには、__BLOBPATH__ に `example/data/filename.txt` を指定します。

##例: Ambari 構成の更新

1. Ambari で "desired configuration (望ましい構成)" として格納される現在の構成を取得します。

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    これは、クラスターにインストールされているコンポーネントの現在の構成 (_タグ_値で特定) を含む JSON ドキュメントを返します。たとえば、次は Spark タイプのクラスターから返されるデータからの抜粋です。
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    この一覧から、コンポーネントの名前をコピーする必要があります (たとえば、__spark\_thrift\_sparkconf__ と__タグ__値)。
    
2. 次のコマンドを利用し、コンポーネントとタグの構成を取得します。構成を取得するコンポーネントとタグで __spark-thrift-sparkconf__ と __INITIAL__ を置換します。

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curl が JSON ドキュメントを取得します。jq が使用されていくつかの変更が行われ、構成値の追加/変更に使用できるテンプレートが作成されます。具体的には次の操作が行われます。
    
    * 文字列 "version" と __newtag__ に保存されている日付を含む一意の値が作成されます。
    * 新しい望ましい構成のルート ドキュメントが作成されます。
    * .items array のコンテンツが取得され、__desired\_config__ 要素の下でそれが追加されます。
    * 新しい構成の送信で必要ないため、__href__、__version__、__Config__ 要素が削除されます。
    * 新しい __tag__ 要素が追加され、その値が __version#################__ に設定されます。数値部分は現在の日付に基づきます。構成ごとに一意のタグを与える必要があります。
    
    最後にデータが __newconfig.json__ ドキュメントに保存されます。ドキュメントの構造は次の構造に似たものになります。
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. __newconfig.json__ ドキュメントを開き、__properties__ オブジェクトの値を変更/追加します。たとえば、__"spark.yarn.am.memory"__ の値を __"1g"__ から __"3g"__ に変更し、値を __"256m"__ として __"spark.kryoserializer.buffer.max"__ の新しい要素を追加します。

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    変更が完了したら、ファイルを保存します。

4. 次を利用し、更新した構成を Ambari に送信します。

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    このコマンドは __newconfig.json__ ファイルのコンテンツを curl 要求にパイプ処理します。curl 要求がそれを新しい望ましい構成としてクラスターに送信します。これで JSON ドキュメントが返されます。このドキュメントの __versionTag__ 要素は、送信したバージョンに一致する必要があります。__configs__ オブジェクトには、要求した構成変更が含まれます。

###例: サービス コンポーネントの再起動

この時点で Ambari Web UI に、新しい構成を有効にするには Spark サービスを再起動する必要がある旨が表示されます。次の手順でサービスを再起動します。注意深く見ると、次の指示があります。

1. 次を利用し、Spark サービスのメンテナンス モードを有効にします。

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    これはメンテナンス モードをオンにするサーバー (`echo` ステートメントに含まれています) に JSON ドキュメントを送信します。次の要求を利用すれば、サービスがメンテナンス モードに入っていることを確認できます。
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    これは値 `"ON"` を返します。

3. 次に、次を利用し、サービスをオフにします。

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    次のような応答を受け取ります。
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    この URI で返される `href` 値はクラスター ノードの内部 IP アドレスを利用します。クラスターの外部からこれを利用するには、`10.0.0.18:8080' 部分をクラスターの FQDN に置換します。たとえば、次は要求の状態を取得します。
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    この値が `"COMPLETED"` を返す場合、要求は完了しています。

4. 前の要求が完了したら、次を利用し、サービスを開始します。

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    サービスが再起動すると、新しい構成設定が使用されます。

5. 最後に、次を利用し、メンテナンス モードをオフにします。

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##次のステップ

REST API の完全なリファレンスについては、「[Ambari API リファレンス V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)」をご覧ください。

> [AZURE.NOTE] HDInsight クラウド サービスが管理しているため、一部の Ambari 機能が無効になっています (クラスターに対するホストの追加や削除、新規サービスの追加など)。

<!---HONumber=AcomDC_0330_2016-->