<properties 
	pageTitle="ソース管理へのプロジェクト コードの保存 - Azure Mobile Services" 
	description="コンピューターのローカル Git リポジトリにサーバー スクリプト ファイルとモジュールを格納する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="ggailey777"/>

# ソース管理にプロジェクトのコードを格納する

<div class="dev-center-tutorial-subselector">
	<a href="/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/" title=".NET バックエンド">.NET バックエンド</a> | <a href="/documentation/articles/mobile-services-store-scripts-source-control/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a>
</div>

このトピックでは、Azure Mobile Services で提供されているソース管理を使用してサーバー スクリプトを保存する方法を示します。スクリプトとその他の JavaScript バンクエンド コード ファイルは、ローカル Git リポジトリから運用モバイル サービスに昇格させることができます。さらに、複数のスクリプトで必要になる場合がある共有コードを定義する方法と、package.json ファイルを使用して Node.js モジュールをモバイル サービスに追加する方法も説明します。

このチュートリアルでは、以下の各手順について説明します。

1. [モバイル サービスでソース管理を有効にする]。
2. [Git をインストールし、ローカル リポジトリを作成する]。
3. [更新されたスクリプト ファイルをモバイル サービスに展開する]。
4. [サーバー スクリプトで共有コードと Node.js モジュールを活用する]。

このチュートリアルを完了するには、「[Mobile Services の使用]」または「[既存のアプリにモバイル サービスを追加する]」チュートリアルを完了し、モバイル サービスを作成しておく必要があります。

##<a name="enable-source-control"></a>モバイル サービスでソース管理を有効にする

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Git をインストールし、ローカル リポジトリを作成する

1. ローカル コンピューターに Git をインストールします。 

	Git をインストールするために必要な手順は、オペレーティング システムによって異なります。オペレーティング システム固有の配布とインストールのガイダンスについては、「[Installing Git (Git のインストール)]」を参照してください。

	> [AZURE.NOTE]オペレーティング システムによっては、コマンド ラインと GUI の両方のバージョンの Git を使用できます。この記事で説明する手順では、コマンド ライン バージョンを使用します。

2. **GitBash** (Windows) や **Bash** (Unix シェル) などのコマンド ラインを開きます。OS X システムでは、**ターミナル** アプリケーションを使用してコマンド ラインにアクセスできます。

3. コマンド ラインで、スクリプトを格納するディレクトリに移動します。たとえば、「`cd SourceControl`」のように入力します。

4. 次のコマンドを使用して、新しい Git リポジトリのローカル コピーを作成します。`<your_git_URL>` の部分は、モバイル サービスの Git リポジトリの URL に置き換えます。

		git clone <your_git_URL>

5. ユーザー名とパスワードの指定を求めるメッセージが表示されたら、モバイル サービスのソース管理を有効にしたときに設定したユーザー名とパスワードを入力します。認証が成功すると、次のような一連の応答が表示されます。

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. `git clone` コマンドを実行するディレクトリに移動し、次のディレクトリ構造を確認します。

	![4][4]

	ここでは、新しいディレクトリがモバイル サービスの名前で作成されています。これが、データ サービスのローカル リポジトリになります。

7. .\\service\\table サブフォルダーを開くと、TodoItem.json ファイルが含まれていることがわかります。これは、TodoItem テーブルに対する操作のアクセス許可の JSON 表現です。

	このテーブルにサーバー スクリプトが定義されている場合、<code>TodoItem._&lt;operation&gt;_.js</code> という名前のファイルが 1 つ以上存在します。このファイルには特定のテーブル操作に対応するスクリプトが含まれます。スケジューラ スクリプトとカスタム API スクリプトは、各スクリプトの名前が付けられたフォルダーに別々に格納されます。詳細については、「[ソース管理]」を参照してください。

これでローカル リポジトリが作成できたので、サーバー スクリプトを変更して、モバイル サービスに変更をプッシュ バックできます。

##<a name="deploy-scripts"></a>更新されたスクリプト ファイルをモバイル サービスに展開する

1. .\\service\\table サブフォルダーに移動し、まだファイル todoitem.insert.js が存在しない場合は、ここで作成します。

2. テキスト エディターで新しいファイル todoitem.insert.js を開き、次のコードを貼り付けて変更を保存します。

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	このコードは、挿入された項目を単にログに書き込みます。このファイルに既にコードが含まれている場合は、単に少量の有効な JavaScript コード (`console.log()` への呼び出しなど) をこのファイルに追加し、変更を保存します。

3. Git コマンド プロンプトで次のコマンドを入力し、新しいスクリプト ファイルの追跡を開始します。

		$ git add .
	

4. 次のコマンドを入力し、変更をコミットします。

		$ git commit -m "updated the insert script"

5. 次のコマンドを入力し、変更をリモート リポジトリにアップロードします。

		$ git push origin master
	
	コミットがモバイル サービスにデプロイされることを示す一連のコマンドが表示されるはずです。

6. 管理ポータルに戻って **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

	![][5]

3. **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

	![][6]

	表示された挿入操作のスクリプトが、直前にリポジトリにアップロードした JavaScript コードと同じであることを確認します。

##<a name="use-npm"></a>サーバー スクリプトで共有コードと Node.js モジュールを活用する

モバイル サービスでは、すべての Node.js コア モジュールにアクセスでき、コード内で **require** 関数を使用することでそのモジュールを利用することができます。さらに、Node.js コア パッケージに含まれない Node.js モジュールも使用できるだけでなく、自分で作成した共有コードを Node.js モジュールとして定義することもできます。モジュールの作成方法の詳細については、Node.js API リファレンス ドキュメントの「[Modules (モジュール)][Node.js API Documentation: Modules]」を参照してください。

Node.js モジュールをモバイル サービスに追加するための推奨される方法では、リファレンスをサービスの package.json ファイルに追加します。次に、package.json ファイルを更新して、[node-uuid] Node.js モジュールを Mobile Services に追加します。更新が Azure にプッシュされると、モジュール サービスが再起動され、モジュールがインストールされます。次に、このモジュールを使用して、挿入された項目の **uuid** プロパティに対応する新しい GUID 値を生成します。

2. ローカル Git リポジトリの `.\service` フォルダーに移動し、テキスト エディターで package.json ファイルを開きます。

3. 次を見つけます。

		npm install node-uuid

	NPM は現在の場所で `node_modules` ディレクトリを作成し、`\node-uuid` サブディレクトリで [node-uuid] モジュールをインストールします。

	> [AZURE.NOTE]ディレクトリ階層の中に `node_modules` ディレクトリが既に存在している場合、リポジトリに新しい `node_modules` が作成されるのではなく、その既存のディレクトリの中に `\node-uuid` サブディレクトリが作成されます。この場合、既存の `node_modules` ディレクトリを削除してください。

4. .\\service\\table サブフォルダーに移動し、todoitem.insert.js ファイルを開いて、次のように変更します。

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(item);
		}

	このコードはテーブルに uuid 列を追加し、一意な GUID 識別子を設定します。

5. 前のセクションと同様に、Git コマンド プロンプトで次のコマンドを入力します。

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	これで、新しいファイルが追加され、変更がコミットされ、新しい node-uuid モジュールと todoitem.insert.js スクリプトの変更がモバイル サービスにプッシュされます。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、スクリプトをソース管理に保存する方法について説明しました。サーバー スクリプトとカスタム API の操作の詳細については、以下を参照してください。

+ [モバイル サービスのサーバー スクリプトの操作] <br/>サーバー スクリプト、ジョブ スケジューラ、およびカスタム API の操作方法について説明します。

+ [クライアントからのカスタム API 呼び出し] <br/>クライアントから呼び出すことのできる、カスタム API を作成する方法を説明します。

<!-- Anchors. -->
[モバイル サービスでソース管理を有効にする]: #enable-source-control
[Git をインストールし、ローカル リポジトリを作成する]: #clone-repo
[更新されたスクリプト ファイルをモバイル サービスに展開する]: #deploy-scripts
[サーバー スクリプトで共有コードと Node.js モジュールを活用する]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[ソース管理]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installing Git (Git のインストール)]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Mobile Services の使用]: mobile-services-ios-get-started.md
[既存のアプリにモバイル サービスを追加する]: mobile-services-ios-get-started-data.md
[モバイル サービスのサーバー スクリプトの操作]: mobile-services-how-to-use-server-scripts.md
[Azure 管理ポータル]: https://manage.windowsazure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[クライアントからのカスタム API 呼び出し]: mobile-services-ios-call-custom-api.md
[Node.js API Documentation: Modules]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!--HONumber=54--> 