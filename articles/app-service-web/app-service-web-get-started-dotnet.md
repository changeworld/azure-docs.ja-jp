<properties 
	pageTitle="初めての .NET Web アプリを Azure に 5 分でデプロイする | Microsoft Azure" 
	description="サンプル アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。実際の開発を速やかに開始し、すぐに成果を確認できます。" 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""
/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/16/2016" 
	ms.author="cephalin"
/>
	
# 初めての .NET Web アプリを Azure に 5 分でデプロイする

このチュートリアルでは、シンプルな .NET Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md) にデプロイします。App Service を使用すると、Web アプリ、[モバイル アプリ バックエンド](/documentation/learning-paths/appservice-mobileapps/)、および [API アプリ](../app-service-api/app-service-api-apps-why-best-platform.md)を作成できます。

このチュートリアルの内容は次のとおりです。

- Azure App Service で Web アプリを作成する。
- サンプルの ASP.NET コードをデプロイする。
- 運用環境でライブ実行されているコードを確認する。
- [Git コミットをプッシュする](https://git-scm.com/docs/git-push)ときと同じ方法で Web アプリを更新する。

## 前提条件

- [Git をインストールします](http://www.git-scm.com/downloads)。新しい Windows コマンド プロンプト、PowerShell ウィンドウ、Linux のシェル、または OS X ターミナルから `git --version` を実行して、インストールが正常に完了したことを確認します。
- Microsoft Azure アカウントを取得します。アカウントを持っていない場合は、[無料試用版にサインアップする](/pricing/free-trial/?WT.mc_id=A261C142F)か [Visual Studio サブスクライバー特典を有効](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

>[AZURE.NOTE] Azure アカウントがなくても、[App Service を試用](http://go.microsoft.com/fwlink/?LinkId=523751)できます。スターター アプリを作成し、最大 1 時間使用できます。クレジット カードも契約も不要です。

<a name="create"></a>
## Web アプリを作成する

1. Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

2. 左側のメニューで、**[新規]**、**[Web + モバイル]**、**[Web アプリ]** の順にクリックします。

    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)

3. アプリ作成ブレードでは、新しいアプリに次の設定を使用します。

    - **[アプリ名]**: 一意の名前を入力します。
    - **[リソース グループ]**: **[新規作成]** を選択し、リソース グループに名前を付けます。
    - **[App Service プラン/場所]**: このオプションをクリックし、**[新規作成]** をクリックして、App Service プランの名前、場所、価格レベルを設定します。**Free** 価格レベルを自由に使用してください。

    設定が完了すると、アプリ作成ブレードは次のようになります。

    ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)

3. 下部にある **[作成]** をクリックします。上部にある **[通知]** アイコンをクリックすると、進行状況を確認できます。

    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)

4. デプロイが完了すると、次の通知メッセージが表示されます。メッセージをクリックして、デプロイメントのブレードを開きます。

    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)

5. **[デプロイメントに成功しました]** ブレードで、**[リソース]** のリンクをクリックして、新しい Web アプリのブレードを開きます。

    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## Web アプリにコードをデプロイする

次に、Git を使用して Azure にコードをデプロイしましょう。

5. [Web アプリ] ブレードで、下にスクロールして **[デプロイ オプション]** を表示するかこれを探してクリックします。

    ![](./media/app-service-web-get-started-languages/deploy-web-app-deployment-options.png)

6. **[ソースの選択]**、**[ローカル Git リポジトリ]**、**[OK]** の順にクリックします。

7. [Web アプリ] ブレードに戻り、**[デプロイ資格情報]** をクリックします。

8. デプロイ資格情報を設定し、**[保存]** をクリックします。

7. [Web アプリ] ブレードに戻り、下にスクロールして **[プロパティ]** を表示するかこれを探してクリックします。**[Git URL]** の横の **[コピー]** ボタンをクリックします。

    ![](./media/app-service-web-get-started-languages/deploy-web-app-properties.png)

    これで Git を使用してコードをデプロイする準備ができました。

1. コマンド ライン端末で、作業ディレクトリに移動し (`CD`)、次のようにサンプル アプリを複製します。

        git clone https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git

    ![Clone the app sample code for your first web app in Azure](./media/app-service-web-get-started-languages/dotnet-git-clone.png)

2. サンプル アプリのリポジトリに移動します。たとえば、次のように入力します。

        cd app-service-web-dotnet-get-started

3. いくつか前の手順でポータルからコピーした Azure アプリの Git URL に対応する Git リモートを構成します。

        git remote add azure <giturlfromportal>

4. Git でコードをプッシュする場合と同様に、サンプル コードを Azure アプリにデプロイします。

        git push azure master

    ![Push code to your first web app in Azure](./media/app-service-web-get-started-languages/dotnet-git-push.png)

    `git push` を実行すると、Azure にコードが配置されるだけでなく、必要なパッケージが復元され、ASP.NET バイナリがビルドされます。

これで完了です。 Azure でコードがライブ実行されています。ブラウザーで http://*&lt;appname>*.azurewebsites.net に移動して、実行中のコードを確認します。

## アプリを更新する

Git を使用してプロジェクト (リポジトリ) のルートからプッシュして、いつでもライブ サイトを更新することができるようになりました。これは、初めてコードをデプロイしたときと同様に行います。たとえば、ローカルでテストした新しい変更をプッシュする場合は、プロジェクト (リポジトリ) のルートから次のコマンドを実行するだけで済みます。

    git add .
    git commit -m "<your_message>"
    git push azure master

## 次のステップ

Visual Studio で .NET Web アプリを作成して開発し、Azure に直接デプロイする方法については、「[Visual Studio を使用した Azure App Service への ASP.NET Web アプリのデプロイ](web-sites-dotnet-get-started.md)」を参照してください。

または、最初の Web アプリを活用します。次に例を示します。

- [Azure にコードをデプロイする他の方法](../app-service-web/web-sites-deploy.md)を試してみます。たとえば、GitHub リポジトリのいずれかからデプロイする場合、**[デプロイ オプション]** の **[ローカル Git リポジトリ]** ではなく、**[GitHub]** を選択します。
- Azure アプリを次のレベルに進めます。ユーザーを認証します。必要に応じてスケールを変更したり、パフォーマンスのアラートを設定したりできます。いずれも、数回のクリックで実現できます。「[初めての Web アプリに機能を追加する](app-service-web-get-started-2.md)」を参照してください。

<!-----HONumber=AcomDC_0920_2016-->