<properties
	pageTitle="HTML/JavaScript アプリ用モバイル アプリ バックエンドの使用 | Azure App Service Mobile Apps"
	description="このチュートリアルでは、HTML5 および JavaScript で Web アプリ開発用の Azure モバイル アプリ バックエンドを使用する方法を説明します。"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="09/24/2015"
	ms.author="glenga"/>


#HTML アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##概要

このチュートリアルでは、Azure Mobile App バックエンドを使用して、クラウド ベースのバックエンド サービスを HTML5/JavaScript Web アプリに追加する方法を説明します。新しい Mobile App バックエンドと、アプリのデータを Azure に格納する簡単な *To do list* Web アプリの両方を作成します。

完成したアプリケーションのスクリーンショットは次のようになります。

![完成したアプリのスクリーン ショット](./media/app-service-mobile-dotnet-backend-html-get-started-preview/mobile-quickstart-completed-html.png)

HTML アプリの他のすべての Mobile Apps チュートリアルを行う前に、このチュートリアルを完了する必要があります。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

* [Visual Studio Community 2013] 以降のバージョン。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)に関するページにアクセスしてください。App Service で、有効期限付きのスターター Mobile App をすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##新しい Mobile App バックエンドの作成

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## サーバー プロジェクトのダウンロード

1. [Azure ポータル]で **[すべて参照]**、**[Web Apps]** の順にクリックし、作成した Mobile App バックエンドをクリックします。 

2. Mobile App バックエンドで、**[すべての設定]** をクリックし、**[Mobile App]** の下で **[クイック スタート]**、**[HTML/JavaScript]** の順にクリックします。

3. **[新しいアプリの作成]** の **[サーバー プロジェクトをダウンロードして実行する]** の下で **[ダウンロード]** をクリックし、圧縮されたプロジェクト ファイルをローカル コンピューターに抽出して、Visual Studio でソリューションを開きます。

4. プロジェクトをビルドして、NuGet パッケージを復元します。

##サーバー プロジェクトでの CORS の有効化

クロス オリジン リソース共有 (CORS) は、Web ベースのアプリでどのドメインからの要求が安全であり、ブラウザーによって許可される必要があるかを示す方法の 1 つです。Mobile App バックエンドにアクセスするすべての Web サイトに対して CORS エントリを追加する必要があります。標準的な ASP.NET Web API の振る舞いを使用して CORS 設定の制御を行います。詳細については、[ASP.NET Web API でのクロスオリジン要求の有効化](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api#enable-cors)に関する文書を参照してください。

既定では、ポータルからダウンロードするクライアントのクイック スタート プロジェクトは、localhost 上のポート 8000 で実行されます。このため、次にサーバー プロジェクトで `http://localhost:8000` に対して CORS を有効にします。

1. ツールメニューの Visual Studio で **[NuGet Package Manager]**、**[Package Manager Console]** の順にクリックし、**[パッケージのソース]** として Nuget.org を選択して、コンソール ウィンドウで次のコマンドを実行します。
 
		Install-Package Microsoft.AspNet.WebApi.Cors  

2. App\_Start/Startup.MobileApp.cs プロジェクト ファイルを開き、次の using ステートメントを追加します。

		using System.Web.Http.Cors;

3. 次に、**HttpConfiguration** (*config*) の作成後に、次のコードを **Startup.ConfigureMobileApp** メソッドに追加します。

        // Enable CORS support for localhost port 8000, all headers and methods.
        var cors = new EnableCorsAttribute("http://localhost:8000", "*", "*");
        config.EnableCors(cors);

4. 更新内容を保存します。

次に、CORS が有効なプロジェクトを Azure にデプロイします。

##Azure へのサーバー プロジェクトの発行

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##クライアント プロジェクトのダウンロードおよび実行

1. Mobile App バックエンドのブレードに戻り、**[すべての設定]** をクリックし、**[Mobile App]** の下で **[クイック スタート]**、**[HTML/JavaScript]** の順にクリックします。 

2.  **[新しいアプリの作成]** の **[HTML/Javascript プロジェクトをダウンロードして実行する]** の下で **[ダウンロード]** をクリックし、圧縮されたプロジェクト ファイルをローカル コンピュータに保存します。

3. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、**server** サブフォルダーから次のコマンド ファイルの 1 つを開始します。

	+ **launch-windows** (Windows コンピューター)
	+ **launch-mac.command** (Mac OS X コンピューター)
	+ **launch-linux.sh** (Linux コンピューター)

	> [AZURE.NOTE]Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「`R`」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。

	これにより、新しいアプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

4. Web ブラウザーで URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> を開いて、アプリケーションを開始します。クライアント アプリが Azure の Mobile App バックエンドに接続されるように構成されます。

5. アプリケーションで、意味のあるテキスト (たとえば、_チュートリアルの完了_) を **[Enter new task]** に入力し、**[追加]** をクリックします。

   	![アプリの実行](./media/app-service-mobile-dotnet-backend-html-get-started-preview/mobile-quickstart-startup-html.png)

   	これで、Azure でホストされている新しい Mobile App バックエンドに POST 要求が送信されます。要求のデータは、Mobile App スキーマの TodoItem テーブルに挿入されます。テーブルに格納された項目はサービスによって返され、データはアプリケーションの 2 番目の列に表示されます。

	> [AZURE.TIP]モバイル サービスにアクセスして app.js ファイルにあるデータを照会および挿入するコードを確認できます。

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Get started with authentication]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure ポータル]: https://portal.azure.com/

[Visual Studio Community 2013]: https://www.visualstudio.com/downloads
 

<!---HONumber=Oct15_HO1-->