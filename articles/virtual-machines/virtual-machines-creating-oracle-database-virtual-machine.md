<properties title="Creating an Oracle Database Virtual Machine in Azure" pageTitle="Azure での Oracle Database 仮想マシンの作成" description="例として Microsoft Azure で Oracle 仮想マシンを作成し、続いてその上に Oracle データベースを作成します。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Azure での Oracle Database 仮想マシンの作成
次の例は、Windows Server 2012 で実行しているマイクロソフト提供の Oracle Database のイメージに基づいて、Azure で仮想マシンを作成する方法を示しています。手順は 2 つです。仮想マシンを作成し、次に仮想マシン内に Oracle Database を作成します。この例で示されているのは Oracle Database バージョン 12c ですが、手順はバージョン 11g とほぼ同じです。

##Azure で Oracle Database 仮想マシンを作成するには

1.	[Azure ポータル](https://ms.portal.azure.com/)にログインします。

2.	**[Marketplace]**、**[Compute]** をクリックし、続いて検索ボックスに**「Oracle」**と入力します。

3.	使用できる Oracle Database のイメージを 1 つ選択します **(バージョン 11g、バージョン 12c、Standard Edition、Enterprise Edition、または基本的なオプションまたは高度なオプションのバンドルのいずれか)**。 選択したイメージに関する情報 (最小の推奨サイズなど) を確認し、**[次へ]** をクリックします。

4.	VM の**ホスト名**を指定します。

5.	VM の**ユーザー名**を指定します。なお、このユーザーは VM にリモートログインするためのもので、Oracle データベースのユーザー名ではありませんのでご注意ください。

6.	VM のパスワードを指定し確認するか、または SSH 公開キーを入力します。

7.	**価格レベル**を選択。既定では推奨される価格レベルが表示されます。すべての構成オプションを見るには、右上の **[すべて表示]** をクリックします。

8.	必要に応じて [[オプションの構成]](https://msdn.microsoft.com/library/azure/dn763935.aspx) を設定します。このとき、次に注意してください。

	1. VM 名で新しいストレージ アカウントを作成するには、**[ストレージ アカウント]** をそのままにします。

	2. **[可用性セット]** を「未構成」のままにします。

	3. この時点では**エンドポイント**を追加しないでください。

9.	リソース グループを選択または作成する

10. **[サブスクリプション]** を選択

11. **[場所]** を選択

12. **[作成]** をクリックします。すると仮想マシンを作成するプロセスが開始します。仮想マシンの状態が **[実行中]** になったら、次の手順に進みます。


##Azure で Oracle Database 仮想マシンを使ってデータベースを作成するには

1.	[Azure ポータル](https://ms.portal.azure.com/)にログインします。

2.	**[仮想マシン]** をクリックします。

3.	ログインする仮想マシンの名前をクリックします。

4.	**[接続]** をクリックします。

5.	表示される画面で必要に応じて入力して、仮想マシンに接続します。管理者名とパスワードの入力画面が表示されたら、仮想マシンの作成時に指定した値を使用します。

6.	**ORACLE_HOSTNAME** という環境変数を作成します。このとき、値を仮想マシンのコンピューター名に設定します。環境変数は次の手順で作成できます。

	1.	**Windows の [スタート]** をクリックし**「コントロール パネル」**と入力し、**コントロール パネル**アイコンをクリックし、続いて **[システムとセキュリティ]**、**[システム]**、**[システムの詳細設定]** の順にクリックします。

	2.	**[詳細設定]** タブをクリックし、続いて **[環境変数]**をクリックします。

	3.	**[システム環境変数]** セクションの **[新規]** をクリックして変数を作成します。

	4.	**[新しいシステム変数]** ダイアログ ボックスで、変数名に**「ORACLE_HOSTNAME」** 、変数値に仮想マシンのコンピューター名を入力します。コンピューター名を確認するには、コマンド プロンプトを開き **SET COMPUTERNAME** を実行します (このコマンドの出力にコンピューター名が含まれます)。
	
	5.	**[OK]** クリックすると、新しい環境変数が保存され **[新しいシステム変数]** ダイアログが閉じます。

	6.	コントロール パネルで開かれた他のダイアログ ボックスを閉じます。

7.	**Windows の [スタート]** をクリックし、続いて**「Database Configuration Assistant」**と入力します。**Database Configuration Assistant** アイコンをクリックします。

8.	**Database Configuration Assistant** ウィザード内で、ダイアログ ボックスの各手順で必要な値を指定します。

	1.	**手順 1:** **[データベースを作成する]**、**[次へ]** の順にクリックします。

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

	2. **手順 2:** **[グローバル データベース名]** の値を入力します。**[管理パスワード]** の値を入力し確認します。このパスワードは、Oracle データベースの**システム** ユーザー用です。**[コンテナー データベースとして作成]** チェック ボックスをオフにします。**[次へ]** をクリックします。

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

	3. **手順 3:** 前提条件の確認が自動的に始まり、**手順 4** に進みます。
	
	4. **手順 4:** **[データベースの作成 – 概要]** オプションを確認し、続いて **[完了]** をクリックします。

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)
	5. **手順 5:** **進行状況ページ**にデータベース作成の状態が表示されます。

		![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)
	6. データベースの作成後、オプションで **[パスワード管理]** ダイアログを使えます。必要に応じてパスワード設定を変更し、続いてダイアログ ボックスが閉じて **[Database Configuration Assistant]** ウィザードを終了します。

##データベースがインストールされていることを確認するには

1.	仮想マシンにログインしたまま、SQL Plus コマンド プロンプトを起動します。**Windows の [スタート]** をクリックし、続いて**「SQL Plus」**と入力します。**SQL Plus** アイコンをクリックします。

2.	メッセージが表示されたら、**システム**のユーザー名と Oracle データベースを作成したときに指定したパスワードを使ってログインします。

3.	SQL Plus コマンド プロンプトで、次のコマンドを実行します。

		select * from GLOBAL_NAME;

	結果に、作成したデータベースのグローバル名が表示されます。

	![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

##データベースのリモート アクセスを許可する
データベースのリモート アクセスを許可するには (たとえば、Java コードを実行しているクライアント コンピューターから)、データベースのリスナーを開始し、仮想マシンのファイアウォールでポート 1521 を開き、ポート 1521 のパブリック エンドポイントを作成する必要があります。

### データベースのリスナーを開始する
1.	仮想マシンにログインします。

2.	コマンド プロンプトを開きます。

3.	コマンド プロンプトで、次のコマンドを実行します。

		lsnrctl start

(`lsnrctl status` を実行するとリスナーの状態を確認できます。リスナーを停止する場合は `lsnrctl stop` を実行します。)

### 仮想マシンのファイアウォールでポート 1521 を開く

1.	仮想マシンにログインした状態でいます。**Windows の [スタート]** をクリックし、**「セキュリティが強化された Windows ファイアウォール」**と入力し、続いて**セキュリティが強化された Windows ファイアウォール** アイコンをクリックします。**[セキュリティが強化された Windows ファイアウォール]** 管理コンソールが開きます。

2.	ファイアウォール管理コンソール内で、左側のウィンドウの **[受信の規則]** をクリックし (**[受信の規則]**が表示されない場合は、左側のウィンドウの上のノードを展開します)、続いて右側のウィンドウの **[新しい規則]** をクリックします。

3.	**[規則の種類]** では **[ポート]** を選択し、**[次へ]** をクリックします。

4.	**[プロトコルとポート]** は、**[TCP]** を選択し、**[特定のローカル ポート]** を選択し、ポートに**「1521」**と入力し、続いて [次へ] をクリックします。

5.	**[接続を許可する]** を選択し、**[次へ]** をクリックします。

6.	規則を適用するプロファイルの既定値をそのまま使用し、**[次へ]** をクリックします。

7.	規則の名前とオプションで説明を指定し、続いて **[完了]** をクリックします。

### ポート 1521 のパブリック エンドポイントを作成する

1.	[Azure ポータル](https://ms.portal.azure.com/)にログインします。

2.	**[参照]** をクリックします

3.    **[仮想マシン]** をクリックします

4.	仮想マシンを選択します

5.	**[設定]** をクリックします

6.	**[エンドポイント]** をクリックします。

7.	**[追加]** をクリックします。

8.	エンドポイントの名前を指定します

	1. プロトコルに **TCP** を使用します
	
	2. パブリック ポートに **1521** を使用します
	
	3. プライベート ポートに **1521** を使用します。

9.	残りのオプションはそのままにします

10. **[OK]** をクリックします。

##Oracle Database Enterprise Manager のリモート アクセスを有効にする
Oracle Database Enterprise Manager のリモート アクセスを有効にする場合は、ファイアウォールでポート 5500 を開き、Azure ポータルで 5500 に対して仮想マシンのエンドポイントを作成します (ポート 1521 を開き、1521 に対してエンドポイントを作成する上記手順を使用)。続いて、Oracle Enterprise Manager をリモート コンピューターから実行するには、ブラウザーを開いて `http://<<unique_domain_name>>:5500/em` の形式の URL にアクセスします。(*<<unique_domain_name>>* の値を確認するには、[Azure ポータル](https://ms.portal.azure.com/)内で **[仮想マシン]** をクリックし、続いて Oracle Database を実行するために使用している仮想マシンを選択します)。

##基本的なオプションと高度なオプションのバンドルを構成する
**Oracle Database と基本的なオプション** または**Oracle Database と高度なオプションのバンドル** を選択した場合、次の手順で Oracle のインストールにアドオン機能を構成します。Windows でこれらを設定する手順については、Oracle のマニュアルを参照してください。個々のコンポーネントのニーズ次第で構成は大きく変わります。

**Oracle Database と基本的なオプションのバンドル**には、Oracle Database Enterprise Edition とライセンスを含むインスタンス ([Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html)、[Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html)、[Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm)、[Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ)、[Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html)) が含まれます。

**Oracle Database と高度なオプションのバンドル**には、基本的なオプションのバンドルのすべてのオプションのライセンスを含むインスタンスに加えて、[Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html)、[Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html)、[Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html)、[Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html)、[Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html)、[OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF)、[Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF)、[In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html)、[Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB)、Oracle Test Data Management Pack (Data Masking Pack の一部として) が含まれます。

##その他のリソース
これで仮想マシンの設定とデータベースの作成が終わりました。追加情報については、次のトピックを参照してください。

-	[Oracle 仮想マシン イメージ - 他の考慮事項](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Oracle Database 12c ドキュメント ライブラリ](http://www.oracle.com/pls/db1211/homepage)

-	[Java アプリケーションから Oracle Database に接続する](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Azure の Oracle 仮想マシン イメージ](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-	[Oracle Database 2 日間 DBA 12c リリース 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=July15_HO2-->