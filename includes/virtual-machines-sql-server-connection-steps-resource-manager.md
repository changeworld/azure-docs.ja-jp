### パブリック IP アドレスの DNS ラベルの構成

インターネットから SQL Server データベース エンジンに接続するには、最初にパブリック IP アドレスの DNS ラベルを構成します。

> [AZURE.NOTE] 同じ Virtual Network 内の SQL Server インスタンスのみに接続する場合、またはローカルでのみ接続する予定の場合は、DNS ラベルは必要ありません。

DNS ラベルを作成するには、最初にポータルで **[Virtual Machines]** を選びます。SQL Server VM を選択し、そのプロパティを表示します。

1. 仮想マシン ブレードで、**[パブリック IP アドレス]** を選びます。

	![パブリック IP アドレス](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

2. パブリック IP アドレスのプロパティで、**[構成]** を展開します。

3. DNS ラベル名を入力します。この名前は、IP アドレスを直接使用するのではなく名前で SQL Server VM に接続するために使用できる A レコードです。

4. **[保存]** ボタンをクリックします。

	![DNS ラベル](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### 別のコンピューターからデータベース エンジンに接続する

1. インターネットに接続されたコンピューターで、SQL Server Management Studio (SSMS) を開きます。

2. **[サーバーへの接続]** または **[データベース エンジンへの接続]** ダイアログ ボックスで、**[サーバー名]** の値を編集します。前のタスクで決定した仮想マシンの完全な DNS 名を入力します。

3. **[認証]** ボックスで、**[SQL Server 認証]** を選択します。

5. **[ログイン]** ボックスに、有効な SQL ログインの名前を入力します。

6. **[パスワード]** ボックスに、ログインのパスワードを入力します。

7. **[接続]** をクリックします。

	![SSMS 接続](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)

<!---HONumber=AcomDC_0629_2016-->