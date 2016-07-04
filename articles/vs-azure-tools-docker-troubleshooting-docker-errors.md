<properties
   pageTitle="Visual Studio を使用した Windows での Docker クライアント エラーのトラブルシューティング | Microsoft Azure"
   description="Visual Studio を使用して、Windows 上で Web アプリを作成し、Docker にデプロイする際に発生する問題のトラブルシューティングを行います。"
   services="azure-container-service"
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

# Docker エラーのトラブルシューティング

Docker Preview 用 Visual Studio ツールを使用する場合は、プレビューの性質によりいくつかの問題が発生する可能性があります。いくつかの一般的な問題と解決策を次に示します。

##Docker サポートのための Program.cs の構成に失敗する

Docker サポートを追加するときに、`.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_SERVER.URLS"))` を WebHostBuilder() に追加する必要があります。Program.cs の `Main()` 関数または新しい WebHostBuilder が見つからない場合は、警告が表示されます。Docker コンテナー内で実行する localhost 以外で Kestrel が受信トラフィックを待機できるようにするには `.UseUrls()` が必要です。完了すると、一般的なコードは次のようになります。

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls() が WebHost で受信 URL トラフィックを待機するように構成します。[Visual Studio 用 Docker ツール](http://aka.ms/DockerToolsForVS)により、dockerfile.debug/release mode の環境変数が次のように構成されます。

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## ボリューム マッピングが機能しない
編集と更新の機能を有効にするには、プロジェクトのソース コードがコンテナー内の .app フォルダーで共有されるように、ボリューム マッピングを構成します。 ホスト コンピューター上でファイルが変更されると、コンテナーの /app ディレクトリは同じディレクトリを使用します。docker-compose.debug.yml では、次の構成によりボリューム マッピングが有効になります。

```
volumes:
    - ..:/app
```

ボリューム マッピングが機能しているかどうかをテストするには、次のコマンドを試してください。

**Windows から**

```
a
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
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

**注:** *Linux VM を使用する場合、コンテナーのファイル システムでは大文字と小文字が区別されます*。

内容が表示されない場合は、以下を試します。

**Docker for Windows (ベータ)**
- Docker for Windows のデスクトップ アプリが動作していることを確認するには、システム トレイにあるクジラのアイコンが白く表示され、機能していることを確認します。
- ボリューム マッピングが構成されていることを確認するには、システム トレイのクジラのアイコンを右クリックして [Settings (設定)] を選択し、**[Manage shared drives... (共有ドライブの管理)]** をクリックします。

**Docker Toolbox (VirtualBox を含む)**

既定では、VirtualBox は `C:\Users` を `c:/Users` として共有します。可能であれば、プロジェクトをこのディレクトリの下に移動します。または、VirtualBox の[共有フォルダー](https://www.virtualbox.org/manual/ch04.html#sharedfolders)に手動で追加することもできます。
	
##"Build : Failed to build the image"、"Error checking TLS connection: Host is not running" と表示される

- 既定の Docker ホストが実行されていることを確認します。[Docker クライアントの構成](./vs-azure-tools-docker-setup.md)に関するページをご覧ください。

##Microsoft Edge を既定のブラウザーとして使用している

Microsoft Edge ブラウザーを使用している場合、IP アドレスがセキュリティで保護されていないと見なされるため、サイトが表示されないことがあります。この問題を解決するには、次の手順を実行します。

1. **[インターネット オプション]** に移動します。
    - Windows 10 の場合は、Windows の [ファイル名を指定して実行] ボックスに「`Internet Options`」と入力します。
    - Internet Explorer から **[設定]** メニューに移動し、**[インターネット オプション]** を選択してもかまいません。 
1. **[インターネット オプション]** が表示されたら選択します。 
1. **[セキュリティ]** タブを選択します。
1. **[ローカル イントラネット]** ゾーンを選択します。
1. **[サイト]** を選択します。 
1. 一覧に仮想マシンの IP (この場合は Docker ホスト) を追加します。 
1. Edge でページを更新すると、サイトが実行されていることがわかります。 
1. この問題の詳細については、Scott Hanselman のブログ記事「[Microsoft Edge can't see or open VirtualBox-hosted local web sites (VirtualBox にホストされたローカル Web サイトを Microsoft Edge で表示したり開いたりできない)](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx)」を参照してください。 

##0\.15 以前のバージョンのトラブルシューティング


###アプリを実行すると、PowerShell が開き、エラーを表示した後に閉じる。ブラウザーのページは開かない。

これは、`docker-compose-up` の実行中のエラーであると考えられます。このエラーを表示するには、次の手順を実行します。

1. `Properties\launchSettings.json` ファイルを開きます。
1. Docker エントリを見つけます。
1. 次の内容で始まる行を見つけます。

    ```
    "commandLineArgs": "-ExecutionPolicy RemoteSigned …”
    ```
	
1. 次のように、この行に `-noexit` パラメーターを追加します。これにより、PowerShell が開いたままになり、エラーを確認できるようになります。

    ```
	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”
    ```

<!---HONumber=AcomDC_0622_2016-->