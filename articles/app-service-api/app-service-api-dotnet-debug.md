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
	ms.date="07/08/2015" 
	ms.author="bradyg;tarcher"/>

# Azure App Service の API アプリのデバッグ

## 概要

このチュートリアルでは、[Azure App Service](../app-service/app-service-value-prop-what-is.md) の [API アプリ](app-service-api-apps-why-best-platform.md)で実行するように構成された ASP.NET Web API コードをデバッグする方法について説明します。ローカルに行う方法と (Azure で実行中に) リモートで行う方法の両方のデバッグ方法について説明します。このチュートリアルでは、このシリーズ内の前のチュートリアルで[作成](app-service-dotnet-create-api-app.md)して[デプロイ](app-service-dotnet-deploy-api-app.md)した API アプリを扱います。

## リモートでの API アプリのデバッグ 

次の手順では、テスト クライアントとして Swagger UI を使用して、クラウドで実行中の API アプリをデバッグします。

1. Visual Studio の**ソリューション エクスプローラー**で、[前のチュートリアルでデプロイした](app-service-dotnet-deploy-api-app.md)プロジェクトを右クリックし、**[発行]** を選択します。

	![Publish project](./media/app-service-api-dotnet-debug/rd-publish.png)

2. **[Web の発行]** ダイアログ ボックスで、[設定] タブを選択し、**[デバッグ]** ビルド構成が選択されていることを確認します。確認したら、**[発行]** をクリックして、Azure サブスクリプションにすべての変更をプッシュ送信します。

	![Publish project](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

3. ブラウザー ウィンドウが開き、API アプリが正常に作成されたことを示すメッセージが表示されます。

4. ブラウザーのアドレス バーで、URL の最後に "swagger/" を追加し、Enter キーを押します。Swagger UI クライアントが表示されます。

	![Swagger UI](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Visual Studio に戻り、**[表示]** メニューの **[サーバー エクスプローラー]** を選択します。

6. **サーバー エクスプローラー**で、**[Azure]、[App Service]** ノードの順に展開します。

7. API アプリをデプロイしたときに作成したリソース グループを探します。

8. リソース グループの下にある API アプリのノードを右クリックし、**[デバッガーの接続]** を選択します。

	![デバッガーの接続](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	リモート デバッガーが接続を試みます。場合によっては、接続を確立するために **[デバッガーの接続]** を再度クリックすることが必要になる場合があります。失敗した場合は、再試行してください。

	![デバッガーの接続](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. 接続が確立されたら、API アプリ プロジェクトの **ContactsController.cs** ファイルを開き、`Get` メソッドと `Post` メソッドにブレークポイントを追加します。最初はアクティブとして表示されない場合がありますが、リモート デバッガーが接続された時点で、デバッグの準備が完了します。

	![コントローラーへのブレークポイントの適用](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. ブラウザーのセッションに戻り、**[Get]** 動詞をクリックして *Contact* オブジェクトのスキーマを表示し、**[Try it Out]** をクリックします。コントローラーの **Get** メソッドにブレークポイントを設定すると、プログラムの実行が停止するため、コントローラーのロジックをデバッグできます。

	![実際に使ってみる](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## ローカルでの API アプリのデバッグ 

テストかデバッグ サイクル中にラウンドトリップが低速になる状況を避ける場合など、API アプリをローカルでデバッグすることが必要になる場合があります。次の手順では、テスト クライアントとして Swagger UI を使用して API アプリをローカルでデバッグする方法を示します。

1. Visual Studio で、API アプリのプロジェクトの *web.config* ファイルを開きます。 
 
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

9. **[アプリケーション設定]** で次の各値を確認し、この値を *web.config* ファイルの **appSettings** セクションに追加します。
	- **EMA_MicroserviceId**
	- **EMA_Secret**
	- **EMA_RuntimeUrl**

	この手順を完了すると、*web.config* の **appSettings** セクションは、次のスクリーンショットのようになります。

	![API App Host Application Settings for Local Debug](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**注:** このセクションで *web.config* ファイルに追加した *EMA_* 値には、機密の認証情報が含まれています。したがって、このファイルをパブリックのソース管理メディア (*github* など) にコミットするとこれらの機密情報を他のユーザーが見ることができるようになるため、注意が必要です。詳細については、[ASP.NET と Azure App Service にパスワードや他の機密情報をデプロイするときのベスト プラクティス](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)に関するページをご覧ください。

10. API アプリのコントローラーのコード (`Get` メソッドと `Post` メソッド内) に 1 つ以上のブレークポイントを設定します。

	![Setting breakpoints](./media/app-service-api-dotnet-debug/ld-breakpoints.png)

11. F5 キーを押して Visual Studio のデバッグ セッションを開始します。
 
13.  API アプリのアクセス レベルが **[パブリック (匿名)]** に設定されている場合、Swagger UI ページをテストに使用できます。

	* ページがブラウザーに読み込まれると、エラー メッセージが表示されます。ブラウザーのアドレス バーで、URL の最後に */swagger* を追加し、Enter キーを押します。

	* Swagger UI が読み込まれたら、ブラウザー ウィンドウの **[Get]** 動詞をクリックして Contact オブジェクトのスキーマを表示し、**[Try it Out]** をクリックします。

		前の手順で設定したブレークポイントでプログラムの実行が停止するため、コントローラーのロジックをデバッグできます。

		![実際に使ってみる](./media/app-service-api-dotnet-debug/ld-try-it-out.png)

14.	API アプリのアクセス レベルが **[パブリック (認証)]** に設定されている場合は、以下のとおり、「[API アプリの保護](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request)」で説明する Post 要求の手順に従って認証を行い、ブラウザー ツールを使用する必要があります。

	* ゲートウェイのログイン URL に移動し、ログインするための資格情報を入力します。
	* x-zumo-auth Cookie から Zumo トークンの値を取得します。
	* 要求に x-zumo-auth ヘッダーを追加し、その値を x-zumo-auth Cookie の値に設定します。
	* 要求を送信します。

	**注:** ローカルで実行している場合は、API アプリに対するアクセスを Azure で制御して、そのメソッドの実行を認証済みのユーザーのみに限定することができません。Azure で実行している場合、API アプリを対象とするすべてのトラフィックはゲートウェイを介してルーティングされるため、認証されていない要求がゲートウェイを通過することはありません。ローカルで実行している場合はリダイレクトされません。つまり、認証されていない要求から API アプリへのアクセスが妨げられることはありません。このような認証により、ログオン ユーザーに関する情報を取得するコードなど、API アプリの認証関連コードを正常に実行できるという利点があります。ゲートウェイが API アプリの認証を処理する方法の詳細については、[API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md#azure-app-service-gateway)に関するページを参照してください。

## 次のステップ

リモートで API Apps をデバッグすることで、コードが Azure App Service でどのように実行されているかを容易に確認できます。Azure API Apps の豊富な診断とデバッグのデータを Visual Studio IDE で直接利用できます。

App Service API アプリは、Web サービスをホストする追加機能を備えた App Service の Web アプリです。そのため、Web アプリに使用する API アプリ用と同じデバッグとトラブルシューティング ツールを使用できます。詳細については、「[Visual Studio を使用した Azure Web App Service のトラブルシューティング](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)」を参照してください。

このシリーズで作成した API アプリは、すべてのユーザーが呼び出しに使用できます。認証済みユーザーのみが API アプリを呼び出すことができるように、API アプリを保護する方法については、「[Azure App Service での API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md)」を参照してください。
 

<!---HONumber=July15_HO3-->