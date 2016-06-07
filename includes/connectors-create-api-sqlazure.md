### 前提条件

- [SQL Azure](https://www.microsoft.com/sql) アカウント  


ロジック アプリで SQL Azure アカウントを使用するには、SQL Azure アカウントに接続するロジック アプリを承認しておく必要があります。これは、Azure ポータルのロジック アプリ内から簡単に実行できます。

SQL Azure アカウントに接続するロジック アプリを承認する手順を次に示します。
1. SQL Azure への接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Show Microsoft managed APIs (Microsoft Managed API を表示)]** を選択し、検索ボックスに「*SQL Azure*」と入力します。使用するトリガーまたはアクションを選択します。![SQL Azure の接続の作成手順](./media/connectors-create-api-sql/sql-1.png)  
2. これまでに SQL Azure への接続を作成したことがない場合は、SQL Azure の資格情報の入力を求められます。この資格情報を使用して、接続するロジック アプリの承認と、SQL Azure アカウントのデータへのアクセスが行われます。![SQL Azure の接続の作成手順](./media/connectors-create-api-sql/sql-2.png)  
3. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。![SQL Azure の接続の作成手順](./media/connectors-create-api-sql/sql-3.png)  

<!---HONumber=AcomDC_0525_2016-->