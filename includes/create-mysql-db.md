#Azure で MySQL データベースを作成する方法

このガイドでは、[ClearDB] を使用して [Azure ストア]から MySQL データベースを作成する方法と、[Azure の Web サイト][waws]を作成するときに MySQL データベースをリンク済みリソースとして作成する方法について説明します。[ClearDB] は、サービスとしてのフォルト トレラント データベース プロバイダーであり、Azure データセンターで MySQL データベースを実行および管理し、どのアプリケーションからもデータベースに接続できるようにしています。  

##目次
* [方法: Azure ストアからの MySQL データベースの作成](#CreateFromStore)
* [方法: Azure Website のリンク済みリソースとしての MySQL データベースの作成](#CreateForWebSite)

<div class="dev-callout"> 
<b>注</b> 
<p>MySQL データベースを Web サイト作成プロセスの一部として作成するときは、無料データベースを作成するだけでかまいません。Azure ストアから MySQL データベースを作成すると、無料データベースを作成するか、有料オプションから選択することができます。</p> 
</div>

<h2><a id="CreateFromStore"></a>方法: Azure ストアからの MySQL データベースの作成</h2>

[Azure ストア]から MySQL を作成するには、以下の手順を実行します。

1. [Azure の管理ポータル][portal]にログインします。
2. ページの下部にある **[+新規]** をクリックし、**[ストア]** を選択します。

	![Select add-on from store](./media/create-mysql-db/select-store.png)

3. **[ClearDB MySQL データベース]** を選択し、フレームの下部にある矢印をクリックします。

	![Select ClearDB MySQL Database](./media/create-mysql-db/select-cleardb-mysql.png)

4. プランを選択してデータベース名を入力し、リージョンを選択してフレームの下部にある矢印をクリックします。

	![Purchase MySQL database from store](./media/create-mysql-db/purchase-mysql.png)

5. チェックマークをクリックして購入を完了します。

	![Review and complete your purchase](./media/create-mysql-db/complete-mysql-purchase.png)

6. データベースが作成されたら、管理ポータルの **[アドオン]** タブから管理できます。

	![Manage MySQL database in Azure portal](./media/create-mysql-db/manage-mysql-add-on.png)

7. ページの下部にある **[接続文字列]** をクリックして、データベース接続情報を取得できます。

	![MySql connection information](./media/create-mysql-db/mysql-conn-info.png) 


<h2><a id="CreateForWebSite"></a>方法: Azure Website のリンク済みリソースとしての MySQL データベースの作成</h2>

[Azure の Web サイト][waws]を作成するときに MySQL データベースをリンク済みリソースとして作成するには、以下の手順を実行します。

1. [Azure の管理ポータル][portal]にログインします。
2. ページの下部にある **[+新規]** をクリックし、**[コンピューティング]**、**[Web サイト]**、**[データベースとともに作成]** の順に選択します。

	![Create website with database](./media/create-mysql-db/custom_create.png)

3. Web サイトの **URL** を指定して、サイトの**リージョン**を選択し、**[データベース]** ドロップダウン リストから **[新しい MySQL データベースを作成します]** を選択します。オプションで、接続文字列の既定の名前を置き換えることができます。ページの下部にある矢印をクリックします。

	![Provide website details](./media/create-mysql-db/provide-website-details.png) 

4. データベースの**名前**を指定してデータベースの**リージョン**を選択し (これは Web サイトのリージョンと同じにしてください)、ClearDB の法律条項に同意して、フレームの下部にあるチェックマークをクリックします。

	![Provide MySQL details](./media/create-mysql-db/provide-mysql-details.png)

5. Web サイトが作成されたら、サイトの名前をクリックしてサイトのダッシュボードに移動します。

	![Go to web site dashboard](./media/create-mysql-db/go-to-website-dashboard.png)

6. **[構成]** をクリックします。

	![Go to configure tab](./media/create-mysql-db/go-to-configure-tab.png)

7. **[接続文字列]** セクションまで下方向へスクロールし、**[接続文字列の表示]** をクリックします。 

	![Show connection string](./media/create-mysql-db/show-conn-string.png)

8. アプリケーションで使用する接続文字列をコピーします。

	![Shown connection string](./media/create-mysql-db/shown-conn-string.png)

> [WACOM.NOTE]接続文字列には、Web サイト アプリケーションから接続文字列名によりアクセスできます。.NET アプリケーションでは、接続文字列は **connectionStrings** オブジェクトにあります。他のプログラミング言語では、接続文字列には環境変数としてアクセスできます。詳細については、「[Web サイトの構成方法][configure]」を参照してください。

[ClearDB]: http://www.cleardb.com/
[waws]: /ja-jp/documentation/services/web-sites/
[Azure ストア]: /ja-jp/gallery/store/
[portal]: http://manage.windowsazure.com
[configure]: ../web-sites-configure/
