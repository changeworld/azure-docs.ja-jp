---
title: 'クイックスタート: ASP.NET Web アプリをデプロイする'
description: 初めての ASP.NET アプリをデプロイして、Azure App Service で Web アプリを実行する方法について説明します。
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 482bf6d29fbc1e982ee4d17099d82915ff3a0241
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762463"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>クイックスタート: ASP.NET Web アプリをデプロイする

このクイックスタートでは、初めての ASP.NET Web アプリを作成し、[Azure App Service](overview.md) にデプロイする方法について説明します。 App Service はさまざまなバージョンの .NET アプリをサポートし、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 ASP.NET Web アプリはクロスプラットフォームであり、Linux でも Windows でもホストすることができます。 完了すると、App Service ホスティング プランと、デプロイされた Web アプリケーションを含む App Service とで構成される、Azure リソース グループを作成できます。

> [!TIP]
> .NET Core 3.1 は、最新の長期サポート (LTS) リリースの .NET です。 詳細については、[.NET のサポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

:::zone target="docs" pivot="development-environment-vs"

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet)。
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> と **ASP.NET と Web 開発** ワークロード。

    既に Visual Studio 2019 がインストール済みである場合:

    - **[ヘルプ]**  >  **[更新プログラムの確認]** の順に選択して、Visual Studio に最新の更新プログラムをインストールします。
    - **[ツール]**  >  **[ツールと機能を取得]** の順に選択し、ワークロードを追加します。

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet)。
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>。
- <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure Tools</a> 拡張機能。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> 最新の .NET Core 3.1 SDK をインストールします。</a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> 最新の .NET 5.0 SDK をインストールします。 </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4.8 Developer Pack をインストールします。 </a>

> [!NOTE]
> Visual Studio Code はクロスプラットフォームですが、.NET Framework は違います。 Visual Studio Code を使用して .NET Framework アプリを開発する場合は、ビルドの依存関係を満たすために Windows マシンを使用することを検討してください。

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet)。
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>。
- .NET SDK (ランタイムと CLI を含む)。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> 最新の .NET Core 3.1 SDK をインストールします。</a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> 最新の .NET 5.0 SDK をインストールします。 </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> 最新の .NET 5.0 SDK </a>および <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank">.NET Framework 4.8 Developer Pack をインストールします。 </a>

> [!NOTE]
> [.NET CLI](/dotnet/core/tools) はクロスプラットフォームですが、.NET Framework は違います。 .NET CLI を使用して .NET Framework アプリを開発する場合は、ビルドの依存関係を満たすために Windows マシンを使用することを検討してください。

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>ASP.NET Web アプリを作成する

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で **[ASP.NET Web Core App]\(ASP.NET Web Core アプリ\)** を検索して選択し、 **[次へ]** を選択します。
1. **[新しいプロジェクトの構成]** で、アプリケーションに _MyFirstAzureWebApp_ という名前を付け、 **[次へ]** を選択します。

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="ASP.NET Core 3.1 Web アプリを構成する" border="true":::

1. **[.NET Core 3.1 (Long-term support)]\(.NET Core 3.1 (長期サポート)\)** を選択します。
1. **[認証の種類]** が **[なし]** に設定されていることを確認します。 **[作成]** を選択します。

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio - .NET Core 3.1 を選択し、[認証の種類] として [なし] を選択する。" border="true":::

1. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、Web アプリをローカルで実行します。

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio - .NET Core 3.1 をローカルで参照" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で **[ASP.NET Web Core App]\(ASP.NET Web Core アプリ\)** を検索して選択し、 **[次へ]** を選択します。
1. **[新しいプロジェクトの構成]** で、アプリケーションに _MyFirstAzureWebApp_ という名前を付け、 **[次へ]** を選択します。

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio - ASP.NET 5.0 Web アプリを構成する。" border="true":::

1. **[.NET Core 5.0 (Current)]\(.NET Core 5.0 (最新)\)** を選択します。
1. **[認証の種類]** が **[なし]** に設定されていることを確認します。 **[作成]** を選択します。

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio - .NET Core 5.0 を選択する際の追加情報。" border="true":::

1. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、Web アプリをローカルで実行します。

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio - ASP.NET Core 5.0 をローカルで実行。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で、 **[ASP.NET Web アプリケーション (.NET Framework)]** を選択し、 **[次へ]** を選択します。
1. **[新しいプロジェクトの構成]** で、アプリケーションに _MyFirstAzureWebApp_ という名前を付け、 **[作成]** を選択します。

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio - ASP.NET Framework 4.8 Web アプリを構成する。" border="true":::

1. **MVC** テンプレートを選択します。
1. **[認証]** に **[認証なし]** が設定されていることを確認してください。 **[作成]** を選択します。

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio - MVC テンプレートを選択する。" border="true":::

1. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、Web アプリをローカルで実行します。

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio - ASP.NET Framework 4.8 をローカルで実行。" lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

_MyFirstAzureWebApp_ という名前の新しいフォルダーを作成し、Visual Studio Code で開きます。 <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">ターミナル</a> ウィンドウを開き、[`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) コマンドを使用して新しい .NET Web アプリを作成します。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> `--target-framework-override` フラグは、プロジェクトのターゲット フレームワーク モニカー (TFM) の代替となる自由形式のテキストです。サポートしているテンプレートが存在するか、コンパイルされるという "*保証はありません*"。 .NET Framework アプリのビルドと実行は、Windows 上でのみ行うことができます。

---

Visual Studio Code の **ターミナル** から [`dotnet run`](/dotnet/core/tools/dotnet-run) コマンドを使用し、アプリケーションをローカルで実行します。

```dotnetcli
dotnet run
```

Web ブラウザーを開き、`https://localhost:5001` のアプリに移動します。


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

テンプレート ASP.NET Core 3.1 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - .NET Core 3.1 をブラウザーからローカルで実行する。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

テンプレート ASP.NET Core 5.0 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - .NET 5.0 をブラウザーからローカルで実行する。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

テンプレート ASP.NET Framework 4.8 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - .NET 4.8 をブラウザーからローカルで実行する。" lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

自分のマシンでターミナル ウィンドウを開き、作業ディレクトリに移動します。 [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) コマンドを使用して新しい .NET Web アプリを作成し、新しく作成したアプリのディレクトリに移動します。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> `--target-framework-override` フラグは、プロジェクトのターゲット フレームワーク モニカー (TFM) の代替となる自由形式のテキストです。サポートしているテンプレートが存在するか、コンパイルされるという "*保証はありません*"。 .NET Framework アプリのビルドは、Windows 上でのみ行うことができます。

---

同じターミナル セッションから [`dotnet run`](/dotnet/core/tools/dotnet-run) コマンドを使用して、アプリケーションをローカルで実行します。

```dotnetcli
dotnet run
```

Web ブラウザーを開き、`https://localhost:5001` のアプリに移動します。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

テンプレート ASP.NET Core 3.1 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - ローカル ブラウザーでの ASP.NET Core 3.1。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

テンプレート ASP.NET Core 5.0 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - ローカル ブラウザーでの ASP.NET Core 5.0。" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

テンプレート ASP.NET Framework 4.8 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - ローカル ブラウザーでの ASP.NET Framework 4.8。" lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Web アプリを発行する

Web アプリを発行するには、まず、アプリの発行先となる新しい App Service を作成して構成する必要があります。

App Service の設定の一環として、次のものを作成します。

- サービスのすべての Azure リソースを格納する新しい[リソース グループ](../azure-resource-manager/management/overview.md#terminology)。
- アプリをホストする Web サーバー ファームの場所、サイズ、および機能を指定する新しい[ホスティング プラン](overview-hosting-plans.md)。

App Service を作成して Web アプリを発行するには、次の手順に従います。

:::zone target="docs" pivot="development-environment-vs"

1. **ソリューション エクスプローラー** で **MyFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。
1. **[発行]** で **[Azure]** を選択し、 **[次へ]** を選択します。

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio - Web アプリを発行し、Azure をターゲットにする。" border="true":::

1. 表示されるオプションは、既に Azure にサインインしているかどうかや、Visual Studio アカウントが Azure アカウントとリンクされているかどうかによって異なります。 **[アカウントの追加]** または **[サインイン]** を選択して Azure サブスクリプションにサインインします。 既にサインインしている場合は、目的のアカウントを選択します。

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio - Azure へのサインインを選択するためのダイアログ。":::

1. **[特定のターゲット]** で、 **[Azure App Service (Linux)]** または **[Azure App Service (Windows)]** を選択します。

    > [!IMPORTANT]
    > ASP.NET Framework 4.8 をターゲットにする場合は、 **[Azure App Service (Windows)]** を使用します。

1. **[App Service インスタンス]** の右側にある **[+]** を選択します。

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio - App Service アプリの新規作成ダイアログ。":::

1. **[サブスクリプション]** で、リストされているサブスクリプションを受け入れるか、ドロップダウン リストから新しいサブスクリプションを選択します。
1. **[リソース グループ]** で、 **[新規]** を選択します。 **[新しいリソース グループ名]** に「*myResourceGroup*」と入力し、 **[OK]** を選択します。
1. **[ホスティング プラン]** で、 **[新規]** を選択します。
1. **ホスティング プランの [新規作成]** ダイアログで、次の表で指定されている値を使用します。

    | 設定          | 推奨値          | 説明                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **ホスティング プラン** | *MyFirstAzureWebAppPlan* | App Service プランの名前です。                                         |
    | **場所**     | "*西ヨーロッパ*"            | Web アプリがホストされているデータ センターです。                           |
    | **[サイズ]**         | *Free*                   | [価格レベル][app-service-pricing-tier]によって、ホスティング機能が決まります。 |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="新しいホスティング プランを作成する":::

1. **[名前]** に、有効な文字 (`a-z`、`A-Z`、`0-9`、`-`) のみから成る一意のアプリ名を入力します。 自動的に生成される一意の名前をそのまま使用してもかまいません。 Web アプリの URL は `http://<app-name>.azurewebsites.net` です。`<app-name>` には自分のアプリの名前を指定します。
1. **[作成]** を選択して、Azure リソースを作成します。

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio - アプリ リソースの作成ダイアログ。":::

   ウィザードが完了すると、Azure リソースが作成され、発行する準備が整います。

1. **[完了]** を選択して、ウィザードを終了します。
1. **[発行]** ページで **[発行]** を選択します。 Visual Studio によってアプリのビルド、パッケージ化、および Azure への発行が行われた後、既定のブラウザーでアプリが起動されます。

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    ASP.NET Core 3.1 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure での ASP.NET Core 3.1 Web アプリ。":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    ASP.NET Core 5.0 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure での ASP.NET Core 5.0 Web アプリ。":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ASP.NET Framework 4.8 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Azure での ASP.NET Framework 4.8 Web アプリ。":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Visual Studio Azure Tools 拡張機能を使用して Web アプリをデプロイするには:

1. Visual Studio Code で [**コマンド パレット**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)を開き、<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> キーを押します。
1. [Azure App Service: Web アプリにデプロイ] を検索して選択します。
1. プロンプトに次のように応答します。

    - デプロイするフォルダーとして *[MyFirstAzureWebApp]* を選択します。
    - 確認を求められたら **[構成の追加]** を選択します。
    - メッセージに従って既存の Azure アカウントにサインインします。

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code - Azure にサインインする。" border="true":::

    - **サブスクリプション** を選択します。
    - **[Create new Web App]\(新しい Web アプリの作成\)、[詳細設定]** の順に選択します。
    - **[Enter a globally unique name]\(グローバルに一意の名前を入力する\)** には、Azure 全体で一意の名前を使用します ("*有効な文字は `a-z`、`0-9`、`-` です*")。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。
    - **[新しいリソース グループの作成]** を選択して、`myResourceGroup` のような名前を指定します。
    - "**ランタイム スタックを選択してください**" という確認を求められたら、次の操作を行います。
      - *[.NET Core 3.1]* の場合は、 **[.NET Core 3.1 (LTS)]** を選択します
      - *[.NET 5.0]* の場合は、 **[.NET 5]** を選択します
      - *[.NET Framework 4.8]* の場合は、 **[ASP.NET V4.8]** を選択します
    - オペレーティング システム (Windows または Linux) を選択します。
        - *.NET Framework 4.8* の場合は、暗黙的に Windows が選択されます。
    - **[新しい App Service プランの作成]** を選択し、名前を指定して、 **[F1 Free]** [価格レベル][app-service-pricing-tier]を選択します。
    - Application Insights リソースに対して **[Skip for now]\(今はしない\)** を選択します。
    - お近くの場所を選択します。

1. 発行が完了したら、通知の **[Web サイトの参照]** を選択し、確認を求められたら **[開く]** を選択します。

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    ASP.NET Core 3.1 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure での ASP.NET Core 3.1 Web アプリ。":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    ASP.NET Core 5.0 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure での ASP.NET Core 5.0 Web アプリ。":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ASP.NET Framework 4.8 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Azure での ASP.NET Framework 4.8 Web アプリ。":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

[`az webapp up`](/cli/azure/webapp#az_webapp_up) コマンドを使用して、ローカルの *MyFirstAzureWebApp* ディレクトリにコードをデプロイします。

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- `az` コマンドが認識されない場合は、「[前提条件](#prerequisites)」の説明に従って Azure CLI がインストールされていることを確認してください。
- `<app-name>` を Azure 全体で一意の名前で置き換えます ("*有効な文字は、`a-z`、`0-9`、および `-` です*")。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。
- `--sku F1` 引数を使用すると、**Free** [価格レベル][app-service-pricing-tier]で Web アプリが作成されます。 この引数を省略するとより高速な Premium レベルが使用されるため、時間単位のコストが発生します。
- `<os>` を `linux` または `windows` に置き換えます。 *ASP.NET Framework 4.8* をターゲットにする場合は、`windows` を使用する必要があります。
- 必要に応じて、引数 `--location <location-name>` を含めることができます。ここで、`<location-name>` は利用可能な Azure リージョンです。 [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) コマンドを実行すると、お使いの Azure アカウントで使用可能なリージョンの一覧を取得できます。

コマンドが完了するまでに数分かかる場合があります。 実行中には、リソース グループ、App Service プラン、およびホスティング アプリの作成、ログ記録の構成、ZIP デプロイの実行に関するメッセージが表示されます。 その後、アプリの URL を含むメッセージが出力されます。

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Web ブラウザーを開き、URL に移動します。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - Azure での ASP.NET Core 3.1 Web アプリ。":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - Azure での ASP.NET Core 5.0 Web アプリ。":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI - Azure での ASP.NET Framework 4.8 Web アプリ。":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>アプリを更新して再デプロイする

Web アプリを更新して再デプロイするには、次の手順に従います。

:::zone target="docs" pivot="development-environment-vs"

1. **ソリューション エクスプローラー** で、対象プロジェクトの *[Index.cshtml]* を開きます。
1. 最初の `<div>` 要素を次のコードに置き換えます。

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   変更内容を保存します。

1. Azure に再デプロイするには、**ソリューション エクスプローラー** で **MyFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。
1. **発行** の概要ページで **[発行]** を選択します。

    発行が完了すると、Visual Studio で Web アプリの URL のブラウザーが起動されます。

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    更新された ASP.NET Core 3.1 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure での更新された ASP.NET Core 3.1 Web アプリ。":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    更新された ASP.NET Core 5.0 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure での更新された ASP.NET Core 5.0 Web アプリ。":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    更新された ASP.NET Framework 4.8 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Azure での更新された ASP.NET Framework 4.8 Web アプリ。":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. *Index.cshtml* を開きます。
1. 最初の `<div>` 要素を次のコードに置き換えます。

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   変更内容を保存します。

1. Visual Studio Code の **サイド バー** を開き、**Azure** アイコンを選択して、そのオプションを展開します。
1. **[APP SERVICE]** ノードで自分のサブスクリプションを展開し、 **[MyFirstAzureWebApp]** を右クリックします。
1. **[Web アプリにデプロイ]** を選択します。
1. 確認を求められたら **[デプロイ]** を選択します。
1. 発行が完了したら、通知の **[Web サイトの参照]** を選択し、確認を求められたら **[開く]** を選択します。

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    更新された ASP.NET Core 3.1 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure での更新された ASP.NET Core 3.1 Web アプリ。":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    更新された ASP.NET Core 5.0 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure での更新された ASP.NET Core 5.0 Web アプリ。":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    更新された ASP.NET Framework 4.8 Web アプリがページに表示されていることがわかります。

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Azure での更新された ASP.NET Framework 4.8 Web アプリ。":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

ローカル ディレクトリの *Index.cshtml* ファイルを開きます。 最初の `<div>` 要素を置き換えます。

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

変更を保存してから、もう一度 `az webapp up` コマンドを使用してアプリを再デプロイします。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 はクロスプラットフォームです。前のデプロイに基づいて、`<os>` を `linux` または `windows` に置き換えてください。

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 はクロスプラットフォームです。前のデプロイに基づいて、`<os>` を `linux` または `windows` に置き換えてください。

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 は、フレームワークの依存関係があり、また、Windows でホストされている必要があります。

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> .NET アプリを Linux でホストすることに関心がある方は、[ASP.NET Framework から ASP.NET Core](/aspnet/core/migration/proper-to-2x) に移行することを検討してください。

---

このコマンドでは、 *.azure/config* ファイルにローカルでキャッシュされている値 (アプリ名、リソース グループ、App Service プランなど) を使用します。

デプロイが完了したら、「**アプリの参照**」の手順で開いた元のブラウザー ウィンドウに切り替えて、更新をクリックします。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

更新された ASP.NET Core 3.1 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Azure での更新された ASP.NET Core 3.1 Web アプリ。":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

更新された ASP.NET Core 5.0 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Azure での更新された ASP.NET Core 5.0 Web アプリ。":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

更新された ASP.NET Framework 4.8 Web アプリがページに表示されていることがわかります。

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI - Azure での更新された ASP.NET Framework 4.8 Web アプリ。":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Azure アプリの管理

Web アプリを管理するには、[Azure portal](https://portal.azure.com) に移動し、 **[App Services]** を検索して選択します。

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure portal - [App Services] オプションを選択する。":::

**[App Services]** ページで、Web アプリの名前を選択します。

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure portal - サンプル Web アプリが選択されている [App Services] ページ。":::

Web アプリの **[概要]** ページには、参照、停止、開始、再起動、削除などの基本的な管理のためのオプションが含まれています。 左側のメニューは、アプリを構成するためのさらなるページを示しています。

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure portal - App Service の概要ページ。":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>次の手順

このクイックスタートでは、ASP.NET Web アプリを作成して Azure App Service にデプロイしました。

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

次の記事に進み、.NET Core アプリを作成して SQL Database に接続する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル: ASP.NET Core アプリと SQL データベース](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 3.1 アプリを構成する](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

次の記事に進み、.NET Core アプリを作成して SQL Database に接続する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル: ASP.NET Core アプリと SQL データベース](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 5.0 アプリを構成する](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

次の記事に進み、.NET Framework アプリを作成して SQL Database に接続する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル: ASP.NET アプリと SQL データベース](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET Framework アプリを構成する](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
