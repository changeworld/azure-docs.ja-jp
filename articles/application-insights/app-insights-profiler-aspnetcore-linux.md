---
title: Application Insights Profiler を使用して ASP.NET Core Azure Linux Web アプリをプロファイルする | Microsoft Docs
description: 概念と有効化方法に関する詳細手順チュートリアル
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 2d7405baee84b53311f01e748ca7975147c107d8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Application Insights Profiler を使用して ASP.NET Core Azure Linux Web アプリをプロファイルする

現在、この機能はプレビュー段階にあります。

[Application Insights](app-insights-overview.md) を使用するときにライブ Web アプリの各メソッドにどれくらい時間がかかっているかを確認できます。 Profiler は現在、App Services の Linux でホストされている ASP.NET Core Web アプリで利用できます。 このガイドでは、ASP.NET Core Linux Web アプリのためにプロファイラー トレースを収集するしくみについて段階的に説明します。

このチュートリアルを完了すると、下のスクリーンショットのようにアプリはプロファイラー トレースを収集します。 この例では、プロファイラー トレースは、特定の Web 要求が遅くなっていることを示しています。ほとんどが待ち時間になっています。 アプリを遅くしているコードのホット パスには炎を模したアイコンが付きます。 この例では、`HomeController` の `About` メソッドが Web アプリを遅くしています。`Thread.Sleep` を呼び出しているためです。

![プロファイラー トレース](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>前提条件
以下の指示は、あらゆる Windows、Linux、Mac 開発環境に適用されます。

* [.NET core SDK 2.1.2 以降](https://www.microsoft.com/net/download/windows/build)をインストールします。
* 「[Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)」の指示に基づいて Git をインストールします。

## <a name="setup-project-locally"></a>プロジェクトをローカルでセットアップする

1. お使いのコンピューターでコマンド プロンプトを開きます。 以下の指示は、あらゆる Windows、Linux、Mac 開発環境に適用されます。

2. ASP.NET Core MVC Web アプリケーションを作成します。
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. コマンド プロンプトのディレクトリをプロジェクトのルート フォルダーに変更します。

4. プロファイラー トレースを収集するための NuGet パッケージを追加します。
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. HomeController.cs に無作為で数秒遅らせるためのコード行を追加します。

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. 変更内容をローカル リポジトリに保存し、コミットします。

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>プロジェクトをホストするための Azure App Service を作成する
1. App Services Linux 環境を作成します。

    ![Linux App Services を作成する](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. デプロイ資格情報を作成します。 パスワードをメモしておきます。後でアプリをデプロイするときに必要になります。

    ![デプロイ資格情報を作成する](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. デプロイ オプションを選択します。 Azure Portal の指示に従い、Web アプリでローカル Git リポジトリを設定します。 Git リポジトリが自動的に作成されます。

    ![Git リポジトリを設定する](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

その他のデプロイ オプションは[こちら](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)にあります。

## <a name="deploy-your-project"></a>プロジェクトのデプロイ

1. コマンド プロンプトで、プロジェクトのルート フォルダーに移動します。 App Services のものを指すように Git リモート リポジトリを追加します。

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * "デプロイ資格情報の作成" 手順の 'ユーザー名' を使用します。
    * "アプリ サービスの作成" 手順の 'ユーザー名' を使用します。

2. Azure に変更をプッシュし、プロジェクトをデプロイします。

    ```
    git push azure master
    ```
次のような出力が表示されます。

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Application Insights を追加して Web アプリを監視する
1. [Application Insights リソースを作成します](./app-insights-create-new-resource.md)。
2. Application Insights リソースの iKey をコピーし、自分のアプリ サービスで次のように設定します。

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![アプリの設定](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    アプリの設定を変更すると、サイトが自動的に再起動します。 新しい設定が適用されると、直後、プロファイラーが 2 分間の実行を開始します。 その後、1 時間ごとに 2 分間実行します。

3. Web サイトにいくらかのトラフィックを生成させます。 たとえば、サイトの ```About``` ページを数回更新してみます。

4. イベントが Application Insights に集計されるように、2 - 5 分間待ちます。

5. Azure Portal の Application Insights パフォーマンス ウィンドウに移動します。 右下隅にプロファイラー トレースが表示されます。

    ![トレースを表示する](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="next-steps"></a>次の手順
App Services でホストされているカスタム コンテナーを使用している場合は、[ コンテナー化された ASP.NET Core アプリケーションのサービス プロファイラーを有効にする方法](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp)に関するページの手順で App Insight Profiler を有効にします

問題や提案があれば、Github リポジトリ [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues) に報告してください。
