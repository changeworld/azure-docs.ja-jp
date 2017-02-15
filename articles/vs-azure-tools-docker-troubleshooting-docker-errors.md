---
title: "Visual Studio を使用した Windows での Docker クライアント エラーのトラブルシューティング | Microsoft Docs"
description: "Visual Studio を使用して、Windows 上で Web アプリを作成し、Docker にデプロイする際に発生する問題のトラブルシューティングを行います。"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 01c10d04606e15082c8842ad87f617703a00c903
ms.openlocfilehash: cbb376dae88d39e965838de74d90158691b59f90


---

# <a name="troubleshooting-visual-studio-docker-development"></a>Docker エラーのトラブルシューティング

Docker Preview 用 Visual Studio ツールを使用する場合は、プレビューの性質によりいくつかの問題が発生する可能性があります。
いくつかの一般的な問題と解決策を次に示します。  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux コンテナー**

###  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>.NET Core Web またはコンソール アプリケーションをデバッグする際にビルド エラーが発生する。  

これは、プロジェクトが存在するドライブが Docker For Windows と共有されていない場合に発生すると考えられます。  次のようなエラーが表示される場合があります。

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
解決するには、システム トレイの Docker for Windows アイコンを右クリックして、[Settings... (選択)] を選択します。  [Shared Drives (共有ドライブ)] タブをクリックし、プロジェクトが存在するドライブの文字を共有します。

### <a name="windows-containers"></a>**Windows コンテナー**

以下は、Windows コンテナーで .NET Framework Web やコンソール アプリケーションをデバッグする際にのみ発生する問題のトラブルシューティングです。

### <a name="pre-requisites"></a>前提条件

1. Visual Studio 2017 RC (またはそれ以降) が .NET Core と Docker (プレビュー) ワークロードとともにインストールされている。
2. 最新の Windows 修正プログラムがインストールされた Windows 10 Anniversary Update。
3. Docker For Windows (ベータ) - https://docs.docker.com/docker-for-windows/ (バージョン 1.12.2-beta28 7813 以降)
4. システム トレイの Docker for Windows アイコンから、[Switch to Windows containers (Windows コンテナーへ切り替え)] を選択します。  コンピューターの再起動後も、この設定が保持されていることを確認します。

### <a name="clicking-docker-debug-project-results-in-the-error--client-version-122-is-too-old--minimum-supported-api-version-is-124-please-upgrade-your-client-to-a-newer-version"></a>**[Docker: Debug Project (Docker: プロジェクトのデバッグ)]** をクリックすると、"client version 1.22 is too old.  Minimum supported API version is 1.24, please upgrade your client to a newer version (クライアント バージョン 1.22 は古すぎます。サポートされる API の最小バージョンは 1.24 です。クライアントを最新のバージョンにアップグレードしてください)" というエラーが表示されます。

Docker For Windows のバージョン 1.13-RC2-beta31 (9123) 以降では、バージョン '2.1' の Docker Compose を使用する必要があります。   この問題を解決するには、プロジェクト内の docker-compose*.yml ファイルに出現するすべてのバージョン '2' を変更します。 Visual Studio がプロジェクトに追加する既定のテンプレートは、ツールの今後のリリースで更新されます。 

### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>コンソール アプリケーションのデバッグ中には、コンソール出力が Visual Studio の出力ウィンドウに表示されません。

これは、Visual Studio Debugger (msvsmon.exe) の既知の問題であり、今回のシナリオのために設計されたものではありません。  この問題のサポートは、今後のリリースで提供する予定です。  Visual Studio でコンソール アプリケーションからの出力を表示するには、[Docker: Start Project (Docker: プロジェクトの開始)] を使用します。これは、[Start without Debugging (デバッグなしで開始)] と同じ機能です。

### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>リリース構成の Web アプリケーションをデバッグすると、「(403) 許可されていません」のエラーで失敗します。

この問題を回避するには、ソリューションの web.release.config ファイルを開き、次の行をコメント アウトするか削除します。

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

### <a name="when-switching-to-windows-containers-you-could-potentially-see-an-error-dialog-stating-error-response-from-daemon-io-timeout"></a>Windows コンテナーに切り替えると、エラー ダイアログに "Error response from daemon: i/o timeout (デーモンからエラーが返されました: i/o タイムアウト)" と表示される場合があります。

Docker For Windows のこの問題は、https://github.com/docker/for-win/issues/178 で追跡できます。


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux コンテナー**

### <a name="unable-to-validate-volume-mapping"></a>ボリューム マッピングを検証できない
ボリューム マッピングは、アプリケーションのソース コードやバイナリをコンテナー内のアプリケーション フォルダーと共有するために欠かせない関連付けです。  個々のボリューム マッピングは、docker-compose.dev.debug.yml ファイルと docker-compose.dev.release.yml ファイルに格納されます。 ホスト コンピューター上でファイルに変更が加えられると、その変更がコンテナーで同様のフォルダー構造で反映されます。

ボリューム マッピングを有効にするには、Docker For Windows の "moby (クジラ)" トレイ アイコンから **[Settings (設定)]** を開き、**[Shared Drives (ドライブ共有)]** タブを選択します。  プロジェクトをホストするドライブ文字と、%USERPROFILE% が存在するドライブ文字の横にあるチェック ボックスをオンにして、**[Apply (適用)]** をクリックします。これで、これらのドライブが共有されます。

ボリューム マッピングが正常に機能しているかどうかをテストするには、1 つ以上のドライブが共有された後で、Visual Studio 内でリビルドを実行するか F5 キーを押します。または、コマンド プロンプトから次のコマンドを実行します。

*Windows コマンド プロンプトで*

> [!Note]
> この例は、Users フォルダー が "C" ドライブ上にあり、共有されていることを前提としています。
> 別のドライブを共有した場合は、必要に応じて変更してください。

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Linux コンテナーで*

```
/ # ls
```

ユーザー/パブリック フォルダーのディレクトリ一覧が表示されます。
/C/ユーザー/パブリック フォルダーが空でないにもかかわらず、ファイルが表示されない場合は、ボリューム マッピングが正しく構成されていません。

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

ワームホール ディレクトリに移動して、 `/c/Users/Public` ディレクトリの内容を確認します。

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!Note]
> Linux VM を使用する場合、コンテナーのファイル システムでは大文字と小文字が区別されます。

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>ビルド: "PrepareForBuild" タスクが予期せずに失敗しました。

Microsoft.DotNet.Docker.CommandLine.ClientException: 接続中にエラーが発生しました:

既定の Docker ホストが実行されていることを確認します。 コマンド プロンプトを開き、次のコマンドを実行します。

```
docker info
```

エラーが返される場合は、 **Docker For Windows** デスクトップ アプリを起動してください。  デスクトップ アプリが実行されているときは、トレイに **moby** アイコンが表示されます。 このトレイ アイコンを右クリックし、**[Settings (設定)]**を開きます。  **[Reset (リセット)]** タブ、**[Restart Docker.. (Docker の再起動)]** の順にクリックします。

##<a name="manually-upgrading-from-version-031-to-040"></a>バージョンを 0.31 から 0.40 に手動でアップグレードする方法


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
1. Properties\launchSettings.json ファイルから次の行を削除します。

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

1. 以前のバージョンをアンインストールし、Docker Tools 0.40 をインストールしてから、もう一度 ASP.NET Core Web アプリケーションまたはコンソール アプリケーションのコンテキスト メニューから、**[追加]、[Docker Support (Docker サポート)]** の順に選択します。 これにより、必要な Docker アーティファクトが元のプロジェクトに新たに追加されます。

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>**[追加]、[Docker Support (Docker サポート)]** を選択すると、またはコンテナー内の ASP.NET Core アプリケーションのデバッグ (F5) を実行すると、エラー ダイアログが表示される

拡張機能をアンインストールしてインストールすると、Visual Studio の MEF (Managed Extensibility Framework) キャッシュが破損することがあります。 このキャッシュが破損すると、Docker サポートを追加する際や、ASP.NET Core アプリケーションのデバッグ (F5) または実行の際に、エラー ダイアログが表示される可能性があります。 一時的な回避策として、次の手順に従って、MEF キャッシュを削除してからもう一度生成してください。

1. Visual Studio のすべてのインスタンスを閉じます。
1. %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\ を開きます。
1. 次のフォルダーを削除します。
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Visual Studio を開きます
1. 目的の操作をもう一度実行します。



<!--HONumber=Dec16_HO2-->


