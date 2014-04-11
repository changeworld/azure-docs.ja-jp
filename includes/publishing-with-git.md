# ソース管理から Windows Azure の Web サイトへの発行

Windows Azure の Web サイトでは、BitBucket、CodePlex、Dropbox、Git、GitHub、Mercurial、TFS などソース コード管理やリポジトリ ツールからの継続的な展開をサポートしています。これらのツールを使用すると、Web サイトのコンテンツとコードをメンテナンスすることができ、必要なときに変更をサイトにすばやく簡単にプッシュできます。

この記事では、Git を使用してローカル コンピューターから Windows Azure の Web サイトに直接発行する方法について説明します (Windows Azure では、この発行方法は**ローカル Git** と呼ばれます)。また、BitBucket、CodePlex、DropBox、GitHub、Mercurial などのリポジトリ Web サイトからの継続的な展開を有効にする方法についても説明します。継続的な展開に TFS を使用する方法の詳細については、「[Continuous delivery to Windows Azure by using Team Foundation Service (Team Foundation Service を使用した Windows Azure への継続的な配信)]」を参照してください。

<div class="dev-callout">
<strong>注</strong>
<p>この記事に記載されている Git コマンドの多くは、<a href="/ja-jp/develop/nodejs/how-to-guides/command-line-tools/">Mac および Linux 用 Windows Azure コマンド ライン ツール</a>を使用して Web サイトを作成する際に自動的に実行されます。</p>
</div>

このタスクの手順は次のとおりです。

* [Git のインストール](#Step1)
* [ローカル リポジトリの作成](#Step2)
* [Web ページの追加](#Step3)
* [Web サイトのリポジトリの有効化](#Step4)
* [プロジェクトの展開](#Step5)
	* [ローカル ファイルの Windows Azure へのプッシュ (ローカル Git)](#Step6)
	* [BitBucket、CodePlex、Dropbox、GitHub、Mercurial などのリポジトリ Web サイトからのファイルの展開](#Step7)
* [トラブルシューティング](#Step8)

<h2><a id="Step2"></a>Git のインストール</h2>

Git をインストールするために必要な手順は、オペレーティング システムによって異なります。オペレーティング システム固有の配布とインストールのガイダンスについては、「[Installing Git (Git のインストール)]」を参照してください。

<div class="dev-callout">
<strong>注</strong>
<p>オペレーティング システムによっては、コマンド ラインと GUI の両方のバージョンの Git を使用できます。この記事で説明する手順では、コマンド ライン バージョンを使用します。</p>
</div>

<h2><a id="Step2"></a>ローカル リポジトリの作成</h2>

次のタスクを実行して、新しい Git リポジトリを作成します。

1. **GitBash** (Windows) や **Bash** (Unix シェル) などのコマンド ラインを開きます。OS X システムでは、**ターミナル** アプリケーションを使用してコマンド ラインにアクセスできます。

2. コマンド ラインで、Web サイトを作成するディレクトリに変更します。たとえば、「cd needsmoregit」と入力します。

3. 次のコマンドを使用して、新しい Git リポジトリを初期化します。

		git init

	このコマンドでは、"**Initialized empty Git repository in [path]]**" などのメッセージが返されます。

<h2><a id="Step3"></a>Web ページの追加</h2>

Windows Azure の Web サイトでは、さまざまなプログラミング言語で作成されたアプリケーションをサポートしています。この例では、静的 .html ファイルを使用します。他のプログラミング言語の Web サイトを Windows Azure に発行する方法の詳細については、[Windows Azure デベロッパー センター]を参照してください。

1. テキスト エディターを使用して、Git リポジトリのルートに **index.html** という名前の新しいファイルを作成します。コンテンツとして "Hello Git!" を追加し、ファイルを保存します。

2. コマンド ラインで、現在のディレクトリがリポジトリを作成したディレクトリであることを確認し、次のコマンドを使用して、リポジトリに **index.html** ファイルを追加します。

		git add index.html 

3. 次に、次のコマンドを使用して、リポジトリへの変更をコミットします。

		git commit -m "Adding index.html to the repository"

	次のような出力が表示されます。

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>Web サイトのリポジトリの有効化</h2>

Windows Azure ポータルを使用し、次のステップを実行して、Web サイトに対して Git リポジトリを有効にします。

1. [Windows Azure ポータル]にログインします。

2. ページの左側にある **[Web サイト]** をクリックし、リポジトリを有効にする Web サイトを選択します。

	![選択した Web サイトが表示されている画像][portal-select-website]

3. **[概要]** セクションで、**[ソース管理からの展開の設定]** をクリックします。**[展開の設定]** ダイアログ ボックスが表示され、**ソース コードの場所**が求められます。

	![git-WhereIsYourSourceCode][git-WhereIsYourSourceCode]

4. **[ローカル Git]** を選択し、**[次へ]** 矢印をクリックします。

	
5. しばらくすると、リポジトリが準備できたことを示すメッセージが表示されます。
	![Git の手順][git-instructions]

<h2><a id="Step5"></a>プロジェクトの展開</h2>

<h3><a id="Step6"></a>ローカル ファイルの Windows Azure へのプッシュ (ローカル Git)</h3>

既にローカル リポジトリを初期化し、ファイルを追加しているため、ポータルに表示されるステップ 1. およびステップ 2. はスキップします。コマンド ラインを使用して、ディレクトリを Web サイトのディレクトリに変更し、ポータルによって返されたステップ 3. に記載されているコマンドを使用します。次に例を示します。

		git remote add azure http://username@needsmoregit.windowsazure.net/NeedsMoreGit.git


**remote** コマンドは、リモート リポジトリに名前付き参照を追加します。この場合は、Windows Azure の Web サイトのリポジトリに対して "azure" という名前付き参照を作成します。

<h4>Web サイトの発行と再発行</h4>

1. コマンド ラインで次のコマンドを使用して、ローカル リポジトリから "azure" リモートに現在のリポジトリのコンテンツをプッシュします。

		git push azure master

	リポジトリを設定するときに作成したパスワードを入力するように求められます。パスワードを入力すると (パスワードの入力時、Gitbash はコンソールにアスタリスクをエコーしない点に注意してください)、次のような出力が表示されます。

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To http://username@needsmoregit.windowsauzre.net/NeedsMoreGit.git
		* [new branch]		master -> master

	<div class="dev-callout">
	<strong>注</strong>
	<p>Windows Azure の Web サイトに対して作成したリポジトリでは、プッシュ要求のターゲットがそのリポジトリの <strong>master</strong> 分岐であると想定しています。そのため、master 分岐が Web サイトのコンテンツとして使用されます。</p>
	</div>

2. ポータルの下部にある **[参照]** をクリックして、**index.html** が展開されていることを確認します。"Hello Git!" を含むページが表示されます。

	!["Hello Git!" を含む Web ページ][hello-git]

3. テキスト エディターを使用し、**index.html** ファイルに変更を加えて "Yay!" を追加します。次に、ファイルを保存します。

4. コマンド ラインから次のコマンドを使用し、変更を**追加**して**コミット**します。その後で、変更をリモート リポジトリに**プッシュ**します。

		git add index.html
		git commit -m "Celebration"
		git push azure master

	**push** コマンドが完了したら、ブラウザーを更新して (場合によっては、ブラウザーを正しく更新するには Ctrl + F5 キーを押す必要があります)、コミットした最新の変更がページのコンテンツに反映されていることを確認します。

	!["Yay!" を含む Web ページ][yay]

<h3><a id="Step7"></a>BitBucket、CodePlex、Dropbox、GitHub、Mercurial などのリポジトリ Web サイトからのファイルの展開</h3>

ローカル Git を使用してローカル ファイルを Windows Azure にプッシュすると、ローカル プロジェクトから Windows Azure の Web サイトに更新を手動でプッシュできるようになります。一方、BitBucket、CodePlex、Dropbox、GitHub、または Mercurial から展開すると、プロジェクトから最新の更新が Windows Azure によってプルされる継続的な展開 プロセスが有効になります。

どちらの方法でも、プロジェクトが Windows Azure の Web サイトに展開されます。ただし、複数の人がプロジェクトに携わっており、だれが最新の更新を行ったかに関係なく、常に最新バージョンが発行されるようにする場合は、継続的な展開が便利です。また、継続的な展開は、上記のいずれかのツールをアプリケーションの中央リポジトリとして使用する場合にも便利です。

GitHub、CodePlex、BitBucket からファイルを展開するには、これらのサービスのいずれかにローカル プロジェクトを発行している必要があります。これらのサービスにプロジェクトを発行する方法の詳細については、「[Create a Repo (GitHub) (Repo の作成 (GitHub))]」、「[Using Git with CodePlex (CodePlex での Git の使用)]」、「[Create a Repo (BitBucket) (Repo の作成 (BitBucket))]」、「[Using Dropbox to Share Git Repositories (Dropbox を使用した Git リポジトリの共有)]」、「[Quick Start - Mercurial (Mercurial のクイック スタート)]」を参照してください。

1. Web サイト プロジェクトがリポジトリ Web サイトにプッシュされたら、Windows Azure ポータルの **[概要]** セクションで **[ソース管理からの展開の設定]** を選択します。**[展開の設定]** ダイアログ ボックスが表示され、**ソース コードの場所**が求められます。

2. 使用するソース管理方法を選択します。
	
3. 要求されたら、選択したサービスの資格情報を入力します。

4. アカウントへのアクセスを Windows Azure に許可すると、リポジトリの一覧が表示されます。

	![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]
  
5. Windows Azure の Web サイトと関連付けるリポジトリを選択します。チェックマークをクリックして続行します。

	<div class="dev-callout">
	<strong>注</strong>
	<p>GitHub または Bitbucket を使用して継続的な展開を有効にする場合、パブリック プロジェクトとプライベート プロジェクトの両方が表示されます。</p>
</div>

6. Windows Azure によって、選択したリポジトリとの関連付けが作成され、master 分岐からファイルがプルされます。このプロセスが完了すると、**[展開]** ページの **[展開履歴]** に、次のような **[アクティブな展開]** メッセージが表示されます。

	![git-githubdeployed][git-githubdeployed]

7. この時点で、選択したリポジトリから Windows Azure の Web サイトにプロジェクトが展開されました。サイトがアクティブであることを確認するには、ポータルの下部にある **[参照]** リンクをクリックします。ブラウザーには Web サイトが表示されます。

8. 継続的な展開が行われていることを確認するには、プロジェクトに変更を加え、この Web サイトに関連付けたリポジトリに更新をプッシュします。リポジトリへのプッシュが完了すると、すぐに Web サイトは更新され、変更が反映されます。更新がプルされていることは、Web サイトの **[展開]** ページで確認できます。

	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]


<h4>継続的な展開のしくみ</h4>
継続的な展開は、サイトの **[構成]** タブの **[展開]** セクションにある **[展開の開始 URL]** を指定することによって機能します。

![git-DeploymentTrigger][git-DeploymentTrigger]

リポジトリが更新されると、この URL に POST 要求が送信され、リポジトリが更新されたことが Windows Azure の Web サイトに通知されます。この時点で更新を取得し、Web サイトに展開します。

<h4>使用する分岐の指定</h4>

継続的な展開を有効にした場合、既定ではリポジトリの **master** 分岐が使用されます。別の分岐を使用するには、次のステップを実行します。

1. ポータルで、Web サイトを選択し、**[構成]** をクリックします。

2. ページの **[展開]** セクションで、**[展開する分岐]** に使用する分岐を入力し、Enter キーを押します。最後に、**[保存]** をクリックします。

	Windows Azure では、新しい分岐への変更に基づいてすぐに更新を開始します。

<h4>継続的な展開の無効化</h4>

継続的な展開は、Windows Azure の**ダッシュボード**から無効にできます。**[概要]** セクションで、使用しているリポジトリから切断するオプションを選択します。

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]	

別のソースからの発行を設定する場合は、確認メッセージで **[はい]** を選択すると、**[概要]** に戻って **[ソース管理からの展開の設定]** をクリックできます。

<h2><a id="Step8"></a>トラブルシューティング</h2>

Git を使用して Windows Azure の Web サイトに発行する場合に発生する一般的なエラーまたは問題を以下に示します。

****

**症状**: Couldn't resolve host 'hostname'

**原因**: このエラーは、"azure" リモートを作成するときに入力したアドレス情報が間違っている場合に発生します。

**解決策**: "git remote -v" コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。"azure" リモートの URL が正しいことを確認します。必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。

****

**症状**: No refs in common and none specified; doing nothing.Perhaps you should specify a branch such as 'master'.

**原因**: このエラーは、git push 操作を実行するときに分岐を指定せず、Git で使用される push.default 値を設定していない場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。次に例を示します。

	git push azure master

****

**Symptom**: src refspec [branchname] does not match any.

**原因**: このエラーは、"azure" リモートの master 以外の分岐にプッシュしようとした場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。次に例を示します。

	git push azure master

****

**症状**: Error - Changes commited to remote repository but your web site not updated.

**原因**: このエラーは、必要な追加モジュールを指定する package.json ファイルを含む Node.js アプリケーションを展開する場合に発生します。

**解決策**: このエラーの前に "npm ERR!" を含む追加のメッセージがログに記録されます。このメッセージによって、このエラーに関する追加のコンテキストが提供される場合があります。このエラーの既知の原因と、対応する "npm ERR!" メッセージを以下に示します。

***形式が正しくない package.json ファイル**: npm ERR!Couldn't read dependencies.

***Windows 用のバイナリ配布がないネイティブ モジュール**:

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		または

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## その他のリソース

* [Windows Azure 用の PowerShell を使用する方法に関するページ]
* [Mac および Linux 用 Windows Azure コマンド ライン ツールの使用方法]
* [Git に関するドキュメント]

[Windows Azure デベロッパー センター]: http://www.windowsazure.com/ja-jp/develop/overview/
[Windows Azure ポータル]: http://manage.windowsazure.com
[Git website (Git の Web サイト)]: http://git-scm.com
[Git のインストール]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[How to use PowerShell for Windows Azure (Windows Azure 用 PowerShell の使用方法)]: http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/powershell-cmdlets/
[How to use the Windows Azure Command-Line Tools for Mac and Linux (Mac および Linux 用 Windows Azure コマンド ライン ツールの使用方法)]: /ja-jp/develop/nodejs/how-to-guides/command-line-tools/
[Git Documentation (Git のドキュメント)]: http://git-scm.com/documentation

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
[Quick Start - Mercurial (Mercurial のクイック スタート)]: http://mercurial.selenic.com/wiki/QuickStart
[Using Dropbox to Share Git Repositories (Dropbox を使用した Git リポジトリの共有)]: https://gist.github.com/trey/2722927
[Continuous delivery to Windows Azure by using Team Foundation Service (Team Foundation Service を使用した Windows Azure への継続的な配信)]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/publishing-with-tfs/

