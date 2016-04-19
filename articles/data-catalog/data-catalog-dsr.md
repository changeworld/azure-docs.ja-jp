<properties
   pageTitle="Azure Data Catalog でサポートされるデータ ソース | Microsoft Azure"
   description="この記事では、Azure Data Catalog での登録でサポートされるデータ ソースとデータ資産の種類を示します。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager=""
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/31/2016"
   ms.author="maroche"/>

# Azure Data Catalog でサポートされるデータ ソース

**Azure Data Catalog** のユーザーは、パブリック API である ClickOnce データ ソース登録ツールを使用するか、Data Catalog の Web ポータルに直接情報を手動で入力して、メタデータを公開できます。

以下の表は、現在 Data Catalog でサポートされているすべてのソースと、それぞれの公開機能をまとめたものです。また、各ソースを Data Catalog ポータルから "オープンイン" で起動できる外部ツールも一覧表示されています。

さらにその下の 2 番目の表には、各データ ソースの接続プロパティの詳細な技術仕様と、Data Catalog API を使用するときにサポートされる各データ資産に対して使用されるデータ ソース参照 (DSR) の仕様を示します。


## サポートされるデータ ソースと資産の一覧

<table>

    <tr>
       <td><b>データ資産</b></td>
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
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Data Warehouse ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
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
      <td><font size=2>[ブラウザー] ボタンを</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server テーブル</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server ビュー</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
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
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>DB2 テーブル</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>DB2 ビュー</td>
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


<br> <br>

## データ ソース参照の仕様

<table>
    <tr>
       <td><b>ソースの種類</b></td>
       <td><b>ルート型</b></td>
       <td><b>オブジェクトの種類</b></td>
       <td><b>含まれるオブジェクトの種類</b></td>
       <td><b>DSR 構造体<b></td>
    </tr>


    <tr>
      <td>Azure Data Lake Store</td>
      <td>テーブル</td>
      <td>Directory</td>
      <td>Data Lake</td>
      <td>
        <font size=2> プロトコル: webhdfs
            <br>認証: {basic、oauth}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure Data Lake Store</td>
      <td>テーブル</td>
      <td>ファイル</td>
      <td>Data Lake</td>
      <td>
        <font size=2> プロトコル: webhdfs}
            <br>認証: {basic、oauth}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>



    <tr>
      <td>SQL Server Reporting Services</td>
      <td>コンテナー</td>
      <td>サーバー</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: reporting services
            <br>認証: {windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; バージョン {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>テーブル</td>
      <td>BLOB</td>
      <td>コンテナー</td>
      <td>
        <font size=2> プロトコル: azure-blobs
            <br>認証: {azure-access-key}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; ドメイン
            <br>&#160;&#160;&#160;&#160;&#160; アカウント
            <br>&#160;&#160;&#160;&#160;&#160; コンテナー
            <br>&#160;&#160;&#160;&#160;&#160; 名前</font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>テーブル</td>
      <td>Directory</td>
      <td>コンテナー</td>
      <td>
        <font size=2> プロトコル: azure-blobs
            <br>認証: {azure-access-key}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; ドメイン
            <br>&#160;&#160;&#160;&#160;&#160; アカウント
            <br>&#160;&#160;&#160;&#160;&#160; コンテナー
            <br>&#160;&#160;&#160;&#160;&#160; 名前</font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>Azure Storage (Azure Storage)</td>
      <td>
        <font size=2> プロトコル: azure-tables
            <br>認証: {azure-access-key}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; ドメイン
            <br>&#160;&#160;&#160;&#160;&#160; アカウント
            <br>&#160;&#160;&#160;&#160;&#160; 名前</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: analysis-services
            <br>認証: {windows、basic、anonymous}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; モデル
            <br>
            <br>* basic/anonymous は https 経由でのみ使用可能* </font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: db2
            <br>認証: {basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; データベース</font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: db2
            <br>認証: {basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>DB2</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: db2
            <br>認証: {basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>ファイル システム</td>
      <td>テーブル</td>
      <td>ファイル</td>
      <td>サーバー</td>
      <td>
        <font size=2> プロトコル: ファイル
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; パス</font>
      </td>
    </tr>

    <tr>
      <td>Ftp</td>
      <td>テーブル</td>
      <td>Directory</td>
      <td>サーバー</td>
      <td>
        <font size=2> プロトコル: ftp
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Ftp</td>
      <td>テーブル</td>
      <td>ファイル</td>
      <td>サーバー</td>
      <td>
        <font size=2> プロトコル: ftp
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: analysis-services
            <br>認証: {windows、basic、anonymous}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; モデル
            <br>
            <br>* basic/anonymous は https 経由でのみ使用可能* </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>テーブル</td>
      <td>Directory</td>
      <td>プロビジョニング</td>
      <td>
        <font size=2> プロトコル: webhdfs
            <br>認証: {basic、oauth}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>テーブル</td>
      <td>ファイル</td>
      <td>プロビジョニング</td>
      <td>
        <font size=2> プロトコル: webhdfs
            <br>認証: {basic、oauth}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure Data Lake Store</td>
      <td>コンテナー</td>
      <td>Data Lake</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: webhdfs
            <br>認証: {basic、oauth}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: hive
            <br>認証: {hdinsight、basic、username}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: hive
            <br>認証: {hdinsight、basic、username}
            <br>アドレス: サーバー
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>コンテナー</td>
      <td>Azure Storage (Azure Storage)</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: azure-tables
            <br>認証: {azure-access-key}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; ドメイン
            <br>&#160;&#160;&#160;&#160;&#160; アカウント</font>
      </td>
    </tr>

    <tr>
      <td>Http</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: http
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Http</td>
      <td>レポート</td>
      <td>レポート</td>
      <td>サイト</td>
      <td>
        <font size=2> プロトコル: http
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Http</td>
      <td>テーブル</td>
      <td>エンドポイント</td>
      <td>サイト</td>
      <td>
        <font size=2> プロトコル: http
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>コンテナー</td>
      <td>コンテナー</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: azure-blobs
            <br>認証: {Azure-Access-Key}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; ドメイン
            <br>&#160;&#160;&#160;&#160;&#160; アカウント
            <br>&#160;&#160;&#160;&#160;&#160; コンテナー </font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: mysql
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: mysql
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>コンテナー</td>
      <td>プロビジョニング</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: webhdfs
            <br>認証: {basic、oauth}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Http</td>
      <td>テーブル</td>
      <td>ファイル</td>
      <td>サイト</td>
      <td>
        <font size=2> プロトコル: http
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>コンテナー</td>
      <td>エンティティ コンテナー</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: odata
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: hiveserver2
            <br>認証: {hdinsight、basic、username、none}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; ポート
            <br>&#160;&#160;&#160;&#160;&#160; データベース</font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>テーブル</td>
      <td>エンティティ セット</td>
      <td>エンティティ コンテナー</td>
      <td>
        <font size=2> プロトコル: odata
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; リソース</font>
      </td>
    </tr>

    <tr>
      <td>Oracle Database</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: oracle
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>Oracle Database</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: oracle
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>OData</td>
      <td>テーブル</td>
      <td>関数</td>
      <td>エンティティ コンテナー</td>
      <td>
        <font size=2> プロトコル: odata
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; リソース</font>
      </td>
    </tr>

    <tr>
      <td>その他</td>
      <td>テーブル</td>
      <td>その他</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: generic-asset
            <br>認証: {none、basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: postgresql
            <br>認証: {basic、windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; データベース</font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: postgresql
            <br>認証: {basic、windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: postgresql
            <br>認証: {basic、windows}
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: mysql
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; データベース</font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>サーバー</td>
      <td>
        <font size=2> プロトコル: sap hana sql
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>Salesforce</td>
      <td>テーブル</td>
      <td>オブジェクト</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: salesforce com
            <br>認証: {basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>

    <tr>
      <td>SharePoint</td>
      <td>テーブル</td>
      <td>一覧表示</td>
      <td>サイト</td>
      <td>
        <font size=2> プロトコル: sharepoint-list
            <br>認証: {basic、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: tds
            <br>認証: {sql、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: tds
            <br>認証: {sql、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: tds
            <br>認証: {sql、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>テーブル</td>
      <td>テーブル値関数</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: tds
            <br>認証: {sql、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: tds
            <br>認証: {sql、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>Oracle Database</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: oracle
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; データベース</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>モデル</td>
      <td>
        <font size=2> プロトコル: analysis-services
            <br>認証: {windows、basic、anonymous}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; モデル
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Kpi}
            <br>
            <br>* basic/anonymous は https 経由でのみ使用可能* </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>モデル</td>
      <td>
        <font size=2> プロトコル: analysis-services
            <br>認証: {windows、basic、anonymous}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; モデル
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>テーブル</td>
      <td>Dimension</td>
      <td>モデル</td>
      <td>
        <font size=2> プロトコル: analysis-services
            <br>認証: {windows、basic、anonymous}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; モデル
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Dimension} </font>
      </td>
    </tr>

    <tr>
      <td>SAP Hana</td>
      <td>コンテナー</td>
      <td>サーバー</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: sap hana sql
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>モデル</td>
      <td>
        <font size=2> プロトコル: analysis-services
            <br>認証: {windows、basic、anonymous}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; モデル
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Table}
            <br>
            <br>* basic/anonymous は https 経由でのみ使用可能* </font>
      </td>
    </tr>

    <tr>
      <td>SQL Data Warehouse</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: tds
            <br>認証: {sql、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; データベース</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: tds
            <br>認証: {sql、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; データベース</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services</td>
      <td>レポート</td>
      <td>レポート</td>
      <td>サーバー</td>
      <td>
        <font size=2> プロトコル: reporting services
            <br>認証: {windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; バージョン {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>該当なし</td>
      <td>
        <font size=2> プロトコル: teradata
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; データベース</font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: teradata
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>データベース</td>
      <td>
        <font size=2> プロトコル: teradata
            <br>認証: {protocol、windows}
            <br>アドレス:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; オブジェクト</font>
      </td>
    </tr>



</table>

<!---HONumber=AcomDC_0406_2016-->