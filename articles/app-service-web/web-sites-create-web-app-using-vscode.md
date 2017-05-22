---
title: "Visual Studio Code を使用した ASP.NET Core Web アプリの作成"
description: "このチュートリアルでは、Visual Studio Code を使用して ASP.NET Core Web アプリを作成する方法について説明します。"
services: app-service\web
documentationcenter: .net
author: erikre
manager: erikre
editor: jimbe
ms.assetid: 877bff08-9ef7-405a-a1ca-1194f33c55f2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: e8f78602b293863b2e58160a5eb2cf8a5855576b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-aspnet-core-web-app-in-visual-studio-code"></a>Visual Studio Code を使用した ASP.NET Core Web アプリの作成
## <a name="overview"></a>概要
このチュートリアルでは、[Visual Studio Code (VS Code)](http://code.visualstudio.com//Docs/whyvscode) を使用して ASP.NET Core Web アプリを作成したり、それを [Azure App Service](../app-service/app-service-value-prop-what-is.md) にデプロイしたりする方法について説明します。 

> [!NOTE]
> この記事は、Web アプリについて言及していますが、API アプリとモバイル アプリにも適用されます。 
> 
> 

ASP.NET Core は、ASP.NET の刷新版です。 ASP.NET Core は、.NET を使用して最新のクラウドベースの Web アプリを構築するための、新しいオープン ソースのクロスプラットフォーム フレームワークです。 詳細については、「[Introduction to ASP.NET Core (ASP.NET Core の概要)](http://docs.asp.net/latest/conceptual-overview/aspnet.html)」を参照してください。 Azure App Service Web Apps については、 [Web Apps の概要](app-service-web-overview.md)に関するページを参照してください。

[!INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## <a name="prerequisites"></a>前提条件
* [VS コード](http://code.visualstudio.com/Docs/setup)をインストールします。
* Git をインストールします。これは、[Chocolatey](https://chocolatey.org/packages/git) または [git-scm.com](http://git-scm.com/downloads) のいずれかの場所からインストールできます。 Git を初めて使う場合は、[git-scm.com](http://git-scm.com/downloads) を選択し、**Windows コマンド プロンプトから Git を使用する**オプションを選択します。 Git をインストールした後、(VS コードからコミットを実行する場合に) チュートリアルの後半で必要になるため、Git のユーザー名と電子メールも設定する必要があります。  

## <a name="install-aspnet-core"></a>ASP.NET Core のインストール
ASP.NET Core は、OS X、Linux、Windows 上で動作する最新のクラウドや Web アプリを構築するための、効率性に優れた .NET スタックです。 ASP.NET 5 および DNX は、一から設計し直され、クラウドにデプロイされるアプリまたはオンプレミスで実行されるアプリ用に最適化された開発フレームワークを提供します。 オーバーヘッドを最小に抑えたモジュラー コンポーネントから構成されるため、ソリューションを構築するときに柔軟性を保つことができます。

このチュートリアルでは、最新の開発バージョンの ASP.NET Core を使用してアプリケーションの構築を開始する方法について説明します。 次の手順は、Windows に固有の手順です。 OS X、Linux、および Windows 用のインストール手順については、「[Getting Sratrted with ASP.NET Core (ASP.NET Core の概要)](https://docs.microsoft.com/aspnet/core/getting-started)」を参照してください。 


> [!NOTE]
> OS X、Linux、および Windows 用の詳細なインストール手順については、[ASP.NET Core のインストール](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)に関するページを参照してください。 
> 
> 

## <a name="create-the-web-app"></a>Web アプリの作成
このセクションでは、donet CLI ツールを使用して新しい ASP.NET Web アプリをスキャフォールディングする方法について説明します。 

1. コマンド プロンプトで次のコマンドを入力して、プロジェクト フォルダーを作成し、アプリをスキャフォールディングします。
   
```terminal
mkdir SampleWebApp
cd SampleWebApp
dotnet new mvc
```
![dotnet CLI - ASP.NET Core ジェネレーター](./media/web-sites-create-web-app-using-vscode/dotnetcore-mvc-01.png)

2. 必要な NuGet パッケージをインストールするためには、次のコマンドを実行します。
   
    ```terminal
    dotnet restore
    ```

## <a name="run-the-web-app-locally"></a>ローカルでの Web アプリの実行
Web アプリが作成され、アプリのすべての NuGet パッケージが取得されたため、Web アプリをローカルで実行できます。

1. アプリを実行します (アプリが古い場合は、`dotnet run` コマンドを実行するとアプリを構築できます)。
    ```terminal
    dotnet run
    ```
2. ブラウザーを開き、次の URL に移動します。
   
    **http://localhost:5000**
   
    Web アプリの既定のページが次のように表示されます。
   
    ![ブラウザーでのローカル Web アプリ](./media/web-sites-create-web-app-using-vscode/08-web-app.png)
3. ブラウザーを閉じます。 **コマンド ウィンドウ**で、**Ctrl+C** キーを押してアプリケーションをシャットダウンするか、**コマンド ウィンドウ**を閉じます。 

## <a name="create-a-web-app-in-the-azure-portal"></a>Azure ポータルで Web アプリを作成する
次の手順では、Azure ポータルでの Web アプリの作成について説明します。

1. [Azure ポータル](https://portal.azure.com)にログインします。
2. ポータルの左上にある **[新規]** をクリックします。
3. **[Web アプリ] > [Web アプリ]** をクリックします。
   
    ![Azure の新しい Web アプリ](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)
4. **[名前]** に **SampleWebAppDemo** などの値を入力します。 この名前は、一意にする必要があります。ポータルで名前を入力しようとするときに、この一意性が要求されます。 したがって、別の値を選択する場合は、このチュートリアルに表示されるすべての **SampleWebAppDemo** をこの値に置き換える必要があります。 
5. 既存の **App Service プラン** を選択するか、新しいプランを作成します。 新しいプランを作成する場合は、価格レベル、および場所などのオプションを選択します。 App Service プランの詳細については、「 [Azure App Service プランの詳細](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」の記事を参照してください。
   
    ![Azure の新しい  Web アプリ ブレード](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)
6. **[作成]**をクリックします。
   
    ![Web アプリ ブレード](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>新しい Web アプリの Git 発行の有効化
Git は、Azure App Service の Web アプリをデプロイするために使用できる分散型バージョン コントロール システムです。 Web アプリ用に記述したコードはローカルの Git リポジトリに格納されます。このコードをリモート リポジトリにプッシュして Azure にデプロイします。   

1. [Azure ポータル](https://portal.azure.com)にログインします。
2. **[参照]**をクリックします。
3. **[Web アプリ]** をクリックして 、Azure サブスクリプションに関連付けられている Web アプリの一覧を表示します。
4. このチュートリアルで作成した Web アプリを選択します。
5. Web アプリのブレードで、**[設定]**  >  **[継続的なデプロイ]** をクリックします。 
   
    ![Azure Web アプリ ホスト](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)
6. **[ソースの選択]、[ローカル Git リポジトリ]** の順にクリックします。
7. **[OK]**をクリックします。
   
    ![Azure のローカル Git リポジトリ](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)
8. Web アプリまたはその他の App Service アプリを発行するためのデプロイメント資格情報をまだ設定していない場合は、ここで設定します。
   
   * **[設定]**  >  **[デプロイ資格情報]** をクリックします。 **[デプロイ資格情報の設定]** ブレードが表示されます。
   * ユーザー名とパスワードを作成します。  このパスワードは、後で Git をセットアップするときに必要になります。
   * **[保存]**をクリックします。
9. Web アプリのブレードで、**[設定]、[プロパティ]** の順にクリックします。 デプロイ先のリモート Git リポジトリの URL は、 **[GIT URL]**の下に表示されます。
10. チュートリアルで後で使用するために、 **GIT URL** の値をコピーします。
    
    ![Azure Git の URL](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## <a name="publish-your-web-app-to-azure-app-service"></a>Azure App Service への Web アプリの発行
このセクションでは、ローカル Git リポジトリを作成し、そのリポジトリから  Azure にプッシュして、Web アプリを Azure にデプロイします。

1. VS コードの左側のナビゲーション バーで、 **[Git]** オプションを選択します。
   
    ![VS コードでの Git アイコン](./media/web-sites-create-web-app-using-vscode/git-icon.png)
2. **[Git リポジトリの初期化]** を選択して、ワークスペースが Git によるソース管理の対象になるように設定します。 
   
    ![Git の初期化](./media/web-sites-create-web-app-using-vscode/19-initgit.png)
3. コマンド ウィンドウを開き、ディレクトリを Web アプリのディレクトリに変更します。 次のコマンドを入力します。
   
        git config core.autocrlf false
   
    このコマンドは、末尾に CRLF と LF が含まれているテキストに関する問題を防ぎます。
4. VS コードで、コミット メッセージを追加し、 **[すべてコミット]** チェック アイコンをクリックします。
   
    ![Git すべてコミット](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)
5. Git の処理が完了した後には、[Git] ウィンドウの **[変更]**の下にいかなるファイルも表示されません。 
   
    ![Git 変更なし](./media/web-sites-create-web-app-using-vscode/no-changes.png)
6. コマンド プロンプトが Web アプリがあるディレクトリを指しているコマンド ウィンドウに戻します。
7. コピーしておいた (".git" で終わる) Git URL を使用して、Web アプリに更新をプッシュするためのリモート参照を作成します。
   
        git remote add azure [URL for remote repository]
8. 資格情報をローカルに保存するように Git を構成して、VS コードから生成される push コマンドに資格情報が自動的に追加されるようにします。
   
        git config credential.helper store
9. 次のコマンドを入力して、変更内容を Azure にプッシュします。 この Azure への初回の push を実行した後、VS コードからすべての push コマンドを実行できます。 
   
        git push -u azure master
   
    Azure で以前に作成したパスワードを入力するように求められます。 **注: パスワードは表示されません。**
   
    このコマンドからは、デプロイメントが成功したというメッセージが最後に出力されます。
   
        remote: Deployment successful.
        To https://user@testsite.scm.azurewebsites.net/testsite.git
        [new branch]      master -> master

> [!NOTE]
> アプリを変更した場合は、組み込み Git 機能を使用して VS コードに直接再パブリッシュできます。これを行うには、**[すべてコミット]** オプションに続けて **[プッシュ]** オプションを選択します。 **[プッシュ]** オプションは、**[すべてコミット]** ボタンと **[更新]** ボタンの隣のドロップダウン メニューに含まれています。
> 
> 

プロジェクトで共同作業を行う必要がある場合は、Azure へのプッシュの間に GitHub へのプッシュを実行することを考慮してください。

## <a name="run-the-app-in-azure"></a>Azure でのアプリの実行
これで Web アプリがデプロイされたため、Azure でホストされているアプリを実行してみましょう。 

これは、2 つの方法で実行できます。

* ブラウザーを開き、次のように、Web アプリの名前を入力します。   
  
        http://SampleWebAppDemo.azurewebsites.net
* Azure ポータルで、Web アプリの Web アプリ ブレードを見つけ、 **[参照]** をクリックして、既定のブラウザーで 
* アプリを表示します。

![Azure の Web アプリ](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## <a name="summary"></a>概要
このチュートリアルでは、VS コードで、Web アプリを作成し、Azure にデプロイする方法を学習しました。 VS コードの詳細については、[Visual Studio Code を使用する理由](https://code.visualstudio.com/Docs/)に関する記事をご覧ください。 App Service Web Apps の詳細については、[Web Apps の概要](app-service-web-overview.md)に関するページを参照してください。 


