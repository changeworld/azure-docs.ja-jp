<properties
   pageTitle="SQL Data Warehouse での Azure Stream Analytics の使用 | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse での Azure Stream Analytics の使用に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# SQL Data Warehouse での Azure Stream Analytics の使用

Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。基本事項については、「[Azure Stream Analytics の概要][]」をお読みください。また、Stream Analytics を使用してエンド ツー エンド ソリューションを作成する方法については、[入門チュートリアル][]を参照してください。

この記事では、Azure SQL Data Warehouse データベースを、Stream Analytics ジョブの出力シンクとして使用する方法について説明します。

## 前提条件

最初に、[入門チュートリアル][]の次の手順を実行します。

1. Event Hub 入力の作成
2. イベント ジェネレーター アプリケーションの構成と起動
3. Stream Analytics のジョブの準備
4. ジョブの入力とクエリの指定

次に、Azure SQL Data Warehouse データベースを作成します

## ジョブの出力の指定: Azure SQL Data Warehouse データベース

### 手順 1.
Stream Analytics ジョブで、ページ上部の **[出力]** をクリックし、**[出力の追加]** をクリックします。

### 手順 2.
SQL Databaseを選択し、[次へ] をクリックします。 ![][Add Output]

### 手順 3.
次のページで次の値を入力します。 - 出力エイリアス: このジョブ出力の表示名を入力します。 - サブスクリプション: - SQL Data Warehouse データベースのサブスクリプションが Stream Analytics ジョブと同じ場合は、[現在のサブスクリプション] から [SQL Database を使用] を選択します。 - データベースのサブスクリプションが異なる場合は、[別のサブスクリプション] から [SQL Database を使用] を選択します。- データベース: 宛先データベースの名前を指定します。- サーバー名: 指定したデータベースのサーバー名を指定します。Azure ポータルを使用すると、これを見つけることができます。

![][Server Name]

- ユーザー名: データベースの書き込みアクセス許可を持つアカウントのユーザー名を指定します。
- パスワード: 指定したユーザー アカウントのパスワードを入力します。
- テーブル: データベースの対象テーブルの名前を指定します。

![][Add Database]

### 手順 4.
チェック ボタンをクリックして、このジョブ出力を追加し、Stream Analytics がデータベースに適切に接続できることを確認します。

![][Test Connection]

データベースへの接続が成功すると、ポータルの下部に通知が表示されます。下部にある [接続のテスト] をクリックすると、データベースへの接続をテストできます。




## 次のステップ
統合の概要については、「[SQL Data Warehouse integration overview (SQL Data Warehouse の統合の概要)][]」をご覧ください。開発に関するその他のヒントについては、「[SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)][]」をご覧ください。

<!--Image references-->
[Add Output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[Server Name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[Add Database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[Test Connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->
[Azure Stream Analytics の概要]: ./stream-analytics-introduction/
[入門チュートリアル]: ./articles/stream-analytics-get-started/
[SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)]: ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview (SQL Data Warehouse の統合の概要)]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=July15_HO4-->