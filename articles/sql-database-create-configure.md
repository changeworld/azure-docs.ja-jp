<properties urlDisplayName="How to create and configure an Azure SQL DB" pageTitle="Azure SQL Database を作成して構成する方法 - Azure チュートリアル" metaKeywords="Azure SQL Database の作成, Azure SQL Database の構成" description="How to create and configure an Azure SQL Database." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure an Azure SQL Database" authors="sidneyh" solutions="" manager="jhubbard" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

<h1><a id="configLogical"></a>Azure SQL Database を作成して構成する方法</h1>

このトピックでは、Azure の管理ポータルを使用して Azure SQL Database を作成して構成します。このワークフローでは、最初にサーバーを作成します。アップロードする既存の SQL Server データベースがある場合は、この方が便利なことがあります。

##目次##
* [方法: 論理サーバーの作成](#createLogical)
* [方法: 論理サーバーのファイアウォールの構成](#configFWLogical)

<h2><a id="createLogical"></a>方法: 論理サーバーの作成</h2>

1. [管理ポータル](http://manage.windowsazure.com)にサインインします。

2. ページの下部にある **[新規]** をクリックします。

	![Click SQL Databases][1]

3. **[データ サービス]**、**[SQL Database]**、**[簡易作成]** の順にクリックします。

	![Click New, Data Services, and Quick Create][2]
	 
5. **[SQL データベース サーバーの設定]** でサブスクリプションを選択します。

	![Select a subscription][3]

6. **[SQL データベース サーバーの設定]** では、空白を含まない 1 語でログイン名を入力します。 

SQL データベースでは、暗号化された接続に対して SQL 認証を使用します。sysadmin 固定のサーバー ロールに割り当てられた新しい SQL Server 認証のログインが、入力した名前を使用して作成されます。 

	ログインには、電子メール アドレス、Windows ユーザー アカウント、または Windows Live ID は使用できません。クレーム認証と Windows 認証のどちらも SQL データベースではサポートされません。
	
7. 大文字、小文字、および数字または記号を組み合わせた 9 文字以上のパスワードを指定します。

7. リージョンを選択します。リージョンによって、サーバーの Geo が決まります。リージョンは簡単に切り替えることができないので、このサーバーに最適なリージョンを選択してください。また、最も近い場所を選択してください。Azure アプリケーションとデータベースを同じリージョンに置くことで、送信帯域幅コストおよびデータ遅延を削減できます。

8. **[Azure サービスにサーバーへのアクセスを許可します]** を選択したままにしてください。そうすることで、管理ポータル、ストレージ サービス、Azure のその他のサービスを使用してこのデータベースに接続できます。 

9. 終了したら、ページの下部にあるチェックマークをクリックします。

###サーバー名の自動生成

サーバー名を指定しなかったことに注意してください。SQL データベースでは、DNS エントリが重複しないようにサーバー名は自動的に生成されます。サーバー名は、10 文字の英数字文字列です。SQL データベース サーバーの名前は変更できません。

次の手順では、ネットワークで実行中のアプリケーションからの接続によるアクセスが許可されるようにファイアウォールを構成します。

##方法: 論理サーバーのファイアウォールの構成

1. [管理ポータル](http://manage.windowsazure.com)で **[SQL Databases]**、**[サーバー]** の順にクリックします。

	![Click Servers][4]
2. リストで、作成したサーバー上をクリックします。

2. **[構成]** をクリックします。

	![Click Configure][5]

3. **[使用できる IP アドレス]** セクションで、**[使用できる IP アドレスに追加します]** をクリックします。これは使用するルーターまたはプロキシ サーバーが現在リッスンしている IP アドレスです。SQL データベースが現在の接続で使用されている IP アドレスを検出すると、このデバイスからの接続要求を受け入れるためのファイアウォール ルールを作成できます。 
	![Click Add to the allowed IP addresses][6]

ルールの既定の名前が生成されます。好みに応じて名前を編集します。 
	

4. 別のコンピューターからデータベースに接続する場合は、その IP アドレスに接続を許可する新しいルールを作成する必要があります。[開始] および [終了] ボックスを使用して、IP アドレス範囲を作成します。

動的に割り当てられた IP アドレスをクライアント コンピューターで使用する場合は、ネットワーク上でコンピューターに割り当てられた IP アドレスを含む範囲を指定できます。狭い範囲から始めて、必要になった場合にのみ範囲を広げます。

7. ページの下部にある **[保存]** をクリックして、手順を完了します。 

これで、論理サーバー、IP アドレスからの受信接続を許可するファイアウォール ルール、および管理者ログインが用意できました。 

**注: ** 作成した論理サーバーは仮想で、データ センターの物理サーバー上で動的にホストされます。サーバーの削除は、回復不可能な操作です。後でサーバーにアップロードするデータベースは必ずバックアップしておいてください。 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png

