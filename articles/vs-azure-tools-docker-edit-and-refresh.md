---
title: ローカルの Docker コンテナーでアプリをデバッグする | Microsoft Docs
description: 編集と更新を使用して、ローカルの Docker コンテナーで実行されているアプリに変更を加え、デバッグのブレークポイントを設定する方法について説明します。
services: container-service
author: ghogen
manager: douge
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.topic: article
ms.workload: multiple
ms.date: 09/11/2018
ms.author: ghogen
ms.openlocfilehash: 0f3f323cb4486c06f6f18de4c695efaf8dce4d99
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715722"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>ローカルの Docker コンテナーでアプリをデバッグする
## <a name="overview"></a>概要
Visual Studio 2017 を使用すると、一貫した方法でアプリケーションの開発と検証を Docker コンテナーでローカルで実行できます。
コード変更のたびにコンテナーを再起動する必要はありません。
この記事では、"編集と更新" の機能を使用して、ローカルの Docker コンテナーで ASP.NET Core Web アプリを起動し、必要な変更を行い、その変更を反映するためにブラウザーの表示を更新する方法について説明します。
また、デバッグ用のブレークポイントを設定する方法についても説明します。

## <a name="prerequisites"></a>前提条件
次のツールをインストールする必要があります。

* Web 開発ワークロードがインストールされている [Visual Studio 2017](https://www.visualstudio.com/downloads/)。

ローカルで Docker コンテナーを実行するには、ローカルの Docker クライアントが必要です。
Hyper-V を無効にして、[Docker Toolbox](https://www.docker.com/products/docker-toolbox) を使用するか、または Hyper-V を使用し、Windows 10 が必要な [Docker for Windows](https://www.docker.com/get-docker) を使用することができます。

Docker Toolbox を使用する場合は、[Docker クライアントを構成する](vs-azure-tools-docker-setup.md)必要があります。

## <a name="1-create-a-web-app"></a>1.Web アプリを作成する
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-edit-your-code-and-refresh"></a>2.コードを編集して更新する
変更をすばやく反復処理するには、コンテナー内でアプリケーションを起動して変更を行うと、IIS Express を使用する場合のように表示できます。

1. ソリューション構成を `Debug` に設定して **Ctrl + F5** キーを押すと、Docker イメージが作成され、ローカルで実行されます。

    コンテナー イメージが作成されて Docker コンテナーで実行されると、Visual Studio は既定のブラウザーでその Web アプリを起動します。
    Microsoft Edge ブラウザーを使用している場合、またはエラーが発生している場合は、「 [トラブルシューティング](vs-azure-tools-docker-troubleshooting-docker-errors.md) 」セクションを参照してください。
2. [About] ページに移動して、変更を加えます。
3. Visual Studio に戻り、 `Views\Home\About.cshtml`を開きます。
4. ファイルの最後に次の HTML コンテンツを追加し、変更を保存します。

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. 出力ウィンドウで .NET ビルドが完了したことを確認し、次の行が表示されたら、お使いのブラウザーに戻り、[About] ページを更新します。

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```

6. 変更が適用されました。

## <a name="3-debug-with-breakpoints"></a>手順 3.ブレークポイントを使ってデバッグする
多くの場合、変更にはさらなる検査が必要になります。この場合、Visual Studio のデバッグ機能を活用します。

1. Visual Studio に戻り、`About.cshtml.cs` を開きます。
2. OnGet() メソッドの内容を次のように置き換えます。

   ```cs
       Message = "Your application description page from within a Container";
   ```

3. ブレークポイントをコード行の左側に設定します。
4. **F5** キーを押して、デバッグを開始します。
5. [About] ページに移動して、ブレークポイントを探します。
6. Visual Studio に切り替えてブレークポイントを表示し、メッセージの値を検査します。

   ![][2]

## <a name="summary"></a>まとめ
Visual Studio 2017 の Docker のサポートにより、Docker コンテナー内での開発の生産性が実際に向上するため、ローカル作業の生産性が上がります。

## <a name="troubleshooting"></a>トラブルシューティング
[Docker エラーのトラブルシューティング](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Visual Studio、Windows、および Azure での Docker の詳細について
* [Visual Studio でのコンテナー開発](/visualstudio/containers) - コンテナー開発のハブ ページ
* [Azure Pipelines 向けの Docker の統合](http://aka.ms/dockertoolsforvsts) - Docker コンテナーの構築とデプロイ
* [Visual Studio Code 用 Docker ツール](http://aka.ms/dockertoolsforvscode) - Docker ファイルを編集するための言語サービス (e2e シナリオは増加予定)
* [Windows コンテナー情報](http://aka.ms/containers)- Windows Server および Nano Server の情報
* [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) - [Azure Kubernetes Service ドキュメント](/azure/aks)

## <a name="various-docker-tools"></a>さまざまな Docker ツール
[Some great docker tools (Steve Lasker's blog) (いくつかの優れた Docker ツール (Steve Lasker のブログ))](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>お勧めの記事
[Introduction to Microservices from NGINX (Nginx のマイクロサービスの概要)](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>プレゼンテーション
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Steve Lasker: VS Live ラスベガス 2016 - Docker e2e)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction to ASP.NET Core @ build 2016 - Where You At Demo (ASP.NET Core の概要 @ ビルド 2016 - デモンストレーション)](https://channel9.msdn.com/Events/Build/2016/B810)
* [コンテナーでの .NET アプリの開発、Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
