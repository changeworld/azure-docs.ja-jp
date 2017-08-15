リモート デスクトップを使用して SQL Server 仮想マシンに接続するには、次の手順に従います。

1. Azure 仮想マシンが作成され、実行されてから、Azure Portal の [仮想マシン] アイコンをクリックして、VM を表示します。

1. 新しい VM の省略記号 **[...]** をクリックします。

1. **[接続]**をクリックします。

   ![ポータルで VM に接続する](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. ブラウザーによって VM のためにダウンロードされた **RDP** ファイルを開きます。

1. リモート デスクトップ接続で、このリモート接続の発行元が特定できないことが通知されます。 **[接続]** をクリックして続行します。

1. **[Windows セキュリティ]** ダイアログで、**[別のアカウントを使う]** をクリックします。 これを表示するには、**[その他]** をクリックする必要があります。 VM の作成時に構成したユーザー名とパスワードを指定します。 ユーザー名の前にバックスラッシュを追加する必要があります。

   ![リモート デスクトップ認証](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

SQL Server 仮想マシンに接続した後は、SQL Server Management Studio を起動し、ローカル管理者の資格情報を使用して Windows 認証で接続できます。 SQL Server 認証を有効にした場合は、プロビジョニングの間に構成した SQL のログインとパスワードを使用して SQL 認証で接続することもできます。

マシンにアクセスすると、要件に基づいてマシンと SQL Server の設定を直接変更することができます。 たとえば、ファイアウォールの設定を構成したり、SQL Server の構成設定を変更したりできます。