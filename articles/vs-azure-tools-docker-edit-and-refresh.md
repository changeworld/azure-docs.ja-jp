<properties
   pageTitle="編集と更新の機能を使用して、ローカルの Docker コンテナーで実行されているライブ ASP.NET 5 Web アプリケーションに変更を加える | Microsoft Azure"
   description="編集と更新を使用して、ローカルの Docker コンテナーで実行されているアプリケーションに変更を加え、コンテナーを更新する方法について説明します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="02/17/2016"
   ms.author="tarcher" />

# 編集と更新の機能を使用して、ローカルの Docker コンテナーで実行されているライブ ASP.NET 5 Web アプリケーションに変更を加える

## 概要
Visual Studio Tools for Docker を使用すると、アプリケーションの開発とテストを Docker コンテナーでローカルで実行でき、しかも、コードに変更を加えるたびにコンテナーを再起動する必要がありません。この記事では、"編集と更新" の機能を使用して、ローカルの Docker コンテナーで ASP.NET 5 Web アプリを起動し、必要な変更を行い、その変更を反映するためにブラウザーの表示を更新する方法について説明します。

## 前提条件
次のツールをインストールする必要があります。

- [Visual Studio 2015 Update 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP .NET and Web Tools 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)

> [AZURE.NOTE] Visual Studio 2015 Tools for Docker の以前のバージョンがインストールされている場合は、最新のバージョンをインストールする前に、コントロール パネルで以前のバージョンをアンインストールする必要があります。

## Docker クライアントを構成してテストする 
このセクションでは、Docker マシンの既定のインスタンスが構成されて実行されていることを確認する手順について説明します。

1. 既定の Docker ホスト インスタンスを作成するために、コマンド プロンプトで次のコマンドを発行します。

		docker-machine create --driver virtualbox default
 
1. 既定のインスタンスが構成され、実行されていることを確認するために、コマンド プロンプトで次のコマンドを発行します (通常は、"default" という名前のインスタンスが実行されていることがわかります)。

		docker-machine ls 
		
	![][0]
 
1. 現在のホストを既定として設定するために、コマンド プロンプトで次のコマンドを発行します。

		docker-machine env default

1. 次のコマンドを発行して、シェルを構成します。

		FOR /f "tokens=*" %i IN ('docker-machine env default') DO %i

1. 次のコマンドを発行すると、実行中のアクティブなコンテナーから返された空の応答が表示されます。

		docker ps

	![][1]
 
> [AZURE.NOTE] 開発用コンピューターを再起動するたびに、ローカルの Docker ホストを再起動する必要があります。これを行うには、コマンド プロンプトで、次のコマンドを発行します。`docker-machine start default`

## ローカルの Docker コンテナーで実行されているアプリを編集する
ASP .NET 5 Web アプリの開発に Visual Studio 2015 Tools for Docker を使用すると、Docker コンテナーでアプリケーションをテストして実行し、Visual Studio でアプリケーションに変更を加えた後、ブラウザーの表示を更新して、コンテナー内で実行されているアプリに加えた変更を反映することができます。

1. Visual Studio のメニューで、**[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。 

1. **[新しいプロジェクト]** ダイアログ ボックスの **[テンプレート]** セクションで、**[Visual C#]、[Web]** の順に選択します。

1. **[ASP.NET Web アプリケーション]** を選択します。

1. 新しいアプリケーションに名前を設定します (または、既定の名前をそのまま使用します)。

1. **[OK]** をタップします。

1. **[ASP.NET 5 テンプレート]** で **[ASP.NET Web アプリケーション]** を選択します。

1. **[OK]** をタップします。

1. Visual Studio のソリューション エクスプローラーで、プロジェクトを右クリックし、**[追加]、[Docer サポート]** の順に選択します。

	![][2]
 
1. プロジェクト ノードに次のファイルが作成されます。

	![][3]

1. ソリューション構成を `Debug` に設定し、**F5** キーを押して、ローカルでアプリケーションのテストを開始します。

1. コンテナー イメージが作成されて Docker コンテナーで実行されると、PowerShell コンソールは既定のブラウザーでその Web アプリの起動を試みます。Microsoft Edge ブラウザーを使用している場合は、「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

1. Visual Studio に戻り、`Views\Home\Index.cshtml` を開きます。

1. ファイルの最後に次の HTML コンテンツを追加し、変更を保存します。

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	ブラウザーに戻り、表示を更新します。

1.	ホーム ページの一番下までスクロールし、変更が反映されていることを確認します。 サイトの再コンパイルに数秒かかる場合があるため、変更がすぐに反映されない場合はブラウザーの表示をもう一度更新してください。

##トラブルシューティング 

- **アプリケーションを実行すると、PowerShell が開き、エラーを表示した後に閉じる。ブラウザーのページは開かない。**

	これは、`docker-compose-up` の実行中のエラーであると考えられます。このエラーを表示するには、次の手順を実行します。

	1. `Properties\launchSettings.json` ファイルを開きます。
	
	1. Docker エントリを見つけます。
	
	1. 次の内容で始まる行を見つけます。

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. 次のように、この行に `-noexit` パラメーターを追加します。これにより、PowerShell が開いたままになり、エラーを確認できるようになります。

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **"Build : Failed to build the image"、"Error checking TLS connection: Host is not running" と表示される**

	既定の Docker ホストが実行されていることを確認します。手順については、「[Docker クライアントを構成してテストする](#configuring-the-docker-client)」セクションを参照してください。

- **ボリューム マッピングが見つからない**

	既定では、VirtualBox は `C:\Users` を `c:/Users` として共有します。プロジェクトが `c:\Users` にない場合は、手動で VirtualBox の[共有フォルダー](https://www.virtualbox.org/manual/ch04.html#sharedfolders)に追加してください。

- **Microsoft Edge を既定のブラウザーとして使用している**

	Microsoft Edge ブラウザーを使用している場合、IP アドレスがセキュリティで保護されていないと見なされるため、サイトが表示されないことがあります。この問題を解決するには、次の手順を実行します。1.Windows の [ファイル名を指定して実行] ボックスに「`Internet Options`」と入力します。2.**[インターネット オプション]** が表示されたらタップします。2.**[セキュリティ]** タブをタップします。3.**[ローカル イントラネット]** ゾーンを選択します。4.**[サイト]** をタップします。5.一覧に仮想マシンの IP (この場合は Docker ホスト) を追加します。6.Edge でページを更新すると、サイトが実行されていることがわかります。7.この問題の詳細については、Scott Hanselman のブログ記事「[Microsoft Edge can't see or open VirtualBox-hosted local web sites (VirtualBox にホストされたローカル Web サイトを Microsoft Edge で表示したり開いたりできない)](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx)」を参照してください。

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-ps.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png

<!---HONumber=AcomDC_0218_2016-->