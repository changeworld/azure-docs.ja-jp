---
title: 'クイックスタート: Azure Monitor Application Insights を使用して ASP.NET Core アプリを監視する'
description: Azure Monitor Application Insights を使用して監視するために ASP.NET Core Web アプリをインストルメント化します。
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 06/11/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 90d4fd76350d03bd876d97a7191011079b11845e
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300959"
---
# <a name="quickstart-monitor-an-aspnet-core-app-with-azure-monitor-application-insights"></a>クイックスタート: Azure Monitor Application Insights を使用して ASP.NET Core アプリを監視する

このクイックスタートでは、Application Insights SDK を使用して ASP.NET Core アプリをインストルメント化し、クライアント側とサーバー側のテレメトリを収集します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet)。
- .NET 5.0 SDK 以降。 お使いのプラットフォーム用の[最新の .NET 5.0 SDK をインストール](https://dotnet.microsoft.com/download/dotnet/5.0)します。

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

テレメトリの取り込みを開始するには、Azure サブスクリプションで Application Insights リソースを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[リソースの作成]**  >  **[開発者ツール]**  >  **[Application Insights]** の順に選択します。

1. 表示されるフォームを完成させます。
    1. 選択された **[サブスクリプション]** を確認します。
    1. 既存または新規の **[リソース グループ]** を選択します。
    1. この Application Insights リソースの **[名前]** を指定します。
    1. お使いの場所の近くの **[リージョン]** を選択します。
    1. **[リソース モード]** を *[クラシック]* に設定します。 

1. **[確認および作成]** ボタンを選択します。
1. **[作成]** ボタンを選択します。
1. デプロイが完了したら、 **[リソースに移動]** ボタンを選択します。
1. 表示される **[概要]** から **[インストルメンテーション キー]** をコピーします ( **[要点]** の下にあります)。

## <a name="create-and-configure-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリの作成と構成

次の手順を完了して、新しい ASP.NET Core Web アプリを作成し、構成します。

1. 新しい ASP.NET Core Razor Pages アプリを作成する
    
    ```dotnetcli
    dotnet new razor -o ai.quickstart
    ```
    
    前のコマンドを使用すると、*ai.quickstart* という名前のディレクトリに新しい ASP.NET Core Razor Pages アプリが作成されます。 
    
    > [!TIP]
    > [Visual Studio を使用してアプリを作成する](/visualstudio/ide/quickstart-aspnet-core)こともできます。

1. プロジェクト ディレクトリ内から `Microsoft.ApplicationInsights.AspNetCore` パッケージをプロジェクトに追加します。 Visual Studio を使用している場合は、[NuGet パッケージ マネージャー](/nuget/consume-packages/install-use-packages-visual-studio)を使用できます。

    ```dotnetcli
    dotnet add package Microsoft.ApplicationInsights.AspNetCore --version 2.17.0 
    ```

1. テキスト エディターまたは IDE を使用して、次のように `ApplicationInsights.InstrumentationKey` の値を含むように *appsettings.json* を修正します。 先ほどコピーしたインストルメンテーション キーを使用します。

    :::code language="json" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/appsettings.json" range="1-12" highlight="2-4":::
    
    > [!IMPORTANT]
    > Application Insights SDK により、`ApplicationInsights.InstrumentationKey` の構成値が想定されています。 必ず正しい名前を付けてください。

## <a name="configure-server-side-telemetry"></a>サーバー側のテレメトリを構成する

*Startup.cs* の `ConfigureServices` メソッドで、Application Insights サービスをパイプラインに追加します。 強調表示された行を追加します。

:::code language="csharp" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/startup.cs" range="24-28" highlight="4":::

## <a name="configure-client-side-telemetry"></a>クライアント側のテレメトリを構成する

次の手順を行い、クライアント側のテレメトリを送信するようにアプリをインストルメント化します。

1. *Pages/_ViewImports.cshtml* に、次の行を追加します。

    ```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

    前の変更により、Application Insights のクライアント側スクリプト要素を含む `Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet` 依存関係が登録されます。

1. *Pages/Shared/_Layout.cshtml* の `<head>` 要素に、強調表示された行を追加します。

    :::code language="cshtml" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/pages/shared/_layout.cshtml" range="3-10" highlight="7":::

   この変更により、挿入された `JavaScriptSnippet` オブジェクトが使用され、アプリ内のすべてのページの `<head>` 要素に `<script>` 要素が確実にレンダリングされます。

## <a name="validate-telemetry-ingestion"></a>テレメトリの取り込みを検証する

テレメトリが分析のために Application Insights に取り込まれるまでに数分かかります。 リアルタイムでアプリからテレメトリが送信されていることを確認するには、 **[Live Metrics]** を使用します。

1. `dotnet run` または IDE を使用して Web アプリを実行します。
1. Azure portal で、Application Insights リソースを表示しているときに、 **[調査]** の **[Live Metrics]** を選択します。
1. アプリで、 **[ホーム]** と **[プライバシー]** のリンクを繰り返し選択します。
1. アプリで要求が行われるときに、 **[Live Metrics]** ディスプレイに表示されるアクティビティを観察します。

## <a name="next-steps"></a>次のステップ

お疲れさまでした。 これで、アプリから送信されるテレメトリを使用して、次のことができるようになりました。

- [ランタイム例外を見つける](tutorial-runtime-exceptions.md)。
- [パフォーマンスの問題を特定する](tutorial-performance.md)。
- [アプリの正常性についてアラートする](tutorial-alert.md)。

> [!div class="nextstepaction"]
> [ASP.NET Core の Application Insights の詳細を確認する](asp-net-core.md)
