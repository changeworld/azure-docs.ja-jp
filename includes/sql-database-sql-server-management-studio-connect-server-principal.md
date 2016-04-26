## サーバー レベルのプリンシパル ログインを使用して Azure SQL Database に接続する

サーバー レベルのプリンシパル ログインを使用して SSMS で Azure SQL Database に接続するには、次の手順を使用します。

1. Windows 検索ボックスで、「Microsoft SQL Server Management Studio」と入力し、デスクトップ アプリをクリックして SSMS を起動します。

2. [サーバーへの接続] ウィンドウで次の情報を入力します。

 - **[サーバーの種類]**: 既定値はデータベース エンジンです。この値は変更しないでください。
 - **[サーバー名]**: SQL Database をホストするサーバーの名前を、*&lt;サーバー名>*.**database.windows.net** の形式で入力します。
 - **[認証の種類]**: 最初は [SQL 認証] を選択します。SQL Database 論理サーバーで Active Directory を有効にしてある場合は、[Active Directory パスワード認証] または [Active Directory 統合認証] を選択できます。
 - **[ユーザー名]**: [SQL 認証] または [Active Directory パスワード認証] を選択した場合は、サーバーでデータベースへのアクセス権を持つユーザーの名前を入力します。
 - **[パスワード]**: [SQL 認証] または [Active Directory パスワード認証] を選択した場合は、指定したユーザーのパスワードを入力します。
   
       ![SQL Server Management Studio: SQL Database サーバーへの接続](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-1.png)

3. **[接続]** をクリックします。
 
4. クライアントの IP アドレスが SQL Database 論理サーバーへのアクセス権を持たない場合は、Azure アカウントにサインインしてサーバー レベルのファイアウォール規則を作成するように求められます。Azure サブスクリプション管理者の場合は、**[サインイン]** をクリックして、サーバー レベルのファイアウォール規則を作成します。それ以外の場合は、Azure 管理者にサーバー レベルのファイアウォール規則を作成してもらいます。
 
      ![SQL Server Management Studio: SQL Database サーバーへの接続](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-2.png)
 
1. Azure サブスクリプション管理者であり、サインインする必要が場合は、サインイン ページが表示されたら、サブスクリプションの資格情報を指定してサインインします。

      ![sign in](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-3.png)
 
1. Azure に正常にサインインした後は、既定のサーバー レベルのファイアウォール規則を確認し (IP アドレスの範囲を許可するように変更できます)、**[OK]** をクリックしてファイアウォール規則を作成し、SQL Database への接続を完了します。
 
      ![新しいサーバー レベルのファイアウォール](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-4.png)
 
5. 指定した資格情報でアクセスできる場合、オブジェクト エクスプローラーが開き、管理タスクやデータのクエリを実行できます。
 
     ![新しいサーバー レベルのファイアウォール](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)
 
     
 ## 接続に関するエラーのトラブルシューティング

接続の失敗の最も一般的な原因は、サーバー名、ユーザー名 (<*servername*> はデータベースではなく論理サーバーの名前です)、パスワードの間違いや、サーバーがセキュリティ上の理由で接続を許可しないといったものです。

<!---HONumber=AcomDC_0420_2016-->