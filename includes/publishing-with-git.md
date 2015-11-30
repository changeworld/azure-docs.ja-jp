[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) では、BitBucket、CodePlex、Dropbox、Git、GitHub、Mercurial、TFS など、ソース コード管理やリポジトリ ツールから Web Apps への継続的なデプロイをサポートしています。これらのツールを使用すると、アプリケーションのコンテンツとコードをメンテナンスすることができ、必要なときに変更を Azure Web アプリにすばやく簡単にプッシュできます。

この記事では、Git を使用してローカル コンピューターから Web Apps に直接発行する方法について説明します (Azure では、この発行方法は**ローカル Git** と呼ばれます)。また、BitBucket、CodePlex、Dropbox、GitHub、Mercurial などのリポジトリ サイトからの継続的なデプロイを有効にする方法についても説明します。継続的なデプロイメントに TFS を使用する方法の詳細については、「[Visual Studio Team Services を使用した Azure への継続的な配信]」を参照してください。

> [AZURE.NOTE]この記事に記載されている Git コマンドの多くは、[Mac および Linux 用 Azure コマンド ライン ツール](/develop/nodejs/how-to-guides/command-line-tools/)を使用して Web アプリを作成する際に自動的に実行されます。

## <a id="Step1"></a>手順 1: Git のインストール

Git をインストールするために必要な手順は、オペレーティング システムによって異なります。オペレーティング システム固有の配布とインストールのガイダンスについては、「[Installing Git (Git のインストール)]」を参照してください。

> [AZURE.NOTE]オペレーティング システムによっては、コマンド ラインと GUI の両方のバージョンの Git を使用できます。この記事で説明する手順では、コマンド ライン バージョンを使用します。

## <a id="Step2"></a>手順 2: ローカル リポジトリの作成

次のタスクを実行して、新しい Git リポジトリを作成します。

1. Git リポジトリと Web アプリのファイルを格納するディレクトリを MyGitRepository という名前で作成します。

2. **GitBash** (Windows) や **Bash** (Unix シェル) などのコマンド ライン ツールを開きます。OS X システムでは、**ターミナル** アプリケーションを使用してコマンド ラインにアクセスできます。

3. コマンド ラインで、MyGitRepository ディレクトリに移動します。

		cd MyGitRepository

4. 次のコマンドを使用して、新しい Git リポジトリを初期化します。

		git init

	このコマンドでは、"**Initialized empty Git repository in [path]**" などのメッセージが返されます。

## <a id="Step3"></a>手順 3: Web ページの追加

Web Apps では、さまざまなプログラミング言語で作成されたアプリケーションをサポートしています。この例では、静的 .html ファイルを使用します。

1. テキスト エディターを使用して、Git リポジトリ (先ほど作成した MyGitRepository ディレクトリ) のルートに **index.html** という名前の新しいファイルを作成します。

2. index.html ファイルの内容として次のテキストを追加し、ファイルを保存します。

		Hello Git!

3. コマンド ラインで、現在の位置が Git リポジトリのルートであることを確認します。その後、次のコマンドを使用して **index.html** ファイルをリポジトリに追加します。

		git add index.html 

	> [AZURE.NOTE]git コマンドのヘルプを見つけるには、コマンドの後に「-help」または「--help」と入力します。たとえば、add コマンドのパラメーターのオプションを見つけるには、「git add -help」と入力します。より詳細なヘルプを見つけるには、「git add --help」と入力します。

4. 次に、次のコマンドを使用して、リポジトリへの変更をコミットします。

		git commit -m "Adding index.html to the repository"

	次のような出力が表示されます。

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

## <a id="Step4"></a>Web アプリのリポジトリの有効化

次に示している手順を実行して、Web アプリに対して Git リポジトリを有効にします。

1. [Azure プレビュー ポータル]にログインします。

2. Web アプリのブレードで、**[デプロイ]** セクションにスクロールし、**[継続的なデプロイの設定]** をクリックします。**[ソースの選択]** をクリックし、**[ローカル Git リポジトリ]** をクリックして **[OK]** をクリックします。

	![ローカルの Git リポジトリ](./media/publishing-with-git/azure1-local-git.png)

4. Azure で初めてリポジトリを設定した場合、そのログイン資格情報を作成する必要があります。それらを使用して、Azure リポジトリにログインし、ローカル Git リポジトリから変更をプッシュします。Web アプリのブレードで **[設定]、[デプロイ資格情報]** の順にクリックし、デプロイ用のユーザー名とパスワードを構成します。完了したら **[OK]** をクリックします。

	![](./media/publishing-with-git/azure2-credentials.png)

## <a id="Step5"></a>プロジェクトのデプロイ

* [ローカル ファイルの Azure へのプッシュ (ローカル Git)](#Step6)
* [BitBucket、CodePlex、Dropbox、GitHub、Mercurial などのリポジトリ Web サイトからのファイルのデプロイ](#Step7)
* [BitBucket、CodePlex、Dropbox、GitHub、Mercurial からの Visual Studio ソリューションのデプロイ](#Step75)

次に示している手順に従って、ローカル Git を使用して Web アプリを Azure に発行します。

1. Web アプリのブレードの [デプロイ] セクションで、**[デプロイが見つかりませんでした]** をクリックします。

	![](./media/publishing-with-git/azure3-repo-details.png)

	**Git URL** は、ローカル リポジトリからデプロイする際のリモート参照です。この URL は次の手順で使用します。

1. コマンド ラインで現在の位置が、先ほど index.html ファイルを作成したローカル Git リポジトリのルートであることを確認します。

2. `git remote` を使用して、手順 1 の **[Git の URL]** にリストされたリモート参照を追加します。コマンドは次のようになります。

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE]この **remote** コマンドは、名前付きの参照をリモート リポジトリに追加します。この例では、Web アプリのリポジトリに「azure」という名前の参照を作成しています。

1. コマンド ラインで次のコマンドを使用して、ローカル リポジトリから "azure" リモートに現在のリポジトリのコンテンツをプッシュします。

		git push azure master

	ポータルでデプロイメント資格情報をリセットしたときに作成したパスワードの入力を求められます。パスワードを入力します (パスワードを入力しても Gitbash によってコンソールにアスタリスクはエコーされません)。次のような出力が表示されます。

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

	> [AZURE.NOTE]Web アプリに対して作成したリポジトリでは、プッシュ要求のターゲットがそのリポジトリの <strong>master</strong> 分岐であると想定しています。そのため、この分岐が Web アプリのコンテンツとして使用されます。

2. Azure ポータルの Web アプリのブレードに戻ります。**[デプロイが見つかりませんでした]** が **[アクティブなデプロイ]** に変わり、直近のプッシュのログ エントリが表示されます。

	![](./media/publishing-with-git/azure4-deployed.png)

2. Web アプリ ブレードの上部にある **URL** をクリックして、**index.html** がデプロイされていることを確認します。"Hello Git!" を含むページが表示されます。

	!["Hello Git!" を含む Web ページ][hello-git]

3. テキスト エディターを使用し、**index.html** ファイルに変更を加えて "Yay!" を追加します。次に、ファイルを保存します。

4. コマンド ラインから次のコマンドを使用し、変更を**追加**して**コミット**します。その後で、変更をリモート リポジトリに**プッシュ**します。

		git add index.html
		git commit -m "Celebration"
		git push azure master

	**push** コマンドが完了したら、ブラウザーを更新して (場合によっては、ブラウザーを正しく更新するには Ctrl + F5 キーを押す必要があります)、コミットした最新の変更がページのコンテンツに反映されていることを確認します。

### <a id="Step7"></a>BitBucket、CodePlex、Dropbox、GitHub、Mercurial などのリポジトリ サイトからのファイルのデプロイ

ローカル Git を使用してローカル ファイルを Azure にプッシュすると、ローカル プロジェクトから Azure の Web アプリに更新を手動でプッシュできるようになります。一方、BitBucket、CodePlex、Dropbox、GitHub、または Mercurial からデプロイすると、プロジェクトから最新の更新が Azure によってプルされる継続的なデプロイ プロセスが有効になります。

どちらの方法でも、プロジェクトが Web Apps にデプロイされます。ただし、複数の人がプロジェクトに携わっており、だれが最新の更新を行ったかに関係なく、常に最新バージョンが発行されるようにする場合は、継続的なデプロイが便利です。また、継続的なデプロイメントは、上記のいずれかのツールをアプリケーションの中央リポジトリとして使用する場合にも便利です。

GitHub、CodePlex、BitBucket からファイルを展開するには、これらのサービスのいずれかにローカル プロジェクトを発行している必要があります。これらのサービスにプロジェクトを発行する方法の詳細については、「[Create a Repo (GitHub) (Repo の作成 (GitHub))]」、「[Using Git with CodePlex (CodePlex での Git の使用)]」、「[Create a Repo (BitBucket) (Repo の作成 (BitBucket))]」、「[Using Dropbox to Share Git Repositories (Dropbox を使用した Git リポジトリの共有)]」、「[Quick Start - Mercurial (Mercurial のクイック スタート)]」を参照してください。

1. まず、継続的なデプロイ用に選択したリポジトリに Web アプリのファイルを配置します。

2. ポータルの Web アプリのブレードで、**[デプロイ]** セクションにスクロールし、**[継続的なデプロイの設定]** をクリックします。**[ソースの選択]** をクリックし、(たとえば) **[GitHub]** をクリックします。

	![](./media/publishing-with-git/azure6-setup-github.png)
	
2. **[継続的なデプロイ]** ブレードで、**[承認]** をクリックした後**、[承認する]** をクリックします。承認プロセスを完了するため、Azure ポータルからリポジトリ サイトにリダイレクトされます。

4. 完了して Azure ポータルに戻ったら、**[承認]** ブレードで **[OK]** をクリックします。

5. **[継続的なデプロイ]** ブレードで、デプロイ元の組織、プロジェクト、および分岐を選択します。完了したら **[OK]** をクリックします。
  
	![](./media/publishing-with-git/azure7-setup-github-configure.png)

	> [AZURE.NOTE]GitHub または Bitbucket を使用して継続的なデプロイメントを有効にする場合、パブリック プロジェクトとプライベート プロジェクトの両方が表示されます。

Azure によって、選択したリポジトリとの関連付けが作成され、指定された分岐からファイルがプルされます。このプロセスが完了したら、Web アプリのブレードの **[デプロイ]** セクションに、デプロイが成功したことを示す** [アクティブなデプロイ]** メッセージが表示されます。

7. この時点で、選択したリポジトリから Web アプリにプロジェクトがデプロイされました。Web アプリがアクティブであることを確認するために、ポータルの上部にある **[URL]** をクリックします。ブラウザーに Web アプリが表示されます。

8. 継続的なデプロイが選択したリポジトリから実行されていることを確認するために、変更をリポジトリにプッシュします。リポジトリへのプッシュが完了すると、すぐに Web アプリは更新され、変更が反映されます。更新がプルされていることは、Web アプリの **[デプロイ]** ブレードで確認できます。

### <a id="Step75"></a>BitBucket、CodePlex、Dropbox、GitHub、Mercurial からの Visual Studio ソリューションのデプロイ

Visual Studio ソリューションを Azure App Service の Web アプリにプッシュすることは、単純な index.html ファイルをプッシュすることと同じくらい簡単です。Web Apps のデプロイ プロセスでは、NuGet 依存関係の復元やアプリケーション バイナリの構築などのすべての詳細が合理化されます。Git リポジトリでコードのみを維持し、Web Apps デプロイメントで残りを処理する、ソース管理のベスト プラクティスに従うことができます。

Visual Studio ソリューションを Web Apps にプッシュする手順は、ソリューションとリポジトリを次のように構成するのであれば、[前のセクション](#Step7)と同じです。

-	リポジトリのルートで、`.gitignore` ファイルを追加してから、`Obj`、`Bin`、`packages` フォルダーなど、リポジトリから除外するすべてのファイルやフォルダーを指定します (形式については、「[gitignore のドキュメント](http://git-scm.com/docs/gitignore)」をご覧ください)。次に例を示します。

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[AZURE.NOTE]GitHub を使用する場合、リポジトリの作成時に、必要に応じて Visual Studio 固有の .gitignore ファイルを生成できます。これには、すべての一般的な一時ファイル、ビルド結果などが含まれます。ファイルは、特定のニーズに合わせてカスタマイズできます。

-	.sln ファイルをリポジトリ ルートに入れて、ソリューションのディレクトリ ツリー全体をリポジトリに追加します。

-	Visual Studio ソリューションで、[NuGet パッケージの復元を有効にして](http://docs.nuget.org/Consume/Package-Restore)、Visual Studio が自動的に失われたパッケージを復元できるよにします。

説明のとおりにリポジトリを設定し、いずれかのオンライン Git リポジトリからの継続的な発行のために Azure の Web アプリを構成したら、Visual Studio で ASP.NET アプリケーションをローカルで開発し、オンライン Git リポジトリに変更をプッシュするだけで、コードを継続的にデプロイできます。

## 継続的なデプロイの無効化

継続的なデプロイメントは、**[デプロイ]** ブレードから無効にできます。Web アプリのブレードの **[デプロイ]** セクションで、**[アクティブなデプロイ]** をクリックします。次に、**[切断]** をクリックします。

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)

別のソースからの発行を設定する場合は、確認メッセージで **[はい]** を選択した後、Web アプリのブレードに戻り、**[継続的なデプロイの設定]** をクリックできます。

## <a id="Step8"></a>トラブルシューティング

Git を使用して Azure の Web アプリに発行する場合に発生する一般的なエラーまたは問題を以下に示します。

****

**症状**: '[siteURL]' にアクセスできません: [scmAddress] に接続できませんでした

**原因**: このエラーは Web アプリが実行されていない場合に発生する可能性があります。

**解決策**: Azure ポータルで Web アプリを起動します。Git デプロイは Web アプリが実行されていない限り機能しません。


****

**症状**: ホスト 'hostname' を解決できませんでした

**原因**: このエラーは、"azure" リモートを作成するときに入力したアドレス情報が間違っている場合に発生します。

**解決策**: `git remote -v` コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。"azure" リモートの URL が正しいことを確認します。必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。

****

**症状**: refs がなく、何も指定されていません。何も処理を行いません。'master' などの分岐を指定する必要があるものと思われます.

**原因**: このエラーは、git push 操作を実行するときに分岐を指定せず、Git で使用される push.default 値を設定していない場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。次に例を示します。

	git push azure master

****

**症状**: src refspec [branchname] と一致するものがありません。

**原因**: このエラーは、"azure" リモートの master 以外の分岐にプッシュしようとした場合に発生します。

**解決策**: master 分岐を指定して、もう一度 push 操作を実行します。次に例を示します。

	git push azure master

****

**症状**: エラー - 変更がリモート リポジトリにコミットされましたが、Web アプリは更新されていません。

**原因**: このエラーは、必要な追加モジュールを指定する package.json ファイルを含む Node.js アプリケーションをデプロイする場合に発生します。

**解決策**: このエラーの前に "npm ERR!" を含む追加のメッセージがログに記録されます。このメッセージによって、このエラーに関する追加のコンテキストが提供される場合があります。このエラーの既知の原因と、対応する "npm ERR!" メッセージを以下に示します。

* **形式が正しくない package.json ファイル**: npm ERR! Couldn't read dependencies.

* **Windows 用のバイナリ配布がないネイティブ モジュール**:

	* npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1

		または

	* npm ERR! [modulename@version] preinstall: `make || gmake`


## その他のリソース

* [How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)]
* [Mac および Linux 用 Azure コマンド ライン ツールの使用方法]
* [Git に関するドキュメント]
* [プロジェクト Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[Azure プレビュー ポータル]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git (Git のインストール)]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)]: ../articles/install-configure-powershell.md
[Mac および Linux 用 Azure コマンド ライン ツールの使用方法]: ../articles/xplat-cli-install.md
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
[Quick Start - Mercurial (Mercurial のクイック スタート)]: http://mercurial.selenic.com/wiki/QuickStart
[Using Dropbox to Share Git Repositories (Dropbox を使用した Git リポジトリの共有)]: https://gist.github.com/trey/2722927
[Visual Studio Team Services を使用した Azure への継続的な配信]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!---HONumber=Nov15_HO4-->