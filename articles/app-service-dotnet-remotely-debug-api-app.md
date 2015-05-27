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
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Azure App Service の API アプリのデバッグ 

## 概要

このチュートリアルでは、[Azure App Service](app-service-value-prop-what-is.md) の [API アプリ](app-service-api-apps-why-best-platform.md)で実行中の ASP.NET Web API のコードをデバッグします。このチュートリアルでは、このシリーズ内の前のチュートリアルで[作成](app-service-dotnet-create-api-app.md)して[デプロイ](app-service-dotnet-deploy-api-app.md)した API アプリを扱います。

Visual Studio の **API アプリ クライアント**機能を使用して、デプロイした API アプリを呼び出すクライアント コードを生成します。その後、API アプリをクラウドで実際に実行している状態で、クライアント アプリと API アプリを同時にデバッグします。

## API アプリ クライアントの生成 

Visual Studio の API アプリ ツールでは、デスクトップ アプリ、ストア アプリ、モバイル アプリから Azure API Apps を呼び出す C# コードを簡単に生成できます。

1. Visual Studio で、[API アプリの作成](app-service-dotnet-create-api-app.md)に関するトピックの API アプリが含まれているソリューションを開きます。 

2. **ソリューション エクスプローラー**で、ソリューションを右クリックし、**[追加]**、**[新しいプロジェクト]** の順に選択します。

	![新しいプロジェクトの追加](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

3. **[新しいプロジェクトの追加]** ダイアログで、次の手順に従います。

	1. **[Windows デスクトップ]** カテゴリを選択します。
	
	2. **[コンソール アプリケーション]** プロジェクト テンプレートを選択します。
	
	3. プロジェクトに名前を付けます。
	
	4. **[OK]** をクリックして、既存のソリューションで新しいプロジェクトを生成します。
	
	![新しいプロジェクトの追加](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

4. 新しく作成されたコンソール アプリケーション プロジェクトを右クリックし、**[追加]**、**[Azure API アプリ クライアント]** の順に選択します。

	![新しいクライアントの追加](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
5. **[Microsoft Azure API アプリ クライアントの追加]** ダイアログで、次の手順に従います。

	1. **[ダウンロード]** オプションを選択します。 
	
	2. ドロップダウン リストから、先ほど作成した API アプリを選択します。
	
	3. **[OK]** をクリックします。

	![生成画面](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

	ウィザードによって API メタデータ ファイルがダウンロードされ、API アプリを呼び出すための型指定されたインターフェイスが生成されます。

	![生成中を示す画面](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

	コードの生成が完了すると、API アプリの名前が付いた新しいフォルダーがソリューション エクスプローラーに表示されます。このフォルダーには、クライアントとデータ モデルを実装するコードが含まれています。

	![生成完了](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

6. プロジェクトのルートから **Program.cs** ファイルを開き、**Main** メソッドを次のコードに置き換えます。

		static void Main(string[] args)
	    {
	        var client = new ContactsList();
	
	        // Send GET request.
	        var contacts = client.Contacts.Get();
	        foreach (var c in contacts)
	        {
	            Console.WriteLine("{0}: {1} {2}",
	                c.Id, c.Name, c.EmailAddress);
	        }
	
	        // Send POST request.
			client.Contacts.Post(new Models.Contact
		    {
		        EmailAddress = "lkahn@contoso.com",
		        Name = "Loretta Kahn",
		        Id = 4
		    });
	
	        Console.WriteLine("Finished");
	        Console.ReadLine();
	    }

## API アプリ クライアントのテスト

API アプリのコーディングが終わったら、次はブラウザーでコードをテストします。

1. **ソリューション エクスプローラー**を開きます。

2. 前のセクションで作成したコンソール アプリケーションを右クリックします。

3. コンソール アプリケーションのコンテキスト メニューから、**[デバッグ]、[新しいインスタンスを開始]** の順に選択します。

4. コンソール ウィンドウが開き、すべての連絡先が表示されます。

	![コンソール アプリの実行](./media/app-service-dotnet-remotely-debug-api-app/running-console-app.png)

5. **Enter** キーを押してコンソール ウィンドウを閉じます。

## API アプリのデバッグ 

これで API アプリとそのクライアントのコーディングとテストが終了しました。次に、これをデバッグする方法を見てみましょう。

1. Visual Studio の **[表示]** メニューで **[サーバー エクスプローラー]** を選択します。 

2. **[サーバー エクスプローラー]** で、**[Azure]、[App Service]** ノードの順に展開します。

3. API アプリをデプロイしたときに作成したリソース グループを探します。

4. リソース グループの下にある API アプリのノードを右クリックし、**[デバッガーの接続]** を選択します。

	![デバッガーの接続](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

	リモート デバッガーが接続を試みます。場合によっては、接続を確立するために **[デバッガーの接続]** を再度クリックすることが必要になる場合があります。失敗した場合は、再試行してください。

	![デバッガーの接続](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

16. 接続が確立されたら、API アプリ プロジェクトの **ContactsController.cs** ファイルを開き、`Get` メソッドと `Post` メソッドにブレークポイントを追加します。最初はアクティブとして表示されない場合がありますが、リモート デバッガーが接続された時点で、デバッグの準備が完了します。

	![コントローラーへのブレークポイントの適用](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

17. デバッグするには、**ソリューション エクスプローラー**でコンソール アプリを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順に選択します。これで、リモートで API アプリを、ローカルでクライアント アプリをデバッグでき、データ フロー全体を確認できます。

	次のスクリーン ショットは、デバッガーが `Post` メソッドのブレークポイントに到達したときのようすを示しています。クライアントの contact データが、厳密に型指定された `Contact` オブジェクトに逆シリアル化されていることがわかります。

	![API に達したローカル クライアントのデバッグ](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## 次のステップ

リモートで API Apps をデバッグすることで、コードが Azure App Service でどのように実行されているかを容易に確認できます。Azure API Apps の豊富な診断とデバッグのデータを Visual Studio IDE で直接利用できます。

App Service API アプリは、Web サービスをホストする追加機能を備えた App Service の Web アプリです。そのため、Web アプリに使用する API アプリ用と同じデバッグとトラブルシューティング ツールを使用できます。詳細については、「[Visual Studio を使用した Azure Web App Service のトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)」を参照してください。

このシリーズで作成した API アプリは、すべてのユーザーが呼び出しに使用できます。認証されたユーザーだけが呼び出すことができるように API アプリを保護する方法については、「[API アプリの保護: Azure Active Directory またはソーシャル プロバイダーの認証の追加](app-service-api-dotnet-add-authentication.md)」を参照してください。

<!--HONumber=54-->