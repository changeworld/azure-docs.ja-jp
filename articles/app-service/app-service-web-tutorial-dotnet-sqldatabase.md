---
title: チュートリアル:Azure SQL Database を使用した ASP.NET アプリ
description: C# ASP.NET アプリを Azure と Azure SQL Database にデプロイする方法について説明します
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/08/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 72ea37f483055ae3bcb97520efa7f2a4fb704037
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058865"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>チュートリアル:Azure SQL Database を使用して Azure に ASP.NET アプリをデプロイする

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、App Service でデータ主導の ASP.NET アプリをデプロイし、それを <bpt id="p1">[</bpt>Azure SQL Database<ept id="p1">](../azure-sql/database/sql-database-paas-overview.md)</ept> に接続する方法について説明します。 これが完了すると、ASP.NET アプリは Azure 内で実行され、SQL Database に接続された状態になります。

![Azure App Service に発行された ASP.NET アプリケーション](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Azure SQL Database でデータベースを作成する
> * ASP.NET アプリを SQL Database に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure からターミナルにログをストリーミングする
> * Azure Portal でアプリを管理する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

**ASP.NET および Web の開発** のワークロードと共に <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2022</a> をインストールする。

既に Visual Studio をインストールしている場合は、 <bpt id="p1">**</bpt>[ツール]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[Get Tools and Features]\(ツールと機能の取得\)<ept id="p2">**</ept> の順にクリックして、Visual Studio 内でワークロードを追加します。

## <a name="download-the-sample"></a>サンプルのダウンロード

1. <bpt id="p1">[</bpt>サンプル プロジェクトをダウンロードします<ept id="p1">](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip)</ept>。

1. <bpt id="p1">*</bpt>dotnet-sqldb-tutorial-master.zip<ept id="p1">*</ept> ファイルを抽出 (解凍) します。

このサンプル プロジェクトには、<bpt id="p2">[</bpt>Entity Framework Code First<ept id="p2">](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)</ept> を使用した基本的な <bpt id="p1">[</bpt>ASP.NET MVC<ept id="p1">](https://www.asp.net/mvc)</ept> "作成、読み取り、更新、削除" (CRUD) アプリが含まれています。

### <a name="run-the-app"></a>アプリを実行する

1. Visual Studio で <bpt id="p1">*</bpt>dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln<ept id="p1">*</ept> ファイルを開きます。

1. デバッグせずにアプリを実行するには、<ph id="ph1">`Ctrl+F5`</ph> キーを押します。 アプリが既定のブラウザーに表示されます。

1. **[新規作成]** リンクを選択し、いくつかの *To Do* アイテムを作成します。

    ![[新しい ASP.NET プロジェクト] ダイアログ ボックス](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. <bpt id="p1">**</bpt>[編集]<ept id="p1">**</ept> 、 <bpt id="p2">**</bpt>[詳細]<ept id="p2">**</ept> 、 <bpt id="p3">**</bpt>[削除]<ept id="p3">**</ept> リンクをテストします。

アプリはデータベース コンテキストを使用してデータベースに接続します。 このサンプルでは、データベース コンテキストは <ph id="ph1">`MyDbConnection`</ph> という接続文字列を使用します。 この接続文字列は <bpt id="p1">*</bpt>Web.config<ept id="p1">*</ept> ファイルで設定され、<bpt id="p2">*</bpt>Models/MyDatabaseContext.cs<ept id="p2">*</ept> ファイルで参照されます。 この接続文字列名は、このチュートリアルの後半で Azure アプリを Azure SQL Database に接続するために使用します。

## <a name="publish-aspnet-application-to-azure"></a>ASP.NET アプリケーションを Azure に発行する

1. **ソリューション エクスプローラー** で **DotNetAppSqlDb** プロジェクトを右クリックし、 **[発行]** を選択します。

    ![ソリューション エクスプローラーから発行する](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. ターゲットとして <bpt id="p1">**</bpt>[Azure]<ept id="p1">**</ept> を選択し、 <bpt id="p2">**</bpt>[次へ]<ept id="p2">**</ept> をクリックします。

1. <bpt id="p1">**</bpt>[Azure App Service (Windows)]<ept id="p1">**</ept> が選択されていることを確認し、 <bpt id="p2">**</bpt>[次へ]<ept id="p2">**</ept> をクリックします。

#### <a name="sign-in-and-add-an-app"></a>サインインしてアプリを追加する

1. **[発行]** ダイアログの **[サインイン]** をクリックします。

1. Azure サブスクリプションにサインインします。 既に Microsoft アカウントにサインインしている場合は、アカウントが Azure サブスクリプションを保持していることを確認します。 サインインしている Microsoft アカウントが Azure サブスクリプションを備えていない場合は、正しいアカウントをクリックして追加します。

1. <bpt id="p1">**</bpt>[App Service インスタンス]<ept id="p1">**</ept> ペインで <bpt id="p2">**</bpt>[<ph id="ph1">+</ph>]<ept id="p2">**</ept> をクリックします。

    ![Azure へのサインイン](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>Web アプリ名を構成する

生成された Web アプリ名をそのまま使用するか、別の一意の名前に変更することができます (有効な文字は <ph id="ph1">`a-z`</ph>、<ph id="ph2">`0-9`</ph>、および <ph id="ph3">`-`</ph> です)。 この Web アプリ名は、アプリの既定の URL の一部として使用されます (既定の URL は <ph id="ph1">`<app_name>.azurewebsites.net`</ph> で、<ph id="ph2">`<app_name>`</ph> が Web アプリ名です)。 この Web アプリ名は、Azure のすべてのアプリで一意である必要があります。

> [!NOTE]
> まだ <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> は選択しないでください。

![[App Service の作成] ダイアログ](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. <bpt id="p1">**</bpt>[リソース グループ]<ept id="p1">**</ept> の横にある <bpt id="p2">**</bpt>[新規]<ept id="p2">**</ept> をクリックします。

   ![[リソース グループ] の横にある [新規] をクリックする](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. リソース グループに <bpt id="p1">**</bpt>myResourceGroup<ept id="p1">**</ept> という名前を付けます。

#### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. <bpt id="p1">**</bpt>[ホスティング プラン]<ept id="p1">**</ept> の隣にある <bpt id="p2">**</bpt>[新規]<ept id="p2">**</ept> をクリックします。

1. <bpt id="p1">**</bpt>[App Service プランの構成]<ept id="p1">**</ept> ダイアログ ボックスで、新しい App Service プランを次の設定で構成し、 <bpt id="p2">**</bpt>[OK]<ept id="p2">**</ept> をクリックします。

   | 設定  | 推奨値 | 詳細情報 |
   | ----------------- | ------------ | ----|
   |**App Service プラン**| myAppServicePlan | <bpt id="p1">[</bpt>App Service プラン<ept id="p1">](../app-service/overview-hosting-plans.md)</ept> |
   |**場所**| 西ヨーロッパ | [Azure リージョン](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**[サイズ]**| Free | [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![Create App Service plan](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> をクリックして、Azure リソースが作成されるまで待ちます。

1. 構成したリソースが <bpt id="p1">**</bpt>[発行]<ept id="p1">**</ept> ダイアログに表示されます。 **[完了]** をクリックします。

   ![作成したリソース](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>サーバーとデータベースを作成する

データベースを作成するには、<bpt id="p1">[</bpt>論理 SQL サーバー<ept id="p1">](../azure-sql/database/logical-servers.md)</ept>が必要です。 論理 SQL サーバーは、1 つのグループとして管理される一連のデータベースを含む論理コンストラクトです。

1. <bpt id="p1">**</bpt>[発行]<ept id="p1">**</ept> ダイアログで、下の <bpt id="p2">**</bpt>[サービスの依存関係]<ept id="p2">**</ept> セクションまでスクロールします。 <bpt id="p1">**</bpt>[SQL Server データベース]<ept id="p1">**</ept> の横にある <bpt id="p2">**</bpt>[構成]<ept id="p2">**</ept> をクリックします。

    > [!NOTE]
    > SQL Database は、必ず **[接続済みサービス]** ページではなく、 **[発行]** ページから構成してください。

   ![SQL Database の依存関係を構成する](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. <bpt id="p1">**</bpt>[Azure SQL Database]<ept id="p1">**</ept> を選択し、 <bpt id="p2">**</bpt>[次へ]<ept id="p2">**</ept> をクリックします。

1. <bpt id="p1">**</bpt>[Configure Azure SQL Database]\(Azure SQL Database の構成\)<ept id="p1">**</ept> ダイアログで <bpt id="p2">**</bpt>[<ph id="ph1">+</ph>]<ept id="p2">**</ept> をクリックします。

1. <bpt id="p1">**</bpt>[データベース サーバー]<ept id="p1">**</ept> の横にある <bpt id="p2">**</bpt>[新規]<ept id="p2">**</ept> をクリックします。

   このサーバー名はサーバーの既定の URL (`<server_name>.database.windows.net`) の一部として使用されます。 これは、Azure SQL のすべてのサーバーで一意である必要があります。 サーバー名を目的の値に変更します。

1. 管理者のユーザー名とパスワードを追加します。 パスワードの複雑さの要件については、「<bpt id="p1">[</bpt>パスワード ポリシー<ept id="p1">](/sql/relational-databases/security/password-policy)</ept>」をご覧ください。

   このユーザー名とパスワードを覚えておいてください。 これらは、後でサーバーを管理する際に必要になります。

   ![Create server](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > (Visual Studio および App Service で) 接続文字列のパスワードがマスクされていても、それがどこかに保持されているのは事実であり、アプリの攻撃対象領域が増えることになります。 App Service では、<bpt id="p1">[</bpt>マネージド サービス ID<ept id="p1">](overview-managed-identity.md)</ept> を使用して、コードやアプリの構成にシークレットを保持する必要性をなくすことで、このリスクを排除できます。 詳細については、「<bpt id="p1">[</bpt>次のステップ<ept id="p1">](#next-steps)</ept>」を参照してください。

1. **[OK]** をクリックします。

1. <bpt id="p1">**</bpt>[Azure SQL Database]<ept id="p1">**</ept> ダイアログの <bpt id="p2">**</bpt>[データベース名]<ept id="p2">**</ept> は、生成された既定の名前のままにします。 <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択して、データベース リソースが作成されるまで待ちます。

    ![データベースを構成する](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>データベース接続を構成する

1. ウィザードでデータベース リソースの作成が完了したら、 <bpt id="p1">**</bpt>[次へ]<ept id="p1">**</ept> をクリックします。

1. <bpt id="p1">**</bpt>[データベース接続文字列名]<ept id="p1">**</ept> に「<bpt id="p2">_</bpt>MyDbConnection<ept id="p2">_</ept>」と入力します。 この名前は、<bpt id="p1">_</bpt>Models/MyDatabaseContext.cs<ept id="p1">_</ept> で参照されている接続文字列と一致する必要があります。

1. <bpt id="p1">**</bpt>[データベース接続ユーザー名]<ept id="p1">**</ept> と <bpt id="p2">**</bpt>[データベース接続パスワード]<ept id="p2">**</ept> に、<bpt id="p3">[</bpt>サーバーの作成<ept id="p3">](#create-a-server-and-database)</ept>で使用した管理者のユーザー名とパスワードを入力します。

1. <bpt id="p1">**</bpt>[Azure アプリの設定]<ept id="p1">**</ept> が選択されていることを確認し、 <bpt id="p2">**</bpt>[完了]<ept id="p2">**</ept> をクリックします。

    > [!NOTE]
    > **ローカル ユーザー シークレット ファイル** が表示される場合は、 **[発行]** ページではなく、 **[接続済みサービス]** ページから SQL Database を構成しておく必要があります。

    ![データベース接続文字列を構成する](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. 構成ウィザードの完了を待って <bpt id="p1">**</bpt>[閉じる]<ept id="p1">**</ept> をクリックします。

#### <a name="deploy-your-aspnet-app"></a>ASP.NET アプリをデプロイする

1. <bpt id="p1">**</bpt>[発行]<ept id="p1">**</ept> タブで再び上へスクロールし、 <bpt id="p2">**</bpt>[発行]<ept id="p2">**</ept> をクリックします。 ASP.NET アプリが Azure にデプロイされると、 既定のブラウザーが、デプロイされたアプリの URL を参照した状態で起動します。

1. いくつかの To Do アイテムを追加します。

    ![Azure アプリに発行された ASP.NET アプリケーション](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    お疲れさまでした。 データ主導の ASP.NET アプリケーションを Azure App Services でライブ実行することができました。

## <a name="access-the-database-locally"></a>データベースにローカルでアクセスする

Visual Studio では、<bpt id="p1">**</bpt>SQL Server オブジェクト エクスプローラー<ept id="p1">**</ept>を使用して、Azure の新しいデータベースの確認と管理を簡単に行うことができます。 新しいデータベースのファイアウォールは、作成した App Service アプリに対しては既に開放されていますが、ローカル コンピューター (Visual Studio など) からアクセスするためには、ローカル コンピューターのパブリック IP アドレスに対してファイアウォールを開放する必要があります。 お使いのパブリック IP アドレスがインターネット サービス プロバイダーによって変更された場合、Azure データベースに再度アクセスするためには、ファイアウォールを再構成する必要があります。

#### <a name="create-a-database-connection"></a>データベース接続を作成する

1. <bpt id="p1">**</bpt>[表示]<ept id="p1">**</ept> メニューの <bpt id="p2">**</bpt>[SQL Server オブジェクト エクスプローラー]<ept id="p2">**</ept> を選択します。

1. <bpt id="p1">**</bpt>SQL Server オブジェクト エクスプローラー<ept id="p1">**</ept>の上部で、 <bpt id="p2">**</bpt>[SQL Server の追加]<ept id="p2">**</ept> ボタンをクリックします。

#### <a name="configure-the-database-connection"></a>データベース接続を構成する

1. <bpt id="p1">**</bpt>[接続]<ept id="p1">**</ept> ダイアログで、<bpt id="p2">**</bpt>Azure<ept id="p2">**</ept> ノードを展開します。 Azure 上のすべての SQL Database が一覧表示されます。

1. 前に作成したデータベースを選択します。 前に作成した接続が、一番下に自動的に入力されます。

1. 前に作成したデータベース管理者のパスワードを入力し、 <bpt id="p1">**</bpt>[接続]<ept id="p1">**</ept> をクリックします。

    ![Visual Studio からデータベース接続を構成する](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>コンピューターからのクライアント接続を許可する

<bpt id="p1">**</bpt>[新しいファイアウォール規則の作成]<ept id="p1">**</ept> ダイアログが開かれています。 既定では、サーバーはお使いの Azure アプリなどの Azure サービスからデータベースへの接続のみを許可します。 Azure の外部からデータベースに接続するには、サーバー レベルでファイアウォール規則を作成します。 ファイアウォール規則で、ローカル コンピューターのパブリック IP アドレスを許可します。

ダイアログには、既にコンピューターのパブリック IP アドレスが入力されています。

1. <bpt id="p1">**</bpt>クライアント IP を追加<ept id="p1">**</ept>するための項目が選択されていることを確認し、 <bpt id="p2">**</bpt>[OK]<ept id="p2">**</ept> をクリックします。

    ![ファイアウォール規則の作成](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Visual Studio で SQL データベース インスタンスのファイアウォール設定の作成が完了すると、<bpt id="p1">**</bpt>SQL Server オブジェクト エクスプローラー<ept id="p1">**</ept>に接続が表示されます。

    ここでは、クエリの実行やビューとストアド プロシージャの作成など、最も一般的なデータベース操作を実行できます。

1. 接続を展開し、 <bpt id="p1">**</bpt>[データベース]<ept id="p1">**</ept> 、<ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[<ph id="ph2">&lt;</ph>お使いのデータベース>]<ept id="p2">**</ept> 、<ph id="ph3"> > </ph> <bpt id="p3">**</bpt>[テーブル]<ept id="p3">**</ept> の順に選択します。 <ph id="ph1">`Todoes`</ph> テーブルを右クリックし、 <bpt id="p1">**</bpt>[データの表示]<ept id="p1">**</ept> を選択します。

    ![SQL Database オブジェクトを確認する](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Code First Migrations を使用してアプリを更新する

Visual Studio の使い慣れたツールを使用して、Azure でデータベースとアプリを更新できます。 この手順では、Entity Framework の Code First Migrations を使用して、データベース スキーマに変更を加え、Azure に発行します。

Entity Framework Code First Migrations の使用方法の詳細については、「<bpt id="p1">[</bpt>Getting Started with Entity Framework 6 Code First using MVC 5 (MVC 5 を使用した Entity Framework 6 Code First の概要)<ept id="p1">](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)</ept>」をご覧ください。

#### <a name="update-your-data-model"></a>データ モデルを更新する

コード エディターで <bpt id="p1">_</bpt>Models\Todo.cs<ept id="p1">_</ept> を開きます。 `ToDo` クラスに次のプロパティを追加します。

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Code First Migrations をローカルで実行する

いくつかのコマンドを実行して、ローカル データベースを更新します。

1. <bpt id="p1">**</bpt>[ツール]<ept id="p1">**</ept> メニューで、 <bpt id="p2">**</bpt>[NuGet パッケージ マネージャー]<ept id="p2">**</ept> <ph id="ph1"> > </ph> <bpt id="p3">**</bpt>[パッケージ マネージャー コンソール]<ept id="p3">**</ept> の順にクリックします。

1. [パッケージ マネージャー コンソール] ウィンドウで、Code First Migrations を有効にします。

    ```powershell
    Enable-Migrations
    ```
    
1. 移行を追加します。

    ```powershell
    Add-Migration AddProperty
    ```
    
1. ローカル データベースを更新します。

    ```powershell
    Update-Database
    ```
    
1. <ph id="ph1">`Ctrl+F5`</ph> キーを押してアプリを実行します。 編集、詳細、作成のリンクをテストします。

エラーが発生せずにアプリケーションが読み込まれたら、Code First Migrations は成功です。 ただし、ページはまだ変わっていないように見えます。これは、この新しいプロパティがまだアプリケーション ロジックで使用されていないためです。

#### <a name="use-the-new-property"></a>新しいプロパティを使用する

<ph id="ph1">`Done`</ph> プロパティを使用するために、コードにいくつかの変更を加えます。 このチュートリアルでは、わかりやすくするために `Index` ビューと `Create` ビューのみを変更して、実際のプロパティを確認します。

1. <bpt id="p1">_</bpt>Controllers\TodosController.cs<ept id="p1">_</ept> を開きます。

1. 52 行目にある <ph id="ph1">`Create()`</ph> メソッドを探し、<ph id="ph3">`Bind`</ph> 属性内のプロパティの一覧に <ph id="ph2">`Done`</ph> を追加します。 完了すると、`Create()` メソッドのシグネチャは次のコードのようになります。

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. <bpt id="p1">_</bpt>Views\Todos\Create.cshtml<ept id="p1">_</ept> を開きます。

1. Razor コードでは、<ph id="ph2">`model.Description`</ph> を使用する <ph id="ph1">`<div class="form-group">`</ph> 要素、<ph id="ph4">`model.CreatedDate`</ph> を使用する別の <ph id="ph3">`<div class="form-group">`</ph> 要素が表示されます。 これら 2 つの要素の直後に、次のように、<ph id="ph2">`model.Done`</ph> を使用する別の <ph id="ph1">`<div class="form-group">`</ph> 要素を追加します。

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. <bpt id="p1">_</bpt>Views\Todos\Index.cshtml<ept id="p1">_</ept> を開きます。

1. 空の `<th></th>` 要素を探します。 この要素のすぐ上に、次の Razor コードを追加します。

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. <ph id="ph2">`Html.ActionLink()`</ph> ヘルパー メソッドを含む <ph id="ph1">`<td>`</ph> 要素を探します。 この <ph id="ph1">`<td>`</ph> の <bpt id="p1">_</bpt>上<ept id="p1">_</ept> に、次の Razor コードで別の <ph id="ph2">`<td>`</ph> 要素を追加します。

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    これだけで、`Index` ビューと `Create` ビューの変更を確認できます。

1. <ph id="ph1">`Ctrl+F5`</ph> キーを押してアプリを実行します。

これで、To Do 項目を追加し、 **[完了]** チェック ボックスをオンにすることができるようになります。 そうすると、完了済みの項目としてホームページに表示されます。 `Edit` ビューを変更していないため、`Edit` ビューには `Done` フィールドが表示されないことに注意してください。

#### <a name="enable-code-first-migrations-in-azure"></a>Azure で Code First Migrations を有効にする

データベースの移行を含むコードの変更に成功したので、Azure アプリに発行し、SQL Database も Code First Migrations を使用して更新します。

1. 前と同じように、プロジェクトを右クリックし、 <bpt id="p1">**</bpt>[発行]<ept id="p1">**</ept> を選択します。

1. <bpt id="p1">**</bpt>[その他の操作]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[編集]<ept id="p2">**</ept> をクリックして発行設定を開きます。

    ![発行の設定を開く](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. <bpt id="p1">**</bpt>[MyDatabaseContext]<ept id="p1">**</ept> ボックスの一覧で、Azure SQL Database のデータベース接続を選択します。

1. <bpt id="p1">**</bpt>[Code First Migrations を実行する (アプリケーション開始時に実行)]<ept id="p1">**</ept> チェック ボックスをオンにし、 <bpt id="p2">**</bpt>[保存]<ept id="p2">**</ept> をクリックします。

    ![Azure アプリで Code First Migrations を有効にする](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>変更を発行する

Azure アプリで Code First Migrations を有効にしたので、コードの変更を発行します。

1. 発行ページで **[発行]** をクリックします。

1. 再度、To Do 項目を追加してみてください。その後、 <bpt id="p1">**</bpt>[完了]<ept id="p1">**</ept> を選択すると、完了済みの項目としてホームページに表示されます。

    ![Code First Migration の手順後の Azure アプリ](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

既存のすべての To Do 項目がまだ表示されています。 ASP.NET アプリケーションを再発行しても、SQL データベースの既存のデータは消失しません。 また、Code First Migrations によって変更されるのはデータ スキーマのみであり、既存のデータはそのまま残されます。

## <a name="stream-application-logs"></a>アプリケーション ログをストリーミングする

Azure アプリから Visual Studio に、トレース メッセージを直接ストリーム配信することができます。

<bpt id="p1">_</bpt>Controllers\TodosController.cs<ept id="p1">_</ept> を開きます。

各アクションが <ph id="ph1">`Trace.WriteLine()`</ph> メソッドで開始されます。 このコードは、Azure アプリにトレース メッセージを追加する方法を示すために追加されています。

#### <a name="enable-log-streaming"></a>ログ ストリーミングを有効にする

1. **[表示]** メニューの **[Cloud Explorer]** を選択します。

1. <bpt id="p1">**</bpt>Cloud Explorer<ept id="p1">**</ept> で、自分のアプリがある Azure サブスクリプションを展開し、 <bpt id="p2">**</bpt>[App Service]<ept id="p2">**</ept> を展開します。

1. Azure アプリを右クリックし、 <bpt id="p1">**</bpt>[ストリーミング ログの表示]<ept id="p1">**</ept> を選択します。

    ![ログ ストリーミングを有効にする](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    ログが <bpt id="p1">**</bpt>[出力]<ept id="p1">**</ept> ウィンドウにストリーミングされるようになりました。

    ![出力ウィンドウでのログ ストリーミング](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    しかし、トレース メッセージはまだ表示されません。 最初に <bpt id="p1">**</bpt>[ストリーミング ログの表示]<ept id="p1">**</ept> を選択する際、Azure アプリによってトレース レベルが <ph id="ph1">`Error`</ph> に設定されるためです。このレベルでは、エラー イベントのみが (<ph id="ph2">`Trace.TraceError()`</ph> メソッドによって) 記録されます。

#### <a name="change-trace-levels"></a>トレース レベルを変更する

1. トレース レベルを変更して別のトレース メッセージを出力するには、<bpt id="p1">**</bpt>Cloud Explorer<ept id="p1">**</ept> に戻ります。

1. アプリをもう一度右クリックし、 <bpt id="p1">**</bpt>[ポータルで開く]<ept id="p1">**</ept> を選択します。

1. アプリのポータル管理ページで、左側のメニューから <bpt id="p1">**</bpt>[App Service ログ]<ept id="p1">**</ept> を選択します。

1. <bpt id="p1">**</bpt>[Application Logging (File System)]\(アプリケーション ログ記録 (ファイル システム)\)<ept id="p1">**</ept> の <bpt id="p3">**</bpt>[レベル]<ept id="p3">**</ept> から <bpt id="p2">**</bpt>[詳細]<ept id="p2">**</ept> を選択します。 **[保存]** をクリックします。

    ![トレース レベルを [詳細] に変更する](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > 異なるトレース レベルを試しながら、各レベルでどのような種類のメッセージが表示されるかを確認することができます。 たとえば、 <bpt id="p1">**</bpt>[情報]<ept id="p1">**</ept> レベルを指定した場合、<ph id="ph1">`Trace.TraceInformation()`</ph>、<ph id="ph2">`Trace.TraceWarning()`</ph>、<ph id="ph3">`Trace.TraceError()`</ph> で作成されたすべてのログが表示されますが、<ph id="ph4">`Trace.WriteLine()`</ph> で作成されたログは除外されます。

1. ブラウザーでアプリにもう一度アクセスし (<bpt id="p1">*</bpt>http://<ph id="ph1">&lt;</ph>アプリ名>.azurewebsites.net<ept id="p1">*</ept>)、Azure の To Do リスト アプリケーションの周囲をクリックしてみます。 Visual Studio の <bpt id="p1">**</bpt>[出力]<ept id="p1">**</ept> ウィンドウにトレース メッセージがストリーミングされます。

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>ログ ストリーミングを停止する

ログ ストリーミング サービスを停止するには、 <bpt id="p2">**</bpt>[出力]<ept id="p2">**</ept> ウィンドウの <bpt id="p1">**</bpt>[監視の停止]<ept id="p1">**</ept> ボタンをクリックします。

![ログ ストリーミングを停止する](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Azure アプリを管理する

<bpt id="p1">[</bpt>Azure Portal<ept id="p1">](https://portal.azure.com)</ept> に移動して、Web アプリを管理します。 <bpt id="p1">**</bpt>[App Services]<ept id="p1">**</ept> を検索して選択します。

![Azure App Services を検索します](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

使用する Azure アプリの名前を選択します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

アプリのページが表示されます。

既定では、ポータルは <bpt id="p1">**</bpt>[概要]<ept id="p1">**</ept> ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクも行うことができます。 ページの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の [App Service] ページ](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> * Azure SQL Database でデータベースを作成する
> * ASP.NET アプリを SQL Database に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure からターミナルにログをストリーミングする
> * Azure Portal でアプリを管理する

次のチュートリアルに進んで、接続 Azure SQL Database のセキュリティを簡単に改善する方法を学んでください。

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>Azure リソースのマネージド ID を使用して SQL データベースに安全にアクセスする<ept id="p1">](app-service-web-tutorial-connect-msi.md)</ept>

その他のリソース:

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>ASP.NET アプリの構成<ept id="p1">](configure-language-dotnet-framework.md)</ept>

クラウドの支出を最適化して節約しますか?

> [!div class="nextstepaction"]
> <bpt id="p1">[</bpt>Cost Management を使用してコスト分析を開始する<ept id="p1">](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)</ept>