
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2015-09-04 , GeneMi.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. http://portal.azure.com/ で [Azure ポータル](https://portal.azure.com/)にログインします。

2. 左側のバナーの **[参照]** をクリックします。**[参照]** ブレードが表示されます。

3. スクロールを行い、**[SQL サーバー]** をクリックします。**[SQL サーバー]** ブレードが表示されます。

	![ポータルで Azure SQL Database サーバーを見つける][b21-FindServerInPortal]

4. サーバーの行をクリックします。サーバー用のブレードが表示されます。

5. サーバー用のブレードで **[設定]** をクリックします。**[設定]** ブレードが表示されます。

6. **[ファイアウォール]** をクリックします。**[ファイアウォール設定]** ブレードが表示されます。

	![[設定]、[ファイアウォール] の順にクリックします。][b31-SettingsFirewallNavig]

7. **[クライアント IP の追加]** をクリックして、クライアント コンピューターの IP アドレスを追加します。ポータルでルールの名前が構築されます。また、名前を入力し、使用する IP アドレス範囲の下限値と上限値を指定することができます。低いほうの値を **.0**、高いほうの値を **.255** にしておくと便利です。

	![許可する IP アドレスの範囲を追加する][b41-AddRange]

8. **[保存]** をクリックします。



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=AcomDC_0316_2016-->