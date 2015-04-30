<properties 
	pageTitle="Azurre ストレージを使った Azure App Service での PHP Web アプリの作成" 
	description="このチュートリアルでは、Azure App Service で PHP Web アプリを作成し、バックエンドで Azure テーブル ストレージ サービスを使用する方法について説明します。" 
	services="app-service\web, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="tomfitz"/>

# Azurre ストレージを使った Azure App Service での PHP Web アプリの作成

このチュートリアルでは、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) で PHP Web アプリを作成し、バックエンドで Azure テーブル ストレージ サービスを使用する方法について説明します。このチュートリアルは、コンピューターに [PHP][install-php] および Web サーバーがインストールされていることを前提としています。このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで使用できます。このチュートリアルを完了すると、Azure で動作し、テーブル ストレージ サービスにアクセスする PHP Web アプリが完成します。
 
学習内容:

* Azure クライアント ライブラリをインストールしてアプリケーションに含める方法
* クライアント ライブラリを使用してテーブルを作成し、テーブル エンティティを作成、照会、削除する方法
* Azure Storage アカウントを作成し、これを使用できるようにアプリケーションをセットアップする方法
*  Azure の Web アプリを作成して Git で展開する方法
 
このチュートリアルでは、タスク一覧用の単純な Web アプリケーション (Tasklist) を PHP で作成します。完成したアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web アプリ][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Azure アカウントのサインアップより前に Azure App Service の使用を開始したい場合は、「[アプリ サービスを試す](http://go.microsoft.com/fwlink/?LinkId=523751)」をご覧ください。そこでは、アプリ サービスで有効期間の短いスターター Web アプリをすぐに作成することができます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##Azure クライアント ライブラリのインストール

Azure 向け PHP クライアント ライブラリを Composer 経由でインストールするには、次の手順に従います。

1. [Git のインストール][install-git]

	> [AZURE.NOTE]
	> Windows では、Git 実行可能ファイルを PATH 環境変数に追加する必要があります。

2. プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. **[composer.phar][composer-phar]** をプロジェクトのルートにダウンロードします。

4. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。

		php composer.phar install

##クライアント ライブラリの使用開始

ライブラリを使用して Azure API を呼び出すには、4 つの基本手順を事前に実行しておく必要があります。ここでは、これらの手順を実行する初期化スクリプトを作成します。

* **init.php** という名前のファイルを作成します。

* まず、オートローダー スクリプトを含めます。

		require_once 'vendor\autoload.php'; 
	
* 使用する名前空間を含めます。

	いずれの Azure サービス クライアントを作成するにも、**ServicesBuilder** クラスを使用する必要があります。

		use WindowsAzure\Common\ServicesBuilder;

	API 呼び出しによって発生した例外をキャッチするには、**ServiceException** クラスが必要です。

		use WindowsAzure\Common\ServiceException;
	
* サービス クライアントをインスタンス化するには、有効な接続文字列も必要です。テーブル サービスの接続文字列の形式は次のとおりです。

	ライブ サービスにアクセスする場合:
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	エミュレーター ストレージにアクセスする場合:
	
		UseDevelopmentStorage=true

*  `ServicesBuilder::createTableService` ファクトリ メソッドを使用して、テーブル サービス呼び出しのラッパーをインスタンス化します。

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` には、Azure テーブルに対して使用可能なすべての REST 呼び出しに使用するメソッドが格納されます。


## テーブルの作成

データを保存するには、そのためのコンテナー (テーブル) を先に作成しておく必要があります。 

* **createtable.php** という名前のファイルを作成します。

* 先ほど作成した初期化スクリプトを含めます。このスクリプトは、Azure にアクセスするすべてのファイルに含めます。

		<?php
		require_once "init.php";

* 次に、テーブルの名前を渡して  *createTable* を呼び出します。他の NoSQL テーブル ストアと同様、Azure テーブルにスキーマは必要ありません。
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	エラー コードとメッセージについてはこちらをご覧ください:[https://msdn.microsoft.com/library/azure/dd179438.aspx/][msdn-errors]


##テーブルの照会

Tasklist アプリケーションのホーム ページでは、既存のタスクを一覧表示し、新しいタスクを挿入できるようにする必要があります。

* **index.php** という名前のファイルを作成し、ページ ヘッダーの形成用に次の HTML および PHP コードを挿入します。
	
		<html>
		<head>
			<title>Index</title>
			<style type="text/css">
			    body { background-color: #fff; border-top: solid 10px #000;
			        color: #333; font-size: .85em; margin: 20; padding: 20;
			        font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			    }
			    h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			    h1 { font-size: 2em; }
			    h2 { font-size: 1.75em; }
			    h3 { font-size: 1.2em; }
			    table { margin-top: 0.75em; }
			    th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			    td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
			</style>
		</head>
		<body>
		<h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
		<?php		
		require_once "init.php";

* Azure テーブルに対して、 *tasks* テーブルに格納されている**すべてのエンティティ**を照会するには、テーブル名のみを渡して  *queryEntities* メソッドを呼び出します。後の **[エンティティの更新]** セクションでは、特定のエンティティを照会するためのフィルターを渡す方法についても確認します。

		try {
		    $result = $tableRestProxy->queryEntities('tasks');
		}
		catch(ServiceException $e){
		    $code = $e->getCode();
		    $error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		
* 結果セット内のエンティティを反復処理するには:

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

*  `Entity` を取得した後、データを読み取るには  `Entity->getPropertyValue('[name]')` の形式を使用します。

			if ($i == 0) {
				echo "<table border='1'>
				<tr>
					<td>Name</td>
					<td>Category</td>
					<td>Date</td>
					<td>Mark Complete?</td>
					<td>Delete?</td>
				</tr>";
			}
			echo "
				<tr>
					<td>".$entities[$i]->getPropertyValue('name')."</td>
					<td>".$entities[$i]->getPropertyValue('category')."</td>
					<td>".$entities[$i]->getPropertyValue('date')."</td>";
					if ($entities[$i]->getPropertyValue('complete') == false)
						echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
					else
						echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
					echo "
					<td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
				</tr>";
		}
	
		if ($i > 0)
			echo "</table>";
		else
			echo "<h3>No items on list.</h3>";
		?>

* Last, you must insert the form that feeds data into the task insertion script and complete the HTML:

			<hr/>
			<form action="additem.php" method="post">
				<table border="1">
					<tr>
						<td>Item Name: </td>
						<td><input name="itemname" type="textbox"/></td>
					</tr>
					<tr>
						<td>Category: </td>
						<td><input name="category" type="textbox"/></td>
					</tr>
					<tr>
						<td>Date: </td>
						<td><input name="date" type="textbox"/></td>
					</tr>
				</table>
				<input type="submit" value="Add item"/>
			</form>
		</body>
		</html>

## テーブルへのエンティティの挿入

これで、アプリケーションはテーブルに格納されているすべてのアイテムを読み取ることができるようになりました。最初はアイテムが何も格納されていないので、データをデータベースに書き込む関数を追加しましょう。

* **additem.php** という名前のファイルを作成します。

* このファイルに次のコードを追加します。

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* エンティティを挿入する最初の手順は、 `Entity` オブジェクトをインスタンス化してプロパティを設定することです。
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* 次に、作成した `$entity` を  `insertEntity` メソッドに渡します。

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* 最後に、エンティティの挿入後にホーム ページに戻るように指定します。

		header('Location: index.php');		
		?>
	
## エンティティの更新

タスク一覧アプリケーションでは、アイテムに完了マークを付けることも外すこともできます。ホーム ページでは、エンティティの  *RowKey* および  *PartitionKey* と、対象の状態 (marked==1, unmarked==0) が渡されます。

* **markitem.php** という名前のファイルを作成し、初期化の部分を追加します。

		<?php		
		require_once "init.php";
		

* エンティティを更新するための最初の手順は、そのエンティティをテーブルから取得することです。
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	`Key eq 'Value'` という形式のクエリ フィルターが渡されています。クエリ構文の詳しい説明については、[こちら][msdn-table-query-syntax] をご覧ください。

* 次に、必要に応じてプロパティを変更できます。

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

*  `updateEntity` メソッドにより、更新が実行されます。

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* エンティティの挿入後にホーム ページに戻るように指定するには:

		header('Location: index.php');		
		?>


## エンティティの削除

アイテムの削除は、 `deleteItem` を 1 回呼び出すことで実行できます。渡す値は **PartitionKey** と **RowKey** であり、これらの組み合わせがエンティティのプライマリ キーになります。**deleteitem.php** という名前のファイルを作成し、次のコードを挿入します。

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Azure のストレージ アカウントの作成

アプリケーションでデータをクラウドに保存するには、まず Azure にストレージ アカウントを作成し、適切な認証情報を  *Configuration* クラスに渡す必要があります。

1. [Azure ポータル][management-portal]にログインします。

2. ポータルの左下にある **[新規作成]** をクリックし、その後 **[データ + ストレージ]** > **[ストレージ]** をクリックします。ストレージ アカウントに一意の名前を付け、新しい [リソースグループ] を作成します(azure-preview-portal-using-resource-groups.md) 。

	![新しいストレージ アカウントの作成][storage-quick-create]
	
	ストレージ アカウントが作成されると、**[通知]** ボタンが緑色の **[成功]** と点滅し、ストレージ アカウントのブレードが開き、作成した新しいリソース グループに所属することが示されます。

5. ストレージ アカウントのブレードの **[設定]** 部をクリックします。アカウント名とプライマリ キーをメモします。

	![キーの管理を選択][storage-access-keys]

7. **init.php** を開き、`[YOUR_STORAGE_ACCOUNT_NAME]` と `[YOUR_STORAGE_ACCOUNT_KEY]`  を、先ほどの手順でメモしたアカウント名とキーに置き換えます。ファイルを保存します。

## Azure Web アプリの作成と Git 発行の設定

次の手順に従って、Azure の Web アプリを作成します。

1. [Azure ポータル][management-portal]にログインします。

2. 「[方法: Azure ポータルを使用して web アプリを作成する](web-sites-create-deploy.md#createawebsiteportal)」の指示に従い、空の Web アプリを作成します。必ず新しい [App Service プラン] を作成し、(azure-web-sites-web-hosting-plans-in-depth-overview) ストレージ アカウントで先に作成したリソース グループを選択してください。

	Web アプリが作成されると、**[通知]** ボタンが緑色の **[成功]** と点滅し、Web アプリのブレードが開き、作成した新しいリソース グループに所属することが示されます。

6. Web アプリのブレードで、**[継続的配置の設定]** をクリックし、**[ローカル Git リポジトリ]** を選択します。**[OK]** をクリックします。

	![Git 発行の設定][setup-git-publishing]

7. ローカル Git リポジトリを Azure に展開するには、展開の資格情報も設定する必要があります。Web アプリのブレードで、**[すべての設定]** > **[展開の資格情報]** をクリックして資格情報を構成します。終了したら **[保存]** をクリックします。

	![発行資格情報の作成][credentials]

	リポジトリの設定にかかる時間はわずかです。

8. Git リポジトリの準備ができたら、変更をプッシュします。Web アプリのブレードにある同じ展開部をクリックするとリポジトリ URL を確認できます。 

	![Web アプリのリポジトリの作成後に返される Git 展開の手順][git-instructions]

	表示された指示を書き留めてください。次のセクションで、アプリケーションを発行するときに使用します。

##アプリケーションの発行

Git でアプリケーションを発行するには、次の手順に従います。

1. アプリケ－ションのルートにある **vendor/microsoft/windowsazure** フォルダーを開き、次のファイルとフォルダーを削除します。
	* .git
	* .gitattributes
	* .gitignore
			
	Composer パッケージ マネージャーが Azure クライアント ライブラリとその依存関係をダウンロードする際には、これらが格納されている GitHub リポジトリが複製されます。次の手順では、アプリケーションのルート フォルダーからリポジトリを作成することにより、Git 経由でアプリケーションを展開します。クライアント ライブラリがあるサブリポジトリは、リポジトリ固有のファイルが削除されていない限り無視されます。

2. GitBash (Git が  `PATH` にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	先ほど作成したパスワードを入力するように求められます。

3. アプリケーション用のテーブルを作成するには、**http://[Web アプリ ドメイン]/createtable.php** に移動します。
4. アプリケーションの使用を開始するには、**http://[Web アプリ ドメイン]/index.php** に移動します。

アプリケーションを発行した後、アプリケーションへの変更を開始し、Git を使用してその変更を発行することもできます。 

##アプリケーションへの変更の発行

アプリケーションへの変更を発行するには、次のステップに従います。

1. ローカルでアプリケーションへの変更を行います。
2. GitBash (Git が  `PATH` にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。

		git add .
		git commit -m "comment describing changes"
		git push azure master

	先ほど作成したパスワードを入力するように求められます。

3. 変更内容を確認できるように、**http://[Web アプリ ドメイン]/index.php** に移動します。 

## 変更内容
* Web サイトからアプリ サービスへの変更ガイドについては、次のものをご覧ください。[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)
* 古いポータルから新しいポータルへの変更ガイドについては、次のものをご覧ください。[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-php]: http://www.php.net/manual/en/install.php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx

[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://portal.azure.com

[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png

[git-instructions]: ./media/web-sites-php-storage/git-instructions.png

<!--HONumber=52-->