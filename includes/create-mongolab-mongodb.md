#Azure で MongoDB データベースを作成する方法

この記事では、[MongoLab] を使用して [Azure ストア]から MongoDB データベースを作成する方法について説明します。[MongoLab] は、サービスとしての MongoDB プロバイダーであり、Azure データセンターで MongoDB データベースを実行および管理し、どのアプリケーションからもデータベースに接続できるようにしています。  

[Azure ストア]から MongoDB データベースを作成するには、以下の手順を実行します。

1. [Azure の管理ポータル][portal]にログインします。
2. ページの下部にある **[+新規]** をクリックし、**[ストア]** を選択します。

	![Select add-on from store](./media/create-mongolab-mongodb/select-store.png)

3. **[MongoLab]** を選択し、フレームの下部にある矢印をクリックします。

	![Select MongoLab](./media/create-mongolab-mongodb/select-mongo-db.png)

4. データベース名を入力してリージョンを選択し、フレームの下部にある矢印をクリックします。

	![Purchase MongoLab database from store](./media/create-mongolab-mongodb/purchase-mongodb.png)

5. チェックマークをクリックして購入を完了します。

	![Review and complete your purchase](./media/create-mongolab-mongodb/complete-mongolab-purchase.png)

6. データベースが作成されたら、管理ポータルの **[アドオン]** タブから管理できます。

	![Manage MongoLab database in Azure portal](./media/create-mongolab-mongodb/manage-mongolab-add-on.png)

7. ページの下部にある **[接続文字列]** をクリックして、データベース接続情報を取得できます (上図を参照)。

	![MongoLab connection information](./media/create-mongolab-mongodb/mongolab-conn-info.png) 

[MongoLab]: https://mongolab.com/home
[waws]: /ja-jp/manage/services/web-sites/
[Azure ストア]: /ja-jp/store/overview/
[portal]: http://windows.azure.com/

<!--HONumber=42-->
