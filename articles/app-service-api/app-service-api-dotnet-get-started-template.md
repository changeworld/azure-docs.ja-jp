<properties
	pageTitle="Azure App Service と ASP.NET Web API 2 を開始する | Microsoft Azure"
	description="Visual Studio で ASP.NET Web API 2 プロジェクトを作成し、Azure App Service の新しい API アプリにデプロイする方法について説明します。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="tdykstra"/>

# Visual Studio で Azure App Service と ASP.NET Web API 2 を開始する

## 概要

このチュートリアルでは、Visual Studio 2015 を使用して ASP.NET Web API 2 アプリケーションを Azure App Service の [API アプリ](app-service-api-apps-why-best-platform.md)にデプロイする方法を紹介します。Visual Studio プロジェクトを作成し、図のように API アプリにデプロイします。

![Visual Studio での作成とデプロイの図](./media/app-service-api-dotnet-get-started-template/Create_App.png)

このチュートリアルは、Azure を使用した経験がない ASP.NET 開発者を対象に作成されています。このチュートリアルでは、クラウドで動作する単純な Web API を作成します。

学習内容:

* Visual Studio で新しい Web API 2 プロジェクトを作成するときに新しい App Service API アプリを作成する方法。
* Visual Studio を使用し、App Service API アプリに Web API 2 プロジェクトをデプロイする方法。
* [Azure ポータル](/features/azure-portal/)を使用して API アプリを監視および管理する方法

このチュートリアルの最後の「[トラブルシューティング](#troubleshooting)」セクションでは、うまくいかない場合の対処方法を説明します。また、「[次のステップ](#next-steps)」セクションには、Azure App Service の使い方をさらに学習できる他のチュートリアルへのリンクがあります。

## 前提条件

### Azure アカウント

このチュートリアルを完了するには、Azure アカウントが必要です。そのための方法は次のとおりです。

* [無料で Azure アカウントを開きます](/pricing/free-trial/?WT.mc_id=A261C142F)。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、無料の Azure サービスと機能をご利用になれます。
* [Visual Studio サブスクライバーの特典を有効にします](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。

Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Azure App Service アプリケーションの作成](http://go.microsoft.com/fwlink/?LinkId=523751)」にアクセスしてください。有効期間が短いスターター アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

### <a name="setupdevenv"></a>Visual Studio 2015 と Azure SDK for .NET

このチュートリアルは、Visual Studio 2015 と [Azure SDK for .NET](../dotnet-sdk.md) 2.8.2 以降向けに書かれています。[最新の Azure SDK for Visual Studio 2015 はここからダウンロードしてください](http://go.microsoft.com/fwlink/?linkid=518003)。Visual Studio をまだ持っていない場合は、SDK と共に自動的にインストールされます。

Visual Studio 2013 を持っている場合は、[最新の Azure SDK for Visual Studio 2013 をダウンロード](http://go.microsoft.com/fwlink/?LinkID=324322)できます。一部の画面は画像と異なる場合があります。

>[AZURE.NOTE] マシンに既にある SDK の依存関係の数に応じて、SDK のインストールには長時間 (数分から 30 分以上) かかる場合があります。

### ASP.NET Web API 2

このチュートリアルでは、Azure App Service と共に ASP.NET Web API 2 を使用する方法を説明しており、ASP.NET Web API の開発方法については扱っていません。ASP.NET Web API 2 の概要については、[ASP.NET](http://asp.net/) サイトの [ASP.NET Web API 2 の使用](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)に関するページを参照してください。

## Azure App Service でプロジェクトと API アプリを作成する

最初の手順では、Visual Studio で ASP.NET Web API 2 プロジェクトを作成し、Azure App Service で API アプリケーションを作成します。その後、Web API をインターネット上で利用できるように、プロジェクトを API アプリにデプロイします。

1. Visual Studio 2015 を開きます。

2. **[ファイル]、[新規作成]、[プロジェクト]** の順にクリックします。

3. **[新しいプロジェクト]** ダイアログ ボックスで、**[Visual C#]、[Web]、[ASP.NET Web アプリケーション]** の順にクリックします (好みに応じて、**[Visual Basic]** を選択してもかまいません)。

3. ターゲット フレームワークとして **[.NET Framework 4.5.2]** が選択されていることを確認します。

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) は、API アプリの可用性、パフォーマンス、利用状況を監視します。**[Application Insights をプロジェクトに追加する]** チェック ボックスは、Visual Studio のインストール後に初めて Web プロジェクトを作成するときは、既定でオンになっています。このチェック ボックスがオンになっているが Application Insights を使う予定がない場合は、オフにしてください。

4. アプリケーションに「**MyExample**」という名前を付けます。

5. **[OK]** をクリックします。

	![[新しいプロジェクト] ダイアログ ボックス](./media/app-service-api-dotnet-get-started-template/GS13newprojdb.png)

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[Azure API アプリ]** テンプレートを選択します。

5. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスの **[Microsoft Azure]** セクションで、**[クラウド内でホスト]** が選択されていることと、ドロップダウン リストで **[App Service]** が選択されていることを確認します。

	![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/app-service-api-dotnet-get-started-template/GS13newaspnetprojdb.png)

	このように設定すると、Visual Studio によってこの Web プロジェクトの Azure API アプリが自動的に作成されます。

6. **[OK]** をクリックします。

5. Azure にまだサインインしていない場合は、サインインを求めるメッセージが表示されます。Azure サブスクリプションを管理するために使用するアカウントの ID とパスワードを使用してサインインします。

	サインインすると、**[App Service の作成]** ダイアログ ボックスが表示され、作成するリソースの情報が求められます。

	![Azure へのサインイン](./media/app-service-api-dotnet-get-started-template/configuresitesettings.png)

3. **[App Service の作成]** ダイアログ ボックスで、*azurewebsites.net* ドメインで一意となる **API アプリの名前**を入力します。たとえば、MyExample という名前の右側に番号を付加して一意にすることができます (例: MyExample810)。既定の名前が作成される場合は、それが一意になるため、そのまま使用することができます。

	入力した名前が既に使用されている場合は、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別の名前を入力する必要があります。

	Web API のベース URL は、この名前に *.azurewebsites.net* を追加したものとなります (**[Web アプリ名]** ボックスの横に表示されます)。たとえば、名前が `MyExample810` である場合、URL は `myexample810.azurewebsites.net` になります。

6. **[リソース グループ]** ボックスの横にある **[新規作成]** をクリックし、「MyExample」(またはお好きな名前) を入力します。

	このボックスでは、既存のリソース グループを選択することも、新規作成することもできます。作成するには、サブスクリプションの既存のリソース グループとは異なる名前を入力します。

	リソース グループは、API アプリ、データベース、VM などの Azure リソースをひとまとめにしたものです。チュートリアルを行うときは、新しいリソース グループを作成することをお勧めします。このようにすると、チュートリアルのために作成した Azure リソースをまとめて簡単に削除できるためです。詳細については、「[Azure Resource Manager の概要](../resource-group-overview.md)」を参照してください。

4. **[App Service プラン]** ドロップダウンの隣にある **[新規]** ボタンをクリックします。

	![[App Service の作成] ダイアログ](./media/app-service-api-dotnet-get-started-template/createas.png)

	スクリーン ショットの **[Web アプリ名]**、**[サブスクリプション]**、**[リソース グループ]** に指定されている値はサンプルです。実際の値を使用してください。

	以降の手順では、新しいリソース グループの App Service プランを作成します。App Service プランには、API アプリの実行環境となるコンピューティング リソースを指定します。たとえば、Free レベルを選択した場合、API アプリは共有 VM 上で実行され、一部の有料レベルを選択した場合は専用 VM で実行されます。詳細については、「[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。

5. **[App Service プランの構成]** ダイアログに「MyExamplePlan」(またはお好きな名前) を入力します。

5. **[場所]** ドロップダウン リストで、現在の所在地に最も近い場所を選択します。

	この設定によって、アプリが実行される Azure データ センターが指定されます。このチュートリアルでは、任意のリージョンを選択することができます。任意のリージョンを選択しても、大きな違いはありません。ただし、運用アプリでは、[待機時間](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)を最小限に抑えるために、アクセスするクライアントに最も近いサーバーを選択することをお勧めします。

5. **[サイズ]** ドロップダウンで、**[Free]** をクリックします。

	無料の価格レベルでこのチュートリアルに十分な性能が提供されます。

6. **[App Service プランの構成]** ダイアログで、**[OK]** をクリックします。

	![[App Service の構成] ダイアログ](./media/app-service-api-dotnet-get-started-template/configasp.png)

7. **[App Service の作成]** ダイアログ ボックスで、**[作成]** をクリックします。

	![[App Service の作成] ダイアログ ボックス](./media/app-service-api-dotnet-get-started-template/clickcreate.png)

	ごく短時間で (通常は 1 分未満)、Visual Studio によって Web プロジェクトと API アプリが作成されます。

	**[ソリューション エクスプローラー]** ウィンドウには、新しいプロジェクトのファイルとフォルダーが表示されます

	![ソリューション エクスプローラー](./media/app-service-api-dotnet-get-started-template/solutionexplorer.png)

	**[Azure App Service アクティビティ]** ウィンドウには、API アプリが作成されたことが表示されます。(ただし、メッセージには「Web アプリ」と表示されることがあります。)

	![[Azure App Service アクティビティ] ウィンドウで作成された API アプリ](./media/app-service-api-dotnet-get-started-template/GS13sitecreated1.png)

	Visual Studio の **[Cloud Explorer]** ウィンドウには API アプリが表示されます。

	![Cloud Explorer で作成された API アプリ](./media/app-service-api-dotnet-get-started-template/siteinse.png)
	
	このウィンドウを使用して、Azure リソースを表示し、管理できます。**Cloud Explorer** には、この例とは異なるリソース タイプが表示されることがあります。API アプリなどのリソースを右クリックし、そのリソースの管理オプションを表示します。

## Visual Studio プロジェクトを Azure API アプリにデプロイする

このセクションでは、図の手順 2. に示すように、Web プロジェクトを API アプリにデプロイします。

![Visual Studio での作成とデプロイの図](./media/app-service-api-dotnet-get-started-template/Create_App.png)

1. **[ソリューション エクスプローラー]** でプロジェクトを右クリックし、**[発行]** を選択します。

	![Visual Studio メニューの [発行場所の選択]](./media/app-service-api-dotnet-get-started-template/choosepublish.png)

	数秒で、**Web の発行**ウィザードが表示されます。このウィザードは、新しい API アプリにプロジェクトをデプロイするための設定が含まれる*発行プロファイル*に対して開かれます。他の API アプリにデプロイする場合は、**[プロファイル]** タブをクリックして別のプロファイルを作成します。このチュートリアルでは、以前に作成した API アプリにデプロイする設定を使用します。

	発行プロファイルにはデプロイ用のユーザー名とパスワードが含まれています。これらの資格情報は自動的に生成されているので、入力や変更の必要はありません。パスワードは、`Properties\PublishProfiles` フォルダー内のユーザー固有の隠しファイルで暗号化されています。

8. **Web の発行**ウィザードの **[接続]** タブで、**[次へ]** をクリックします。

	![Web の発行ウィザードの [接続] タブで [次へ] をクリック](./media/app-service-api-dotnet-get-started-template/GS13ValidateConnection.png)

	次のタブは **[設定]** タブです (下図参照)。ここで、ビルド構成を変更して、デバッグ ビルドをデプロイするように設定すると、[リモート デバッグ](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)ができるようになります。このタブには他にも、次のようにさまざまな**ファイル発行オプション**があります。

	* 発行先の追加ファイルを削除する
	* 発行中にプリコンパイルする
	* App\_Data フォルダーのファイルを除外する

	このチュートリアルでは、いずれも必要ありません。これらの詳しい説明については、「[How to: Deploy a Web Project Using One-Click Publish in Visual Studio (方法: Visual Studio でワンクリック発行を使用して Web プロジェクトをデプロイする)](https://msdn.microsoft.com/library/dd465337.aspx)」を参照してください。

10. **[設定]** タブの **[次へ]** をクリックします。

	![Web の発行ウィザードの [設定] タブ](./media/app-service-api-dotnet-get-started-template/GS13SettingsTab.png)

	**[プレビュー]** タブ (下の図) が次になります。ここで、どのファイルがプロジェクトから API アプリにコピーされるかを確認できます。既にデプロイ済みの API アプリにプロジェクトをデプロイした場合は、変更されたファイルだけがコピーされます。コピーされるファイルの一覧を確認するには、**[プレビューの開始]** ボタンをクリックしてください。

11. **[プレビュー]** タブで、**[発行]** をクリックします。

	![Web の発行ウィザードの [プレビュー] タブ](./media/app-service-api-dotnet-get-started-template/GS13previewoutput.png)

	**[発行]** をクリックすると、Visual Studio で Azure サーバーにファイルをコピーする処理が開始されます。これには 1 ～ 2 分ほどかかることがあります。

	**[出力]** ウィンドウと **[Azure App Service アクティビティ]** ウィンドウでは、実行されたデプロイ操作が表示され、デプロイが問題なく完了したことが報告されます。

	![デプロイが正常に完了したことを示す Visual Studio の出力ウィンドウ](./media/app-service-api-dotnet-get-started-template/PublishOutput.png)

	デプロイが成功すると、自動的に既定のブラウザーが開き、デプロイ先の API サイトのベース URL にアクセスします。ブラウザーにページが表示されます。そのページには "この Web アプリの作成が完了しました" というメッセージが表示されます。

	![デプロイが正常に完了したことを示す Visual Studio の出力ウィンドウ](./media/app-service-api-dotnet-get-started-template/successfullycreated.png)

> [AZURE.TIP] **[Web の 1 クリック発行]** ツール バーを有効にすると、簡単にデプロイできるようになります。**[表示]、[ツール バー]** の順にクリックし、**[Web の 1 クリック発行]** を選択します。このツール バーを使用すると、プロファイルを選択できるほか、ボタンをクリックして発行したり、ボタンをクリックして **Web の発行**ウィザードを開いたりできます。![[Web の 1 クリック発行] ツール バー](./media/app-service-api-dotnet-get-started-template/weboneclickpublish.png)

## デプロイした Web API をテストする

1. 好みの HTTP クライアント ツールを利用し、URL *{your API app name}.azurewebsites.net/api/values* に HTTP GET 要求を送信します。

	Web API プロジェクト テンプレートにより、GET 要求に対して 2 つの文字列からなる配列を JSON 形式で返す `Values` コントローラーが定義されます。次の図は、[Postman](http://www.getpostman.com/) により送信された要求を示しています。応答の本文で JSON が返されています。

	![デプロイが正常に完了したことを示す Visual Studio の出力ウィンドウ](./media/app-service-api-dotnet-get-started-template/postman.png)

2. これでコードを変更したり、初回デプロイと同じ方法でプロジェクトを再デプロイしたり、Azure で変更を数秒で確認したりできます。

## <a id="portal"></a> 任意: Azure ポータルで API アプリを監視および管理する

[Azure ポータル](/services/management-portal/)は、作成した API アプリなどの Azure のサービスを管理および監視するために使用できる Web インターフェイスです。チュートリアルのこのセクションでは、ポータルで何ができるかを確認します。

1. ブラウザーで [https://portal.azure.com](https://portal.azure.com) に移動し、Azure アカウントの管理に使用している資格情報でサインインします。
	
2. **[App Services]** をクリックした後、API アプリの名前をクリックします。

	![Azure ポータルの App Services](./media/app-service-api-dotnet-get-started-template/selinportal.png)

	**[API アプリ]** ブレードには、API アプリの設定と利用統計の概要が表示されます。(ポータルの右側に表示されるウィンドウを*ブレード*といいます。)

	![Azure ポータルの [API アプリ] ブレード](./media/app-service-api-dotnet-get-started-template/portaldashboard.png)

	まだ API アプリにはトラフィックがそれほど存在しないため、グラフには何も表示されない可能性があります。さらにいくつかの要求を Web API に送信し、その後、ポータル ページを更新すると、いくつかの統計値が表示されます。

3. **[設定]** ブレードには、API アプリを構成するための多数のオプションが表示されます。

	![Azure ポータルの [設定] ブレード](./media/app-service-api-dotnet-get-started-template/portalconfigure1.png)

	**[API]** セクションは API メタデータ エンドポイントと CORS を構成するためのブレードにリンクしています。[次の手順](#next-steps)セクションにこれらの機能を紹介するチュートリアルがあります。

	**[発行]** セクションの **[デプロイ資格情報]** リンクに注目してください。ここで、デプロイに使用するカスタム ユーザー名とパスワードを作成します。ブレードの上部にある **[保存]** ボタンをクリックし、変更を送信します。新しいユーザー名とパスワードを作成する場合は、対象の Web プロジェクトで、**Web の発行**ウィザードの **[接続]** タブで同じ値を入力する必要があります。
	
	このスクリーン ショットには **[設定]** ブレードの一部しか表示されていません。このブレードには、表示されている以外のセクションもあります。

これらは、ポータル機能の一部にすぎません。新しい API アプリを作成したり、既存の API アプリを削除したり、API アプリを停止して再起動したり、データベースや仮想マシンなど、その他の種類の Azure サービスを管理したりできます。

## 任意: Azure API アプリを削除する

このチュートリアル用に作成した API アプリは、不要になったら削除できます。

API アプリを削除する簡単な方法は、Azure ポータルで **[API アプリ]** ブレードの上部にある **[削除]** ボタンをクリックすることです。もっと良い方法は、API アプリを含めるために作成したリソース グループを削除することです。このチュートリアルでは、リソース グループに含まれているのは API アプリのみですが、通常、リソース グループには関連リソースのコレクションが含まれています。たとえば、API アプリではデータベースや Azure ストレージ アカウントが使用されることがあります。これらは API アプリを削除すると不要になります。リソース グループを削除すると、そのグループに含まれているすべてのリソースが削除されます。Azure ポータルを使用してリソース グループを削除するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)のホーム ページに移動します。

2. **[リソース グループ]** をクリックします。

3. リソース グループの一覧で、削除するリソース グループをクリックします。

	表示される **[リソース グループ]** ブレードには、リソース グループに含まれているリソースの一覧が示されています。

4. **[リソース グループ]** ブレードの **[削除]** をクリックします。

	![Azure ポータルでのリソース グループの削除](./media/app-service-api-dotnet-get-started-template/delresgrp.png)

## トラブルシューティング

このチュートリアルの手順を行う際に問題が発生した場合は、必ず最新バージョンの Azure SDK for .NET を使用するようにしてください。これを行う最も簡単な方法は、[Azure SDK for Visual Studio 2015 をダウンロード](http://go.microsoft.com/fwlink/?linkid=518003)することです。最新バージョンをインストール済みの場合は、Web Platform Installer によってインストールが不要であることが示されます。

企業ネットワークを使用しており、ファイアウォールを介して Azure App Service にデプロイしようとしている場合は、Web デプロイのためにポート 443 と 8172 を開いてください。これらのポートを開くことができない場合は、次の「次のステップ」セクションで、その他のデプロイ オプションについて確認してください。

ASP.NET API アプリが Azure App Service で動作するようになったら、トラブルシューティングを容易にする Visual Studio の機能についてさらに学習できます。ログ記録、リモート デバッグなどの詳細については、「[Visual Studio を使用した Azure API アプリのトラブルシューティング](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)」を参照してください。

## 次のステップ

このチュートリアルでは、単純な Web API を作成し、それを Azure App Service の API アプリにデプロイする方法について説明しました。Web API の開発と利用を簡単にする App Service 機能の説明は、「[API Apps と ASP.NET を使用する](app-service-api-dotnet-get-started.md)」から始まるチュートリアル シリーズで確認できます。

<!---HONumber=AcomDC_0504_2016-->