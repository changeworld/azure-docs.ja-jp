---
title: Linux ASP.NET Core アプリを構成する
description: アプリ用に事前構築済みの ASP.NET Core コンテナーを構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255906"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Azure App Service 向けの Linux ASP.NET Core アプリを構成する

ASP.NET Core アプリは、コンパイル済みバイナリとしてデプロイする必要があります。 Visual Studio 発行ツールではソリューションがビルドされてからコンパイル済みバイナリが直接デプロイされますが、App Service 展開エンジンではまずコード リポジトリがデプロイされ、その後にバイナリがコンパイルされます。

このガイドでは、App Service のビルトイン Linux コンテナーを使用する ASP.NET Core 開発者のために、主要な概念と手順を示します。 Azure App Service を初めて使用する場合は、まず [ASP.NET Core クイックスタート](quickstart-dotnetcore.md)と [ASP.NET Core と SQL Database のチュートリアル](tutorial-dotnetcore-sqldb-app.md)に従ってください。

## <a name="show-net-core-version"></a>.NET Core バージョンを表示する

現在の .NET Core バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

サポートされているすべての .NET Core バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>.NET Core バージョンを設定する

.NET Core バージョンを 2.1 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>ビルドの自動化のカスタマイズ

ビルドの自動化を有効にして Git または zip パッケージを使用してアプリをデプロイする場合、App Service のビルドの自動化によって、次の手順が実行されます。

1. `PRE_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。
1. `dotnet restore` を実行して、NuGet の依存関係を復元します。
1. `dotnet publish` を実行して、運用環境用のバイナリを構築します。
1. `POST_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。

`PRE_BUILD_COMMAND` および `POST_BUILD_COMMAND` は、既定では空の環境変数です。 ビルド前のコマンドを実行するには、`PRE_BUILD_COMMAND` を定義します。 ビルド後のコマンドを実行するには、`POST_BUILD_COMMAND` を定義します。

次の例では、一連のコマンドに対して 2 つの変数をコンマ区切りで指定しています。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

ビルドの自動化をカスタマイズするためのその他の環境変数については、「[Oryx の構成](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)」を参照してください。

Linux 上で App Service によって ASP.NET Core アプリが実行およびビルドされる方法に関する詳細については、[Oryx ドキュメントの.NET Core アプリの検出およびビルド方法](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)に関するページを参照してください。

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)できます。 次に、標準の ASP.NET Core 依存関係の挿入パターンを使用して、任意のクラスでこれらにアクセスできます。

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

たとえば、App Service と *appsettings.json* で同じ名前のアプリ設定を構成した場合は、App Service の値が *appsettings.json* の値よりも優先されます。 ローカルの *appsettings.json* 値ではアプリをローカルでデバッグできますが、App Service の値では実稼働設定の製品内でアプリを実行できます。 接続文字列は同じように機能します。 これにより、コード リポジトリの外部にアプリケーション シークレットを保存し、コードを変更することなく適切な値にアクセスできます。

## <a name="get-detailed-exceptions-page"></a>例外の詳細ページを表示する

Visual Studio デバッガーで ASP.NET アプリの実行中に例外が発生すると、ブラウザーに例外の詳細ページが表示されますが、App Service ではそのページの代わりに汎用の **HTTP 500** エラーが表示されるか、「**要求の処理中にエラーが発生しました。** 」 メッセージが表示されます。 App Service で例外の詳細ページを表示するには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを実行してアプリ設定 `ASPNETCORE_ENVIRONMENT` をアプリに追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されるかどうかをアプリのロジックで認識する必要がある場合は、*Startup.cs* で Forwarded Headers Middleware を構成します。

- `Startup.ConfigureServices` で、`X-Forwarded-For` ヘッダーと `X-Forwarded-Proto` ヘッダーを転送するように [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) を使ってミドルウェアを構成します。
- ミドルウェアが App Service のロード バランサーを信頼できるようにするために、既知のネットワークにプライベート IP アドレス範囲を追加します。
- 他のミドルウェアを呼び出す前に、`Startup.Configure` 内で [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) メソッドを呼び出します。

3 つの要素をすべてまとめると、次の例のようなコードになります。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)」を参照してください。

## <a name="deploy-multi-project-solutions"></a>マルチ プロジェクト ソリューションをデプロイする

ルート ディレクトリの *.csproj* ファイルを使用して ASP.NET リポジトリを展開エンジンにデプロイすると、エンジンによってプロジェクトがデプロイされます。 ルート ディレクトリの *.sln* ファイルを使用して ASP.NET リポジトリをデプロイすると、エンジンは最初に検出した Web サイトまたは Web アプリケーション プロジェクトを App Service アプリとして選択します。 必要なプロジェクトがエンジンで選択されない可能性があります。

マルチ プロジェクト ソリューションをデプロイする場合は、App Service で使用するプロジェクトを次の 2 つの方法で指定できます。

### <a name="using-deployment-file"></a>.deployment ファイルを使用

リポジトリのルートに *.deployment* ファイルを追加し、次のコードを追加します。

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>アプリ設定を使用

<a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a> で、次の CLI コマンドを実行して App Service アプリにアプリ設定を追加します。 *\<app-name>* 、 *\<resource-group-name>* 、および *\<project-name>* を適切な値で置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:ASP.NET Core アプリと SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](app-service-linux-faq.md)
