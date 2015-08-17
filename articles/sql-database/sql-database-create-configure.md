<properties 
	pageTitle="Azure SQL Database を作成して構成する方法 - Azure チュートリアル" 
	description="Azure SQL Database を作成して構成する方法。" 
	services="sql-database" 
	documentationCenter="" 
	authors="sidneyh" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="sidneyh"/>

# Azure SQL Database を作成して構成する方法

このトピックでは、Microsoft Azure 管理ポータルの **[簡易作成]** を使用して、新しい Azure SQL Database を作成し、構成します。このプロセスでは、既存サーバーを使用して SQL データベースを作成する方法、また、必要に応じて新しいサーバーを作成する方法を示します。

> [AZURE.NOTE]**[簡易作成]** を使用して SQL Database を作成すると、Standard (S0) データベースがプロビジョニングされます。サービス層で Standard (S0) 以外のパフォーマンス レベルの SQL Database を作成するには、**[カスタム作成]** を使用します。**[カスタム作成]** を使用して Azure SQL Database を作成する方法の詳細については、[Microsoft Azure SQL Database の概要](sql-database-get-started.md)に関するページをご覧ください。

## 方法: Azure SQL Database を作成する

1. [管理ポータル](https://portal.azure.com/)にサインインします。

2. ページの下部にある **[新規]** をクリックします。

	![[SQL Databases] をクリックする][1]

3. **[DATA SERVICES]**、**[SQL DATABASE]**、**[簡易作成]** の順にクリックします。

	![[新規]、[Data Services]、[簡易作成] の順にクリックする][2]
	 
5. **[簡易作成]** で、新しいデータベースの名前を入力し、サブスクリプションを選択します。次に、**[サーバー]** 一覧からサーバーを選択します (新しいサーバーを作成する場合は、この手順を省略して次の手順に進みます)。

	![既存のサーバーで新しい SQL Database を作成する][7]

	必要に応じて、**[新しい SQL データベース サーバー]** を選択して、新しいサーバーを作成します。![新しい SQL Database と新しいサーバーを作成する][8]

	1. リージョンを選択します。リージョンによって、サーバーの Geo が決まります。リージョンは簡単に切り替えることができないので、このサーバーに最適なリージョンを選択してください。また、最も近い場所を選択してください。Azure アプリケーションとデータベースを同じリージョンに置くことで、送信帯域幅コストおよびデータ遅延を削減できます。
	2. 空白を含まない 1 語でログイン名を入力します。 

		SQL データベースでは、暗号化された接続を経由して SQL 認証を使用します。sysadmin 固定のサーバー ロールに割り当てられた新しい SQL Server 認証のログインが、入力した名前を使用して作成されます。

		ログインには、電子メール アドレス、Windows ユーザー アカウント、または Windows Live ID は使用できません。クレーム認証と Windows 認証のどちらも SQL データベースではサポートされません。 
	3. 大文字、小文字、および数字または記号を組み合わせた 9 文字以上のパスワードを指定します。

	


9. 終了したら、ページの下部にある **[SQL データベースの作成]** チェック マークをクリックします。

### サーバー名の自動生成

新しいサーバーを作成した場合は、サーバー名を指定しないことに注意してください。SQL データベースでは、DNS エントリが重複しないようにサーバー名は自動的に生成されます。サーバー名は、10 文字の英数字文字列です。SQL データベース サーバーの名前は変更できません。

次の手順では、ネットワークで実行中のアプリケーションからの接続によるアクセスが許可されるようにファイアウォールを構成します。

<a id="configFWLogical"></a>

## 方法: 論理サーバーのファイアウォールを構成する

1. [管理ポータル](http://manage.windowsazure.com)で **[SQL Databases]** をクリックし、**[サーバー]** をクリックします。

	![[サーバー] をクリックする][4]
2. リストで、作成したサーバー上をクリックします。

2. **[構成]** をクリックします。

	![[構成] をクリックする][5]

3. **[使用できる IP アドレス]** セクションで、**[使用できる IP アドレスに追加します]** をクリックします。これは使用するルーターまたはプロキシ サーバーが現在リッスンしている IP アドレスです。SQL Database が現在の接続で使用されている IP アドレスを検出し、このデバイスからの接続要求を受け入れるためのファイアウォール ルールを作成します。![[使用できる IP アドレスに追加します] をクリックする][6]

	ルールの既定の名前が生成されます。好みに応じて名前を編集します。
	

4. 別のコンピューターからデータベースに接続する場合は、その IP アドレスに接続を許可する新しいルールを作成する必要があります。[開始] と [終了] ボックスを使用して、IP アドレス範囲を作成します。

	動的に割り当てられた IP アドレスをクライアント コンピューターで使用する場合は、ネットワーク上でコンピューターに割り当てられた IP アドレスを含む範囲を指定できます。狭い範囲から始めて、必要になった場合にのみ範囲を広げます。

7. ページの下部にある **[保存]** をクリックして、手順を完了します。

これで、SQL Database、論理サーバー、IP アドレスからの受信接続を許可するファイアウォール ルール、管理者ログインが用意できました。

**注:** 作成した論理サーバーは仮想であり、データ センターの物理サーバー上で動的にホストされます。サーバーの削除は、回復不可能な操作です。後でサーバーにアップロードするデータベースは必ずバックアップしておいてください。


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png
[7]: ./media/sql-database-create-configure/quick-create-existing-server.png
[8]: ./media/sql-database-create-configure/quick-create-new-server.png



 

<!---HONumber=August15_HO6-->