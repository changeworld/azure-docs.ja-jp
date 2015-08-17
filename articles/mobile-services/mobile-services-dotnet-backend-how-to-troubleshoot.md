<properties 
	pageTitle="Mobile Services .NET バックエンドのトラブルシューティング - Azure Mobile Services" 
	description=".NET バックエンドを使用する Mobile Services で問題を診断して解決する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="wesmc;ricksal"/>

# Mobile Services .NET バックエンドのトラブルシューティング

Mobile Services を使用した開発は、通常は手間もかからないのですが、場合によっては問題が発生することもあります。このチュートリアルでは、Mobile Services .NET バックエンドで発生する可能性がある一般的な問題のトラブルシューティングの手法をいくつか説明します。

1. [HTTP デバッグ](#HttpDebugging)
2. [ランタイム デバッグ](#RuntimeDebugging)
3. [診断ログの分析](#Logs)
4. [デバッグによるクラウド アセンブリの問題の解決](#AssemblyResolution)
5. [Entity Framework の移行に関するトラブルシューティング](#EFMigrations)

<a name="HttpDebugging"></a>
## HTTP デバッグ

Mobile Services でアプリケーションを開発するには、通常、使用しているプラットフォーム (Windows ストア、iOS、Android など) 向けの Mobile Services クライアント SDK を活用します。ただし、HTTP レベルに下がって、ネットワーク上で直接の HTTP 呼び出しが実行された時点を観察すると役立つことがあります。この方法は、特に接続の問題とシリアル化の問題をデバッグするときに便利です。Mobile Services .NET バックエンドでは、この方法を Visual Studio のローカル デバッグおよびリモート デバッグ (詳細については、次のセクションを参照) と組み合わせて使用して、サービス コードを起動するまでに HTTP 呼び出しがたどったパスを明確に把握できます。

HTTP トラフィックを送信し、調査するために、任意の HTTP デバッガーを使用できます。[Fiddler](http://www.telerik.com/fiddler) は、この目的で一般的に開発者が使用する普及しているツールです。Mobile Services では、開発者が作業しやすくなるように、Web ベースの HTTP デバッガー ("テスト クライアント" とも呼びます) がモバイル サービスに付属していて、外部のツールをできるだけ使用しないで済みます。ローカルでモバイル サービスをホストする場合は、このデバッガーは [http://localhost:59233](http://localhost:59233) のような URI で入手できます。またクラウドでは、[http://todo-list.azure-mobile.net](http://todo-list.azure-mobile.net) の形式の URI で入手できます。次の手順は、サービスがホストされている場所に関係なく、同じように機能します。

1. **Visual Studio 2013 Update 2** 以降で Mobile Services サーバー プロジェクトを開いて開始します。すぐにプロジェクトを用意できない場合は、**[ファイル]**、**[新規作成]**、**[プロジェクト]** を順に選択してプロジェクトを作成できます。次に、**[クラウド]** ノード、**[Microsoft Azure Mobile Services]** テンプレートを順に選択します。
2. **F5** キーを押して、アプリケーションをビルドおよび実行します。スタート ページで、**[試してみる]** を選択します。 

    >[AZURE.NOTE]サービスがローカルでホストされている場合は、リンクをクリックすると次のページに進みます。ただし、クラウドでホストされている場合は、一連の資格情報を入力するよう求められます。これにより、認証されていないユーザーは、開発者の API とペイロードに関する情報にアクセスできなくなります。ページを参照するには、**空のユーザー名**と、パスワードに**アプリケーション キー**を入力してログインする必要があります。アプリケーション キーを入手するには、**Azure 管理ポータル**で、モバイル サービスの **[ダッシュボード]** タブに移動して、**[キーの管理]** を選択します。
    >
    > ![Authentication prompt to access help page][HelpPageAuth]

3. 表示される画面 ("ヘルプ ページ" と呼びます) に、モバイル サービスが使用可能にしている HTTP API がすべて一覧で表示されます。いずれかの API を選択します (Visual Studio で Mobile Services プロジェクト テンプレートを使用して開始した場合は **GET tables/TodoItem** が表示されます)。

    ![Help page][HelpPage]

4. 表示されたページには、API に必要な要求と応答に関するドキュメントと JSON の例が表示されます。**[試してみる]** ボタンをクリックします。

    ![Test page for an API][HelpPageApi]

5. これが "テスト クライアント" で、HTTP 要求を送信して API のテストに使用できます。**[送信]** を選択します。

    ![Test client][TestClient]

6. ブラウザーのウィンドウに、モバイル サービスから返される HTTP 応答が表示されます。

    ![Test client with HTTP response][TestClientResponse]

これで、Web ブラウザーを使用して、モバイル サービスによって公開されるさまざまな HTTP API を調査できます。

<a name="RuntimeDebugging"></a>
## ランタイム デバッグ

.NET バックエンドの重要な機能の 1 つに、ローカルでサービス コードをデバッグできるだけでなく、クラウド環境でライブで実行されているコードもデバッグできる機能があります。次の手順に従います。

1. **Visual Studio 2013 Update 2** 以降でデバッグするモバイル サービス プロジェクトを開きます。
2. シンボルの読み込みを構成します。**[デバッグ]** メニューに移動して、**[オプションと設定]** を選択します。**[マイ コードのみを有効にする]** がオフになっていて、**[ソース サーバー サポートを有効にする]** がオンになっていることを確認します。

    ![Configure symbol loading][SymbolLoading]

3. 左側の [**シンボル**] ノードを選択し、URI [http://srv.symbolsource.org/pdb/Public](http://srv.symbolsource.org/pdb/Public) を使用して (SymbolSource)[http://symbolsource.org] サーバーへの参照を追加します。Mobile Services .NET バックエンドのシンボルが、すべての新しいリリースで使用可能になります。
 
    ![Configure symbol server][SymbolServer]

4. デバッグするコードの一部にブレークポイントを設定します。たとえば、Visual Studio の Mobile Services プロジェクト テンプレートに付属する **TodoItemController** の **GetAllTodoItems()** メソッドにブレークポイントを設定します。
5. **F5** キーを押して、ローカルでサービスをデバッグします。Visual Studio が Mobile Services .NET バックエンドのシンボルをダウンロードするため、最初の読み込みに時間がかかる場合があります。
6. 前の HTTP デバッグのセクションで説明したように、テスト クライアントを使用して、ブレークポイントを設定したメソッドに HTTP 要求を送信します。たとえば、**GetAllTodoItems()** メソッドに要求を送信するには、ヘルプ ページで **GET tables/TodoItem** を選択してから、**[試してみる]**、**[送信]** の順に選択します。
7. 設定したブレークポイントで Visual Studio が一時停止し、ソース コード付きの完全なスタック トレースが Visual Studio の **[コール スタック]** ウィンドウで利用できます。

    ![Hitting a breakpoint][Breakpoint]

8. これで Azure にサービスを発行できるようになったので、上で実行したようにデバッグを使用できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[発行]** を選択して、プロジェクトを発行します。
9. 発行ウィザードの **[設定]** タブで、**[デバッグ]** 構成を選択します。これにより、関連するデバッグ シンボルがコードと共に発行されます。

    ![Publish debug][PublishDebug]

10. サービスが正常に発行されたら、**サーバー エクスプローラー**を開き、**[Azure]** ノードと **[Mobile Services]** ノードを展開します。必要に応じてサインインします。
11. 発行先となったモバイル サービスを右クリックし、**[デバッガーの接続]** を選択します。

    ![デバッガーの接続][AttachDebugger]

12. 手順 6 で実行したように、HTTP 要求を送信して、ブレークポイントを設定したメソッドを起動します。今回は、Azure でホストされるモバイル サービスのヘルプ ページとテスト クライアントを使用します。
13. ブレークポイントで Visual Studio が一時停止します。

これで、ローカルでの開発と Azure のライブ モバイル サービスに対する開発に Visual Studio デバッガーの機能を十分に利用できます。

<a name="Logs"></a>
## 診断ログの分析

モバイル サービスでは顧客からの要求を処理するときに、さまざまな役立つ診断情報を生成します。また、発生した例外も取得します。これ以外にも、各 [**TableController**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx) の [**Services**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx) プロパティで使用可能な [**Log**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) プロパティを利用して、追加のログでコントローラー コードをインストルメントすることができます。

ローカルでデバッグすると、Visual Studio の **[出力]** ウィンドウにログが表示されます。

![Logs in Visual Studio Output window][LogsOutputWindow]

Azure にサービスを発行した後、Visual Studio の**サーバー エクスプローラー**でモバイル サービスを右クリックして、**[ログの表示]** を選択すると、クラウドで実行されるサービス インスタンスのログが使用できます。

![Logs in Visual Studio Server Explorer][LogsServerExplorer]

同じログは、**Azure 管理ポータル**で、モバイル サービスの **[ログ]** タブでも使用できます。

![Logs in Azure Management Portal][LogsPortal]

<a name="AssemblyResolution"></a>
## デバッグによるクラウド アセンブリの問題の解決

モバイル サービスを Azure に発行すると、モバイル サービスは Mobile Services のホスティング環境によって読み込まれ、コントローラー コードをホストする HTTP パイプラインに対してシームレスにアップグレードとパッチが適用されます。これには、[.NET バックエンド NuGet パッケージ](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22)が参照するすべてのアセンブリが含まれます。チームは、常にサービスを更新して、これらのアセンブリの最新バージョンを使用します。

必要なアセンブリの*異なるメジャー バージョン*を参照することでバージョンの競合が起こる可能性があります (*マイナー* バージョンは異なってもかまいません)。この現象は、Mobile Services .NET バックエンドが使用するいずれかのパッケージの最新バージョンにアップグレードするよう NuGet から求められるときに頻繁に発生します。

>[AZURE.NOTE]Mobile Services は、現在 ASP.NET 5.1 のみに対応しています。現時点で ASP.NET 5.2 はサポートされていません。お使いの ASP.NET NuGet パッケージを 5.2.* にアップグレードすると、デプロイ後にエラーが発生する可能性があります。

更新されたサービスを Azure に発行するときに、そのいずれかのパッケージをアップグレードする場合は、次のように競合を示す警告ページが表示されます。

![Help page indicating assembly loading conflict][HelpConflict]

この現象と共に、次と類似する例外メッセージがサービス ログに記録されます。

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

この問題の修正は容易です。必要なアセンブリのバージョンをサポートされているバージョンに戻して、サービスを再発行するだけです。

<a name="EFMigrations"></a>
## Entity Framework の移行に関するトラブルシューティング

SQL Database と共に Mobile Services .NET バックエンドを使用している場合、Entity Framework (EF) をデータ アクセス テクノロジとして使用することで、データベースのクエリの実行とデータベースへのオブジェクトの保存が可能となります。開発者に代わって EF が処理する重要な側面に、コードに指定したモデル クラスが変更されるときのデータベースの列 (*スキーマ*とも呼ばれます) の変更方法があります。このプロセスは、[Code First Migrations](http://msdn.microsoft.com/data/jj591621) とも呼ばれます。

移行は複雑となる場合があり、データベースの状態が EF モデルと常に同期されていないと成功しない可能性があります。モバイル サービスで移行を処理する手順と発生する可能性があるエラーについては、「[データ モデルの変更を .NET バックエンド モバイル サービスに加える方法](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)」を参照してください。

<!-- IMAGES -->

[HelpPageAuth]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[HelpPage]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[HelpPageApi]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[TestClient]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[TestClientResponse]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[SymbolLoading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[SymbolServer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[PublishDebug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[AttachDebugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[LogsOutputWindow]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[LogsServerExplorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[LogsPortal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[HelpConflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png

<!---HONumber=August15_HO6-->