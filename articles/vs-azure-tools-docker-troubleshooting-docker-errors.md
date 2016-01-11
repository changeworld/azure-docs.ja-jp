<properties
   pageTitle="Visual Studio を使用した Windows での Docker クライアント エラーのトラブルシューティング | Microsoft Azure"
   description="Visual Studio を使用して、Windows 上で Web アプリを作成し、Docker にデプロイする際に発生する問題のトラブルシューティングを行います。"
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
   ms.date="12/18/2015"
   ms.author="tarcher" />

# Docker エラーのトラブルシューティング

アプリケーションの Docker コンテナーのすべての設定を構成したら、設定とパスが正しいことを確認する必要があります。Visual Studio では、[発行] ダイアログに、この確認に役立つ [検証] ボタンがあります。

このトピックは、Docker で Visual Studio アプリケーションをホストする場合に発生する最も一般的な問題を診断し、修正または回避するために役立ちます。問題が検出されると、このトピックに追加されます。

## [Web の発行] ダイアログ ボックスで Docker ホストへの接続を検証しようとすると失敗メッセージが表示される

以下に、この問題のいくつかの解決策を示します。

- **[発行]** ダイアログの **[接続]** タブで、**[サーバーの URL]** が正しいことと、**[サーバーの URL]** の末尾の `:<port_number>` が Docker デーモンがリッスンしているポートであることを確認します。

- **[発行]** ダイアログの **[接続]** タブで、**[Docker の詳細オプション]** セクションを展開し、適切な **[認証]** オプションが指定されていることを確認します。
  - サーバー上の Docker デーモンが TLS セキュリティを使用するように構成されている場合、Windows Docker コマンド ライン インターフェイス (docker.exe) は既定で `<%userprofile%>\.docker` フォルダーの下にあるクライアント キー (key.pem) と証明書 (cert.pem) を検索します。これらの項目が存在しない場合、OpenSSL を使用して生成する必要があります。TLS 用の Docker の構成の詳細については、[HTTPS による Docker デーモン ソケットの保護](https://docs.docker.com/articles/https/)に関するページを参照してください。

	Docker で Windows クライアントから Linux サーバーへの認証が適切に行われていることを確認する 1 つの方法は、[プレビュー] ボックスの内容を新しいコマンド ウィンドウにコピーし、次に示すように `<command>` を "info" に変更することです。

    ```
    // This example assumes the Docker daemon is configured to use the default port
    // of 2376 to listen for connections.docker.

    --tls -H tcp://contoso.cloudapp.net:2376 info
    ```

    .docker フォルダーにクライアント証明書とキー ファイルをコピーする代わりに、次のパラメーターを追加することで **[認証]** オプションを変更できます。

    ```
    --tls --tlscert=C:\mycert\cert.pem --tlskey=C:\mycert\key.pem
    ```
- Docker ホスト マシン上の Docker デーモンがバージョン 1.6 以降であることを確認します。

## Docker フォルダー内にクライアント証明書がなく独自の証明書を使用している場合のタイムアウト エラー

Visual Studio で Docker ホストを作成する際に独自の証明書を使用するように選択した (つまり、**[Microsoft Azure で仮想マシンを作成します]** ダイアログ ボックスの **[Docker 証明書を自動生成]** チェック ボックスをオフにした) 場合は、クライアント証明書とキー ファイル (cert.pem と key.pem) Docker フォルダー (`<%userprofile%>\.docker`) にコピーする必要があります。そうしないと、プロジェクトの発行時に、1 時間でタイムアウト エラーが発生し、発行操作が失敗します。

## Docker コンテナーへの発行には PowerShell 3.0 が必要である

オペレーティング システムが Windows 7 または Windows Server 2008 の場合は、Docker コンテナーに発行する前に、PowerShell 3.0 をインストールする必要があります。PowerShell 3.0 は、[Windows Management Framework 3.0](https://www.microsoft.com/ja-JP/download/details.aspx?id=34595) に含まれています。インストール後にシステムを再起動する必要があります。

別の回避策として、PowerShell 3.0 が既に組み込まれている Windows 8.1 または Windows 10 にアップグレードすることができます。

## PowerShell ウィンドウは自動的に閉じない

VM の作成後、PowerShell ウィンドウが自動的に閉じないことがあります。このウィンドウを閉じると、Visual Studio も閉じられます。ウィンドウは、Visual Studio や Docker ツールの機能に影響しないため、作業を完了するまで、開いたままにしておいてください。

## FAQ

Q: Visual Studio ツールを使用して、Azure で新しい Docker 対応の Linux マシンを作成するにはどうすればよいですか?

A: 作成方法については、「[Docker での Web アプリのホスト](vs-azure-tools-docker-hosting-web-apps-in-docker.md)」を参照してください。

Q: Linux Docker コンテナーへの発行ではどのような Visual Studio プロジェクト テンプレートがサポートされていますか?

Visual Studio では現在、C# コンソール アプリケーション (パッケージ) と、次のような C# ASP.NET 5 Web プレビュー テンプレートをサポートしています。

- Empty

- Web API

- Web Application

Q: コマンドラインから MSBUILD を使用して、Docker に ASP.NET 5 Web またはコンソール プロジェクトを発行するにはどうすればよいですか?

A: 次の MSBuild コマンドを使用します。

    `msbuild <projectname.xproj> /p:deployOnBuild=true;publishProfile=<profilename>`

Q: コマンドラインから PowerShell を使用して、Docker に ASP.NET 5 Web またはコンソール プロジェクトを発行するにはどうすればよいですか?

A: 次の PowerShell コマンドを使用します。

```
.\contoso-Docker-publish.ps1 -packOutput $env:USERPROFILE\AppData\Local\Temp\PublishTemp -pubxmlFile .\contoso-Docker.pubxml
```

Q: Docker がインストールされた独自の Linux サーバーがありますが、**[Web の発行]** ダイアログでこれを指定するにはどうすればよいですか?

A: 「[Docker での Web アプリのホスト](vs-azure-tools-docker-hosting-web-apps-in-docker.md)」トピックの「**カスタム Docker ホストの指定**」セクションを参照してください。

Q: Docker がインストールされた独自の Linux サーバー使用しています。TLS を使用して認証を構成するためにキーと証明書を生成するにはどうすればよいですか?

A: 1 つの方法は、サーバーで OpenSSL を使用して、CA、サーバー、およびクライアントに必要な証明書とキーを生成することです。そうすれば、サード パーティのソフトウェアを使用して SSH/SFTP 接続を確立してから、証明書をローカルの Windows 開発用コンピューターにコピーできます。既定では、Docker (CLI) は `<userprofile>\.docker` フォルダーにある証明書の使用を試みます。

別のオプションは、Windows 用の OpenSSL をダウンロードして、必要な証明書とキーを生成してから、CA、サーバーの証明書、およびキーを Linux コンピューターにアップロードすることです。Docker へのセキュリティで保護された接続の確立の詳細については、[HTTPS による Docker デーモン ソケットの保護](https://docs.docker.com/articles/https/)に関するページを参照してください。

<!---HONumber=AcomDC_1223_2015-->