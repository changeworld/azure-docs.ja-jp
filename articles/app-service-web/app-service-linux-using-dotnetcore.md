---
title: "Web App on Linux で .NET Core を使用する | Microsoft Docs"
description: "Web App on Linux で .NET Core を使用します。"
keywords: "Azure App Service, Web アプリ, .NET, コア, Linux, OSS"
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>Azure Web App on Linux で .NET Core を使用する #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

[Web App](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro) on Linux は、Linux オペレーティング サービスを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルには、Azure Web App on Linux で [.NET Core](https://docs.microsoft.com/aspnet/core/) アプリを作成する方法の詳細な手順が含まれています。 

![Web App on Linux][10]

以下の手順は、Mac、Windows、または Linux コンピューターを使用して実行できます。

## <a name="prerequisites"></a>前提条件 ##

このチュートリアルを完了するには、以下が必要です。 

* [.NET Core SDK](https://www.microsoft.com/net/download/core) のインストール。
* [Git](https://git-scm.com/downloads) のインストール。

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>ローカルの .NET Core アプリケーションを作成する ##

新しいターミナル セッションを開始します。 `hellodotnetcore` という名前のディレクトリを作成し、現在のディレクトリをそのディレクトリに変更します。 その後、次のように入力します。 

```
dotnet new web
``` 

  このコマンドは、3 つのファイル (*hellodotnetcore.csproj**Program.cs*および *Startup.cs*) と 1 つの空のフォルダー (*wwwroot/*) を現在のディレクトリに作成します。 `.csproj` ファイルの内容は次のようになります。 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

このアプリは Web アプリケーションであるため、ASP.NET Core パッケージへの参照が *hellodotnetcore.csproj* ファイルに自動的に追加されました。 パッケージのバージョン番号は、選択したフレームワークに従って設定されます。 .NET Core 1.1 が使用されているため、この例では ASP.NET Core バージョン 1.1.2 を参照しています。

## <a name="build-and-test-the-application-locally"></a>アプリケーションをローカルに作成してテストする ##

次に示すように、`dotnet restore` コマンドと `dotnet run` コマンドを使用して、.NET Core アプリをビルドして実行できます。

```
dotnet restore
dotnet run
```


アプリケーションが起動すると、アプリがポートで着信要求をリッスンしていることを示すメッセージが表示されます。 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

ブラウザーで `http://localhost:5000/` を参照してアプリをテストします。 すべて正常に動作していれば、"Hello World!" が 結果のテキストとして表示されます。

![ブラウザーを使用してテストする][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>Azure Portal で .NET Core アプリを作成する ##

最初に空の Web アプリを作成する必要があります。 [Azure ポータル](https://portal.azure.com/)にログインし、新しい [Web App on Linux](https://portal.azure.com/#create/Microsoft.AppSvcLinux) を作成します。

![Web アプリの作成][1]

**[作成]** ページが表示されたら、Web アプリの詳細を指定します。

![.NET Core ランタイム スタックの選択][2]

次の表をガイドとして使用して **[作成]** ページに入力し、**[OK]** と **[作成]** を選択してアプリを作成します。

| 設定      | 推奨値  | 説明                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| アプリの名前 | hellodotnetcore  | アプリの名前。 この名前は一意である必要があります。 |
| サブスクリプション | 既存のサブスクリプションを選択 | Azure サブスクリプション。 |
| リソース グループ | myResourceGroup |  Web アプリを格納するリソース グループ。 |
| App Service プラン | 既存の App Service プランの名前 |  App Service プラン。  |
| コンテナーの構成 | .NET Core 1.1 | この Web アプリのコンテナーの種類。組み込み、Docker、またはプライベート レジストリ。 |
| イメージのソース  | 組み込み  |  イメージのソース。 |
| ランタイム スタック  | .NET Core 1.1  | ランタイム スタックとバージョン。  |

## <a name="deploy-your-application-via-git"></a>Git 経由でアプリケーションをデプロイする ##

Git を使用して .NET Core アプリケーションを Azure App Service Web App on Linux にデプロイします。

新しい Azure Web アプリでは、Git のデプロイが既に構成されています。 Git のデプロイ URL は、Web アプリ名を指定して次の URL に移動することで見つけられます。

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

Git の URL は、Web アプリ名に基づく次の形式になります。

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

次のコマンドを実行して、ローカル アプリケーションを Azure Web アプリにデプロイします。 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

アプリケーションは、アプリケーションのソース ファイルが Azure にプッシュされるときにクラウド内にビルドされるため、*bin/* または *obj/* ディレクトにあるファイルをプッシュする必要はありません。 ビルド プロセスが完了したら、バイナリ ファイルがアプリケーションのディレクトリ */home/site/wwwroot/* にコピーされます。

リモート デプロイ操作が正しく報告されていることを確認します。 パッケージの解決とビルド プロセスはクラウドで実行されるため、プッシュ操作はしばらく時間がかかる可能性があります。 ファイルがコピーされていることを示すものなど、いくつかのステータス メッセージが表示されます。 出力は次のようになります。

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

デプロイが完了したら、デプロイを有効にするために Web アプリを再起動します。 これを行うには、Azure ポータルに移動し、Web アプリの **[概要]** ページに移動します。 ページの **[再起動]** ボタンを選択します。 ポップアップ ウィンドウが表示されたら、**[はい]** を選択して確認します。 次のように Web アプリを参照できます。

![Azure App Service on Linux にデプロイされた .NET Core アプリの参照][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ
* [Azure App Service Web App on Linux の FAQ](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

