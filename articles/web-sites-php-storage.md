<properties linkid="develop-php-website-with-storage" urlDisplayName="Web w/ Storage" pageTitle="PHP website with table storage - Azure tutorial" metaKeywords="Azure table storage PHP, Azure PHP website, Azure PHP web site, Azure PHP tutorial, Azure PHP example" description="This tutorial shows you how to create a PHP website and use the Azure Tables storage service in the back-end." metaCanonical="" services="web-sites,storage" documentationCenter="PHP" title="Create a PHP Website using Azure Storage" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Azure Storage を使用した PHP Web サイトを作成する

このチュートリアルでは、PHP Web サイトを作成し、バックエンドで Azure テーブル ストレージ サービスを使用する方法を説明します。このチュートリアルは、コンピューターに [PHP][PHP] および Web サーバーがインストールされていることを前提としています。このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで使用できます。このチュートリアルを完了すると、Azure で動作し、テーブル ストレージ サービスにアクセスする PHP Web サイトが完成します。

学習内容:

-   Azure クライアント ライブラリをインストールしてアプリケーションに含める方法
-   クライアント ライブラリを使用してテーブルを作成し、テーブル エンティティを作成、照会、削除する方法
-   Azure Storage アカウントを作成し、これを使用できるようにアプリケーションをセットアップする方法
-   Azure Website を作成して Git を使用してデプロイする方法

このチュートリアルでは、タスク一覧用の単純な Web アプリケーション (Tasklist) を PHP で作成します。完成したアプリケーションのスクリーンショットは次のようになります。

![Azure の PHP Web サイト][Azure の PHP Web サイト]

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## Azure クライアント ライブラリのインストール

Azure 向け PHP クライアント ライブラリを Composer 経由でインストールするには、次の手順に従います。

1.  [Git のインストール][Git のインストール]

    > [WACOM.NOTE]
    > Windows では、Git 実行可能ファイルを PATH 環境変数に追加する必要があります。

2.  プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。

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

3.  **[composer.phar][composer.phar]** をプロジェクトのルートにダウンロードします。

4.  コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。

        php composer.phar install

## クライアント ライブラリの使用開始

ライブラリを使用して Azure API を呼び出すには、4 つの基本手順を事前に実行しておく必要があります。ここでは、これらの手順を実行する初期化スクリプトを作成します。

-   **init.php** という名前のファイルを作成します。

-   まず、オートローダー スクリプトを含めます。

        require_once 'vendor\autoload.php'; 

-   使用する名前空間を含めます。

    いずれの Azure サービス クライアントを作成するにも、**ServicesBuilder** クラスを使用する必要があります。

        use WindowsAzure\Common\ServicesBuilder;

    API 呼び出しによって発生した例外をキャッチするには、**ServiceException** クラスが必要です。

        use WindowsAzure\Common\ServiceException;

-   サービス クライアントをインスタンス化するには、有効な接続文字列も必要です。テーブル サービスの接続文字列の形式は次のとおりです。

    ライブ サービスにアクセスする場合:

        DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

    エミュレーター ストレージにアクセスする場合:

        UseDevelopmentStorage=true

-   `ServicesBuilder::createTableService` ファクトリ メソッドを使用して、テーブル サービス呼び出しのラッパーをインスタンス化します。

        $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    `$tableRestProxy` には、Azure テーブルに対して使用可能なすべての REST 呼び出しに使用するメソッドが格納されます。

## テーブルの作成

データを保存するには、そのためのコンテナー (テーブル) を先に作成しておく必要があります。

-   **createtable.php** という名前のファイルを作成します。

-   先ほど作成した初期化スクリプトを含めます。このスクリプトは、Azure にアクセスするすべてのファイルに含めます。

        <?php
        require_once "init.php";

-   次に、テーブルの名前を渡して *createTable* を呼び出します。他の NoSQL テーブル ストアと同様、Azure テーブルにスキーマは必要ありません。

        try {
            $tableRestProxy->createTable('tasks');
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }
        ?>

    エラー コードとメッセージについてはこちらをご覧ください: [][]<http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx></a>

## テーブルの照会

Tasklist アプリケーションのホーム ページでは、既存のタスクを一覧表示し、新しいタスクを挿入できるようにする必要があります。

-   **index.php** という名前のファイルを作成し、ページ ヘッダーの形成用に次の HTML および PHP コードを挿入します。

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

-   Azure テーブルに対して、*tasks* テーブルに格納されている**すべてのエンティティ**を照会するには、テーブル名のみを渡して *queryEntities* メソッドを呼び出します。後の「**エンティティの更新**」セクションでは、特定のエンティティを照会するためのフィルターを渡す方法についても確認します。

        try {
            $result = $tableRestProxy->queryEntities('tasks');
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   結果セット内のエンティティを反復処理するには:

        $entities = $result->getEntities();

        for ($i = 0; $i < count($entities); $i++) {

-   `Entity` を取得した後、データを読み取るには `Entity->getPropertyValue('[name]')` の形式を使用します。

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

-   最後に、データをタスク挿入スクリプトに取り込むためのフォームを挿入して、HTML を完成する必要があります。

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

-   **additem.php** という名前のファイルを作成します。

-   このファイルに次のコードを追加します。

        <?php       
        require_once "init.php";        
        use WindowsAzure\Table\Models\Entity;
        use WindowsAzure\Table\Models\EdmType;      

-   エンティティを挿入する最初の手順は、`Entity` オブジェクトをインスタンス化してプロパティを設定することです。

        $entity = new Entity();
        $entity->setPartitionKey('p1');
        $entity->setRowKey((string) microtime(true));
        $entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
        $entity->addProperty('category', EdmType::STRING, $_POST['category']);
        $entity->addProperty('date', EdmType::STRING, $_POST['date']);
        $entity->addProperty('complete', EdmType::BOOLEAN, false);

-   次に、作成した `$entity` を `insertEntity` メソッドに渡します。

        try{
            $tableRestProxy->insertEntity('tasks', $entity);
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   最後に、エンティティの挿入後にホーム ページに戻るように指定します。

        header('Location: index.php');      
        ?>

## エンティティの更新

タスク一覧アプリケーションでは、アイテムに完了マークを付けることも外すこともできます。ホーム ページでは、エンティティの *RowKey* および *PartitionKey* と、対象の状態 (marked==1、unmarked==0) が渡されます。

-   **markitem.php** という名前のファイルを作成し、初期化の部分を追加します。

        <?php       
        require_once "init.php";

-   エンティティを更新するための最初の手順は、そのエンティティをテーブルから取得することです。

        $result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');     
        $entities = $result->getEntities();     
        $entity = $entities[0];

    `Key eq 'Value'` という形式のクエリ フィルターが渡されています。クエリ構文の詳しい説明については、[こちら][こちら]を参照してください。

-   次に、必要に応じてプロパティを変更できます。

        $entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

-   `updateEntity` メソッドにより、更新が実行されます。

        try{
            $result = $tableRestProxy->updateEntity('tasks', $entity);
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   エンティティの挿入後にホーム ページに戻るように指定するには:

        header('Location: index.php');      
        ?>

## エンティティの削除

アイテムの削除は、`deleteItem` を 1 回呼び出すことで実行できます。渡す値は **PartitionKey** と **RowKey** であり、これらの組み合わせがエンティティのプライマリ キーになります。**deleteitem.php** という名前のファイルを作成し、次のコードを挿入します。

        <?php
        
        require_once "init.php";        
        $tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);       
        header('Location: index.php');
        
        ?>

## Azure のストレージ アカウントの作成

アプリケーションでデータをクラウドに保存するには、まず Azure にストレージ アカウントを作成し、適切な認証情報を *Configuration* クラスに渡す必要があります。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。

2.  ポータルの左下にある **[+ 新規]** アイコンをクリックします。

    ![新しい Azure の Web サイトの作成][新しい Azure の Web サイトの作成]

3.  **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

    ![新しい Web サイトのカスタム作成][新しい Web サイトのカスタム作成]

    **[URL]** ボックスに値を入力し、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログ ボックスの下部にある **[ストレージ アカウントの作成]** ボタンをクリックします。

    ![Fill in web site details][Fill in web site details]

    ストレージ アカウントが作成されると、"**ストレージ アカウント "[NAME]" の作成が正常に完了しました**" というテキストが表示されます。

4.  **[ストレージ]** タブが選択されていることを確認してから、作成したストレージ アカウントを一覧から選択します。

5.  下部にあるアプリケーション バーの **[キーの管理]** をクリックします。

    ![キーの管理 を選択する][キーの管理 を選択する]

6.  作成したストレージ アカウントの名前とプライマリ キーの値をメモしておきます。

    ![キーの管理 を選択する][1]

7.  **init.php** を開き、`[YOUR_STORAGE_ACCOUNT_NAME]` と `[YOUR_STORAGE_ACCOUNT_KEY]` を、先ほどの手順でメモしたアカウント名とキーに置き換えます。ファイルを保存します。

## Azure Website の作成と Git 発行の設定

Azure Website を作成するには、次のステップに従います。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。
2.  ポータルの左下にある **[+ 新規]** アイコンをクリックします。

    ![新しい Azure の Web サイトの作成][新しい Azure の Web サイトの作成]

3.  **[コンピューティング]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。

    ![新しい Web サイトのカスタム作成][2]

    **[URL]** ボックスに値を入力し、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログ ボックスの下部にある **[新しい Web サイトの作成]** ボタンをクリックします。

    ![Fill in web site details][3]

    Web サイトが作成されると、"**Web サイト '[SITENAME]' の作成が正常に完了しました**" というテキストが表示されます。これで、Git 発行を有効にする準備ができました。

4.  Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトの**クイックスタート** ダッシュボードを開きます。

    ![Open web site dashboard][Open web site dashboard]

5.  クイックスタート ページの右下隅で、**[ソース管理からの展開の設定]** を選択します。

    ![Git 発行の設定][Git 発行の設定]

6.  ソース コードの位置をたずねるメッセージが表示されたら、**[ローカル Git リポジトリ]** を選択し、矢印をクリックします。

    ![ソース コードの位置][ソース コードの位置]

7.  Git 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。作成するユーザー名とパスワードはメモしておいてください (Git リポジトリを設定したことがある場合は、この手順をスキップできます)。

    ![発行資格情報の作成][発行資格情報の作成]

    リポジトリの設定にかかる時間はわずかです。

8.  Git リポジトリの準備ができると、ローカル リポジトリを設定し、ファイルを Azure にプッシュするために使用する Git コマンドに関する手順が表示されます。

    ![Web サイトのリポジトリの作成後に返される Git 展開の手順][Web サイトのリポジトリの作成後に返される Git 展開の手順]

    表示された指示を書き留めてください。次のセクションで、アプリケーションを発行するときに使用します。

## アプリケーションの発行

Git でアプリケーションを発行するには、次の手順に従います。

1.  アプリケーションのルートにある **vendor/microsoft/windowsazure** フォルダーを開き、次のファイルとフォルダーを削除します。

    -   .git
    -   .gitattributes
    -   .gitignore

    Composer パッケージ マネージャーが Azure クライアント ライブラリとその依存関係をダウンロードする際には、これらが格納されている GitHub リポジトリが複製されます。次の手順では、アプリケーションのルート フォルダーからリポジトリを作成することにより、Git 経由でアプリケーションを展開します。クライアント ライブラリがあるサブリポジトリは、リポジトリ固有のファイルが削除されていない限り無視されます。

2.  GitBash (Git が `PATH` にある場合はターミナル) を開き, ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します (**注:** これらは、前の「**Azure Website の作成と Git 発行の設定**」セクションの最後でメモした手順と同じです)。

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    先ほど作成したパスワードを入力するように求められます。

3.  アプリケーション用のテーブルを作成するには、**[http://[your][http://[your] web site domain]/createtable.php** に移動します。
4.  アプリケーションの使用を開始するには、**[http://[your][http://[your] web site domain]/index.php** に移動します。

アプリケーションを発行した後、アプリケーションへの変更を開始し、Git を使用してその変更を発行することもできます。

## アプリケーションへの変更の発行

アプリケーションへの変更を発行するには、次のステップに従います。

1.  ローカルでアプリケーションへの変更を行います。
2.  GitBash (Git が `PATH` にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。

        git add .
        git commit -m "comment describing changes"
        git push azure master

    先ほど作成したパスワードを入力するように求められます。

3.  変更内容を確認するには、**[http://[your][http://[your] web site domain]/index.php** に移動します。

  [PHP]: http://www.php.net/manual/en/install.php
  [Azure の PHP Web サイト]: ./media/web-sites-php-storage/ws-storage-app.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Git のインストール]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  []: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179438.aspx
  [こちら]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd894031.aspx
  [Azure の管理ポータル]: https://manage.windowsazure.com
  [新しい Azure の Web サイトの作成]: ./media/web-sites-php-storage/new_website.jpg
  [新しい Web サイトのカスタム作成]: ./media/web-sites-php-storage/storage-quick-create.png
  [Fill in web site details]: ./media/web-sites-php-storage/storage-quick-create-details.png
  [キーの管理 を選択する]: ./media/web-sites-php-storage/storage-manage-keys.png
  [1]: ./media/web-sites-php-storage/storage-access-keys.png
  [2]: ./media/web-sites-php-storage/website-quick-create.png
  [3]: ./media/web-sites-php-storage/website-quick-create-details.png
  [Open web site dashboard]: ./media/web-sites-php-storage/go_to_dashboard.png
  [Git 発行の設定]: ./media/web-sites-php-storage/setup_git_publishing.png
  [ソース コードの位置]: ./media/web-sites-php-storage/where_is_code.png
  [発行資格情報の作成]: ./media/web-sites-php-storage/git-deployment-credentials.png
  [Web サイトのリポジトリの作成後に返される Git 展開の手順]: ./media/web-sites-php-storage/git-instructions.png
  [http://[your]: http://[your
