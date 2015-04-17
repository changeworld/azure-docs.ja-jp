<properties 
	pageTitle="Azure App Service の API アプリのリモートでのデバッグ" 
	description="Visual Studio を使用して Azure App Service の API アプリをリモートでデバッグします。" 
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

# Azure App Service の API アプリのリモートでのデバッグ 

## 概要

Visual Studio のリモート デバッグ機能は拡張され、Azure App Service の API アプリのサポートが追加されました。そのため、使い慣れたデバッグ ツールで、Azure で実際に実行中のコードを確認できます。このトピックでは、Visual Studio の **API アプリ クライアント**を使用して、デプロイした API アプリを呼び出すクライアント コードを作成する方法について説明します。その後、API アプリをクラウドで実際に実行している状態で、クライアント アプリと API アプリを同時にデバッグします。

このチュートリアルは、3 部構成になっている最後のチュートリアルです。

1. [API アプリの作成](app-service-dotnet-create-api-app.md)に関するチュートリアルでは、API アプリ プロジェクトを作成しました。 
* [API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するチュートリアルでは、API アプリを Azure サブスクリプションにデプロイしました。
* このチュートリアルでは、Azure で実行中のコードを Visual Studio を使用してリモートでデバッグします。

## API アプリ クライアントの生成 

Visual Studio の API アプリ ツールでは、デスクトップ アプリ、ストア アプリ、モバイル アプリから Azure API Apps を呼び出す C# コードを簡単に生成できます。 

Visual Studio で、[最初の](app-service-dotnet-create-api-app.md)チュートリアルの API アプリが含まれているソリューションを開きます。ソリューションを右クリックして、**[追加]**、**[新しいプロジェクト]** の順に選択します。

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

**[Windows デスクトップ]** カテゴリ、**[コンソール アプリケーション]** プロジェクト テンプレートの順に選択します。

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

コンソール アプリケーション プロジェクトを右クリックして、**[追加]**、**[Azure API アプリ クライアント]** の順に選択します。 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
このダイアログで、**ダウンロード** オプションを選択します。ドロップダウン リストから、先ほど作成した API アプリを選択します。**[OK]** をクリックします。 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

ウィザードによって API メタデータ ファイルがダウンロードされ、API アプリを呼び出すための型指定されたインターフェイスが生成されます。

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

コードの生成が完了すると、API アプリの名前が付いた新しいフォルダーがソリューション エクスプローラーに表示されます。このフォルダーには、クライアントとデータ モデルを実装するコードが含まれています。 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

プロジェクトのルートから **Program.cs** ファイルを開き、**Main** メソッドを次のコードに置き換えます。 

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

**[表示]** メニューで、**[サーバー エクスプローラー]** を選択します。[サーバー エクスプローラー] ウィンドウで、*[Azure]、[App Service]** ノードの順に展開します。API アプリをデプロイしたときに作成したリソース グループを探します。API アプリのノードを右クリックし、**[デバッガーの接続]** を選択します。 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

リモート デバッガーが接続を試みます。場合によっては、接続を確立するために **[デバッガーの接続]** を再度クリックすることが必要になる場合があります。失敗した場合は、再試行してください。

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

接続が確立されたら、API アプリ プロジェクトの **ContactsController.cs** ファイルを開き、`Get` メソッドと `Post` メソッドにブレークポイントを追加します。最初はアクティブとして表示されない場合がありますが、リモート デバッガーが接続された時点で、デバッグの準備が完了します。 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

デバッグするには、ソリューション エクスプローラーでコンソール アプリを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順に選択します。これで、リモートで API アプリを、ローカルでクライアント アプリをデバッグでき、全体的なデータ フローを確認できます。 

次のスクリーン ショットは、デバッガーが `Post` メソッドのブレークポイントに到達したときのようすを示しています。クライアントの contact データが、厳密に型指定された `Contact` オブジェクトに逆シリアル化されていることがわかります。 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## まとめ

リモートで API Apps をデバッグすることで、コードが Azure App Service でどのように実行されているかを容易に確認できます。リモートで実行されている Azure API Apps の豊富な診断およびデバッグ データを Visual Studio IDE で直接利用できます。


<!--HONumber=49-->