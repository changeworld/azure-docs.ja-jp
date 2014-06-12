# ソース管理から Azure の Web サイトへの発行

Azure の Web サイトでは、BitBucket、CodePlex、Dropbox、Git、GitHub、Mercurial、TFS などソース コード管理やリポジトリ ツールからの継続的な展開をサポートしています。これらのツールを使用すると、Web サイトのコンテンツとコードをメンテナンスすることができ、必要なときに変更をサイトにすばやく簡単にプッシュできます。

この記事では、Git を使用してローカル コンピューターから Azure の Web サイトに直接発行する方法について説明します (Azure では、この発行方法は**ローカル Git** と呼ばれます)。また、BitBucket、CodePlex、DropBox、GitHub、Mercurial などのリポジトリ Web サイトからの継続的な展開を有効にする方法についても説明します。継続的な展開に TFS を使用する方法の詳細については、「[Continuous delivery to Azure using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信)]」を参照してください。

> [WACOM.NOTE] この記事に記載されている Git コマンドの多くは、<a href="/ja-jp/develop/nodejs/how-to-guides/command-line-tools/">Mac および Linux 用 Azure コマンド ライン ツール</a>を使用して Web サイトを作成する際に自動的に実行されます。

このタスクの手順は次のとおりです。

* [Git のインストール](#Step1)
* [ローカル リポジトリの作成](#Step2)
* [Web ページの追加](#Step3)
* [Web サイトのリポジトリの有効化](#Step4)
* [プロジェクトの展開](#Step5)
	* [ローカル ファイルの Azure へのプッシュ (ローカル Git)](#Step6)
	* [BitBucket、CodePlex、Dropbox、GitHub、Mercurial などのリポジトリ Web サイトからのファイルの展開](#Step7)
* [トラブルシューティング](#Step8)

<h2><a id="Step2"></a>Git のインストール</h2>

Git をインストールするために必要な手順は、オペレーティング システムによって異なります。オペレーティング システム固有の配布とインストールのガイダンスについては、「[Installing Git (Git のインストール)]」を参照してください。

> [WACOM.NOTE] オペレーティング システムによっては、コマンド ラインと GUI の両方のバージョンの Git を使用できます。この記事で説明する手順では、コマンド ライン バージョンを使用します。

<h2><a id="Step2"></a>ローカル リポジトリの作成</h2>

次のタスクを実行して、新しい Git リポジトリを作成します。

1. Git リポジトリと Web サイトのファイルを格納するディレクトリを MyGitRepository という名前で作成します。

2. **GitBash** (Windows) や **Bash** (Unix シェル) などのコマンド ラインを開きます。OS X システムでは、**ターミナル** アプリケーションを使用してコマンド ラインにアクセスできます。

3. コマンド ラインで、MyGitRepository ディレクトリに移動します。

		cd MyGitRepository

4. 次のコマンドを使用して、新しい Git リポジトリを初期化します。

		git init

	このコマンドでは、"**Initialized empty Git repository in [path]]**" などのメッセージが返されます。

<h2><a id="Step3"></a>Web ページの追加</h2>

Azure の Web サイトでは、さまざまなプログラミング言語で作成されたアプリケーションをサポートしています。この例では、静的 .html ファイルを使用します。他のプログラミング言語の Web サイトを Azure に発行する方法の詳細については、[Azure デベロッパー センター]を参照してください。

1. テキスト エディターを使用して、Git リポジトリ (先ほど作成した MyGitRepository ディレクトリ) のルートに **index.html** という名前の新しいファイルを作成します。

2. index.html ファイルの内容として次のテキストを追加し、ファイルを保存します。

		Hello Git!

3. コマンド ラインで、現在の位置が Git リポジトリのルートであることを確認します。その後、次のコマンドを使用して **index.html** ファイルをリポジトリに追加します。

		git add index.html 

	> [WACOM.NOTE] git コマンドのヘルプを見つけるには、コマンドの後に「-help」または「--help」と入力します。たとえば、add コマンドのパラメーターのオプションを見つけるには、「git add -help」と入力します。より詳細なヘルプを見つけるには、「git add --help」と入力します。

4. 次に、次のコマンドを使用して、リポジトリへの変更をコミットします。

		git commit -m "Adding index.html to the repository"

	次のような出力が表示されます。

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>Web サイトのリポジトリの有効化</h2>

Azure ポータルを使用し、次に示している手順を実行して、Web サイトに対して Git リポジトリを有効にします。

1. [Azure ポータル]にログインします。

2. ページの左側にある **[Web サイト]** をクリックし、リポジトリを有効にする Web サイトを選択します。

	![選択した Web サイトが表示されている画像][portal-select-website]

3. **[ダッシュボード]** タブを選択します。

4. **[概要]** セクションで、**[ソース管理からの展開の設定]** をクリックします。次の **[展開の設定]** ダイアログ ボックスが表示されます。

	![git-WhereIsYourSourceCode][git-WhereIsYourSourceCode]

4. **[ローカル Git]** を選択し、**[次へ]** 矢印をクリックします。

	
5. しばらくすると、リポジトリが準備できたことを示すメッセージが表示されます。

	![git-instructions][git-instructions]

<h2><a id="Step5"></a>プロジェクトの展開</h2>

<h3><a id="Step6"></a>ローカル ファイルの Azure へのプッシュ (ローカル Git)</h3>

この時点でポータルには、ローカル リポジトリを初期化してファイルを追加する手順が表示されています。この手順はこのトピックの前の手順で実行済みです。展開資格情報をまだ設定していない場合は、表示されている手順 3. に従う必要があります。この手順で **[展開資格情報のリセット]** というリンクをクリックしてください。

次に示している手順に従って、ローカル Git を使用して Web サイトを Azure に発行します。

1. コマンド ラインで現在の位置が、先ほど index.html ファイルを作成したローカル Git リポジトリのルートであることを確認します。

2. ポータルに表示されている手順 3. の git remote add コマンドをコピーします。そのコマンドは次のようになります。

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [WACOM.NOTE] この **remote** コマンドは、名前付きの参照をリモート リポジトリに追加します。この例では、Azure の Web サイト リポジトリに "azure" という名前の参照を作成しています。

1. コマンド ラインで次のコマンドを使用して、ローカル リポジトリから "azure" リモートに現在のリポジトリのコンテンツをプッシュします。

		git push azure master

	ポータルで展開資格情報をリセットしたときに作成したパスワードの入力を求められます。パスワードを入力します (パスワードを入力しても Gitbash によってコンソールにアスタリスクはエコーされません)。次のような出力が表示されます。

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [WACOM.NOTE] Azure の Web サイトに対して作成したリポジトリでは、プッシュ要求のターゲットがそのリポジトリの <strong>master</strong> 分岐であると想定しています。そのため、この分岐が Web サイトのコンテンツとして使用されます。

2. ポータルの下部にある **[参照]** をクリックして、**index.html** が展開されていることを確認します。"Hello Git!" を含むページが表示されます。

	![A webpage containing 'Hello Git!'][hello-git]

3. テキスト エディターを使用して、**index.html** ファイルを変更して "Yay!" を追加し、ファイルを保存します。

4. コマンド ラインから次のコマンドを使用して、変更を**追加**して**コミット**した後、リモート リポジトリに**プッシュ**します。

		git add index.html
		git commit -m "Celebration"
		git push azure master

	**push** コマンドが完了したら、ブラウザーを更新して (場合によっては、ブラウザーを正しく更新するには Ctrl + F5 キーを押す必要があります)、コミットした最新の変更がページのコンテンツに反映されていることを確認します。

	![A webpage containing 'Yay!'][yay]

<h3><a id="Step7"></a>BitBucket、CodePlex、Dropbox、GitHub、Mercurial などのリポジトリ Web サイトからのファイルの展開</h3>

ローカル Git を使用してローカル ファイルを Azure にプッシュすると、ローカル プロジェクトから Azure の Web サイトに更新を手動でプッシュできるようになります。一方、BitBucket、CodePlex、Dropbox、GitHub、または Mercurial から展開すると、プロジェクトから最新の更新が Azure によってプルされる継続的な展開 プロセスが有効になります。

どちらの方法でも、プロジェクトが Azure の Web サイトに展開されます。ただし、複数の人がプロジェクトに携わっており、だれが最新の更新を行ったかに関係なく、常に最新バージョンが発行されるようにする場合は、継続的な展開が便利です。また、継続的な展開は、上記のいずれかのツールをアプリケーションの中央リポジトリとして使用する場合にも便利です。

GitHub、CodePlex、BitBucket からファイルを展開するには、これらのサービスのいずれかにローカル プロジェクトを発行している必要があります。これらのサービスにプロジェクトを発行する方法の詳細については、「[Create a Repo (GitHub) (Repo の作成 (GitHub))]」、「[Using Git with CodePlex (CodePlex での Git の使用)]」、「[Create a Repo (BitBucket) (Repo の作成 (BitBucket))]」、「[Using Dropbox to Share Git Repositories (Dropbox を使用した Git リポジトリの共有)]」、「[Quick Start - Mercurial (Mercurial のクイック スタート - Mercurial)]」を参照してください。

1. まず、継続的な展開用に選択したリポジトリに Web サイトのファイルを配置します。

2. Web サイトの Azure 管理ポータルで、**[ダッシュボード]** タブに移動します。**[概要]** セクションで、**[ソース管理からの展開の設定]** をクリックします。**[展開の設定]** ダイアログ ボックスが表示され、**ソース コードの場所**が求められます。

2. 継続的な展開に使用するソース管理方法を選択します。
	
3. 要求されたら、選択したサービスの資格情報を入力します。

4. アカウントへのアクセスを Azure に許可すると、リポジトリの一覧が表示されます。

	![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]
  
5. Azure の Web サイトと関連付けるリポジトリを選択します。チェックマークをクリックして続行します。

	> [WACOM.NOTE] GitHub または BitBucket を使用して継続的な展開を有効にする場合、パブリック プロジェクトとプライベート プロジェクトの両方が表示されます。

6. Azure によって、選択したリポジトリとの関連付けが作成され、master 分岐からファイルがプルされます。このプロセスが完了すると、**[展開]** ページの **[展開履歴]** に、次のような **[アクティブな展開]** メッセージが表示されます。

	![git-githubdeployed][git-githubdeployed]

7. この時点で、選択したリポジトリから Azure の Web サイトにプロジェクトが展開されました。サイトがアクティブであることを確認するには、ポータルの下部にある **[参照]** リンクをクリックします。ブラウザーには Web サイトが表示されます。

8. 継続的な展開が行われていることを確認するには、プロジェクトに変更を加え、この Web サイトに関連付けたリポジトリに更新をプッシュします。リポジトリへのプッシュが完了すると、すぐに Web サイトは更新され、変更が反映されます。更新がプルされていることは、Web サイトの **[展開]** ページで確認できます。

	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]


<h4>継続的な展開のしくみ</h4>
継続的な展開は、サイトの **[構成]** タブの **[展開]** セクションにある **[展開の開始 URL]** を指定することによって機能します。

![git-DeploymentTrigger][git-DeploymentTrigger]

リポジトリが更新されると、この URL に POST 要求が送信され、リポジトリが更新されたことが Azure の Web サイトに通知されます。この時点で更新を取得し、Web サイトに展開します。

<h4>使用する分岐の指定</h4>

継続的な展開を有効にした場合、既定ではリポジトリの **master** 分岐が使用されます。別の分岐を使用するには、次に示している手順を実行します。

1. ポータルで、Web サイトを選択し、**[構成]** をクリックします。

2. ページの **[展開]** セクションで、**[展開する分岐]** に使用する分岐を入力し、Enter キーを押します。最後に、**[保存]** をクリックします。

	Azure では、新しい分岐への変更に基づいてすぐに更新を開始します。

<h4>継続的な展開の無効化</h4>

継続的な展開は、Azure の**ダッシュボード**から無効にできます。**[概要]** セクションで、使用しているリポジトリから切断するオプションを選択します。

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]	

別のソースからの発行を設定する場合は、確認メッセージで **[はい]** を選択すると、**[概要]** に戻って **[ソース管理からの展開の設定]** をクリックできます。

<h2><a id="Step8"></a>トラブルシューティング</h2>

Git を使用して Azure の Web サイトに発行する場合に発生する一般的なエラーまたは問題を以下に示します。

****

**症状**: Couldn't resolve host 'hostname'

**原因**: このエラーは、"azure" リモートを作成するときに入力したアドレス情報が間違っている場合に発生します。

**解決策**: `git remote -v` コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。"azure" リモートの URL が正しいことを確認します。必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。

****

**症状**: No refs in common and none specified; doing nothing.Perhaps you should specify a branch such as 'master'.

**原因**: このエラーは、git push 操作を実行するときに分岐を指定せず、Git で使用される push.default 値を設定していない場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。次に例を示します。

	git push azure master

****

**Symptom**: src refspec [branchname] does not match any.

**原因**: このエラーは、"azure" リモートの master 以外の分岐にプッシュしようとした場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。たとえば、次のように実行します。

	git push azure master

****

**症状**: Error - Changes commited to remote repository but your web site not updated.

**原因**: このエラーは、必要な追加モジュールを指定する package.json ファイルを含む Node.js アプリケーションを展開する場合に発生します。

**解決策**: このエラーの前に "npm ERR!" を含む追加のメッセージがログに記録されます。このメッセージによって、このエラーに関する追加のコンテキストが提供される場合があります。このエラーの既知の原因と、対応する "npm ERR!" メッセージを以下に示します。

* **形式が正しくない package.json ファイル**: npm ERR!Couldn't read dependencies.

* **Windows 用のバイナリ配布がないネイティブ モジュール**:

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		または

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## その他のリソース

* [How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)]
* [Mac および Linux 用 Azure コマンド ライン ツールの使用方法]
* [Git に関するドキュメント]

[Azure デベロッパー センター]: http://www.windowsazure.com/ja-jp/develop/overview/
[Azure ポータル]: http://manage.windowsazure.com
[Git website (Git の Web サイト)]: http://git-scm.com
[Installing Git (Git のインストール)]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)]: http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/powershell-cmdlets/
[Mac および Linux 用 Azure コマンド ライン ツールの使用方法]: /ja-jp/develop/nodejs/how-to-guides/command-line-tools/
[Git に関するドキュメント]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Create a Repo (GitHub) (Repo の作成 (GitHub))]: https://help.github.com/articles/create-a-repo
[Using Git with CodePlex (CodePlex での Git の使用)]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Create a Repo (BitBucket) (Repo の作成 (BitBucket))]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Quick Start - Mercurial (Mercurial のクイック スタート - Mercurial)]: http://mercurial.selenic.com/wiki/QuickStart
[Using Dropbox to Share Git Repositories (Dropbox を使用した Git リポジトリの共有)]: https://gist.github.com/trey/2722927
[Continuous delivery to Azure using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信)]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/publishing-with-tfs/

