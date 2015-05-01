<properties 
   pageTitle="Oracle コネクタ" 
   description="Oracle コネクタの使用方法" 
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


# Oracle データベース コネクタ #

コネクタをロジック アプリで使用すると、フローの一部として、データをフェッチ、処理、またはプッシュ転送できます。Oracle コネクタをフローで使用すると、さまざまなシナリオを実現できます。例をいくつか示します。  

1.	Web またはモバイル ユーザーのフロント エンドを介して、Oracle データベースに存在するデータの一部を公開する
2.	ストレージ用の Oracle データベース テーブルにデータを挿入する (例: 従業員レコード、販売注文など)
3.	ビジネス プロセスで使用するデータを Oracle から抽出する

これらのシナリオに対しては、次の操作を実行する必要があります。 

1. Oracle コネクタ API アプリのインスタンスを作成する
2. API アプリがオンプレミスの Oracle サーバーと通信できるようにハイブリッド接続を確立する
3. 作成した API アプリをロジックで使用して、目的のビジネス プロセスを実現する

	### 基本的なトリガーとアクション
		
    - データのポーリング (トリガー) 
    - テーブルに挿入
    - テーブルの更新
    - テーブルから選択
    - テーブルから削除
    - ストアド プロシージャの呼び出し

## Oracle データベース コネクタ API アプリのインスタンスを作成する ##

Oracle コネクタを使用するには、まず Oracle コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の操作を実行します。

1. Azure ポータルの左下にある '[+ 新規]' を使用して Azure Marketplace を開きます。
2. [Web + モバイル]、[API アプリ] の順に移動して、"Oracle コネクタ" を検索します。
3. 最初のブレードで、名前、アプリ サービス プランなどの一般的な詳細を入力します。
4. 下の表に記載されているパッケージ設定を入力します。.



<table class="tableizer-table">
<tr class="tableizer-firstrow"><th>名前</th><th>必須</th><th>説明</th></tr>
 <tr><td>データ ソース</td><td>あり</td><td>Oracle クライアントがインストールされているコンピューターの tnsnames.ora ファイルで指定されているデータ ソース (ネット サービス) の名前です。データ ソース名と tnsnames.ora の詳細については、「[Configuring the Oracle Client (Oracle クライアントの構成)]」をご覧ください。</td></tr>
 <tr><td>ユーザー名</td><td>あり</td><td>Oracle サーバーに接続する有効なユーザー名を指定します。</td></tr>
 <tr><td>パスワード</td><td>あり</td><td>Oracle サーバーに接続する有効なパスワードを指定します。</td></tr>
 <tr><td>Service Bus の接続文字列</td><td>あり</td><td>省略可能。Oracle サーバーが内部設置型の場合は、このパラメーターを指定します。有効な Service Bus 名前空間の接続文字列である必要があります。Oracle サーバーにアクセスできるサーバーにリスナー エージェントをインストールする必要があります。API アプリの概要ページに移動し、 [ハイブリッド接続]' をクリックしてエージェントをインストールできます。</td></tr>
 <tr><td>テーブル</td><td>いいえ</td><td>省略可能。コネクタで変更できるデータベース内のテーブルを指定します。例: OrdersTable、EmployeeTable</td></tr>
 <tr><td>ストアド プロシージャ</td><td>いいえ</td><td>省略可能。コネクタで呼び出すことができるデータベースにストアド プロシージャを指定します。例:IsEmployeeEligible、CalculateOrderDiscount</td></tr>
 <tr><td>関数</td><td>いいえ</td><td>省略可能。コネクタで呼び出すことができるデータベースに関数を指定します。例:IsEmployeeEligible、CalculateOrderDiscount</td></tr>
 <tr><td>パッケージ エンティティ</td><td>いいえ</td><td>省略可能。コネクタで呼び出すことができるデータベースにパッケージを指定します。例:PackageOrderProcessing.CompleteOrder、PackageOrderProcessing.GenerateBill</td></tr>
 <tr><td>データを使用できるステートメント</td><td>いいえ</td><td>省略可能。任意のデータをポーリングに使用できるかどうかを決定するステートメントを指定します。例:SELECT * from table_name</td></tr>
 <tr><td>ポーリングの種類</td><td>いいえ</td><td>省略可能。ポーリングの種類を指定します。許可値は、"Select"、"Procedure"、"Function"、"Package" です。</td></tr>
 <tr><td>ポーリング ステートメント</td><td>いいえ</td><td>省略可能。Oracle サーバー データベースをポーリングするステートメントを指定します。例: SELECT * from table_name</td></tr>
 <tr><td>ポスト ポーリング ステートメント</td><td>いいえ</td><td>省略可能。ポーリング後に実行するステートメントを指定します。例: DELETE * from table_name.</td></tr>
</table>



 ![][1]  

## ハイブリッド構成 ##

[参照]、[API アプリ]、[<作成した API アプリの名前>] の順に選択して、作成した API アプリを参照します。次の動作が表示されます。この段階でハイブリッド接続がまだ確立されていないため、セットアップは完了していません。

![][2] 

ハイブリッド接続を確立するには、次の操作を行います。

1. プライマリ接続文字列をコピーします。
2.  [Download and configure (ダウンロードして構成)]' リンクをクリックします。
3. 開始されるインストール プロセスに従い、要求に応じてプライマリ接続文字列を入力します。
4. セットアップ プロセスが完了すると、次のようなダイアログ ボックスが表示されます。

![][3] 

もう一度作成した API アプリを参照すると、ハイブリッド接続の状態が "接続中" と表示されます。 

![][4] 

注: セカンダリ接続文字列に切り替える場合は、ハイブリッドのセットアップをやり直して、プライマリ接続文字列の代わりにセカンダリ接続文字列を指定します。  

## ロジック アプリでの使い方 ##

Oracle コネクタは、ロジック アプリでトリガーまたはアクションとして使用できます。トリガーとすべてのアクションは、JSON と XML の両方のデータ形式をサポートします。パッケージ設定の一部として提供されているすべてのテーブルには、JSON と XML の一連のアクションがあります。XML のトリガーとアクションを使用している場合は、変換 API アプリを使用してデータを別の XML データ形式に変換できます。 

Oracle テーブルからデータをポーリングし、そのデータを別のテーブルに追加し、データを更新する、簡単なロジック アプリを見てみましょう。



-  ロジック アプリを作成または編集するときに、トリガーとして作成した Oracle コネクタの API アプリを選択します。ここでは、使用できるトリガーとして Poll Data (JSON) と Poll Data (XML) が表示されます。

 ![][5] 


- トリガーを選択し (ここでは、Poll Data (JSON))、頻度を指定して、[✓] をクリックします。

![][6] 



- トリガーは、ロジック アプリで構成されたように表示されます。トリガーの出力が表示されます。これは、後続のアクションの入力として使用できます。 

![][7] 


- ギャラリーからアクションと同じ Oracle コネクタを選択します。いずれかの挿入アクションを選択します (ここでは、Insert Into TempEmployeeDetails (JSON))。

![][8] 



- 挿入するレコードの入力を指定し、[✓] をクリックします。 

![][9] 



- ギャラリーからアクションと同じ Oracle コネクタを選択します。同じテーブルの更新アクションを選択します (例: Update EmployeeDetails)。

![][11] 



- 更新アクションの入力を指定し、[✓] をクリックします。 

![][12] 

ポーリングされるテーブルに新しいレコードを追加すると、ロジック アプリをテストできます。

<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.jpg
[2]: ./media/app-service-logic-connector-oracle/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-oracle/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-oracle/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.jpg

<!--Links-->
[Configuring the Oracle Client (Oracle クライアントの構成)]: https://msdn.microsoft.com/ja-jp/library/dd787872.aspx



<!--HONumber=52-->