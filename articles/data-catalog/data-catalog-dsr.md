<properties
   pageTitle="Azure Data Catalog でサポートされるデータ ソース | Microsoft Azure"
   description="現在サポートされているデータ ソースの仕様。"
   services="data-catalog"
   documentationCenter=""
   authors="trhabe"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="trhabe"/>

# Azure Data Catalog でサポートされるデータ ソース

Azure Data Catalog のユーザーは、ワンクリック登録ツールのパブリック API を使用して、または Data Catalog の Web ポータルに直接情報を手動で入力してメタデータを公開できます。以下の表は、現在カタログでサポートされているすべてのソースと、それぞれの公開機能をまとめたものです。また、各ソースを当社のポータルから「オープンイン」で起動できる外部ツールも一覧表示されています。記事の 2 番目の表には、各データ ソースの接続プロパティの詳細な技術仕様があります。


## サポートされるデータ ソースの一覧

<table>

    <tr>
       <td><b>データ ソース オブジェクト</b></td>
       <td><b>API</b></td>
       <td><b>手動入力</b></td>
       <td><b>登録ツール</b></td>
       <td><b>オープンイン ツール</b></td>
       <td><b>メモ</b></td>
    </tr>

    <tr>
      <td>Azure Data Lake Store のディレクトリ</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Data Lake Store のファイル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage BLOB</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage ディレクトリ</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage Table</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>HDFS ディレクトリ</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS ファイル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive テーブル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL テーブル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle Database テーブル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle Database ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>その他 (汎用アセット)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Data Warehouse テーブル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Data Warehouse ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services ディメンション</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services メジャー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services テーブル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services レポート</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>ブラウザー</font></td>
      <td><font size=2>ネイティブ モードのサーバーのみ。SharePoint モードはサポートされていません。</font></td>
    </tr>

    <tr>
      <td>SQL Server テーブル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata テーブル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>計算ビューと分析ビューのみ。属性ビューはサポートされていません。</font></td>
    </tr>

    <tr>
      <td>Db2 テーブル</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 ビュー</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>ファイル システム ファイル</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp ディレクトリ</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp ファイル</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http レポート</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http エンドポイント</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http ファイル</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata エンティティ セット</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 関数</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql テーブル</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql ビュー</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana ビュー</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Salesforce オブジェクト</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SharePoint リスト </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

その他のソースのサポートが必要な場合は、[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)を使用して機能のリクエストを送信してください。


<br> <br>
## データ ソース参照の仕様
> [AZURE.NOTE] 以下の表の「DSL 構造体」列には、Azure Data Catalog に使用される "address" プロパティ バッグの接続プロパティのみが表示されます (つまり、"address" プロパティ バッグには、Azure Data Catalog が保持し、使用しないデータ ソースのその他の接続プロパティを含めることができます)。
<table>
    <tr>
       <td><b>ソースの種類</b></td>
       <td><b>資産の型</b></td>
       <td><b>オブジェクトの種類</b></td>
       <td><b>DSL 構造体<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>コンテナー</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocol: webhdfs
            <br>authentication: {basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> protocol: webhdfs
            <br>authentication: {basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>コンテナー</td>
      <td>コンテナー</td>
      <td>
        <font size=2> protocol: azure-blobs
            <br>authentication: {azure-access-key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>テーブル</td>
      <td>BLOB、ディレクトリ</td>
      <td>
        <font size=2> protocol: azure-blobs
            <br>authentication: {azure-access-key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; container
            <br>&#160;&#160;&#160;&#160;&#160; name</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>コンテナー</td>
      <td>コンテナー</td>
      <td>
        <font size=2> protocol: azure-tables
            <br>authentication: {azure-access-key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>
        <font size=2> protocol: azure-tables
            <br>authentication: {azure-access-key}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; domain
            <br>&#160;&#160;&#160;&#160;&#160; account
            <br>&#160;&#160;&#160;&#160;&#160; name</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>コンテナー</td>
      <td>仮想クラスター</td>
      <td>
        <font size=2> protocol: cosmos
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>テーブル</td>
      <td>ストリーム、ストリーム セット、ビュー</td>
      <td>
        <font size=2> protocol: cosmos
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: db2
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: db2
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; schema </font>
      </td>
    </tr>
    <tr>
      <td>ファイル システム</td>
      <td>テーブル</td>
      <td>ファイル</td>
      <td>
        <font size=2> protocol: file
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; path</font>
      </td>
    </tr>
    <tr>
      <td>Ftp</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> protocol: ftp
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>コンテナー</td>
      <td>クラスター</td>
      <td>
        <font size=2> protocol: webhdfs
            <br>authentication: {basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> protocol: webhdfs
            <br>authentication: {basic, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: hive
            <br>authentication: {hdinsight, basic, username, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>connectionProperties:
            <br>&#160;&#160;&#160;&#160;&#160; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: hive
            <br>authentication: {hdinsight, basic, username, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>connectionProperties:
            <br>&#160;&#160;&#160;&#160;&#160; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>テーブル</td>
      <td>エンド ポイント、ファイル</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: mysql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: mysql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>コンテナー</td>
      <td>エンティティ コンテナー</td>
      <td>
        <font size=2> protocol: odata
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>テーブル</td>
      <td>エンティティ セット、関数</td>
      <td>
        <font size=2> protocol: odata
            <br>authentication: {none, basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; resource</font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: oracle
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database</font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: oracle
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: postgresql
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database</font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: postgresql
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> protocol: http
            <br>authentication: {none, basic, windows, oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>テーブル</td>
      <td>データのマッシュアップ</td>
      <td>
        <font size=2> protocol: power-query
            <br>authentication: {oauth}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>テーブル</td>
      <td>オブジェクト</td>
      <td>
        <font size=2> protocol: salesforce com
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>コンテナー</td>
      <td>サーバー</td>
      <td>
        <font size=2> protocol: sap hana sql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>
        <font size=2> protocol: sap hana sql
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>テーブル</td>
      <td>一覧表示</td>
      <td>
        <font size=2> protocol: sharepoint-list
            <br>authentication: {basic, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>コマンド</td>
      <td>ストアド プロシージャ</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>テーブル値関数</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: tds
          <br>authentication: {protocol, windows}
          <br>address:
          <br>&#160;&#160;&#160;&#160;&#160; server
          <br>&#160;&#160;&#160;&#160;&#160; database</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>コマンド</td>
      <td>ストアド プロシージャ</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>テーブル値関数</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: tds
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>テーブル</td>
      <td>Dimension</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; object
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>
        <font size=2> protocol: analysis-services
            <br>authentication: {windows, basic, anonymous, none}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; model
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>コンテナー</td>
      <td>サーバー</td>
      <td>
        <font size=2> protocol: reporting services
            <br>authentication: {windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>レポート</td>
      <td>レポート</td>
      <td>
        <font size=2> protocol: reporting services
            <br>authentication: {windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: teradata
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: teradata
            <br>authentication: {protocol, windows}
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; object</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size="2"> protocol: mssql-mds
          <br>authentication: {windows}
          <br>address:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; model
          <br>&#160;&#160;&#160;&#160;&#160; version </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>テーブル</td>
      <td>エンティティ</td>
      <td>
        <font size="2"> protocol: mssql-mds
          <br>authentication: {windows}
          <br>address:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; model
          <br>&#160;&#160;&#160;&#160;&#160; version
          <br>&#160;&#160;&#160;&#160;&#160; entity </font>
      </td>
    </tr>
    <tr>
      <td>その他 (上記以外)</td>
      <td>*</td>
      <td>*</td>
      <td>
        <font size=2> protocol: generic-asset
            <br>address:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>
</table>

<!---HONumber=AcomDC_0921_2016-->