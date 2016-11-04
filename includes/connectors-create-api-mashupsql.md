### 前提条件
* SQL Server データベースへのアクセス

ロジック アプリで Mashup SQL を使用するには、Mashup SQL アカウントに接続するロジック アプリを承認する必要があります。幸い、Azure ポータルのロジック アプリ内から簡単に実行できます。

次に、Mashup SQL アカウントに接続するロジック アプリを承認する手順を示します。

1. Mashup SQL への接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Show Microsoft managed APIs]** (Microsoft Managed API を表示) を選択し、検索ボックスに「*Mashup SQL*」と入力します。使用するトリガーまたはアクションを選択します。 ![Mashup SQL 手順 1](./media/connectors-create-api-mashupsql/mashupsql-1.png)
2. これまでに Mashup SQL への接続を作成していない場合は、Mashup SQL の資格情報を指定するよう求められます。この資格情報を使用して、接続するロジック アプリの承認と、Mashup SQL アカウントのデータへのアクセスが行われます。 ![Mashup SQL 手順 2](./media/connectors-create-api-mashupsql/mashupsql-2.png)
3. 接続が作成され、ロジック アプリで他の手順を実行できるようになります。 ![Mashup SQL 手順 3](./media/connectors-create-api-mashupsql/mashupsql-3.png)

