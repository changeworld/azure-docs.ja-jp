---
title: "Ambari ビューによる HDInsight (Hadoop) での Hive の使用 | Microsoft Docs"
description: "Web ブラウザーから Hive ビューを使用して Hive クエリを送信する方法について説明します。 Hive ビューは、Linux ベースの HDInsight クラスターに付属する Ambari Web UI の要素です。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/05/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 8789643474ac84d4509cd25206091e49925c9dcd
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>HDInsight での Hive View と Hadoop の使用

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari Hive ビューを使用して Hive クエリを実行する方法について説明します。 Ambari は、Linux ベースの HDInsight クラスターに付属する管理および監視ユーティリティです。 Ambari が提供する機能の 1 つに、Hive クエリを実行するときに使用される Web UI があります。

> [!NOTE]
> Ambari には数多くの機能がありますが、このドキュメントではそれらについて説明しません。 詳細については、「 [Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Linux ベースの HDInsight クラスター。 クラスターの作成方法については、[Linux ベースの HDInsight の使用](hdinsight-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date)に関する記事を参照してください。

## <a name="open-the-hive-view"></a>Hive ビューを開く

Azure Portal から Ambari ビューを表示するには、HDInsight クラスターを選択し、**[クイック リンク]** セクションの **[Ambari ビュー]** を選択します。

![quick links section](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Web ブラウザーで https://CLUSTERNAME.azurehdinsight.net にアクセスして Ambari に直接移動することもできます。 **CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。 使用可能なビューを一覧表示するには、メニューから四角形のセットを選択します。 ビューを開くには、**[Hive ビュー]** エントリを選択します。

![Selecting Ambari views](./media/hdinsight-hadoop-use-hive-ambari-view/select-hive-view.png)に関するページを参照してください。

> [!NOTE]
> Ambari にアクセスすると、サイトに対する認証が求められます。 クラスターを作成するときに使用した管理者アカウント名 (既定値は `admin`) とパスワードを入力します。

次の図ようなページが表示されます。

![Image of the hive view page, containing a query editor section](./media/hdinsight-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="view-tables"></a>テーブルを表示する

ページの **[Database Explorer]** セクションの **[Database]** タブで、**[default]** エントリを選択します。 既定のデータベース内にあるテーブルの一覧が表示されます。 HDInsight には、**hivesampletable** という名前のテーブルが含まれています。

![database explorer with the default database expanded](./media/hdinsight-hadoop-use-hive-ambari-view/database-explorer.png)

このドキュメントの手順に従ってテーブルを追加した際、データベース エクスプローラーの右上隅にある更新アイコンを使用すると、一覧を更新できます。

## <a name="hivequery"></a>クエリ エディター

Hive クエリを実行するには、Hive ビューから次の手順を使用します。

1. ページの **[Query Editor]** セクションで、次の HiveQL ステートメントをワークシートに貼り付けます。

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    これらのステートメントは次のアクションを実行します。

   * `DROP TABLE` - テーブルが既存の場合にテーブルとデータ ファイルを削除します。

   * `CREATE EXTERNAL TABLE` - Hive に新しい「外部」テーブルを作成します。
   外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。

   * `ROW FORMAT` - データがどのように書式設定されるか。 ここでは、各ログのフィールドは、スペースで区切られています。

   * `STORED AS TEXTFILE LOCATION` - データの格納先、テキストとして格納されています。

   * `SELECT` - t4 列の値が [ERROR] であるすべての行の数を指定します。

     > [!NOTE]
     > 基になるデータが外部ソースによって更新されると考えられる場合は、外部テーブルを使用する必要があります。 たとえば、データの自動アップロード処理や別の MapReduce 操作によってです。 外部テーブルを削除しても、データは削除*されません*。テーブル定義のみが削除されます。

2. クエリを開始するには、クエリ エディターの下部にある **[Execute]** ボタンを使用します。 ボタンがオレンジ色に変わり、テキストが **[Stop execution]** に変わります。 クエリ エディターの下に **[Query Process Results]** セクションが表示され、ジョブに関する情報が表示されます。

   > [!IMPORTANT]
   > ブラウザーによっては、ログまたは結果の情報が正しく更新されない場合があります。 ジョブを実行したときに、ジョブの実行が続いているにもかかわらずログが更新されたり結果が返されたりしない場合は、Mozilla FireFox または Google Chrome を代わりに使用してください。

3. クエリが完了すると、**[Query Process Results (クエリ処理結果)]** セクションに操作の結果が表示されます。 クエリが完了すると、**[Stop execution]** ボタンも緑色の **[Execute]** ボタンに戻ります。 **[Results]** タブには次の情報が表示されます。

        sev       cnt
        [ERROR]   3

    **[Logs]** タブを使用すると、ジョブによって作成されたログ情報を表示できます。

   > [!TIP]
   > **[Query Process Results]** セクションの左上にある **[Save results]** ドロップダウン ダイアログでは、結果をダウンロードまたは保存できます。

4. このクエリの最初の 4 行を選択し、**[Execute]** を選択します。 ジョブが完了したときに結果は生成されません。 クエリの一部が選択されているときに **[Execute]** ボタンを使用すると、選択したステートメントのみが実行されます。 この場合は、テーブルの行を取得する最後のステートメントが選択範囲に含まれていませんでした。 その行のみを選択して **[Execute]** を使用すると、予想どおりの結果が表示されます。

5. ワークシートを追加するには、**クエリ エディター**の下部にある **[New Worksheet]** ボタンを使用します。 新しいワークシートに、次の HiveQL ステートメントを入力します。

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  これらのステートメントは次のアクションを実行します。

   * **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードは使用されないため、内部テーブルが作成されます。 内部テーブルは Hive データ ウェアハウスに格納され、完全に Hive によって管理されます。 外部テーブルとは異なり、内部テーブルを削除すると基盤となるデータも削除されます。

   * **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 ORC は、Hive データを格納するための高度に最適化された効率的な形式です。

   * **INSERT OVERWRITE ...SELECT** - `[ERROR]` を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。

     **[Execute]** ボタンを使用して、このクエリを実行します。 クエリから返される行が 0 の場合、**[Results]** タブには情報が含まれません。 クエリが完了すると、状態として **[SUCCEEDED]** が表示されます。

### <a name="hive-settings"></a>Hive の設定

エディターの右側にある **[Settings]** アイコンを選択します。

![Settings icon for hive view](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-settings-icon.png)

設定を使用して、さまざまな Hive 設定を変更できます。 たとえば、Hive の実行エンジンを Tez (既定値) から MapReduce に変更します。

### <a name="visualization"></a>グラフ

エディターの右側にある __[Visualization]__ アイコンを選択します。

![Visualization icon for hive view](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization-icon.png)

この視覚化のインターフェイスでは、クエリから返されるデータの視覚化を作成できます。 HDInsight に含まれる `hivesampletable` のデータを使用した視覚化の例を次の図に示します。

![Example visualization](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization.png)

### <a name="visual-explain"></a>ビジュアルの説明

エディターの右側にある **[Visual Explain]** アイコンを選択します。

![Visual explain icon for hive view](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visual-explain-icon.png)

クエリの **[Visual Explain]** ビューは、複雑なクエリのフローを理解する際に役立ちます。 クエリ エディターの **[Explain]** ボタンを使用して、このビューに対応するテキストを表示できます。

![visual explain image](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez

エディターの右側にある **[Tez]** アイコンを選択します。

![Tez icon for hive view](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-tez-icon.png)

クエリの解決に Tez を使用している場合、有向非巡回グラフ (DAG) が表示されます。 過去に実行したクエリの DAG を表示する場合、または Tez プロセスをデバッグする場合は、 [[Tez View]](hdinsight-debug-ambari-tez-view.md) を使用します。

### <a name="notifications"></a>通知

エディターの右側にある **[Notifications]** アイコンを選択します。

![Notifications icon for hive view](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-notifications-icon.png)

通知とは、クエリを実行するときに生成されるメッセージです。 たとえば、クエリを送信したときや、エラーが発生したときに、通知が届きます。

## <a name="saved-queries"></a>保存済みのクエリ

1. クエリ エディターでワークシートを作成し、次のクエリを入力します。

    ```hiveql
    SELECT * from errorLogs;
    ```

    クエリを実行して、動作することを確認します。 結果は次の例のようになります。

        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id

2. エディターの下部にある **[Save as]** ボタンを使用します。 このクエリに **Errorlogs** という名前を付け、**[OK]** を選択します。 ワークシートの名前が **Errorlogs** に変わります。

3. [Hive ビュー] ページの上部にある **[Saved Queries]** タブを選択します。 **Errorlogs** が、保存済みのクエリとして表示されます。 クエリは、削除されるまでこの一覧に表示されます。 名前を選択すると、クエリ エディターにクエリが表示されます。

## <a name="query-history"></a>クエリの履歴

[Hive ビュー] ページの上部にある **[History]** ボタンを選択すると、以前に実行したクエリを表示できます。 ここで、これを使用して、以前に実行したクエリの一部を選択します。 クエリを選択すると、そのクエリがクエリ エディターに表示されます。

## <a name="user-defined-functions-udf"></a>ユーザー定義関数 (UDF)

Hive は **ユーザー定義関数 (UDF)**で拡張することもできます。 UDF では、HiveQL で簡単にモデル化されない機能やロジックを実装することができます。

Hive ビューの上部にある [UDF] タブでは、UDF のセットを宣言して保存できます。 これらの UDF は**クエリ エディター**で使用できます。

Hive ビューに UDF を追加すると、**[Insert udfs]** ボタンが**クエリ エディター**の下部に表示されます。 このエントリを選択すると、Hive ビューで定義した UDF のドロップダウン リストが表示されます。 UDF を選択すると、HiveQL ステートメントがクエリに追加され、UDF が有効になります。

たとえば、次のプロパティを持つ UDF を定義したとします。

* リソース名: myudfs

* リソースのパス: /myudfs.jar

* UDF 名: myawesomeudf

* UDF のクラス名: com.myudfs.Awesome

**[Insert udfs]** ボタンを使用すると、**myudfs** という名前のエントリと、そのリソースに対して定義されている UDF ごとにドロップダウン リストが表示されます。 ここでは、**myawesomeudf** が表示されます。 このエントリを選択すると、クエリの先頭に次の内容が追加されます。

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

これにより、クエリでこの UDF を使用できます。 たとえば、「 `SELECT myawesomeudf(name) FROM people;`」のように入力します。

HDInsight において Hive で UDF を使用する方法の詳細については、以下のドキュメントを参照してください。

* [HDInsight における Python と Hive および Pig の使用](hdinsight-python.md)
* [HDInsight にカスタムの Hive UDF を追加する方法](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

