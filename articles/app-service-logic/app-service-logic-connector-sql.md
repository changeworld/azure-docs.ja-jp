<properties 
   pageTitle="SQL コネクタ" 
   description="SQL コネクタの使用方法" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Microsoft SQL コネクタ #

コネクタをロジック アプリで使用すると、フローの一部として、データをフェッチ、処理、またはプッシュ転送できます。(ファイアウォールの背後にあるオンプレミスにインストールされた) Azure SQL または SQL Server で SQL データベースを操作する必要があるシナリオがあります。SQL コネクタをフローで使用すると、さまざまなシナリオを実現できます。例をいくつか示します。  

1.	Web またはモバイル ユーザーのフロント エンドを介して、SQL データベースに存在するデータの一部を公開する
2.	ストレージ用の SQL データベース テーブルにデータを挿入する (例: 従業員レコード、販売注文など)
3.	ビジネス プロセスで使用するデータを SQL から抽出する

これらのシナリオに対しては、次の操作を実行する必要があります。 

1. SQL コネクタ API アプリのインスタンスを作成する
2. API アプリが内部設置型の SQL と通信できるようにハイブリッド接続を確立する。この手順はオプションであり、内部設置型の SQL Server でのみ必要です。SQL Azure では必要ありません。
3. 作成した API アプリをロジックで使用して、目的のビジネス プロセスを実現する

	### 基本的なトリガーとアクション
		
    - データのポーリング (トリガー) 
    - テーブルに挿入
    - テーブルの更新
    - テーブルから選択
    - テーブルから削除
    - ストアド プロシージャの呼び出し

## SQL コネクタ API アプリのインスタンスを作成する ##

SQL コネクタを使用するには、まず SQL コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の操作を実行します。

1. Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2. [Web + モバイル]、[API アプリ] の順に移動して、"SQL コネクタ" を検索します。
3. 最初のブレードで、名前、アプリ サービス プランなどの一般的な詳細を入力します。
4. 下の表に記載されているパッケージ設定を入力します。.	

<table class="tableizer-table">
<tr class="tableizer-firstrow"><th>名前</th><th>必須</th><th>既定値</th><th>説明</th></tr>
 <tr><td>サーバー名</td><td>あり</td><td>&nbsp;</td><td>SQL Server の名前を指定します。例:"SQLserver"、"SQLserver/sqlexpress"、"SQLserver.mydomain.com"</td></tr>
 <tr><td>Port</td><td>いいえ</td><td> 1433</td><td>省略可能。接続が確立されているポート番号。値を指定しない場合、コネクタは既定のポート経由で接続します。</td></tr>
 <tr><td>ユーザー名</td><td>あり</td><td>&nbsp;</td><td>SQL Server に接続する有効なユーザー名を指定します。</td></tr>
 <tr><td>パスワード</td><td>あり</td><td>&nbsp;</td><td>SQL Server に接続する有効なパスワードを指定します。</td></tr>
 <tr><td>データベース名</td><td>あり</td><td>&nbsp;</td><td>SQL Server で有効なデータベース名を指定します。例:"orders"、"dbo/orders"、"myaccount/employees"</td></tr>
 <tr><td>オンプレミス</td><td>あり</td><td>FALSE</td><td>SQL Server がファイアウォールの背後にある内部設置型のサーバーかどうかを指定します。TRUE に設定した場合は、SQL Server にアクセスできるリスナー エージェントをサーバーにインストールする必要があります。API アプリの概要ページに移動し、 '[ハイブリッド接続]' をクリックしてエージェントをインストールできます。</td></tr>
 <tr><td>Service Bus の接続文字列</td><td>いいえ</td><td>&nbsp;</td><td>省略可能。SQL Server が内部設置型の場合は、このパラメーターを指定します。有効な Service Bus 名前空間の接続文字列である必要があります。Azure Service Bus の  '標準' エディションを使用していることを確認します ( '基本' エディションではありません)。</td></tr>
 <tr><td>パートナー サーバー名</td><td>いいえ</td><td>&nbsp;</td><td>省略可能。プライマリ サーバーがダウンしたときに接続するパートナー サーバーを指定します。</td></tr>
 <tr><td>テーブル</td><td>いいえ</td><td>&nbsp;</td><td>省略可能。コネクタで変更できるデータベース内のテーブルを指定します。例: OrdersTable、EmployeeTable</td></tr>
 <tr><td>ストアド プロシージャ</td><td>いいえ</td><td>&nbsp;</td><td>省略可能。コネクタで呼び出すことができるデータベースにストアド プロシージャを指定します。例:IsEmployeeEligible、CalculateOrderDiscount</td></tr>
 <tr><td>データを使用できるクエリ</td><td>いいえ</td><td>&nbsp;</td><td>省略可能。任意のデータを SQL Server データベース テーブルのポーリングに使用できるかどうかを決定する SQL ステートメントを指定します。例: SELECT COUNT(*) from table_name.</td></tr>
 <tr><td>データ クエリのポーリング</td><td>いいえ</td><td>&nbsp;</td><td>省略可能。SQL Server データベース テーブルをポーリングする SQL ステートメントを指定します。任意の数の SQL ステートメントをセミコロンで区切って指定できます。例:SELECT * from table_name; DELETE from table_name. 注:無限ループで終了しないようにポーリング ステートメントを指定する必要があります。たとえば、select の後に delete が続き、フラグに基づく select の後にフラグの更新が続く必要があります。</td></tr>
</table>


 ![][1]  

## ハイブリッド構成 (省略可能) ##

注:この手順は、ファイアウォールの背後にある内部設置型の SQL Server を使用している場合のみ必要です。

[参照]、[API アプリ]、[<作成した API アプリの名前>] の順に選択して、作成した API アプリを参照します。次の動作が表示されます。この段階でハイブリッド接続がまだ確立されていないため、セットアップは完了していません。

![][2] 

ハイブリッド接続を確立するには、次の操作を行います。

1. プライマリ接続文字列をコピーします。
2.  '[Download and configure (ダウンロードして構成)]' リンクをクリックします。
3. 開始されるインストール プロセスに従い、要求に応じてプライマリ接続文字列を入力します。
4. セットアップ プロセスが完了すると、次のようなダイアログ ボックスが表示されます。

![][3] 

もう一度作成した API アプリを参照すると、ハイブリッド接続の状態が "接続中" と表示されます。 

![][4] 

注: セカンダリ接続文字列に切り替える場合は、ハイブリッドのセットアップをやり直して、プライマリ接続文字列の代わりにセカンダリ接続文字列を指定します。  

## ロジック アプリでの使い方 ##

SQL コネクタは、ロジック アプリでトリガーまたはアクションとして使用できます。トリガーとすべてのアクションは、JSON と XML の両方のデータ形式をサポートします。パッケージ設定の一部として提供されているすべてのテーブルには、JSON と XML の一連のアクションがあります。XML のトリガーとアクションを使用している場合は、変換 API アプリを使用してデータを別の XML データ形式に変換できます。 

SQL テーブルからデータをポーリングし、そのデータを別のテーブルに追加し、データを更新する、簡単なロジック アプリを見てみましょう。



-  ロジック アプリを作成または編集するときに、トリガーとして作成した SQL コネクタの API アプリを選択します。ここでは、使用できるトリガーとして Poll Data (JSON) と Poll Data (XML) が表示されます。

 ![][5] 


- トリガーを選択し (Poll Data (JSON))、頻度を指定して、[✓] をクリックします。

![][6] 



- トリガーは、ロジック アプリで構成されたように表示されます。トリガーの出力が表示されます。これは、後続のアクションの入力として使用できます。 

![][7] 


- ギャラリーからアクションと同じ SQL コネクタを選択します。いずれかの挿入アクションを選択します (ここでは、Insert Into TempEmployeeDetails (JSON))。

![][8] 



- 挿入するレコードの入力を指定し、[✓] をクリックします。 

![][9] 



- ギャラリーからアクションと同じ SQL コネクタを選択します。同じテーブルの更新アクションを選択します (例: Update EmployeeDetails)。

![][11] 



- 更新アクションの入力を指定し、[✓] をクリックします。 

![][12] 

ポーリングされるテーブルに新しいレコードを追加すると、ロジック アプリをテストできます。

<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.jpg
[2]: ./media/app-service-logic-connector-sql/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sql/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sql/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sql/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sql/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sql/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-sql/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-sql/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-sql/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-sql/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-sql/LogicApp8.jpg




<!--HONumber=52--> 