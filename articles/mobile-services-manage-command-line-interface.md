<properties urlDisplayName="Command Line Administration" pageTitle="コマンド ラインでの Mobile Services の管理 - Azure チュートリアル" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# コマンド ライン ツールを使用したモバイル サービスの自動化 

このトピックでは、Azure コマンド ライン ツールを使用して、Azure モバイル サービスの作成と管理を自動化する方法を紹介します。このトピックでは、コマンド ライン ツールのインストールとその使用を始めるための方法、およびコマンド ライン ツールを使用して次のモバイル サービス タスクを実行する方法ついて説明します。

-	[新しいモバイル サービスを作成する] 
-	[新しいテーブルを作成する]
-   [テーブル操作にスクリプトを登録する][Register a new table script]
-   [テーブルをリストする]
- 	[既存のテーブルを削除する]
-	[モバイル サービスをリストする]
-   [既存のモバイル サービスを削除する]
 
個々のコマンドを単一のスクリプトまたはバッチ ファイルと組み合わせることにより、モバイル サービスの作成、確認、および削除のプロセスを自動化することができます。 

Azure コマンド ライン ツールを使用してモバイル サービスを管理するには、Azure モバイル サービス機能を有効にした Azure アカウントが必要です。

+ アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「 <a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/" target="_blank">Azure 無料評価版</a>」を参照してください。

+ 既にアカウントがあっても、Azure Mobile Services のプレビューを有効にする必要がある場合は、 <a href="http://azure.microsoft.com/ja-jp/documentation/articles/php-create-account/#enable" target="_blank">Azure プレビュー機能の有効化に関するページを参照してください</a>。

このトピックでは、Azure コマンド ライン ツールでサポートされている一般的な管理タスクを取り上げます。詳細については、[Azure コマンド ライン ツールに関するドキュメント][reference-docs]を参照してください。

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic. 

+ (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

<h2><a name="install"></a>Azure コマンド ライン ツールのインストール</h2>

次の説明に従って、お使いのオペレーティング システムに対応するコマンド ライン ツールをインストールしてください。

* **Windows**:[Azure コマンド ライン ツール インストーラー][windows-installer]をダウンロードします。ダウンロードした .msi ファイルを開き、指示に従ってインストール手順を実行します。

* **Mac**:[Azure SDK インストーラー][mac-installer]をダウンロードします。ダウンロードした .pkg ファイルを開き、指示に従ってインストール手順を実行します。

* **Linux**:最新バージョンの [Node.js][nodejs-org] をインストールし ([パッケージ マネージャーを使った Node.js のインストールに関するページ][install-node-linux]を参照)、次のコマンドを実行します。

		npm install azure-cli -g

インストールをテストするには、コマンド プロンプトで「azure」と入力します。インストールが正常に完了している場合は、使用可能なすべての `azure` コマンドの一覧が表示されます。
<h2><a name="import-account"></a>発行の設定をダウンロードおよびインポートする方法</h2>

最初に、発行の設定をダウンロードしてインポートする必要があります。これにより、Azure サービスを作成および管理するためのツールを使用できるようになります。発行の設定をダウンロードするには、次のように account download コマンドを使用します。

		azure account download

既定のブラウザーが開き、管理ポータルにサインインするよう求められます。サインインすると、`.publishsettings` ファイルがダウンロードされます。このファイルを保存した場所をメモしておきます。

次に、次のコマンドを実行して `.publishsettings` ファイルをインポートします。ここで、`<path-to-settings-file>` には、`.publishsettings` ファイルのパスを指定してください。

		azure account import <path-to-settings-file>

account clear コマンドを使用すると、 <code>import</code> コマンドによって保存された <code>すべての情報を</code> 削除できます。

		azure account clear

`account` コマンドのオプションの一覧を表示するには、`-help` オプションを使用します。

		azure account -help

発行の設定をインポートした後は、セキュリティ上の理由から、`.publishsettings` ファイルを削除する必要があります。詳細については、「[Mac および Linux 用 Windows Azure コマンド ライン ツールのインストール方法]」を参照してください。これで、コマンド ラインまたはバッチ ファイルを使用して Azure モバイル サービスの作成および管理を開始する準備が整いました。  

<h2><a name="create-service"></a>新しいモバイル サービスを作成する方法</h2>

コマンド ライン ツールを使用して、新しいモバイル サービス インスタンスを作成することができます。モバイル サービスを作成する間に、SQL データベース インスタンスを新しいサーバーに作成することもできます。 

次のコマンドでは、新しいモバイル サービス インスタンスがサブスクリプション内で作成されます。`<service-name>` は新しいモバイル サービスの名前、`<server-admin>` は新しいサーバーのログイン名、`<server-password>` は新しいログインのパスワードです。

		azure mobile create <service-name> <server-admin> <server-password>

指定されたモバイル サービスが既に存在する場合、`mobile create` コマンドは失敗します。自動化スクリプト内では、モバイル サービスの再作成を試行する前に、そのモバイル サービスを削除する必要があります。

<h2><a name="list-services"></a>サブスクリプション内の既存のモバイル サービスをリストする方法</h2>

次のコマンドでは、Azure サブスクリプション内のすべてのモバイル サービスのリストが返されます。

		azure mobile list

このコマンドでは、各モバイル サービスの現在の状態と URL も表示されます。

<h2><a name="delete-service"></a>既存のモバイル サービスを削除する方法</h2>

コマンド ライン ツールを使用して、関連する SQL データベースおよびサーバーと共に既存のモバイル サービスを削除することができます。次のコマンドでは、モバイル サービスが削除されます。`<service-name>` は、削除するモバイル サービスの名前です。

		azure mobile delete <service-name> -a -q

このコマンドでは、`-a` パラメーターと `-q` パラメーターを含めることで、モバイル サービスで使用されている SQL Database やサーバーも、確認メッセージを表示することなく削除されます。

<div class="dev-callout"><strong>注</strong> 
   <p>-q <code>パラメーター</code> を <code>-a</code> または <code>-d</code>パラメーターと共に指定しないと、実行は一時停止され、SQL Database の削除オプションの選択を求めるメッセージが表示されます。他のサービスがデータベースまたはサーバーを使用していない場合は、 <code>-a</code> パラメーターのみを使用してください。それ以外の場合、 <code>-d</code> パラメーターは、削除するモバイル サービスに属するデータを削除するためにのみ使用します。</p>
</div>

<h2><a name="create-table"></a>モバイル サービスにテーブルを作成する方法</h2>

次のコマンドでは、指定したモバイル サービスにテーブルが作成されます。`<service-name>` は、モバイル サービスの名前で、`<table-name>` は作成するテーブルの名前です。

		azure mobile table create <service-name> <table-name>

これにより、新しいテーブルが作成され、テーブル操作 `insert`、`read`、`update`、`delete` に対して、既定のアクセス許可 `application` が設定されます。 

次のコマンドでは、パブリックの `read` アクセス許可で新しいテーブルが作成されますが、管理者にのみ `delete` アクセス許可が付与されます。

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

次の表では、スクリプトのアクセス許可値と [Azure の管理ポータル]でのアクセス許可値を比較しています。

<table border="1" width="100%"><tr><th>スクリプト値</th><th>管理ポータル値</th></tr>
<tr><td><code>public</code></td><td>すべてのユーザー</td></tr>
<tr><td><code>application</code> (既定)</td><td>アプリケーション キーを持つユーザー</td></tr>
<tr><td><code>user</code></td><td>認証されたユーザーのみ</td></tr>
<tr><td><code>admin	</code></td><td>スクリプトと管理者のみ</td></tr></table>

指定されたテーブルが存在する場合、`mobile table create` コマンドは失敗します。自動化スクリプト内では、テーブルの再作成を試行する前に、そのテーブルを削除する必要があります。

<h2><a name="list-tables"></a>モバイル サービス内の既存のテーブルをリストする方法</h2>

次のコマンドでは、モバイル サービス内のすべてのテーブルのリストが返されます。`<service-name>` は、モバイル サービスの名前です。

		azure mobile table list <service-name>

このコマンドでは、各テーブルのインデックスの数と、テーブル内に現在存在するデータ行の数も表示されます。

<h2><a name="delete-table"></a>モバイル サービスから既存のテーブルを削除する方法</h2>

次のコマンドでは、モバイル サービスからテーブルが削除されます。`<service-name>` は、モバイル サービスの名前で、`<table-name>` は削除するテーブルの名前です。

		azure mobile table delete <service-name> <table-name> -q

自動化スクリプト内では、実行をブロックする確認プロンプトを表示せずにテーブルを削除するために、`-q` パラメーターを使用します。

<h2><a name="register-script"></a>テーブル操作にスクリプトを登録する方法</h2>

次のコマンドでは、関数がアップロードされ、テーブル操作に登録されます。`<service-name>` は、モバイル サービスの名前、`<table-name>` はテーブルの名前、`<operation>` はテーブル操作で `read`、`insert`、`update`、または `delete` を指定します。

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

この操作では、JavaScript (.js) ファイルがローカル コンピューターからアップロードされます。ファイル名はテーブル名と操作名で構成し、ファイルは、コマンドを実行する場所の `table` サブフォルダーに配置する必要があります。たとえば、次の操作では、`TodoItems` テーブルに対する新しい `insert` スクリプトがアップロードおよび登録されます。

		azure mobile script upload todolist table/todoitems.insert.js

スクリプト ファイル内の関数宣言は、登録されているテーブル操作にも一致する必要があります。つまり、`insert` スクリプトの場合は、アップロードされたスクリプトに、次のシグネチャを持つ関数が含まれるということです。

		function insert(item, user, request) {
		    ...
		} 

スクリプトの登録方法の詳細については、[Mobile Services のサーバー スクリプト リファレンス]を参照してください。

<!--<h2><a name="test-service"></a>Test the new mobile service</h2>

When you are automating the creation of your mobile service, you can optionally use cURL or another command-line request generator to 

## <a name="nextsteps"> </a>Next Steps
Next steps here....
-->
<!-- Anchors. -->
[コマンド ライン ツールをダウンロードしインストールする]: #install
[発行設定をダウンロードしインポートする]: #import
[新しいモバイル サービスを作成する]: #create-service
[マスター キーを取得する]: #get-master-key
[新しいテーブルを作成する]: #create-table
[新しいテーブル スクリプトを登録する]: #register-script
[既存のテーブルを削除する]: #delete-table
[既存のモバイル サービスを削除する]: #delete-service
[モバイル サービスをテストする]: #test-service
[モバイル サービスをリストする]: #list-services
[テーブルをリストする]: #list-tables
[次のステップ]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Azure の管理ポータル]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /ja-jp/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Mac および Linux 用 Azure コマンド ライン ツールのインストール方法]: http://go.microsoft.com/fwlink/p/?LinkId=275795


<!--HONumber=35_1-->
