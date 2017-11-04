1. 新しいブラウザー ウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。
2. **[新規]** > **[データベース]** > **[Azure Cosmos DB]** をクリックします。
   
   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. **[新しいアカウント]** ページで、新しい Azure Cosmos DB アカウントの設定を入力します。 
 
    設定|推奨値|説明
    ---|---|---
    ID|<*一意の名前を入力*>|この Azure Cosmos DB アカウントを識別するための一意の名前を入力します。 指定した ID に *documents.azure.com* が付加されて URI が作成されるので、ID は一意であっても識別可能なものを使用してください。<br><br>ID に含めることができるのは英小文字、数字、ハイフン (-) のみ、3 文字以上で 50 文字以内にする必要があります。
    API|SQL (DocumentDB)|API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB には、アプリケーションのニーズに応じて、Gremlin (グラフ)、MongoDB、SQL (DocumentDB)、Table (キー値) の 4 つの API が用意されています。現時点では、それぞれ別個のアカウントが必要です。 <br><br>このクイック スタートでは、SQL 構文を使用してクエリ可能なドキュメント データベースを作成するため、**SQL (DocumentDB)** を選択します。<br><br>[DocumentDB API の詳細を確認する](../articles/cosmos-db/documentdb-introduction.md)|
    [サブスクリプション]|*該当するサブスクリプション*|この Azure Cosmos DB アカウントに使用する Azure サブスクリプションを選択します。 
    リソース グループ|<*上記の ID で指定したものと同じ一意の名前を入力*>|自分のアカウントの新しいリソースグループの名前を入力します。 簡略化のため、ID と同じ名前を使用することができます。 
    場所|<*ユーザーに最も近いリージョンを選択*>|Azure Cosmos DB アカウントをホストする地理的な場所を入力します。 データに最も高速にアクセスできる、ユーザーに最も近い場所を使用します。
    Geo 冗長の有効化| 空白 | データベースのレプリケート バージョンが 2 番目 (ペア) のリージョンに作成されます。 空白のままにします。  
    [ダッシュボードにピン留めする] | Select | このボックスを選択すると、新しいデータベース アカウントが、アクセスしやすいようにポータルのダッシュボードに追加されます。

    **[Create]**をクリックします。

    ![Azure Cosmos DB の [新しいアカウント] ブレード](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

4. アカウントの作成には数分かかります。 アカウントの作成中は、ポータルに **[Deploying Azure Cosmos DB]\(Azure Cosmos DB をデプロイしています\)** というタイルが表示されます。

    ![Azure Portal の [通知] ウィンドウ](./media/cosmos-db-create-dbaccount/deploying-cosmos-db.png)

    アカウントが作成されると、**[Azure Cosmos DB アカウントが作成されました]** ページが表示されます。 

