
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## 新しい Azure SQL Database サーバーレベル ファイアウォールを作成する

次の手順に従い、Azure ポータルで、個々の IP アドレス (クライアント コンピューター) または IP アドレスの範囲全体から SQL Database 論理サーバーに接続できるようなサーバーレベルのファイアウォール規則を作成します。

1. [Azure ポータル](http://portal.azure.com)に接続していない場合は接続します。
2. 既定のブレードで、**[SQL Server]** をクリックします。

  	![新しいサーバー ファイアウォール](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)

2. [SQL Server] ブレードで、ファイアウォール規則を作成する SQL Database サーバーをクリックします。

 	![新しいサーバー ファイアウォール](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)
           
3. サーバーのプロパティを確認します。

 	![新しいサーバー ファイアウォール](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)
      
4. [設定] ブレードで **[ファイアウォール]** をクリックします。

 	![新しいサーバー ファイアウォール](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-4.png)
    
5. **[クライアント IP の追加]** をクリックすると、Azure でそのクライアント IP アドレスの規則が作成されます。

      ![新しいサーバー ファイアウォール](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)

6. 必要に応じて、追加した IP アドレスをクリックしてファイアウォール アドレスを編集し、IP アドレスの範囲にアクセスできるようにします。

      ![新しいサーバー ファイアウォール](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)
    
7. **[保存]** をクリックしてサーバーレベルのファイアウォール規則を作成します。

     ![新しいサーバー ファイアウォール](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)

	>[AZURE.IMPORTANT] クライアント IP アドレスは不定期で変更される可能性があるため、新しいファイアウォール規則を作成するまでサーバーにアクセスできなくなる場合があります。[Bing](http://www.bing.com/search?q=my%20ip%20address) を使用して IP アドレスを確認し、1 つの IP アドレスまたは特定の範囲の IP アドレスを追加できます。詳細については、「[Azure ポータルで既存のサーバー レベルのファイアウォール規則を管理する](sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal)」を参照してください。

<!---HONumber=AcomDC_0427_2016-->