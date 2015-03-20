<properties 
	pageTitle="Azure モバイル エンゲージメントの使用" 
	description="Azure モバイル エンゲージメントで分析とプッシュ通知を使用する方法について説明します。"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# モバイル エンゲージメントの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store"  class="current">Windows ストア</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

このトピックでは、アプリの使用状況を理解して、プッシュ通知を Windows ストア アプリケーションのセグメント化されたユーザーに送信するために、Azure モバイル エンゲージメントの使用方法を説明します。 
このチュートリアルでは、モバイル エンゲージメントを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。その中で、基本的なデータを収集し、Windows 通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ストア アプリを作成します。完了すると、デバイス プロパティに基づいて、すべてのデバイスまたは特定のターゲット ユーザーに、プッシュ通知をブロードキャストできるようになります。モバイル エンゲージメントを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒にご覧ください。


このチュートリアルには、次のものが必要です。

+ Visual Studio 2013
+ [モバイル エンゲージメント Windows ストア SDK]

> [AZURE.IMPORTANT] このチュートリアルを終了することは、その他すべての Windows ストア アプリ向けモバイル エンゲージメント チュートリアルの前提条件です。また、このチュートリアルを終了するには、アクティブな Azure アカウントを持っている必要があります。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。

##<a id="setup-azme"></a>アプリ用にモバイル エンゲージメントをセットアップする

1. Azure 管理ポータルにログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリ サービス]**、**[モバイル エンゲージメント]**、**[作成]** の順にクリックします。

   	![][7]

3. 表示されたポップアップに、次の情報を入力します。
 
   	![][8]

	1. **アプリケーション名**: アプリケーションの名前を入力ができます。自由に任意の文字を使用してください。
	2. **プラットフォーム**: アプリ向けのターゲット プラットフォームを選択します (アプリが複数のプラットフォームをターゲットにしている場合は、各プラットフォームに対してこのチュートリアルを繰り返します)。
	3. **アプリケーション リソース名**: この名前を使用して、API と URL を通じてこのアプリケーションにアクセスします。従来の URL 文字のみを使用することをお勧めします。自動生成された名前は、ベースとなる名前として非常に役立ちます。また、この名前は一意である必要があるので、名前の重複を避けるために、プラットフォーム名を付加することをお勧めします。
	4. **場所**: このアプリ (およびさらに重要なそのコレクション) がホストされるデータ センターを選択します (コレクションについては下記を参照)。
	5. **コレクション**: アプリケーションを既に作成済みの場合は以前に作成したコレクションを選択し、そうでない場合は新しいコレクションを選択します。
	6. **コレクション名**: アプリケーションのグループを意味します。すべてのアプリを 1 つのグループに含めることで、それらを集計ができます。会社名や部門名を使用することを強くお勧めします。


	終了したら、チェック ボタンをクリックして、アプリの作成を完了します。

4. **[アプリケーション]** タブで、作成したアプリをクリックして選択します。
 
   	![][9]

5. 次に、**[接続情報]** をクリックして、統合する SDK に組み込む接続設定を表示します。
 
   	![][10]

6. 最後に、**[接続文字列]** をメモに取ります。これは、アプリケーション コードからこのアプリを特定する際に必要になります。

   	![][11]

	>[AZURE.TIP] 接続文字列の右側にある [コピー] アイコンを使用すると、接続文字列をクリップボードにコピーできるので便利です。

##<a id="connecting-app"></a>アプリをモバイル エンゲージメントのバックエンドに接続する

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。完全な統合のドキュメントについては、[モバイル エンゲージメント Windows ストア SDK ドキュメント] をご覧ください。

統合のデモンストレーションを行うために、Visual Studio で基本的なアプリを作成します。

###新しい Windows ストア プロジェクトを作成する

既にアプリが存在し、Windows 開発に精通している場合は、この手順を省略できます。

1. Visual Studio を起動し、ホーム画面で **[新しいプロジェクト...]** を選択します。

   	![][12]

2. ポップアップで、(1)  `[Windows ストア]` を選択し、次に (2)  `[新しいアプリケーション (XAML)]` を選択します。また、(3) アプリの  `[名前]` と (4)  `[ソリューション名]` を入力し、**[OK]** をクリックします。

   	![][13]

デモ アプリを使用して新しいプロジェクトが作成され、それにモバイル エンゲージメントを統合します。

###SDK ライブラリをプロジェクトに含める

SDK ライブラリをダウンロードして統合する

1. [モバイル エンゲージメント Windows ストア SDK] をダウンロードします。
2. この .zip ファイルをご使用のコンピューターのフォルダーに展開します。
3.  `PROJECT`、[Manage NuGet Packages... (NuGet パッケージの管理)] の順に移動します。
4. ポップアップで、[設定] をクリックします。
5. [+] ボタンをクリックして、新しいソースを作成します。

	![][17]

6. 下部にある  `...`  ボタンをクリックしてソースを選択し、ダウンロードした SDK を展開した lib フォルダー (手順 2 を参照) に移動して、`[選択]` をクリックします。

	![][18]

7. SDK はソースとして追加されます。 `[OK]` をクリックします。「オンライン」パッケージ リストからパッケージを選択し、[インストール] をクリックします。

	![][19]


###接続文字列を使用してアプリをモバイル エンゲージメントのバックエンドに接続します。

1.  `Package.appxmanifest` を開き、次の項目が宣言されていることを確認します (宣言されていない場合は追加します)。
		
		Internet (Client)

	![][20]

2. 同じファイルで、 `[宣言]` タブを開きます。
	1.  `[使用可能な宣言]` で  `[ファイルの種類の関連付け]` を追加します。
	2. 右側の  `[プロパティ]` パーツで、 `[名前]`を `engagement_log_file`に設定します。
	3. また、右側の  `[プロパティ]` パーツで、 `[ファイルの種類]` を `.set`に設定します。
	4. 最後に、 `[使用可能な宣言]` で、 `[キャッシュ ファイル更新プログラム]` を追加します。

		![][21]
 
3. アプリの **[接続情報]** ページで Azure ポータルに戻り、**[接続文字列]** をコピーします。

	![][11]

4.  `Resources\EngagementConfiguration.xml` ファイルの `<connectionString>` タグと `</connectionString>`  タグの間に貼り付けます。

	![][22]

5.  `App.xaml.cs` ファイルで次の操作を行います。
	1.  `using` ステートメントを追加します。

			using Microsoft.Azure.Engagement;

	2.  `OnLaunched` メソッドで SDK を初期化します。
			
			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

	3.  `Application_Activated` に次の内容を挿入します (まだ存在しない場合は、この操作を追加する必要があります)。

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

###画面をモバイル エンゲージメントに送信する

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) をモバイル エンゲージメントのバックエンドに送信する必要があります。そのために、モバイル エンゲージメント SDK が提供する  `EngagementPage` を使用して、 `MainPage` をサブクラス化します。

1. これを実行するには、以下に示すように EngagementPage を使用して、 `Page` の前にある  `MainPage` のスーパー クラスを置き換えます。

	![][23]

	>[AZURE.NOTE] 赤のアンダーラインが表示される場合は、忘れずに `using` 句に  `using Microsoft.Azure.Engagement;` を追加して、クラスを解決してください。

2.  `MainPage.xaml` ファイルで、次の操作を行います。
	1. 次の内容を名前空間宣言に追加します。

			xmlns:engagement="using:Microsoft.Azure.Engagement"
	2. xml タグ名内の  `Page` を  `engagement:EngagementPage` に置き換えます。

##<a id="monitor"></a>アプリがリアルタイム監視に接続していることを確認する方法

このセクションでは、モバイル エンゲージメントのリアルタイム監視機能を使用して、アプリがモバイル エンゲージメントのバックエンドに接続していることを確認する方法を説明します。

1. モバイル エンゲージメント ポータルに移動します。

	Azure ポータルで、このプロジェクト用に使用しているアプリを対象にしていることを確認し、下部にある **[Engage (エンゲージメント)]** ボタンをクリックします。

	![][26]

2. アプリのエンゲージメント ポータルの設定ページに移動します。ここで、以下に示す **[モニター]** タブをクリックします。
![][30]

3. アプリを起動するデバイスをリアルタイムでモニターに表示する準備ができました。
![][31]

4. Visual Studio に戻り、エミュレーターでアプリを起動します。または、緑の三角をクリックしてデバイスを選択し、接続済みのデバイスでアプリを起動します。

5. 正常に動作している場合、モニターに 1 つのセッションが表示されます。 
![][33]

**これで終了です。**このチュートリアルの最初の手順が正常に完了し、アプリがモバイル エンゲージメントのバックエンドに接続して、データが送信されました。


##<a id="integrate-push"></a>プッシュ通知を有効にする

モバイル エンゲージメントにより、ユーザーと通信を行い、キャンペーンのコンテキストに関するプッシュ通知とアプリ内メッセージングを届けることができます。このモジュールは、モバイル エンゲージメント ポータルで REACH として呼び出されます。
次のセクションでは、それらを受信するためにアプリをセットアップします。

###アプリが WNS プッシュ通知を受信できるようにする

1. 新しい `ファイルの種類の関連付け`を、宣言パネルで  `Package.appxmanifest` ファイルに追加します。

		Name: engagement_reach_content
		File type: .txt

	![][34]

2.  `Package.appxmanifest` ファイルの  `[通知]` の下にある  `Application` タブで、`[トースト対応]` に対して  `[はい]` を選択します。

	![][35]

###REACH SDK を初期化する

1.  `App.xaml.cs` で、エージェントの初期化の直後に、 `OnLaunched` 関数で  `EngagementReach.Instance.Init(args);`  を呼び出します。

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		   EngagementAgent.Instance.Init(args);
		   EngagementReach.Instance.Init(args);
		}

2.  `App.xaml.cs` で、エージェントの初期化の直後に、 `Application_Activated` 関数で  `EngagementReach.Instance.OnActivated(args);`  を呼び出します。

		protected override void OnActivated(IActivatedEventArgs args)
		{
		   EngagementAgent.Instance.OnActivated(args);
		   EngagementReach.Instance.OnActivated(args);
		}

3. アプリケーションを実行します。 
トーストを送信するための設定が完了したので、この基本的な統合が正しく実行されたことを検証します。

###通知を送信するためにモバイル エンゲージメントへのアクセスを許可する

1. アプリを Windows ストアに関連付け、 `Package security identifier (SID)` と `秘密鍵` (*クライアント シークレット*) を取得する必要があります。

2. モバイル エンゲージメント ポータルの [設定] に移動し、左側の  `[Native Push (ネイティブのプッシュ)]` セクションをクリックします。

3.  `[編集]` ボタンをクリックし、以下に示すように、 `パッケージ セキュリティ ID (SID)` と `秘密鍵`を入力します。

	![][36]

##<a id="send"></a>アプリに通知を送信する方法

アプリにプッシュ通知を送信する単純なプッシュ通知キャンペーンを作成します。

1. モバイル エンゲージメント ポータルの **[REACH]** タブに移動します。
2. **[新しいお知らせ]** をクリックして、プッシュ キャンペーンを作成します。
![][37]

3. 次の手順に従って、キャンペーンの最初のフィールドをセットアップします。
![][38]

	1. キャンペーンに希望する任意の名前を付けます。
	2. **[配信時刻]** で  *[指定なし]* を選択し、アプリが起動されているかどうかに関係なく、アプリが通知を受信できるようにします。
	3. 通知テキストに、プッシュ通知内で太字で表示されるタイトルを入力します。
	4. 次に、メッセージを入力します。

4. 下にスクロールし、コンテンツ セクションで、**[Notification only (通知のみ)]** を選択します。
![][39]

5. 最も基本的なキャンペーンの設定が完了したので、再度下にスクロールし、キャンペーンを作成して保存します。
![][40]

6. 最後の手順で、キャンペーンをアクティブ化します。
![][41]

デバイスで通知を確認したら、**これで終了です**。



<!-- URLs. -->
[モバイル エンゲージメント Windows ストア SDK ドキュメント]: ../mobile-engagement-windows-store-integrate-engagement/
[モバイル エンゲージメント Windows ストア SDK]: http://go.microsoft.com/?linkid=9864592

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-get-started/aux-create-app.png
[9]: ./media/mobile-engagement-windows-store-get-started/aux-select-app.png
[10]: ./media/mobile-engagement-windows-store-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-store-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-store-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-store-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-store-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-store-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-store-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-store-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-store-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-get-started/campaign-content.png
[40]: ./media/mobile-engagement-windows-store-get-started/campaign-create.png
[41]: ./media/mobile-engagement-windows-store-get-started/campaign-activate.png

<!--HONumber=47-->
