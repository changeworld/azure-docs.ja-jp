<properties 
	pageTitle="コマンド ラインでの Mobile Services の管理 | Microsoft Azure" 
	description="コマンド ライン ツールを使用して Azure Mobile Services を作成、デプロイ、管理する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="Mobile" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/02/2015" 
	ms.author="glenga"/>

# コマンド ライン ツールを使用したモバイル サービスの自動化 

##概要

このトピックでは、Azure コマンド ライン ツールを使用して、Azure モバイル サービスの作成と管理を自動化する方法を紹介します。このトピックでは、コマンド ライン ツールのインストールとその使用を始めるための方法、およびコマンド ライン ツールを使用して主な Mobile Services を実行する方法について説明します。
 
個々のコマンドを単一のスクリプトまたはバッチ ファイルと組み合わせることにより、モバイル サービスの作成、確認、および削除のプロセスを自動化することができます。

このトピックでは、Azure コマンド ライン ツールでサポートされている一般的な管理タスクを取り上げます。詳細については、[Azure コマンド ライン ツールに関するドキュメント][reference-docs]を参照してください。

##Azure コマンド ライン ツールのインストール

次の説明に従って、お使いのオペレーティング システムに対応するコマンド ライン ツールをインストールしてください。

* **Windows**: [Azure コマンド ライン ツール インストーラー][windows-installer]をダウンロードします。ダウンロードした .msi ファイルを開き、指示に従ってインストール手順を実行します。

* **Mac**: [Azure SDK インストーラー][mac-installer]をダウンロードします。ダウンロードした .pkg ファイルを開き、指示に従ってインストール手順を実行します。

* **Linux**: 最新バージョンの [Node.js][nodejs-org] をインストールし (「[パッケージ マネージャーを使った Node.js のインストール][install-node-linux]」を参照)、次のコマンドを実行します。

	npm install azure-cli -g

インストールをテストするには、コマンド プロンプトで「`azure`」と入力します。インストールが正常に完了している場合は、使用可能なすべての `azure` コマンドの一覧が表示されます。

##発行の設定をダウンロードおよびインポートする方法

最初に、発行の設定をダウンロードしてインポートする必要があります。これにより、Azure サービスを作成および管理するためのツールを使用できるようになります。発行の設定をダウンロードするには、次のように `account download` コマンドを使用します。

	azure account download

既定のブラウザーが開き、管理ポータルにサインインするよう求められます。サインインすると、`.publishsettings` ファイルがダウンロードされます。このファイルを保存した場所をメモしておきます。

次に、次のコマンドを実行して `.publishsettings` ファイルをインポートします。ここで、`<path-to-settings-file>` には、`.publishsettings` ファイルのパスを指定してください。

	azure account import <path-to-settings-file>

<code>account clear</code> コマンドを使用すると、<code>import</code> コマンドによって格納されたすべての情報を削除できます。

	azure account clear

`account` コマンドのオプションの一覧を表示するには、`-help` オプションを使用します。

	azure account -help

発行設定をインポートした後は、セキュリティ上の理由から、`.publishsettings` ファイルを削除する必要があります。詳細については、「[Mac および Linux 用 Windows Azure コマンド ライン ツールのインストール方法]」を参照してください。これで、コマンド ラインまたはバッチ ファイルを使用して Azure モバイル サービスの作成および管理を開始する準備が整いました。

##新しい Mobile Services を作成する方法

コマンド ライン ツールを使用して、新しいモバイル サービス インスタンスを作成することができます。モバイル サービスを作成する間に、SQL データベース インスタンスを新しいサーバーに作成することもできます。

次のコマンドでは、新しいモバイル サービス インスタンスがサブスクリプション内で作成されます。`<service-name>` は新しいモバイル サービスの名前、`<server-admin>` は新しいサーバーのログイン名、`<server-password>` は新しいログインのパスワードです。

	azure mobile create <service-name> <server-admin> <server-password>

指定されたモバイル サービスが既に存在する場合、`mobile create` コマンドは失敗します。自動化スクリプト内では、モバイル サービスの再作成を試行する前に、そのモバイル サービスを削除する必要があります。

##サブスクリプション内の既存の Mobile Services をリストする方法

> [AZURE.NOTE]"リスト" と "スクリプト" に関連する CLI でのコマンドは、JavaScript バックエンドでのみ機能します。

次のコマンドでは、Azure サブスクリプション内のすべてのモバイル サービスのリストが返されます。

	azure mobile list

このコマンドでは、各モバイル サービスの現在の状態と URL も表示されます。

##既存の Mobile Services を削除する方法

コマンド ライン ツールを使用して、関連する SQL データベースおよびサーバーと共に既存のモバイル サービスを削除することができます。次のコマンドでは、モバイル サービスが削除されます。`<service-name>` は、削除するモバイル サービスの名前です。

	azure mobile delete <service-name> -a -q

このコマンドでは、`-a` パラメーターと `-q` パラメーターを含めることで、モバイル サービスで使用されている SQL データベースやサーバーも、確認メッセージを表示することなく削除されます。

> [AZURE.NOTE]<code>-a</code> または <code>-d</code> と共に <code>-q</code> パラメーターを指定しなかった場合、例外が一時停止され、SQL Database の削除オプションを選択するように求めるメッセージが表示されます。<code>-a</code> パラメーターは、他のサービスでデータベースまたはサーバーが使用されていない場合にのみ使用してください。それ以外の場合は、削除する Mobile Services に含まれるデータのみを削除できるように <code>-d</code> パラメーターを使用してください。

##Mobile Services にテーブルを作成する方法

次のコマンドでは、指定されたモバイル サービスにテーブルが作成されます。`<service-name>` はモバイル サービスの名前、`<table-name>` は作成するテーブルの名前です。

	azure mobile table create <service-name> <table-name>

これにより、テーブル操作 `insert`、`read`、`update`、`delete` の既定のアクセス許可 `application` で新しいテーブルが作成されます。

次のコマンドでは、パブリックの `read` アクセス許可で新しいテーブルが作成されますが、管理者にのみ `delete` アクセス許可が付与されます。

	azure mobile table create <service-name> <table-name> -p read=public,delete=admin

次のテーブルでは、スクリプトのアクセス許可値と [Azure 管理ポータル]でのアクセス許可値を比較しています。

|スクリプト値|管理ポータルの値| |========|========| |`public`|すべてのユーザー| |`application`(既定値)|アプリケーション キーを持つユーザー| |`user`|認証されたユーザーのみ| |`admin`|スクリプトと管理者のみ|

指定されたテーブルが既に存在する場合、`mobile table create` コマンドは失敗します。自動化スクリプト内では、テーブルの再作成を試行する前に、そのテーブルを削除する必要があります。

##Mobile Services 内の既存のテーブルをリストする方法

次のコマンドでは、モバイル サービス内のすべてのテーブルのリストが返されます。`<service-name>` は、モバイル サービスの名前です。

	azure mobile table list <service-name>

このコマンドでは、各テーブルのインデックスの数と、テーブル内に現在存在するデータ行の数も表示されます。

##Mobile Services から既存のテーブルを削除する方法

次のコマンドでは、モバイル サービスからテーブルが削除されます。`<service-name>` はモバイル サービスの名前、`<table-name>` は削除するテーブルの名前です。

	azure mobile table delete <service-name> <table-name> -q

自動化スクリプト内では、実行をブロックする確認プロンプトを表示せずにテーブルを削除するために、`-q` パラメーターを使用します。

##テーブル操作にスクリプトを登録する方法

次のコマンドでは、関数をアップロードし、テーブルに対する操作に登録します。`<service-name>` はモバイル サービスの名前、`<table-name>` はテーブルの名前、`<operation>` はテーブル操作 (`read`、`insert`、`update`、`delete`) です。

	azure mobile script upload <service-name> table/<table-name>.<operation>.js

この操作では、JavaScript (.js) ファイルがローカル コンピューターからアップロードされます。ファイル名はテーブル名と操作名で構成し、ファイルは、コマンドを実行する場所の `table` サブフォルダーに配置する必要があります。たとえば、次の操作では、`TodoItems` テーブルに対する新しい `insert` スクリプトがアップロードおよび登録されます。

	azure mobile script upload todolist table/todoitems.insert.js

スクリプト ファイル内の関数宣言は、登録されているテーブル操作にも一致する必要があります。つまり、`insert` スクリプトの場合は、アップロードされたスクリプトに、次のシグネチャを持つ関数が含まれるということです。

	function insert(item, user, request) {
	    ...
	} 

スクリプトの登録方法の詳細については、[モバイル サービスのサーバー スクリプト リファレンス]を参照してください。

<!-- Anchors. -->
[Download and install the command-line tools]: #install
[Download and import publish settings]: #import
[Create a new mobile service]: #create-service
[Get the master key]: #get-master-key
[Create a new table]: #create-table
[Register a new table script]: #register-script
[Delete an existing table]: #delete-table
[Delete an existing mobile service]: #delete-service
[Test the mobile service]: #test-service
[List mobile services]: #list-services
[List tables]: #list-tables
[Next steps]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Azure 管理ポータル]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/#Commands_to_manage_mobile_services
[Mac および Linux 用 Windows Azure コマンド ライン ツールのインストール方法]: http://go.microsoft.com/fwlink/p/?LinkId=275795

 

<!---HONumber=Nov15_HO2-->