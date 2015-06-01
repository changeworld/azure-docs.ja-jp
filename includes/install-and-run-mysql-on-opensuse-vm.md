
1. 権限をエスカレートするには、次のように実行します。

		sudo -s
	
	パスワードを入力します。

2. 次のコマンドを実行し、MySQL Community Server エディションをインストールします。

		# zypper install mysql-community-server

	MySQL がダウンロードおよびインストールされるまで待ちます。
3. システムの起動時に MySQL が開始するように設定するには、次のコマンドを実行します。

		# insserv mysql
4. これで、次のコマンドで MySQL デーモン (mysqld) を手動で開始できます。

		# rcmysql start

	MySQL デーモンの状態を確認するには、次を実行します。

		# rcmysql status

	MySQL デーモンを停止する必要がある場合は、次を実行します。

		# rcmysql stop

5. 警告!インストール後、既定では MySQL ルート パスワードは空になっています。そこで、MySQL の保護に役立つスクリプト、**mysql_secure_installation** を実行することをお勧めします。**mysql_secure_installation** を実行すると、MySQL ルート パスワードの変更、匿名のユーザー アカウントの削除、リモート ルート ログインの無効化、テスト データベースの削除、および権限テーブルの再読み込みを行うように求められます。これらのオプションすべてに対して "はい" と答えて、ルート パスワードを変更することをお勧めします。次のコマンドを実行して、スクリプトを実行します。

		$ mysql_secure_installation

6. 実行後、MySQL にログインできます。

		$ mysql -u root -p

	先ほど変更した MySQL ルート パスワードを入力すると、データベースを操作するための SQL ステートメントを実行できるプロンプトが表示されます。

7. 新しい MySQL ユーザーを作成するには、**mysql>** プロンプトで次のコマンドを実行します。

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	行末のセミコロン (;) は、コマンドの終わりを示すために必要です。

8. データベースを作成し、このデータベースに対するアクセス許可を `mysqluser` ユーザーに付与するには、次のコマンドを実行します。

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	データベースのユーザー名とパスワードは、データベースに接続するスクリプトのみが使用します。データベース ユーザー アカウントは、システム上の実際のユーザー アカウントを表しているとは限りません。

9. 別のコンピューターにログインするには、次のコマンドを実行します。

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	`ip-address` は、MySQL への接続元コンピューターの IP アドレスです。
	
10. MySQL データベース管理ユーティリティを終了するには、次のコマンドを実行します。

		quit

11. MySQL をインストールしたら、MySQL にリモートでアクセスできるように、エンドポイントを構成します。[Azure の管理ポータル][AzurePreviewPortal]にログインします。管理ポータルで、**[仮想マシン]**、新しい仮想マシンの名前、**[エンドポイント]** の順にクリックします。

	![Endpoints][Image7]

12. ページの下部にある **[追加]** をクリックします。
	![Endpoints][Image8]

13. エンドポイントを追加します。名前に「MySQL」、プロトコルに **[TCP]** を指定し、**[パブリック ポート]** と **[プライベート ポート]** に「3306」を指定します。これにより、MySQL にリモートで接続することができます。
	![Endpoints][Image9]

14. Azure の OpenSUSE 仮想マシンで実行されている MySQL にリモート接続するには、ローカル コンピューターで次のコマンドを実行します。

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	たとえば、このチュートリアルで作成した仮想マシンを使用する場合、コマンドは次のようになります。

		mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

15. これで MySQL を適切に構成し、データベースと新しいユーザーを作成できました。MySQL の詳細については、[MySQL のドキュメント][MySQLDocs]を参照してください。	

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!--HONumber=45--> 
