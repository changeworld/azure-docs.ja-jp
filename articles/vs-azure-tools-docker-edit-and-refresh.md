<properties
   pageTitle="ローカルの Docker コンテナーでアプリをデバッグする | Microsoft Azure"
   description="編集と更新を使用して、ローカルの Docker コンテナーで実行されているアプリに変更を加え、デバッグのブレークポイントを設定する方法について説明します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# ローカルの Docker コンテナーでアプリをデバッグする

## 概要
Visual Studio Tools for Docker を使用すると、一貫した方法でアプリケーションの開発と検証を Linux Docker コンテナーでローカルで実行できます。コード変更のたびにコンテナーを再起動する必要はありません。この記事では、"編集と更新" の機能を使用して、ローカルの Docker コンテナーで ASP.NET Core Web アプリを起動し、必要な変更を行い、その変更を反映するためにブラウザーの表示を更新する方法について説明します。デバッグ用のブレークポイントを設定する方法についても説明します。

> [AZURE.NOTE] Windows コンテナーのサポートは今後のリリースで開始する予定です

## 前提条件
次のツールをインストールする必要があります。

- [Visual Studio 2015 Update 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Microsoft ASP .NET Core RC 2](http://go.microsoft.com/fwlink/?LinkId=798481)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)

ローカルで Docker コンテナーを実行するには、ローカルの Docker クライアントが必要です。Hyper-V を無効にして、リリース済みの [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox) を使用するか、または Hyper-V を使用し、Windows 10 が必要な [Docker for Windows のベータ版](https://beta.docker.com)を使用することができます。

Docker Toolbox を使用する場合は、[Docker クライアントを構成する](./vs-azure-tools-docker-setup.md)必要があります

## ローカルの Docker コンテナーで実行されているアプリを編集する
ASP .NET Core RC2 Web アプリの開発に Visual Studio 2015 Tools for Docker を使用すると、Docker コンテナーでアプリケーションをテストして実行し、Visual Studio でアプリケーションに変更を加えた後、ブラウザーの表示を更新して、コンテナー内で実行されているアプリに加えた変更を反映することができます。.NET Core と Visual Studio Tools for Docker バージョン 0.20 により、Docker コンテナーで実行されるコードのブレークポイントを設定することもできます。

1. Visual Studio のメニューで、**[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。

1. **[新しいプロジェクト]** ダイアログ ボックスの **[テンプレート]** セクションで、**[Visual C#]、[Web]** の順に選択します。

1. **ASP.NET Core Web アプリケーション (.NET Core)** を選択します。

1. 新しいアプリケーションに名前を設定 (または、既定の名前をそのまま使用) して、**[OK]** をタップします。

1. **[ASP.NET Core テンプレート]** で、**[Web アプリケーション]** を選択して、**[OK]** をタップします。

1. Docker をデプロイ ソリューションで使用するため、**[クラウドでのホスト]** の選択を解除します。

1. Visual Studio のソリューション エクスプローラーで、プロジェクトを右クリックし、**[追加]、[Docker サポート]** の順に選択します。

	![][0]

1. プロジェクト ノードに次のファイルが作成されます。

	![][1]

> [AZURE.NOTE] [Docker for Windows のベータ版](https://beta.docker.com)を使用している場合は、Properties\\Docker.props を開き、既定値を削除して、Visual Studio を再起動すると、値が反映されます。
>
> ![][2]

##編集と更新
変更をすばやく反復処理するには、コンテナー内でアプリケーションを起動して変更を行うと、IIS Express を使用する場合のように表示できます。

1. ソリューション構成を `Debug` に設定して **Ctrl + F5** を押すと、Docker イメージが作成され、ローカルで実行されます。

    コンテナー イメージが作成されて Docker コンテナーで実行されると、Visual Studio は既定のブラウザーでその Web アプリを起動します。Microsoft Edge ブラウザーを使用している場合、またはエラーが発生している場合は、「[トラブルシューティング](vs-azure-tools-docker-troubleshooting-docker-errors.md)」セクションを参照してください。

1. [About] ページに移動して、変更を加えます。

1. Visual Studio に戻り、`Views\Home\About.cshtml` を開きます。

1. ファイルの最後に次の HTML コンテンツを追加し、変更を保存します。

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	出力ウィンドウで .NET ビルドが完了したことを確認し、次の行が表示されたら、お使いのブラウザーに戻り、[About] ページを更新します。

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.	変更が適用されました。

##ブレークポイントのデバッグ
多くの場合、変更にはさらなる検査が必要になります。この場合、Visual Studio のデバッグ機能を活用します。

1.	Visual Studio に戻り、`Controllers\HomeController.cs` を開きます。

1.  About() メソッドの内容を次のように置き換えます。

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  ブレークポイントを `string message`... 行の左側に設定します。

1.  **F5** キーを押して、デバッグを開始します。

1.  [About] ページに移動して、ブレークポイントを探します。

1.  Visual Studio に切り替えてブレークポイントを表示し、メッセージの値を検査します。

	![][3]

##概要
[Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS) により、Docker コンテナー内での開発の生産性が実際に向上するため、ローカル作業の生産性が上がります。

## トラブルシューティング
[Docker エラーのトラブルシューティング](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Visual Studio、Windows、および Azure での Docker の詳細について

- [Visual Studio 用 Docker ツール](http://aka.ms/dockertoolsforvs) - コンテナー内での .NET Core コードの開発
- [Visual Studio Team Services 用 Docker ツール](http://aka.ms/dockertoolsforvsts) - Docker コンテナーの構築およびデプロイ
- [Visual Studio Code 用 Docker ツール](http://aka.ms/dockertoolsforvscode) - Docker ファイルを編集するための言語サービス (e2e シナリオは増加予定)
- [Windows コンテナー情報](http://aka.ms/containers) - Windows Server および Nano Server の情報
- [Azure コンテナー サービス](https://azure.microsoft.com/services/container-service/) - [Azure コンテナー サービスの内容](http://aka.ms/AzureContainerService)

## さまざまな Docker ツール

[Some great docker tools (Steve Lasker's blog) (いくつかの優れた Docker ツール (Steve Lasker のブログ))](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## お勧めの記事

[Introduction to Microservices from NGINX (Nginx のマイクロサービスの概要)](https://www.nginx.com/blog/introduction-to-microservices/)

## プレゼンテーション

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Steve Lasker: VS Live ラスベガス 2016 - Docker e2e)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo (ASP.NET Core の概要 @ ビルド 2016 - デモンストレーション)](https://channel9.msdn.com/Events/Build/2016/B810)
- [コンテナーでの .NET アプリの開発、Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-props.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0608_2016-->