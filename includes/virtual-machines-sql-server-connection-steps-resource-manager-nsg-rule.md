### 仮想マシンのネットワーク セキュリティ グループの受信の規則を構成する

インターネット経由で SQL Server に接続する場合は、SQL Server インスタンスがリッスンしているポートのネットワーク セキュリティ グループに受信の規則を構成する必要があります。既定では、これは TCP ポート 1433 です。

1. ポータルで、**[仮想マシン]** を選び、SQL Server VM を選びます。

2. **[すべての設定]** リンクをクリックします。

3. **[ネットワーク インターフェイス]** を展開します。

	![ネットワーク インターフェイス](./media/virtual-machines-sql-server-connection-steps/rm-network-interface.png)

4. 仮想マシンのネットワーク インターフェイスを選びます。

4. **[ネットワーク セキュリティ グループ]** リンクをクリックします。

	![ネットワーク インターフェイス](./media/virtual-machines-sql-server-connection-steps/rm-network-security-group.png)

6. ネットワーク セキュリティ グループのプロパティで、**[受信セキュリティ規則]** を広げます。

5. **[Add] \(追加)** ボタンをクリックします。

6. **[名前]** に「SQLServerPublicTraffic」と入力します。

7. **[プロトコル]** を **[TCP]** に変更します。

8. **[宛先ポート範囲]** として「1433」 (または、SQL Server インスタンスがリッスンするポート) を指定します。

9. **[アクション]** が **[許可]** に設定されていることを確認します。セキュリティ規則ダイアログは次のスクリーンショットのようになります。

	![ネットワークのセキュリティ規則](./media/virtual-machines-sql-server-connection-steps/rm-network-security-rule.png)

9. **[OK]** をクリックして VM の規則を保存します。

<!----HONumber=AcomDC_0107_2016-->