<a id="log-in-to-the-azure-portal" class="xliff"></a>

## Azure ポータルにログインする

[Azure Portal](https://portal.azure.com/) にログインします。

<a id="create-a-blank-sql-database-using-the-azure-portal" class="xliff"></a>

## Azure Portal を使用して空の SQL Database を作成する

Azure SQL データベースは、定義済みの一連の[コンピューティング リソースとストレージ リソース](../articles/sql-database/sql-database-service-tiers.md)を使って作成されます。 データベースは、[Azure リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)内と [Azure SQL Database 論理サーバー](../articles/sql-database/sql-database-features.md)内に作成されます。 

空の SQL Database を作成するには、次の手順に従います。 

1. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

2. **[新規]** ページで **[データベース]** を選択し、**[データベース]** ページで **[SQL Database]** を選択します。 

   ![空のデータベースを作成](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. 前の画像で示されているように、[SQL Database] のフォームに次の情報を入力します。   

   | 設定 | 推奨値 | Description |
   | --------| --------------- | ----------- | 
   | **[データベース名]** | mySampleDatabase | 有効なデータベース名については、「[Database Identifiers (データベース識別子)](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)」を参照してください。 | 
   | **サブスクリプション** | 該当するサブスクリプション  | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **[リソース グループ]** | myResourceGroup | 有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   | **[ソースの選択]** | 空のデータベース | 空のデータベースを作成するように指定します。 |
   ||||

4. **[サーバー]** をクリックして、新しいデータベース用の新しいサーバーを作成して構成します。 **[新しいサーバー]** フォームには次の情報を入力してください。 

   | 設定 | 推奨値 | Description |
   | --------| --------------- | ----------- | 
   | **[サーバー名]** | グローバルに一意の名前。 | 有効なサーバー名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 | 
   | **[サーバー管理者ログイン]** | 有効な名前。 | 有効なログイン名については、「[Database Identifiers (データベース識別子)](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)」を参照してください。|
   | **パスワード** | 有効なパスワード。 | パスワードには 8 文字以上が使用され、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。 |
   | **場所** | 有効な場所。 | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |
   ||||

   ![データベース サーバーの作成](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. **[選択]**をクリックします。

6. **[価格レベル]** をクリックして、新しいデータベースのサービス レベルとパフォーマンス レベルを指定します。 このチュートリアルでは、**20 DTU** と **250** GB のストレージを選択します。

   ![データベースの作成 -s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. **[Apply]**をクリックします。  

8. 空のデータベースの**照合順序**を選択します (このチュートリアルでは既定値を使用)。 照合順序の詳細については、「[Collations (照合順序)](https://docs.microsoft.com/sql/t-sql/statements/collations)」を参照してください。

9. **[作成]** をクリックしてデータベースをプロビジョニングします。 プロビジョニングの完了には 1 分 30 秒程度かかります。 

10. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

   ![通知](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

<a id="create-a-server-level-firewall-rule-using-the-azure-portal" class="xliff"></a>

## Azure Portal でサーバーレベルのファイアウォール規則を作成する

SQL Database サービスは、サーバーレベルでファイアウォールを作成します。 初期状態では、外部のツールやアプリケーションがサーバーやサーバー上のデータベースに接続することがファイアウォールで禁止されています。 接続を許可するためには、特定の IP アドレスを開放するファイアウォール規則を作成する必要があります。 以下の手順に従い、クライアントの IP アドレスに対して [SQL Database サーバーレベルのファイアウォール規則](../articles/sql-database/sql-database-firewall-configure.md)を作成し、その IP アドレスのみに SQL Database ファイアウォールを介して外部接続できるようにします。 


> [!NOTE]
> Azure SQL Database の通信は、ポート 1433 上で行われます。 SQL Database に接続するためには、ご利用のネットワークのファイアウォールで、ポート 1433 経由の送信トラフィックを許可する必要があります。


1. デプロイが完了したら、左側のメニューから **[SQL データベース]** をクリックし、**SQL データベース** ページで、**mySampleDatabase** をクリックします。 データベースの概要ページが開き、完全修飾サーバー名 (**mynewserver20170313.database.windows.net** など) や追加の構成オプションが表示されます。 この完全修飾サーバー名は、後で使用するためコピーしておいてください。

   > [!IMPORTANT]
   > 以降のクイック スタートでサーバーとそのデータベースに接続するには、この完全修飾サーバー名が必要になります。
   > 

   ![サーバー名](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

2. 前の画像に示されているように、ツール バーの **[サーバー ファイアウォールの設定]** をクリックします。 SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。 

   ![サーバーのファイアウォール規則](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 


3. ツール バーの **[クライアント IP の追加]** をクリックし、現在の IP アドレスをファイアウォール規則に追加します。 ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

4. [ **Save**] をクリックします。 論理サーバーでポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルのファイアウォール規則が作成されます。

   ![サーバーのファイアウォール規則の設定](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. **[OK]** をクリックし、**[ファイアウォール設定]** ページを閉じます。

これで、Azure SQL Database サーバーとそのデータベースに、SQL Server Management Studio (SSMS) などのツールを使って接続することができます。 接続は、この IP アドレスから、先に作成しておいたサーバーの管理者アカウントを使用して行います。


> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL Database ファイアウォール経由のアクセスが有効になります。 すべての Azure サービスに対して無効にするには、このページの **[オフ]** をクリックします。


<a id="get-connection-string-values-using-the-azure-portal" class="xliff"></a>

## Azure Portal で接続文字列の値を取得する

Azure Portal で、Azure SQL Database サーバーの完全修飾サーバー名を取得します。 その完全修飾サーバー名は、SQL Server Management Studio でのサーバーへの接続に使用します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。

2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 

3. そのデータベースの Azure Portal ページの **[要点]** ウィンドウで、**サーバー名**を見つけてコピーします。

   ![接続情報](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 
