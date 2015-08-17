<properties
	pageTitle="Azure App Service での Java API アプリの構築とデプロイ"
	description="Java API アプリ パッケージを作成して Azure App Service にデプロイする方法について説明します。"
	services="app-service\api"
	documentationCenter="java"
	authors="pkefal"
	manager="mohisri" 
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# Azure App Service での Java API アプリの構築とデプロイ

このチュートリアルでは、Java アプリケーションを作成し、[Git](http://git-scm.com) を使用して Azure App Service API Apps にデプロイする方法について説明します。このチュートリアルの手順は、Java を実行できる任意のオペレーティング システムで使用できます。また、このチュートリアルでは、[Gradle](https://gradle.org) を使用して、Java アプリケーションでのビルドの自動化とパッケージの依存関係の解決も有効にします。最後に、[RESTEasy](http://resteasy.jboss.org/) を使用して RESTful サービスを作成し、[JaxRS](https://jax-rs-spec.java.net/) 仕様を完全に実装します。

完成したアプリケーションのスクリーンショットは次のようになります。

![][sample-api-app-page]

## Azure ポータルでの API アプリの作成

> [AZURE.NOTE]このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。アカウントを持っていない場合は、[MSDN サブスクライバーの特典を有効にする](/pricing/member-offers/msdn-benefits-details/)か、[無料評価版にサインアップ](/pricing/free-trial/)してください。無料評価版の [App Service アプリのサンプル](http://tryappservice.azure.com)を試すこともできます。

1. [Azure プレビュー ポータル](https://portal.azure.com)にログインします。

2. ポータルの左下にある **[新規]** をクリックします。

3. **[Web + モバイル]、[API アプリ]** の順にクリックします。

	![][portal-quick-create]

4. **[名前]** に JavaAPIApp などの値を入力します。

5. App Service プランを選択するか、新しいプランを作成します。新しいプランを作成する場合は、価格レベル、および場所などのオプションを選択します。

	![][portal-create-api]

6. **[作成]** をクリックします。

	![][api-app-blade]

	**[スタート画面に追加]** チェック ボックスをオンのままにしておいた場合、API アプリの作成後にこの API アプリ用のブレードがポータルで自動的に表示されます。チェック ボックスをオフにした場合、ポータルのホーム ページで **[通知]** をクリックして API アプリの作成状態を確認し、通知をクリックして新しい API アプリのブレードに移動します。

7. **[設定]、[アプリケーションの設定]** の順にクリックします。

9. アクセス レベルを **[パブリック (匿名)]** に設定します。

11. **[保存]** をクリックします。

	![][set-api-app-access-level]

## 新しい API アプリの Git 発行の有効化

[Git](http://git-scm.com) は、Azure の Web サイトをデプロイするために使用できる分散型バージョン コントロール システムです。API アプリ用に記述したコードはローカルの Git リポジトリに格納されます。このコードをリモート リポジトリにプッシュして Azure にデプロイします。このデプロイ方法は、API アプリが Web アプリに基づいているために API アプリで使用できる App Service Web アプリの機能です。つまり、Azure App Service の API アプリは、Web サービスをホストするための追加の機能を持つ Web アプリであるということです。

ポータルでは、**[API アプリ]** ブレードで API アプリ固有の機能を管理し、**[API アプリ ホスト]** ブレードで Web アプリと共通する機能を管理します。このセクションでは、**[API アプリ ホスト]** ブレードに移動して、Git デプロイメントの機能を構成します。

1. [API アプリ] ブレードの **[API アプリ ホスト]** をクリックします。

	![][api-app-host]

2. **[API アプリ]** ブレードの **[デプロイメント]** セクションを見つけて、**[継続的配置の設定]** をクリックします。必要に応じて、ブレードのこの部分まで下へスクロールしてください。

	![][deployment-part]

3. **[ソースの選択]、[ローカル Git リポジトリ]** の順にクリックします。

5. **[OK]** をクリックします。

	![][setup-git-publishing]

6. API アプリまたはその他の App Service アプリを発行するためのデプロイ資格情報をまだ設定していない場合は、ここで設定します。

	* **[デプロイ資格情報の設定]** をクリックします。

	* ユーザー名とパスワードを作成します。

	* **[保存]** をクリックします。

	![][deployment-credentials]

1. **[API アプリ ホスト]** ブレードで、**[設定]、[プロパティ]** の順にクリックします。デプロイ先のリモート Git リポジトリの URL は、[GIT URL] の下に表示されます。

2. チュートリアルの後半で使用するためにこの URL をコピーします。

	![][git-url]

## 新しい API アプリでの Java ランタイムの有効化

API アプリで Java アプリを正常にホストするには、Java ランタイムを有効にして、アプリケーション サーバーを選択するがあります。ポータルには、これを行う簡単な方法があります。ここでは、アプリケーションをホストするために Java 7 と Jetty を有効にします。

1. [API アプリ] ブレードの **[API アプリ ホスト]** をクリックします。

	![][api-app-host]

2. **[設定]、[アプリケーションの設定]** の順にクリックします。ここで、Java を有効にし、アプリケーション サーバーとして Jetty を選択します。**[保存]** をクリックします。

	![][api-app-enable-java]

これにより、API アプリで **Java ランタイムが有効になり**、サイトのルートに **webapps/** フォルダーが作成されます。このフォルダーには、アプリケーションのすべての .war ファイルが格納されます。

## Java API アプリのコードのダウンロードと確認

このセクションでは、JavaAPIApp サンプルの一部として提供されるコードをダウンロードして確認します。

1. [この GitHub リポジトリ](http://go.microsoft.com/fwlink/?LinkId=571009)内のコードをダウンロードします。リポジトリを複製するか、**Download Zip** をクリックして .zip ファイルとしてダウンロードすることができます。.zip ファイルをダウンロードした場合は、ローカル ディスクでファイルを解凍します。

2. サンプル ファイルを解凍したフォルダーの `build\libs` フォルダーに移動します。

	![][api-app-folder-browse]

3. テキスト エディターで **apiapp.json** ファイルを開き、その内容を確認します。

	![][apiapp-json]

	Azure App Service で Java アプリケーションを API アプリとして認識するには、次の 2 つの前提条件があります。

	+ *apiapp.json* という名前のファイルがアプリケーションのルート ディレクトリに存在する。
	+ Swagger 2.0 のメタデータのエンドポイントがアプリケーションによって公開される。このエンドポイントの URL は、*apiapp.json* ファイルで指定します。

	**apiDefinition** プロパティに注目してください。この URL のパスは API の URL の相対パスであり、Swagger 2.0 のエンドポイントを指しています。Azure App Service ではこのプロパティを使用して API の定義が検出され、多くの App Service API アプリ機能が有効になります。

4. `src\main\java\com\microsoft\trysamples\javaapiapp` に移動し、**App.java** ファイルを開いて、コードを調べます。

	![][app-java]

	このコードでは、JaxRS 用の Swagger パッケージを使用して Swagger 2.0 のエンドポイントを作成します。

		beanConfig.setVersion("1.0.0");
		beanConfig.setBasePath("/JavaAPIApp/api");
		beanConfig.setHost(websitehostname);
		beanConfig.setResourcePackage("com.microsoft.trysamples.javaapiapp");
		beanConfig.setSchemes(new String[]{"http", "https"});
		beanConfig.setScan(true);

	`setVersion` メソッドは、Swagger によって提供されるメタデータに API のバージョンを設定します。

	`setBasePath` メソッドは、Swagger がメタデータの生成に使用する基本パスを設定します。この URL は、API アプリの基本パスへの相対パスです。

	`setHost` メソッドは、API がリッスンするホストを設定します。この例では、前に数行を割り当てた `websitehostname` 変数を使用して、ローカル実行時には動的に `localhost` に設定し、アプリケーションが Azure App Service で実行しているときは API アプリのホスト名に設定します。

	`setResourcePackage` メソッドはパッケージを設定します。このパッケージは、Swagger がスキャンして Swagger.json ファイルに組み込むもので、API メタデータを含みます。

	`setSchemes` メソッドは、サポートされているスキームを定義します。

	`setScan` メソッドは、Swagger にアプリ ドキュメントを生成させます。

	Swagger の出力のカスタマイズに使用できるメソッドは他にもあります。Swagger の [Wiki ページ](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5#using-swaggers-beanconfig)を参照してください。

	> [AZURE.NOTE]Swagger のメタデータ ファイルには、`/JavaAPIApp/api/swagger.json` でアクセスできます。

## API アプリ コードのローカルでの実行

このセクションでは、アプリケーションをローカルで実行して、動作することをデプロイの前に確認します。

1. サンプルをダウンロードしたフォルダーに移動します。

2. コマンド ライン プロンプトを開き、次のコマンドを入力します。

		gradlew.bat

3. コマンドが完了したら、次のコマンドを入力します。

		gradlew.bat jettyRunWar

	コマンド ライン ウィンドウの出力に次のように表示されます。

		17:25:49 INFO  JavaAPIApp runs at:
		17:25:49 INFO    http://localhost:8080/JavaAPIApp

5. ブラウザーで `http://localhost:8080/JavaAPIApp/` に移動します。

	次のページが表示されます。

	![][sample-api-app-page]

6. Swagger.json ファイルを表示するには、`http://localhost:8080/JavaAPIApp/api/Swagger.json` に移動します。

## Azure App Service への API アプリ コードの発行

このセクションでは、Azure App Service で実行されている API アプリにサンプル アプリケーションをデプロイするために、ローカル Git リポジトリを作成してそのリポジトリから Azure にプッシュします。

1. Git をインストールしていない場合は、[Git のダウンロード ページ](http://git-scm.com/download)からインストールします。

1. コマンド ラインを使用して、サンプル アプリケーションのディレクトリの `build\libs` に移動し、次のコマンドを入力してローカル Git リポジトリを初期化します。

		git init


2. 次のコマンドを入力して、リポジトリにファイルを追加します。

		git add .
		git commit -m "Initial commit of the API App"

3. コピーしておいた Git URL を使用して、作成した Web アプリ (API アプリ ホスト) に更新プログラムをプッシュするためのリモート参照を作成します。

		git remote add azure [URL for remote repository]

4. 次のコマンドを入力して、変更内容を Azure にプッシュします。

		git push azure master

	以前作成したパスワードを入力するように求められます。

	このコマンドからは、デプロイが成功したというメッセージが最後に出力されます。

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	 	* [new branch]      master -> master

## Azure ポータルでの API 定義の表示

API を API アプリにデプロイしたので、Azure ポータルで API 定義を確認できます。最初に*ゲートウェイ*を再起動します。これによって、API アプリの API 定義が変更されたことを Azure で認識できるようになります。ゲートウェイは、API の管理とリソース グループ内の API アプリの承認を処理する Web アプリです。

6. Azure ポータルで、前に作成した API アプリの **[API アプリ]** ブレードに移動して、**[ゲートウェイ]** リンクをクリックします。

	![][click-gateway]

7. **[ゲートウェイ]** ブレードの **[再起動]** をクリックします。これでこのブレードを閉じることができます。

	![][restart-gateway]

8. **[API アプリ]** ブレードの **[API 定義]** をクリックします。

	![][api-definition-click]

	**[API 定義]** ブレードに 1 つの Get メソッドが表示されます。

	![][api-definition-blade]

## Azure でのサンプル アプリケーションの実行

Azure ポータルで、目的の API アプリの **[API アプリ ホスト]** ブレードに移動し、**[参照]** をクリックします。

![][browse-api-app-page]

ブラウザーに、サンプル アプリをローカルで実行したときに表示されたホーム ページが表示されます。

## 次のステップ

API アプリ バックエンドを使用する Java Web アプリケーションを Azure にデプロイしました。Azure での Java の使用に関する詳細については、「[Java デベロッパー センター](/develop/java/)」を参照してください。

このサンプル API アプリは [TryApp Service](http://tryappservice.azure.com) のページで試すことができます。

[portal-quick-create]: ./media/app-service-api-java-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-java-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-java-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-java-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-java-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-java-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-java-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-java-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-java-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-java-api-app/apiapp-json.png
[app-java]: ./media/app-service-api-java-api-app/app-java.png
[sample-api-app-page]: ./media/app-service-api-java-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-java-api-app/browse-api-app-page.png
[api-app-enable-java]: ./media/app-service-api-java-api-app/api-app-enable-java.png
[click-gateway]: ./media/app-service-api-java-api-app/clickgateway.png
[restart-gateway]: ./media/app-service-api-java-api-app/gatewayrestart.png
[api-definition-click]: ./media/app-service-api-java-api-app/apidef.png
[api-definition-blade]: ./media/app-service-api-java-api-app/apidefblade.png
 

<!---HONumber=August15_HO6-->