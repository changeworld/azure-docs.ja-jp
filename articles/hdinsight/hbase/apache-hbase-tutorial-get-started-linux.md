---
title: HDInsight での HBase の例の概要 - Azure
description: この Apache HBase の例に従って、HDInsight で Hadoop を使い始めることができます。 HBase シェルからテーブルを作成し、Hive を使用したクエリを実行します。
keywords: hbasecommand,hbase の例
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 9d94a976c08cdb5184ea4c5e2cd70ac039d78378
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384695"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>HDInsight で Apache HBase の例を使用する

HDInsight で [Apache HBase](https://hbase.apache.org/) クラスターを作成する方法、HBase テーブルを作成する方法、[Apache Hive](https://hive.apache.org/) を使用してテーブルを照会する方法について説明します。  HBase に関する一般的な情報については、[HDInsight HBase の概要](./apache-hbase-overview.md)に関するページを参照してください。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* Bash。 この記事の例では、curl コマンドのために Windows 10 上で Bash シェルを使用します。 インストール手順については、「[Windows Subsystem for Linux Installation Guide for Windows 10 (Windows 10 用 Windows Subsystem for Linux インストール ガイド)](https://docs.microsoft.com/windows/wsl/install-win10)」をご覧ください。  他の [Unix シェル](https://www.gnu.org/software/bash/)も動作します。  これらの curl の例は、少し変更すれば、Windows コマンド プロンプトで動作できます。  あるいは、Windows PowerShell コマンドレット [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod) を使用できます。


## <a name="create-apache-hbase-cluster"></a>Apache HBase クラスターを作成する

次の手順では、Azure Resource Manager テンプレートを使用して、HBase クラスターと依存する既定の Azure Storage アカウントを作成します。 この手順で使用するパラメーターとその他のクラスター作成方法について理解するには、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 Data Lake Storage Gen2 の使用について詳しくは、「[クイック スタート:HDInsight のクラスターを設定する](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」をご覧ください。

1. 次の画像を選択して Azure Portal でテンプレートを開きます。 テンプレートは [Azure クイック スタート テンプレート集](https://azure.microsoft.com/resources/templates/)にあります。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. **[カスタム デプロイ]** ブレードで以下の値を入力します。

    |プロパティ |説明 |
    |---|---|
    |サブスクリプション|クラスターの作成に使用する Azure サブスクリプションを選択します。|
    |リソース グループ|Azure リソース管理グループを作成するか、または既存のグループを使用します。|
    |Location|リソース グループの [場所] を指定します。 |
    |ClusterName|HBase クラスターの名前を入力します。|
    |クラスター ログイン名とパスワード|既定のログイン名は **admin** です。|
    |SSH ユーザー名とパスワード|既定のユーザー名は **sshuser** です。|

    その他のパラメーターは省略可能です。  

    各クラスターには Azure ストレージ アカウントとの依存関係があります。 クラスターを削除すると、データはストレージ アカウントに保持されます。 クラスターの既定のストレージ アカウント名は、クラスター名に "store" が追加されたものです。 これは、テンプレートの variables セクションでハードコードされます。

3. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。 クラスターの作成には約 20 分かかります。

> [!NOTE]  
> HBase クラスターを削除したら、同じ既定の BLOB コンテナーを使用して別の HBase クラスターを作成できます。 新しいクラスターでは、元のクラスターで作成した HBase テーブルを選択します。 不整合を回避するために、クラスターを削除する前に HBase テーブルを無効にしておくことをお勧めします。

## <a name="create-tables-and-insert-data"></a>テーブルを作成してデータを挿入する

SSH を使用して HBase クラスターに接続し、[Apache HBase シェル](https://hbase.apache.org/0.94/book/shell.html)を使用して HBase テーブルの作成、データの挿入、データのクエリを行うことができます。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

多くの場合、データは次のような表形式で表示されます。

![HDInsight HBase の表形式データ](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

HBase ([クラウド BigTable](https://cloud.google.com/bigtable/) の実装) では、同じデータが次のように表示されます。

![HDInsight HBase の Bigtable データ](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**HBase シェルを使用するには**

1. `ssh` コマンドを使用して HBase クラスターに接続します。 次のコマンドを編集して `CLUSTERNAME` をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `hbase shell` コマンドを使用して、HBase 対話型シェルを起動します。 SSH 接続で次のコマンドを入力します。

    ```bash
    hbase shell
    ```

1. `create` コマンドを使用して、2 つの列ファミリを持つ HBase テーブルを作成します。 テーブル名と列名は大文字と小文字が区別されます。 次のコマンドを入力します。

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. `list` コマンドを使用して、HBase 内のすべてのテーブルを一覧表示します。 次のコマンドを入力します。

    ```hbase
    list
    ```

1. `put` コマンドを使用して、特定のテーブルの指定行の指定列に値を挿入します。 次のコマンドを入力します。

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. `scan` コマンドを使用して、`Contacts` テーブルのデータをスキャンして返します。 次のコマンドを入力します。

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Hadoop HBase シェル](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. `get` コマンドを使用して、行のコンテンツを取り込みます。 次のコマンドを入力します。

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    1 行しかないので、`scan` コマンドを使用したときと同じような結果が表示されます。

    HBase テーブル スキーマの詳細については、[Apache HBase スキーマの設計の概要](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)に関するページを参照してください。 HBase コマンドの詳細については、「[Apache HBase のリファレンス ガイド](https://hbase.apache.org/book.html#quickstart)」を参照してください。

1. `exit` コマンドを使用して、HBase 対話型シェルを停止します。 次のコマンドを入力します。

    ```hbaseshell
    exit
    ```

**Contacts HBase テーブルにデータを一括で読み込むには**

HBase では、いくつかの方法でテーブルにデータを読み込ことができます。  詳細については、 [一括読み込み](https://hbase.apache.org/book.html#arch.bulk.load)に関するページを参照してください。

サンプルのデータ ファイルは、パブリック BLOB コンテナー `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt` にあります。  このデータ ファイルの内容は次のとおりです。

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

必要に応じて、自分でテキスト ファイルを作成し、そのファイルを自分のストレージ アカウントにアップロードできます。 手順については、「[HDInsight で Apache Hadoop ジョブのデータをアップロードする](../hdinsight-upload-data.md)」を参照してください。

> [!NOTE]  
> この手順では、前回の手順で作成した Contacts HBase テーブルを使用します。

1. 開いている ssh 接続から、次のコマンドを実行してデータ ファイルを StoreFile に変換し、`Dimporttsv.bulk.output` で指定された相対パスに格納します。

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. 次のコマンドを実行して、データを `/example/data/storeDataFileOutput` から HBase テーブルにアップロードします。

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. HBase シェルを開き、`scan` コマンドを使用してテーブルの内容を一覧表示することができます。

## <a name="use-apache-hive-to-query-apache-hbase"></a>Apache Hive を使用して Apache HBase を照会する

[Apache Hive](https://hive.apache.org/) を使用して HBase テーブルのデータを照会できます。 このセクションでは、HBase テーブルにマッピングする Hive テーブルを作成し、作成した Hive テーブルを使用して HBase テーブルのデータを照会します。

1. 開いている ssh 接続から、次のコマンドを使用して Beeline を開始します。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Beeline の詳細については、「[Beeline による HDInsight での Hive と Hadoop の使用](../hadoop/apache-hadoop-use-hive-beeline.md)」を参照してください。

1. 次の [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) スクリプトを実行して、HBase テーブルにマップする Hive テーブルを作成します。 ここで、HBase シェルを使用して、先ほど参照したサンプル テーブルが HBase に作成されたことを確認してから、このステートメントを実行してください。

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. 次の HiveQL スクリプトを実行して、HBase テーブルのデータを照会します。

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Beeline を終了するには、 `!exit`を使用します。

1. ssh 接続を終了するには、`exit` を使用します。

## <a name="use-hbase-rest-apis-using-curl"></a>Curl を使用して HBase REST API を使用する

REST API のセキュリティは、 [基本認証](https://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

1. 使いやすさのために環境変数を開始します。 下のコマンドを編集して `MYPASSWORD` をクラスター ログイン パスワードに置き換えます。 `MYCLUSTERNAME` を HBase クラスターの名前に置き換えます。 その後、これらのコマンドを入力します。

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. 次のコマンドを使用して、既存の HBase テーブルを一覧表示します。

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. 次のコマンドを使用して、2 つの列ファミリを含む新しい HBase テーブルを作成します。

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    スキーマは、JSon 形式で提供されます。
1. 次のコマンドを使用して、一部のデータを挿入します。

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    -d スイッチで指定された値に base64 エンコードを使用する必要があります。 この例では次のとおりです。

   * MTAwMA==:1,000
   * UGVyc29uYWw6TmFtZQ==:Personal:Name
   * Sm9obiBEb2xl:John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) を使用すると、複数の (バッチ処理された) 値を挿入できます。

1. 次のコマンドを使用して、1 行を取得します。

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

HBase Rest の詳細については、「 [Apache HBase reference guide (Apache HBase リファレンス ガイド)](https://hbase.apache.org/book.html#_rest)」をご覧ください。

> [!NOTE]  
> Thrift は、HDInsight での HBase ではサポートされていません。
>
> Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 また、サーバーへの要求の送信に使用する Uniform Resource Identifier (URI) にクラスター名を含める必要があります。
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    次のような応答が返されます。
>   
>        {"status":"ok","version":"v1"}


## <a name="check-cluster-status"></a>クラスターの状態の確認

HDInsight の HBase には、クラスターを監視するための Web UI が付属します。 この Web UI を使用すると、統計情報やリージョンに関する情報を要求できます。

**HBase Master UI にアクセスするには**

1. `https://Clustername.azurehdinsight.net` で Ambari Web UI にサインインします。
2. 左側のメニューで **[HBase]** をクリックします。
3. ページの上部にある **[Quick links (クイック リンク)]** をクリックし、アクティブな Zookeeper ノード リンクをポイントして、 **[HBase Master UI]** をクリックします。  UI は別のブラウザー タブで開かれます。

   ![HDInsight HBase Master UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   HBase Master UI には次のセクションがあります。

   - リージョン サーバー
   - バックアップ マスター
   - tables
   - タスク
   - ソフトウェア属性

## <a name="delete-the-cluster"></a>クラスターを削除する

不整合を回避するために、クラスターを削除する前に HBase テーブルを無効にしておくことをお勧めします。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](../hdinsight-hadoop-customize-cluster-linux.md#access-control)」を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、Apache HBase クラスターの作成方法と、テーブルを作成してそのテーブルのデータを HBase シェルから表示する方法について学習しました。 また、HBase テーブルのデータに対して Hive クエリを使用する方法と、HBase C# REST API を使用して HBase テーブルを作成し、テーブルからデータを取得する方法についても学習しました。

詳細については、次を参照してください。

* [HDInsight HBase の概要](./apache-hbase-overview.md):Apache HBase は、Apache Hadoop 上に構築された Apache 用のオープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。