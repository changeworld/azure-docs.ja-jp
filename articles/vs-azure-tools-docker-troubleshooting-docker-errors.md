<properties
   pageTitle="Visual Studio を使用した Windows での Docker クライアント エラーのトラブルシューティング | Microsoft Azure"
   description="Visual Studio を使用して、Windows 上で Web アプリを作成し、Docker にデプロイする際に発生する問題のトラブルシューティングを行います。"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
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

# Docker エラーのトラブルシューティング

Docker Preview 用 Visual Studio ツールを使用する場合は、プレビューの性質によりいくつかの問題が発生する可能性があります。いくつかの一般的な問題と解決策を次に示します。


## ボリューム マッピングを検証できない
ボリューム マッピングは、アプリケーションのソース コードやバイナリをコンテナー内のアプリケーション フォルダーと共有するために欠かせない関連付けです。個々のボリューム マッピングは、docker-compose.dev.debug.yml ファイルと docker-compose.dev.release.yml ファイルに格納されます。ホスト コンピューター上でファイルに変更が加えられると、その変更がコンテナーで同様のフォルダー構造で反映されます。

ボリューム マッピングを有効にするには、Docker For Windows の "moby (クジラ)" トレイ アイコンから **[Settings (設定)]** を開き、**[Shared Drives (ドライブ共有)]** タブを選択します。プロジェクトをホストするドライブ文字と、%USERPROFILE% が存在するドライブ文字の横にあるチェック ボックスをオンにして、**[Apply (適用)]** をクリックします。これで、これらのドライブが共有されます。

ボリューム マッピングが正常に機能しているかどうかをテストするには、ドライブが共有された後で、Visual Studio 内でリビルドを実行するか F5 キーを押します。または、コマンド プロンプトから次のコマンドを実行します。

*Windows コマンド プロンプトで*

*[注: この操作は、Users フォルダー が "C" ドライブ上にあり、共有されていることを前提としています。別のドライブを共有した場合は、必要に応じて変更してください]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Linux コンテナーで*

```
/ # ls
```

ユーザー/パブリック フォルダーのディレクトリ一覧が表示されます。/C/ユーザー/パブリック フォルダーが空でないにもかかわらず、ファイルが表示されない場合は、ボリューム マッピングが正しく構成されていません。

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

ワームホール ディレクトリに移動して、`/c/Users/Public` ディレクトリの内容を確認します。

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**注:** *Linux VM を使用する場合、コンテナーのファイル システムでは大文字と小文字が区別されます*。

##ビルド: "PrepareForBuild" タスクが予期せずに失敗しました。

Microsoft.DotNet.Docker.CommandLine.ClientException: 接続中にエラーが発生しました:

既定の Docker ホストが実行されていることを確認します。コマンド プロンプトを開き、次のコマンドを実行します。

```
docker info
```

エラーが返される場合は、**Docker For Windows** デスクトップ アプリを起動してください。デスクトップ アプリが実行されているときは、トレイに **moby** アイコンが表示されます。このトレイ アイコンを右クリックし、**[Settings (設定)]** を開きます。**[Reset (リセット)]** タブ、**[Restart Docker (Docker の再起動)]** の順にクリックします。

##バージョンを 0.31 から 0.40 に手動でアップグレードする方法


1. プロジェクトのバックアップを作成します。
1. プロジェクト内にある次のファイルを削除します。

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. ソリューションを閉じ、.xproj ファイルから次の行を削除します。

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. ソリューションをもう一度開きます。
1. Properties\\launchSettings.json ファイルから次の行を削除します。

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. publishOptions 内の project.json から Docker に関連する次のファイルを削除します。

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. 以前のバージョンをアンインストールし、Docker Tools 0.40 をインストールしてから、もう一度 ASP.NET Core Web アプリケーションまたはコンソール アプリケーションのコンテキスト メニューから、**[追加]、[Docker Support (Docker サポート)]** の順に選択します。これにより、必要な Docker アーティファクトが元のプロジェクトに新たに追加されます。

## **[追加]、[Docker Support (Docker サポート)]** を選択すると、またはコンテナー内の ASP.NET Core アプリケーションのデバッグ (F5) を実行すると、エラー ダイアログが表示される

拡張機能をアンインストールしてインストールすると、Visual Studio の MEF (Managed Extensibility Framework) キャッシュが破損することがあります。このキャッシュが破損すると、Docker サポートを追加する際や、ASP.NET Core アプリケーションのデバッグ (F5) または実行の際に、エラー ダイアログが表示される可能性があります。一時的な回避策として、次の手順に従って、MEF キャッシュを削除してからもう一度生成してください。

1. Visual Studio のすべてのインスタンスを閉じます。
1. %USERPROFILE%\\AppData\\Local\\Microsoft\\VisualStudio\\14.0\\ を開きます。
1. 次のフォルダーを削除します。
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Visual Studio を開きます
1. 目的の操作をもう一度実行します。

<!---HONumber=AcomDC_0921_2016-->