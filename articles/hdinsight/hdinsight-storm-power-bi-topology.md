<properties
 pageTitle="Apache Storm と Power BI の使用 | Microsoft Azure"
 description="HDInsight 内の Apache Storm クラスター上で実行されている C# トポロジのデータを使用して Power BI レポートを作成します。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/16/2016"
 ms.author="larryfr"/>

# Power BI を使用した Apache Storm トポロジのデータの視覚化

Power BI を使用すると、データをレポートとして視覚的に表すことができます。HDInsight 上の Storm の Visual Studio テンプレートを使用すると、HDInsight クラスター上の Apache Storm で実行されているトポロジから SQL Azure にデータを簡単に保存し、Power BI で視覚化することができます。

このドキュメントでは、Apache Storm トポロジによって生成されて Azure SQL Database に保存されたデータから、Power BI を使用してレポートを作成する方法について説明します。

## 前提条件

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* [Power BI](https://powerbi.com) アクセス権を持つ Azure Active Directory ユーザー

* 下記のいずれかのバージョンの Visual Studio

    * Visual Studio 2012 ([update 4](http://www.microsoft.com/download/details.aspx?id=39305))

    * Visual Studio 2013 ([update 4](http://www.microsoft.com/download/details.aspx?id=44921)) または [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

* HDInsight Tools for Visual Studio: インストールの情報については、「[HDInsight Tools for Visual Studio の使用開始](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)」を参照してください。

## 動作のしくみ

この例では、Internet Information Services (IIS) のログ データをランダムに生成する C# Storm トポロジを使用しています。このデータは SQL Database に書き込まれ、そこから Power BI でのレポート生成に使用されます。

この例の主な機能は、以下に示したファイルに実装されています。

* **SqlAzureBolt.cs**: Storm トポロジで生成された情報を SQL Database に書き込みます。

* **IISLogsTable.sql**: データの格納先となるデータベースを生成するための Transact-SQL ステートメント。

> [AZURE.WARNING] HDInsight クラスター上のトポロジを起動する前に、このテーブルを SQL Database に作成しておく必要があります。

## 例のダウンロード

[HDInsight C# Storm Power BI の例](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi)をダウンロードします。ダウンロードするには、[git](http://git-scm.com/) を使用してフォーク/複製するか、**ダウンロード** リンクを使用してアーカイブの ZIP ファイルをダウンロードします。

## データベースの作成

1. [SQL Database チュートリアル](../sql-database/sql-database-get-started.md)のドキュメントに記載されている手順で新しい SQL Database を作成します。

2. 「[Visual Studio で SQL Database に接続する](../sql-database/sql-database-connect-query.md)」の手順に従ってデータベースに接続します。

4. オブジェクト エクスプローラーでデータベースを右クリックし、__[新しいクエリ]__ を作成します。ダウンロードしたプロジェクトに含まれている __IISLogsTable.sql__ ファイルの内容をクエリ ウィンドウに貼り付けて、Ctrl + Shift + E キーを押してクエリを実行します。コマンドが正常に完了した旨のメッセージが返されます。

    この作業が完了すると、__IISLOGS__ という名前の新しいテーブルがデータベースに作成されます。

## サンプルの構成

1. [Azure ポータル](https://portal.azure.com)で SQL データベースを選択します。[SQL Database] ブレードの __[要点]__ セクションから __[データベース接続文字列の表示]__ を選択します。表示された一覧から __ADO.NET (SQL 認証)__ 情報をコピーします。

1. Visual Studio でサンプルを開きます。**ソリューション エクスプローラー**で **App.config** ファイルを開き、次のエントリを見つけます。

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
    
    __##TOBEFILLED##__ の値は、前の手順でコピーしたデータベースの接続文字列に置き換えてください。__{your\_username}__ と __{your\_password}__ は、データベースのユーザー名とパスワードに置き換えます。

2. ファイルを保存して閉じます。

## サンプルのデプロイ

1. **ソリューション エクスプローラー**で **StormToSQL** プロジェクトを右クリックし、**[Submit to Storm on HDInsight (HDInsight の Storm に送信)]** を選択します。[**Storm クラスター**] ドロップダウン ダイアログから HDInsight クラスターを選択します。

    > [AZURE.NOTE] [**Storm クラスター**] ドロップダウンにサーバー名が設定されるには数秒かかることがあります。
    >
    > メッセージが表示されたら、Azure サブスクリプションのログイン資格情報を入力します。2 つ以上のサブスクリプションをお持ちの場合は、HDInsight クラスターの Storm があるサブスクリプションにログインします。

2. トポロジが正常に送信されたら、クラスターの Storm トポロジが表示されます。一覧から SqlAzureWriterTopology を選択して、実行中のトポロジに関する情報を表示します。

    ![The topologies, with the topology selected](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    このビューを使用してトポロジに関する情報を確認できるほか、エントリ (SqlAzureBolt など) をダブルクリックして、トポロジの特定のコンポーネントに固有の情報を表示できます。

3. トポロジを数分間実行した後、データベースの作成に使用した SQL クエリ ウィンドウに戻ります。既存のステートメントを次のように置き換えます。

        select * from iislogs;
    
    Ctrl + Shift + E キーを使用してクエリを実行すると、次のような結果が返されます。
    
        1	2016-05-27 17:57:14.797	255.255.255.255	/bar	GET	200
        2	2016-05-27 17:57:14.843	127.0.0.1	/spam/eggs	POST	500
        3	2016-05-27 17:57:14.850	123.123.123.123	/eggs	DELETE	200
        4	2016-05-27 17:57:14.853	127.0.0.1	/foo	POST	404
        5	2016-05-27 17:57:14.853	10.9.8.7	/bar	GET	200
        6	2016-05-27 17:57:14.857	192.168.1.1	/spam	DELETE	200

    これは、Storm トポロジから書き込まれたデータです。

## レポートの作成

1. Power BI 用の [Azure SQL Database コネクタ](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect)に接続します。

2. __[データベース]__ 内の __[取得]__ を選択します。

3. __[Azure SQL Database]__ を選択し、__[接続]__ を選択します。

4. Azure SQL Database に接続するための情報を入力します。この情報は、[Azure ポータル](https://portal.azure.com)にアクセスし、該当する SQL データベースを選択すると確認できます。

    > [AZURE.NOTE] 接続ダイアログから __[詳細オプションを有効にする]__ を使用して、更新間隔とカスタム フィルターを設定することもできます。

5. 接続後、接続先のデータベースと同じ名前の新しいデータセットが表示されます。データセットを選択して、レポートの設計に着手します。

3. __[フィールド]__ から __[IISLOGS]__ エントリを展開します。__[URISTEM]__ のチェック ボックスをオンにします。これで、データベースに記録された URI ステム (/foo、/bar など) を列挙した新しいレポートが作成されます。

    ![Creating a report](./media/hdinsight-storm-power-bi-topology/createreport.png)

5. 次に、__[メソッド]__ をレポートにドラッグします。レポートが最新の情報に更新され、HTTP 要求に使用されたステムおよび対応する HTTP メソッドが一覧表示されます。

    ![adding the method data](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

4. __[視覚化]__ 列から __[フィールド]__ アイコンを選択し、__[値]__ セクションで __[メソッド]__ の横の下矢印を選択します。表示された一覧から __[カウント]__ を選択します。これで、特定の URI がアクセスされた回数を一覧表示するようにレポートが変更されます。

    ![Changing to a count of methods](./media/hdinsight-storm-power-bi-topology/count.png)

6. 次に __[積み上げ縦棒グラフ]__ を選択して、情報の表示方法を変更します。

    ![Changing to a stacked chart](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

7. レポートの体裁が整ったら、メニュー項目の __[保存]__ を使用して名前を入力し、レポートを保存します。

## トポロジを停止する

トポロジの実行は、意図的に停止するか、HDInsight クラスター上で Storm を削除するまで続きます。トポロジを停止するには、以下の手順を実行します。

1. Visual Studio でトポロジ ビューアーに戻って、トポロジを選択します。

2. **[強制終了]** ボタンを選択してトポロジを停止します。

    ![[トポロジの概要] の [強制終了 (Kill)] ボタン](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## クラスターを削除する

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## 次のステップ

このドキュメントでは、Storm トポロジから SQL Database にデータを送信し、Power BI を使って視覚化する方法について説明しました。HDInsight 上の Storm を使用して他の Azure テクノロジと連携する方法については、次のページを参照してください。

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0914_2016-->