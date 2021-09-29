---
title: Hive の 3.6 から 4.0 への移行に関するトラブルシューティング - Azure HDInsight
description: HDInsight 3.6 から 4.0 への Hive ワークロードの移行に関するトラブルシューティング ガイド
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/12/2021
ms.openlocfilehash: eecf8cd36c1f631176ce836523be802d751ed55b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625964"
---
# <a name="troubleshooting-guide-for-migration-of-hive-workloads-from-hdinsight-36-to-hdinsight-40"></a>HDInsight 3.6 から HDInsight 4.0 への Hive ワークロードの移行に関するトラブルシューティング ガイド

この記事では、Hive ワークロードを HDInsight 3.6 から HDInsight 4.0 に移行するときにお客様が直面する最も一般的な問題の一部について、回答を示します。

## <a name="reduce-latency-when-running-describe-table_name"></a>`DESCRIBE TABLE_NAME` の実行時に待機時間を短縮する

対応策 :

* 1 つのバッチでメタストアから取得できるオブジェクト (テーブルまたはパーティション) の最大数を増やします。 待機時間が十分なレベルに達するまで、大きな数値に設定します (既定値は 300)。 数値が大きいほど、必要とされる Hive メタストア サーバーへのラウンド トリップ数は減少しますが、クライアント側でより多くのメモリが必要になることもあります。

  `hive.metastore.batch.retrieve.max=2000`

* Hive と、古くなったすべてのサービスを再起動します

## <a name="unable-to-query-gzipped-text-file-if-skipheaderlinecount-and-skipfooterlinecount-are-set-for-table"></a>テーブルに対して skip.header.line.count と skip.footer.line.count が設定されている場合に GZip 圧縮されたテキスト ファイルのクエリを実行できない

問題は Interactive Query 4.0 で修正されましたが、Interactive Query 3.1.0 ではまだ修正されていません

対応策 :
* ```"skip.header.line.count"="1"``` と ```"skip.footer.line.count"="1"``` を使用せずにテーブルを作成してから、元のテーブルから、クエリでヘッダーおよびフッター行を除外するビューを作成します。

## <a name="unable-to-use-unicode-characters"></a>Unicode 文字を使用できない

対応策 :
1. クラスターのハイブ メタストア データベースに接続します。

2. 次のコマンドを使用して、`TBLS` テーブルと `TABLE_PARAMS` テーブルのバックアップを作成します。
    ```sql
        select * into tbls_bak from tbls;
        select * into table_params_bak from table_params;
    ```

3. 影響を受ける列の種類を手動で `nvarchar(max)` に変更します。
    ```sql 
        alter table TABLE_PARAMS alter column PARAM_VALUE nvarchar(max);
        alter table TBLS alter column VIEW_EXPANDED_TEXT nvarchar(max) null;    
        alter table TBLS alter column VIEW_ORIGINAL_TEXT nvarchar(max) null;
    ```

## <a name="create-table-as-select-ctas-creates-a-new-table-with-same-uuid"></a>CREATE TABLE AS SELECT (CTAS) を実行すると同じ UUID を持つ新しいテーブルが作成される

Hive 3.1 (HDInsight 4.0) には、一意の UUID を生成する組み込み UDF が用意されています。 Hive UUID() メソッドでは、CTAS を指定しても一意の ID が生成されます。 これを次のように使用できます。
```hql
create table rhive as
select uuid() as UUID
from uuid_test
```

## <a name="hive-job-output-format-differs-from-hdinsight-36"></a>Hive ジョブの出力形式が HDInsight 3.6 とは異なっている

これは、HDInsight 3.6 と HDInsight 4.0 間で WebHCat(Templeton) に違いがあるために発生します。

* Hive Rest API - ```arg=--showHeader=false -d arg=--outputformat=tsv2 -d``` を追加します

* .NET SDK - ```HiveJobSubmissionParameters``` の引数を初期化します
    ```csharp
    List<string> args = new List<string> { { "--showHeader=false" }, { "--outputformat=tsv2" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SELECT clientid,market from hivesampletable LIMIT 10",
                    Defines = defines,
                    Arguments = args
                };
    ```

## <a name="reduce-hive-internal-table-creation-latency"></a>Hive 内部テーブル作成の待機時間を短縮する

1. Advanced hive-site と Advanced hivemetastore-site から ```hive.metastore.transactional.event.listeners``` の値 ```org.apache.hive.hcatalog.listener.DbNotificationListener``` を削除します。 

2. ```hive.metastore.event.listeners``` に値がある場合は、それを削除します。

3. DbNotificationListener は、REPL コマンドを使用する場合にのみ必要で、そうでない場合は削除しても安全です。

    :::image type="content" source="./media/apache-hive-40-migration-guide/hive-reduce-internal-table-creation-latency.png" alt-text="HDInsight 4.0 で内部テーブルの待機時間を短縮します" border="true":::

## <a name="change-hive-default-table-location"></a>Hive テーブルの既定の場所を変更する

この動作変更は、HDInsight 4.0 (Hive 3.1) での設計によるものです。 この変更の主な理由は、ファイル アクセス許可の制御目的です。 

カスタムの場所に外部テーブルを作成するには、create table ステートメントでその場所を指定します。

## <a name="disable-acid-in-hdinsight-40"></a>HDInsight 4.0 で ACID を無効にする

HDInsight 4.0 では ACID を有効にすることをお勧めします。 Hive での最近の機能強化は、機能とパフォーマンスのどちらについてもその大半が ACID テーブルに対してのみ使用できます。

HDInsight 4.0 で ACID を無効にする手順:
1. Ambari で以下のハイブ構成を変更します。

    ```text
    hive.strict.managed.tables=false
    hive.support.concurrency=false; 
    hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DummyTxnManager;
    hive.enforce.bucketing=false;
    hive.compactor.initiator.on=false;
    hive.compactor.worker.threads=0;
    hive.create.as.insert.only=false;
    metastore.create.as.acid=false;
    ```

2. ハイブ サービスを再起動します。

> [!IMPORTANT]
> HDInsight 3.6 および HDInsight 4.0 の Hive で管理されているテーブルと、同じデータやストレージを共有しないことをお勧めします。これはサポートされていないシナリオです。

* 通常、上記の構成は、HDInsight 4.0 クラスターで Hive テーブルを作成する前に設定する必要があります。 マネージド テーブルが作成された後に ACID を無効にしないでください。 データ損失が発生したり、一貫性のない結果になる可能性があります。 そのため、新しいクラスターを作成するときにこれを一度設定し、後で変更しないことをお勧めします。

* テーブル作成後に ACID を無効にすることは危険ですが、それを実行する場合は、データの損失や不整合が生じる可能性を回避するために以下の手順に従ってください。

    1. 同じスキーマを使用して外部テーブルを作成し、CTAS コマンド ```create external table e_t1 select * from m_t1``` を使用して元のマネージド テーブルからデータをコピーします。    
    2. ```drop table m_t1``` を使用してマネージド テーブルを削除します。
    3. 提示した構成を使用して ACID を無効にします。        
    4. m_t1 を再度作成し、CTAS コマンド ```create table m_t1 select * from e_t1``` を使用して外部テーブルからデータをコピーします。        
    5. ```drop table e_t1``` を使用して外部テーブルを削除します。

ACID を無効にする前に、すべてのマネージド テーブルが外部テーブルに変換され、削除されていることを確認してください。 また、各手順の後にスキーマとデータを比較して不一致を回避します。

## <a name="create-hive-external-table-with-755-permission"></a>755 アクセス許可を使用すると Hive 外部テーブルが作成される

この問題は、次の 2 つのオプションのいずれかによって解決できます。

1. フォルダーのアクセス許可を手動で 757 または 777 に設定して、ハイブのユーザーにディレクトリへの書き込みを許可します。

2. "Hive 承認マネージャー" を ```org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider``` から ```org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly``` に変更します。

Hive メタストアは HDInsight 4.0 に埋め込まれていないため、MetaStoreAuthzAPIAuthorizerEmbedOnly を使用するとセキュリティ チェックは事実上に無効にされます。 ただし、これによって他の潜在的な問題が発生する可能性があります。 このオプションを使用する際は注意が必要です。

## <a name="permission-errors-in-hive-job-after-upgrading-to-hdinsight-40"></a>HDInsight 4.0 へのアップグレード後に Hive ジョブでアクセス許可エラーが発生する

* HDInsight 4.0 では、Hive コンポーネントがあるすべてのクラスター図形が、次の新しい認可プロバイダーを使用して構成されます: 

  `org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider`

* HDFS ファイルのアクセス許可は、アクセスされるファイルのハイブ ユーザーに割り当てられる必要があります。 エラー メッセージには、問題の解決に必要な詳細が記載されています。

* HDInsight 3.6 Hive クラスターで使用されている ```MetaStoreAuthzAPIAuthorizerEmbedOnly``` プロバイダーに切り替えることもできます。

  `org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly`

  :::image type="content" source="./media/apache-hive-40-migration-guide/hive-job-permission-errors.png" alt-text="認可を MetaStoreAuthzAPIAuthorizerEmbedOnly に設定します" border="true":::

## <a name="unable-to-query-table-with-opencsvserde"></a>OpenCSVSerde を使用してテーブルのクエリを実行できない

`csv` 形式のテーブルからデータを読み取ると、次のような例外がスローされる場合があります。
```text
MetaException(message:java.lang.UnsupportedOperationException: Storage schema reading not supported)
```

対応策 :

* Ambari UI で `Custom hive-site` に構成 `metastore.storage.schema.reader.impl`=`org.apache.hadoop.hive.metastore.SerDeStorageSchemaReader` を追加します

* 古くなったすべてのハイブ サービスを再起動します

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
