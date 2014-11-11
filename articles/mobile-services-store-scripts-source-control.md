<properties linkid="develop-mobile-tutorials-store-scripts-in-source-control" urlDisplayName="Store server scripts in source control" pageTitle="Store server scripts in source control - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# ソース管理へのサーバー スクリプトの保存

このトピックでは、Azure のモバイル サービスで初めてソース管理を設定し、サーバー スクリプトを Git リポジトリに格納する方法について説明します。スクリプトとその他の JavaScript コード ファイルは、ローカル リポジトリから運用モバイル サービスに昇格させることができます。さらに、複数のスクリプトで必要になる場合がある共有コードを定義する方法と、Node.js モジュールをアップロードする方法も説明します。

このチュートリアルでは、以下の各手順について説明します。

1.  [モバイル サービスでソース管理を有効にする][モバイル サービスでソース管理を有効にする]。
2.  [Git をインストールし、ローカル リポジトリを作成する][Git をインストールし、ローカル リポジトリを作成する]。
3.  [更新されたスクリプト ファイルをモバイル サービスに展開する][更新されたスクリプト ファイルをモバイル サービスに展開する]。
4.  [サーバー スクリプトで共有コードと Node.js モジュールを活用する][サーバー スクリプトで共有コードと Node.js モジュールを活用する]。

このチュートリアルを完了するには、「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」チュートリアルを完了して、モバイル サービスを既に作成してある必要があります。

## <a name="enable-source-control"></a><span class="short-header">ソース管理の有効化</span>モバイル サービスでソース管理を有効にする

1.  [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

    ![][0]

2.  **[ダッシュボード]** タブをクリックし、**[概要]** の **[ソース管理の設定]** をクリックして、確認のために **[はい]** をクリックします。

    ![][1]

    > [WACOM.NOTE]
    > ソース管理はプレビュー機能です。スクリプト ファイルは、モバイル サービスに格納されている場合でも、定期的にバックアップすることをお勧めします。

3.  **[ユーザー名]** と **[新しいパスワード]** を指定し、パスワードを確認して、チェック ボタンをクリックします。

    ![][2]

    Git リポジトリがモバイル サービスに作成されます。指定した資格情報をメモしておきます。後でこのリポジトリにアクセスするときに、必要になります。

4.  [構成] タブをクリックし、新しい **[ソース管理]** フィールドがあることを確認します。

    ![][3]

    Git リポジトリの URL が表示されます。ローカル コンピューターにリポジトリを複製するために、この URL を使用します。

これで、モバイル サービスでソース管理を有効にできたので、次は Git を使用してリポジトリをローカル コンピューターに複製します。

## <a name="clone-repo"></a><span class="short-header">レポジトリの複製</span>Git をインストールし、ローカル リポジトリを作成する

1.  ローカル コンピューターに Git をインストールします。

    Git をインストールするために必要な手順は、オペレーティング システムによって異なります。オペレーティング システム固有の配布とインストールのガイダンスについては、「[Installing Git (Git のインストール)][Installing Git (Git のインストール)]」を参照してください。

    > [WACOM.NOTE]
    > オペレーティング システムによっては、コマンド ラインと GUI の両方のバージョンの Git を使用できます。この記事で説明する手順では、コマンド ライン バージョンを使用します。

2.  **GitBash** (Windows) や **Bash** (Unix シェル) などのコマンド ラインを開きます。OS X システムでは、**ターミナル** アプリケーションを使用してコマンド ラインにアクセスできます。

3.  コマンド ラインで、スクリプトを格納するディレクトリに移動します。たとえば、「`cd SourceControl`」のように入力します。

4.  次のコマンドを使用して、新しい Git リポジトリのローカル コピーを作成します。`<your_git_URL>` の部分は、モバイル サービスの Git リポジトリの URL に置き換えます。

        git clone <your_git_URL>

5.  ユーザー名とパスワードの指定を求めるメッセージが表示されたら、モバイル サービスのソース管理を有効にしたときに設定したユーザー名とパスワードを入力します。認証が成功すると、次のような一連の応答が表示されます。

        remote: Counting objects: 8, done.
        remote: Compressing objects: 100% (4/4), done.
        remote: Total 8 (delta 1), reused 0 (delta 0)
        Unpacking objects: 100% (8/8), done.

6.  `git clone` コマンドを実行するディレクトリに移動し、次のディレクトリ構造を確認します。

    ![4][4]

    ここでは、新しいディレクトリがモバイル サービスの名前で作成されています。これが、データ サービスのローカル リポジトリになります。

7.  .\\service\\table サブフォルダーを開くと、TodoItem.json ファイルが含まれていることがわかります。これは、TodoItem テーブルに対する操作のアクセス許可の JSON 表現です。

    このテーブルでサーバー スクリプトが定義されると、そのテーブル操作でスクリプトを含む`TodoItem.<operation>.js` という名前のファイルも 1 つ以上作成されます。スケジューラ スクリプトとカスタム API スクリプトは、各スクリプトの名前が付けられたフォルダーに別々に格納されます。詳細については、「[ソース管理][ソース管理]」を参照してください。

これでローカル リポジトリが作成できたので、サーバー スクリプトを変更して、モバイル サービスに変更をプッシュ バックできます。

## <a name="deploy-scripts"></a><span class="short-header">スクリプトの展開</span>更新されたスクリプト ファイルをモバイル サービスに展開する

1.  .\\service\\table サブフォルダーに移動し、まだファイル todoitem.insert.js が存在しない場合は、ここで作成します。

2.  テキスト エディターで新しいファイル todoitem.insert.js を開き、次のコードを貼り付けて変更を保存します。

        function insert(item, user, request) {
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    このコードは、挿入された項目を単にログに書き込みます。このファイルに既にコードが含まれている場合は、単に少量の有効な JavaScript コード (`console.log()` への呼び出しなど) をこのファイルに追加し、変更を保存します。

3.  Git コマンド プロンプトで次のコマンドを入力し、新しいスクリプト ファイルの追跡を開始します。

        $ git add .

4.  次のコマンドを入力し、変更をコミットします。

        $ git commit -m "updated the insert script"

5.  次のコマンドを入力し、変更をリモート リポジトリにアップロードします。

        $ git push origin master

    コミットがモバイル サービスにデプロイされることを示す一連のコマンドが表示されるはずです。

6.  管理ポータルに戻って **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][5]

7.  **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

    ![][6]

    表示された挿入操作のスクリプトが、直前にリポジトリにアップロードした JavaScript コードと同じであることを確認します。

## <a name="use-npm"></a><span class="short-header">共有コードとモジュール</span>サーバー スクリプトで共有コードと Node.js モジュールを活用する

モバイル サービスでは、すべての Node.js コア モジュールにアクセスでき、コード内で **require** 関数を使用することでそのモジュールを利用することができます。さらに、Node.js コア パッケージに含まれない Node.js モジュールも使用できるだけでなく、自分で作成した共有コードを Node.js モジュールとして定義することもできます。モジュールの作成方法の詳細については、Node.js API リファレンス ドキュメントの「[Modules (モジュール)][Modules (モジュール)]」を参照してください。

次に、ソース管理と Node.js パッケージ マネージャー (NPM) を使用して、[node-uuid][node-uuid] Node.js モジュールをモバイル サービスに追加します。次に、このモジュールを使用して、挿入された項目の **uuid** プロパティに対応する新しい GUID 値を生成します。

1.  Node.js をまだローカル コンピューターにインストールしていない場合は、[Node.js Web サイト][Node.js Web サイト]で次の手順を実行します。

2.  コマンド プロンプトで、ローカル Git リポジトリの `.\service` フォルダーに移動し、次のコマンドを実行します。

        npm install node-uuid

    NPM は現在の場所で `node_modules` ディレクトリを作成し、`\node-uuid` サブディレクトリで [node-uuid][node-uuid] モジュールをインストールします。

    <div class="dev-callout">
<strong>注</strong>
<p>ディレクトリ階層の中に <code data-inline="1">node_modules</code> ディレクトリが既に存在している場合、リポジトリに新しい <code data-inline="1">node_modules</code> が作成されるのではなく、その既存のディレクトリの中に <code data-inline="1">\node-uuid</code> サブディレクトリが作成されます。この場合、既存の <code data-inline="1">node_modules</code> ディレクトリを削除してください。</p>
</div>

3.  .\\service\\table サブフォルダーに移動し、todoitem.insert.js ファイルを開いて、次のように変更します。

        function insert(item, user, request) {
            var uuid = require('node-uuid');
            item.uuid = uuid.v1();
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    このコードはテーブルに uuid 列を追加し、一意な GUID 識別子を設定します。

4.  前のセクションと同様に、Git コマンド プロンプトで次のコマンドを入力します。

        $ git add .
        $ git commit -m "added node-uuid module"
        $ git push origin master

    これで、新しいファイルが追加され、変更がコミットされ、新しい node-uuid モジュールと todoitem.insert.js スクリプトの変更がモバイル サービスにプッシュされます。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、スクリプトをソース管理に保存する方法について説明しました。サーバー スクリプトとカスタム API の操作の詳細については、以下を参照してください。

-   [モバイル サービスのサーバー スクリプトの操作][モバイル サービスのサーバー スクリプトの操作]
    サーバー スクリプト、ジョブ スケジューラ、およびカスタム API の操作方法について説明します。

-   [プル通知をサポートするカスタム API を定義する][プル通知をサポートするカスタム API を定義する]
     カスタム API を使用し、Windows ストア アプリでライブ タイルを更新する定期的な通知をサポートする方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [モバイル サービスでソース管理を有効にする]: #enable-source-control
  [Git をインストールし、ローカル リポジトリを作成する]: #clone-repo
  [更新されたスクリプト ファイルをモバイル サービスに展開する]: #deploy-scripts
  [サーバー スクリプトで共有コードと Node.js モジュールを活用する]: #use-npm
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
  [1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
  [2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
  [3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
  [4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
  [ソース管理]: http://msdn.microsoft.com/ja-jp/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
  [5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png
  [node-uuid]: https://npmjs.org/package/node-uuid
  [Node.js Web サイト]: http://nodejs.org/
  [モバイル サービスのサーバー スクリプトの操作]: /ja-jp/develop/mobile/how-to-guides/work-with-server-scripts
  [プル通知をサポートするカスタム API を定義する]: /ja-jp/develop/mobile/tutorials/create-pull-notifications-dotnet
