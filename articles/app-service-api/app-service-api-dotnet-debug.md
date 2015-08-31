<properties 
	pageTitle="Azure App Service の API アプリのデバッグ" 
	description="Visual Studio を使用して、Azure App Service で実行中の API アプリをデバッグする方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/14/2015" 
	ms.author="tdykstra"/>

# Azure App Service の API アプリのデバッグ

## 概要

このチュートリアルでは、[Azure App Service](../app-service/app-service-value-prop-what-is.md) の [API アプリ](app-service-api-apps-why-best-platform.md)で実行するように構成された ASP.NET Web API コードをデバッグする方法について説明します。ローカルに行う方法と (Azure で実行中に) リモートで行う方法の両方のデバッグ方法について説明します。

このチュートリアルでは、このシリーズ内の前のチュートリアルで[作成](app-service-dotnet-create-api-app.md)して[デプロイ](app-service-dotnet-deploy-api-app.md)した API アプリを扱います。

## リモートでの API アプリのデバッグ 

リモート デバッグを有効にするには、デバッグ ビルドを Azure にデプロイする必要があります。

1. Visual Studio の**ソリューション エクスプローラー**で、[前のチュートリアルでデプロイした](app-service-dotnet-deploy-api-app.md)プロジェクトを右クリックし、**[発行]** を選択します。

2. **[Web の発行]** ダイアログで、**[設定]** タブ、**[デバッグ]** ビルド構成の順に選択します。

4. **[発行]** をクリックします。

	![Publish project](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

	ブラウザー ウィンドウが API アプリのベース URL に対して開きます。

4. ブラウザーのアドレス バーで、URL の最後に swagger/ を追加し、Enter キーを押します。

	この手順は、[作成](app-service-dotnet-create-api-app.md)チュートリアルの指示に従って Swagger UI を有効にしたことを前提としています。

	![Swagger UI](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Visual Studio に戻って、**[表示]、[サーバー エクスプローラー]** の順にをクリックします。

6. **サーバー エクスプローラー**で、**[Azure]、[App Service]** ノードの順に展開します。

7. API アプリをデプロイしたときに作成または選択したリソース グループを探します。

8. リソース グループの下にある API アプリのノードを右クリックし、**[デバッガーの接続]** を選択します。

	![デバッガーの接続](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	リモート デバッガーが接続を試みます。場合によっては、接続を確立するために **[デバッガーの接続]** を再度クリックすることが必要になる場合があります。失敗した場合は、再試行してください。

	![デバッガーの接続](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. 接続が確立されたら、API アプリ プロジェクトの **ContactsController.cs** ファイルを開き、`Get` メソッドにブレークポイントを追加します。

	![コントローラーへのブレークポイントの適用](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. ブラウザーのセッションに戻り、**[Get]** 動詞をクリックして *Contact* オブジェクトのスキーマを表示し、**[Try it Out]** をクリックします。設定したブレークポイントでプログラムの実行が停止します。ここで、コントローラーのロジックをデバッグできます。

	![実際に使ってみる](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## ローカルでの API アプリのデバッグ 

Azure サーバーへのラウンドトリップによってデバッグが低速になる場合など、ローカルでの API アプリのデバッグが必要になることがあります。このセクションでは、Swagger UI をテスト クライアントとして使用して、API アプリをローカルでデバッグする方法について説明します。

2. ブラウザーで、[Azure プレビュー ポータル](https://portal.azure.com)に移動します。 

3. サイド バーで **[参照]** をクリックし、**[API Apps]** を選択します。

	![Azure ポータルの [参照] のオプション](./media/app-service-api-dotnet-debug/ld-browse.png)

4. サブスクリプションの API アプリの一覧から作成した API アプリを選択します。

	![API App List](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. API アプリのブレードで、**[API アプリ ホスト]** をクリックします。

	![API App Host](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. API アプリ ホストのブレードで、**[すべての設定]** をクリックします。

	![API App Host All Settings](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. **[設定]** ブレードで、**[アプリケーション設定]** をクリックします。

	![API App Host Application Settings](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. **[Web アプリの設定]** ブレードで、下へスクロールして **[アプリケーション設定]** セクションを表示します。

	![API App Host Application Settings for Local Debug](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

1. Visual Studio で、API アプリのプロジェクトの *web.config* ファイルを開きます。

9. **[アプリケーション設定]** で、次の各キーと値を *web.config* ファイルの **appSettings** セクションに追加します。
	- **EMA\_MicroserviceId**
	- **EMA\_Secret**
	- **EMA\_RuntimeUrl**

	この手順を完了すると、*web.config* の **appSettings** セクションは、次のスクリーンショットのようになります。

	![API App Host Application Settings for Local Debug](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**注:** このセクションで *web.config* ファイルに追加した *EMA\_* 値には、機密の認証情報が含まれています。したがって、これらの値は、パブリックなソース管理リポジトリには保存しないことをお勧めします。詳細については、「[Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service (ASP.NET および Azure App Service にパスワードや機密データをデプロイするためのベスト プラクティス)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)」を参照してください。

10. API アプリのコントローラーのコード (`Get` メソッド内) にブレークポイントを設定します。

11. F5 キーを押して Visual Studio のデバッグ セッションを開始します。
 
13.  API アプリのアクセス レベルが **[パブリック (匿名)]** に設定されている場合、Swagger UI ページをテストに使用できます。

	* ページがブラウザーに読み込まれると、HTTP 403 エラー ページが表示されます。ブラウザーのアドレス バーで、URL の最後に */swagger* を追加し、Enter キーを押します。

	* Swagger UI が読み込まれたら、ブラウザー ウィンドウの **[Get]** 動詞をクリックして Contact オブジェクトのスキーマを表示し、**[Try it Out]** をクリックします。

		設定したブレークポイントでプログラムの実行が停止します。ここで、コントローラーのロジックをデバッグできます。

14.	API アプリのアクセス レベルが **[パブリック (認証)]** に設定されている場合は、以下のとおり、「[API アプリの保護](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request)」で説明する Post 要求の手順に従って認証を行い、ブラウザー ツールを使用する必要があります。

	* ゲートウェイのログイン URL に移動し、ログインするための資格情報を入力します。
	* x-zumo-auth Cookie から Zumo トークンの値を取得します。
	* 要求に x-zumo-auth ヘッダーを追加し、その値を x-zumo-auth Cookie の値に設定します。
	* 要求を送信します。

	**注:** ローカルで実行している場合は、API アプリに対するアクセスを Azure で制御して、そのメソッドの実行を認証済みのユーザーのみに限定することができません。Azure で実行している場合、API アプリを対象とするすべてのトラフィックはゲートウェイを介してルーティングされるため、認証されていない要求がゲートウェイを通過することはありません。ローカルで実行している場合はリダイレクトされません。つまり、認証されていない要求から API アプリへのアクセスが妨げられることはありません。このような認証により、ログオン ユーザーに関する情報を取得するコードなど、API アプリの認証関連コードを正常に実行できるという利点があります。ゲートウェイが API アプリの認証を処理する方法の詳細については、[API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md#azure-app-service-gateway)に関するページを参照してください。

## 次のステップ

このチュートリアルでは、API Apps をデバッグする方法について説明しました。トラブルシューティングの詳細については、「[Visual Studio を使用した Azure App Service のトラブルシューティング](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)」を参照してください。API Apps は、Web サービスをホストする追加機能を備えた Web アプリです。そのため、Web アプリに使用する API アプリ用と同じデバッグとトラブルシューティング ツールを使用できます。

 

<!---HONumber=August15_HO8-->