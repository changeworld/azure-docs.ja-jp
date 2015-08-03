<properties
   pageTitle="Microsoft Azure App Service での SQL コネクタの使用"
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
   ms.date="06/30/2015"
   ms.author="sameerch"/>


# Microsoft SQL コネクタ

オンプレミスの SQL Server または Azure SQL Database に接続し、情報またはデータの作成と変更を行います。コネクタを Logic Apps で使用して、"ワークフロー" の一部としてデータを取得、処理、またはプッシュできます。SQL コネクタをワークフローで使用すると、さまざまなシナリオを実現できます。たとえば、次のようなことができます。

- Web またはモバイル アプリケーションを使用して、SQL データベースに存在するデータの一部を公開する。
- ストレージ用の SQL データベース テーブルにデータを挿入する。たとえば、従業員レコードの入力や販売注文の更新などを実行できます。
- データを SQL から抽出してビジネス プロセスで使用する。たとえば、顧客レコードを取得し、SalesForce にそれらの顧客レコードを書き込むことができます。

## トリガーとアクション
*トリガー*とは、発生するイベントを指します。たとえば、注文が更新された、新しい顧客が追加された、といったイベントがあります。*アクション*は、トリガーの結果です。たとえば、注文が更新されたときに、営業担当者にアラートを送信します。または、新しい顧客が追加されたときに、ウェルカム メールを新しい顧客に送信します。

SQL コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。パッケージ設定に含まれるすべてのテーブルに対して (詳細はこのトピックで後述します)、JSON アクションと XML アクションのセットがあります。

SQL コネクタでは、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
データのポーリング | <ul><li>Insert Into Table</li><li>Update Table</li><li>Select From Table</li><li>Delete From Table</li><li>Call Stored Procedure</li></ul>

## SQL コネクタを作成する

コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. **[API Apps]** を選択し、 “SQL コネクタ” を検索します。
3. 名前、App Service プラン、その他のプロパティを入力します。
4. 次のパッケージの設定を入力します。

	名前 | 必須 | 説明
--- | --- | ---
サーバー名 | あり | SQL Server の名前を入力します。たとえば、「*SQLserver/sqlexpress*」や「*SQLserver.mydomain.com*」と入力します。
ポート | いいえ | 既定値は 1433 です。
ユーザー名 | あり | SQL Server にログインできるユーザー名を入力します。オンプレミスの SQL Server に接続する場合は、SQL 認証資格情報を入力します。
パスワード | あり | ユーザー名のパスワードを入力します。
データベース名 | あり | 接続先のデータベースを入力します。たとえば、「*Customers*」や「*dbo/orders*」と入力します。
オンプレミス | あり | 既定値は False です。Azure SQL データベースに接続する場合は False を入力します。オンプレミスの SQL Server に接続する場合は True を入力します。
Service Bus の接続文字列 | いいえ | オンプレミスに接続する場合は、Service Bus Relay の接続文字列を入力します。<br/><br/>[ハイブリッド接続マネージャーの使用](app-service-logic-hybrid-connection-manager.md)<br/>[Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)
パートナー サーバー名 | いいえ | プライマリ サーバーを使用できない場合は、代替サーバーまたはバックアップ サーバーとして、パートナー サーバーを入力できます。
テーブル | いいえ | コネクタによって更新可能なデータベース テーブルの一覧を示します。たとえば、「*OrdersTable*」や「*EmployeeTable*」と入力します。テーブルが入力されない場合はすべてのテーブルを使用できます。このコネクタをアクションとして使用するには有効なテーブルまたはストアド プロシージャが必要です。
ストアド プロシージャ | いいえ | コネクタによって呼び出すことができる、既存のストアド プロシージャを入力します。たとえば、「*sp_IsEmployeeEligible*」や「*sp_CalculateOrderDiscount*」と入力します。このコネクタをアクションとして使用するには有効なテーブルまたはストアド プロシージャが必要です。
Data Available Query (データの存在確認クエリ) | トリガのサポート | SQL Server データベース テーブルのポーリングに使用できるデータが存在するかどうかを調べる SQL ステートメント。これは、存在するデータの行数を表す数値を返します。例: SELECT COUNT(*) from table_name
Poll Data Query (データ ポーリング クエリ) | トリガのサポート | SQL Server データベース テーブルをポーリングする SQL ステートメント。任意の数の SQL ステートメントをセミコロンで区切って指定できます。このステートメントはトランザクション的に実行され、データがロジック アプリで安全に保存される場合にのみコミットされます。例: SELECT * FROM table_name; DELETE FROM table_name <br/><br/>**注**<br/>同じデータが再度ポーリングされないように、選択したデータを削除、移動、または更新して無限ループを回避するポーリング ステートメントを指定する必要があります。

5. 完了すると、パッケージの設定は次のようになります。<br/> ![][1]

## コネクタをトリガーとして使用する
SQL テーブルからデータをポーリングし、そのデータを別のテーブルに追加してデータを更新する単純なロジック アプリを見てみましょう。

SQL コネクタをトリガーとして使用するには、**データの存在確認クエリ**と**データ ポーリング クエリ**の値を入力します。**データの存在確認クエリ**は、入力したスケジュールに従って実行され、データが存在するかどうかを判断します。このクエリはスカラー値のみを返すため、頻繁に実行しても問題のないように調整および最適化することができます。

**データ ポーリング クエリ**は、データの存在確認クエリによって、データが存在することが判明した場合にのみ実行されます。このステートメントはトランザクション内で実行され、抽出されたデータがワークフローで永続的に保存される場合にのみコミットされます。同じデータが無限に再抽出されないようにすることが重要です。この実行のトランザクション的な性質を使用してデータを削除するか更新することで、次回データがクエリされるときに収集されないようにすることができます。

> [AZURE.NOTE]このステートメントから返されるスキーマによって、コネクタで使用可能なプロパティが決まります。すべての列に名前を付ける必要があります。

#### データの存在確認クエリの例

	SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### データ ポーリング クエリの例

	SELECT *, GetData() as 'PollTime' FROM [Order]
		WHERE OrderStatus = 'ProcessedForCollection'
		ORDER BY Id DESC;
	UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
		WHERE Id =
		(SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### トリガーを追加する
1. ロジック アプリを作成または編集するときに、トリガーとして作成した SQL コネクタを選択します。使用できるトリガーが一覧表示されます: **[データのポーリング (JSON)]** および **[データのポーリング (XML)]** <br/> ![][5]

2. **[データのポーリング (JSON)]** トリガーを選択し、頻度を入力して、[✓] をクリックします: <br/> ![][6]

3. これで、トリガーは、ロジック アプリで構成されたものとして表示されます。トリガーの出力が表示され、後続のアクションの入力として使用できます: <br/> ![][7]

## コネクタをアクションとして使用する
SQL テーブルからデータをポーリングする単純なロジック アプリのシナリオを使用して、データを別のテーブルに追加し、データを更新します。

SQL コネクタをアクションとして使用するには、SQL コネクタを作成したときに入力したテーブルまたはストアド プロシージャの名前を入力します。

1. トリガーの後ろに (または [このロジックを手動で実行] を選択して)、作成した SQL コネクタをギャラリーから追加します。いずれかの挿入アクションを選択します (*Insert Into TempEmployeeDetails (JSON)* など)。<br/> ![][8]

2. 挿入されるレコードの入力値を指定し、[✓] をクリックします: <br/> ![][9]

3. ギャラリーから、作成済みの同じ SQL コネクタを選択します。アクションとして、同じテーブルに対する更新アクションを選択します。例: *[Update EmployeeDetails]*: <br/> ![][11]

4. 更新アクション用の入力値を指定し、[✓] をクリックします: <br/> ![][12]

ポーリングされるテーブルに新しいレコードを追加すると、ロジック アプリをテストできます。

## 可能な処理と不可能な処理

SQL クエリ | サポートされています | サポートされていません
--- | --- | ---
WHERE 句 | <ul><li>演算子: AND、OR、=、<>、<、<=、>、>=、および LIKE</li><li>複数のサブ条件は '(' および ')' で結合できます。</li><li>文字列リテラル、日付と時刻 (一重引用符で囲む)、数値 (数字のみを含める必要がある)</li><li>((オペランド 演算子 オペランド) AND/OR (オペランド 演算子 オペランド))* のように厳密なバイナリ式形式にする必要があります</li></ul> | <ul><li>演算子: Between、IN</li><li>ADD()、MAX() NOW()、POWER() などのすべての組み込み関数</li><li>*、-、+ などの算術演算子</li><li>+ を使用した文字列の連結</li><li>すべての結合</li><li>IS NULL と IS NOT Null</li><li>16 進数などの、数値以外の文字を含む任意の数字</li></ul>フィールド (Select クエリ内) | <ul><li>コンマで区切られた有効な列の名前テーブル名のプレフィックスは許可されていません (コネクタは、一度に 1 つのテーブルで動作)。</li><li>'[' と ']' を使用して名前をエスケープすることができます</li></ul> | <ul><li>TOP、DISTINCT などのキーワード</li><li>Street + City + Zip AS Address のようなエイリアス化</li><li>ADD()、MAX() NOW()、POWER() などのすべての組み込み関数</li><li>*、-、+、などの算術演算子</li><li>+ を使用した文字列の連結</li></ul>

#### ヒント

- 高度なクエリでは、ストアド プロシージャを作成し、execute ストアド プロシージャ API を使用して実行することをお勧めします。
- 内部クエリを使用する場合は、ストアド プロシージャ内で使用します。
- 複数の条件を結合するには、'AND' 演算子と 'OR' 演算子を使用できます。

## ハイブリッド構成 (省略可能)

> [AZURE.NOTE]この手順は、ファイアウォールの内側にあるオンプレミスの SQL Server を使用する場合にのみ、実行する必要があります。

App Service では、 ハイブリッド構成マネージャーを使用して、オンプレミス システムに安全に接続します。コネクタでオンプレミスの SQL Server を使用する場合は、ハイブリッド接続マネージャーが必要です。

「[ハイブリッド接続マネージャーの使用](app-service-logic-hybrid-connection-manager.md)」を参照してください。


## コネクタでできること
コネクタが作成されたため、Logic App を使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

REST API を使用した API Apps を作成します。[コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)に関するページを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png

<!---HONumber=July15_HO4-->