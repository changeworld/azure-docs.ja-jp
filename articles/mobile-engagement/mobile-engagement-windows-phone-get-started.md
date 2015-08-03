<properties
	pageTitle="Windows Phone Silverlight アプリの Azure モバイル エンゲージメントの概要"
	description="Windows Phone Silverlight アプリで Azure モバイル エンゲージメントの分析機能やプッシュ通知を使用する方法について説明します。"
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Windows Phone Silverlight アプリの Azure モバイル エンゲージメントの概要

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、Azure モバイル エンゲージメントを使用してアプリの使用状況を把握し、Windows Phone Silverlight アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。このチュートリアルでは、モバイル エンゲージメントを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。チュートリアル内で空白の Windows Phone Silverlight アプリを作成し、このアプリで基本的なデータを収集して、Microsoft Push Notification Service (MPNS) を使用してプッシュ通知を受信します。完了すると、デバイス プロパティに基づいて、すべてのデバイスまたは特定のターゲット ユーザーに (MPNS を利用して) プッシュ通知をブロードキャストできるようになります。モバイル エンゲージメントを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒にご覧ください。

> [AZURE.NOTE]Windows Phone 8.1 (Silverlight 以外) を対象としている場合は、[Windows Universal チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を参照してください。

このチュートリアルには、次のものが必要です。

+ Visual Studio 2013
+ [モバイル エンゲージメント Windows Phone SDK]

> [AZURE.IMPORTANT]Windows Phone Silverlight アプリ向けの他のすべてのモバイル エンゲージメント チュートリアルは、このチュートリアルを完了していることが前提条件となっています。このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>」をご覧ください。

##<a id="setup-azme"></a>Windows Phone Silverlight アプリのモバイル エンゲージメントの設定

1. Azure 管理ポータルにログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリ サービス]**、**[モバイル エンゲージメント]**、**[作成]** の順にクリックします。

   	![][7]

3. 表示されたポップアップに、次の情報を入力します。

   	![][8]

	- **アプリケーション名**: アプリケーションの名前を入力します。自由に任意の文字を使用してください。
	- **プラットフォーム**: アプリ向けのターゲット プラットフォーム (**Windows Phone Silverlight**) を選択します (アプリが複数のプラットフォームをターゲットにしている場合は、各プラットフォームに対してこのチュートリアルを繰り返します)。
	- **アプリケーション リソース名**: この名前を使用して、API と URL を通じてこのアプリケーションにアクセスします。従来の URL の文字のみを使用する必要があります。自動生成された名前は、ベースとなる名前として役立ちます。また、この名前は一意である必要があるので、重複を避けるために、プラットフォーム名を付加することをお勧めします。
	- **場所**: このアプリ (さらに重要なそのコレクション) がホストされるデータ センターを選択します。
	- **コレクション**: アプリケーションを既に作成済みの場合は以前に作成したコレクションを選択し、そうでない場合は新しいコレクションを選択します。
	- **コレクション名**。 これは、グループのアプリケーションを表します。すべてのアプリを 1 つのグループに含めることで、それらをメトリック集計できます。該当する場合は、ここで会社名や部門を使用する必要があります。

4. **[アプリケーション]** タブで、作成したアプリを選択します。

5. **[接続情報]** をクリックして、モバイル アプリに統合する SDK に組み込む接続設定を表示します。

   	![][10]

6. **[接続文字列]** をコピーします - これは、アプリケーション コード内で、このアプリケーションを識別し、Phone アプリからモバイル エンゲージメント サービスに接続するために必要なものです。

   	![][11]

##<a id="connecting-app"></a>アプリをモバイル エンゲージメントのバックエンドに接続する

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。完全な統合のドキュメントについては、「[モバイル エンゲージメント Windows Phone SDK ドキュメント]」をご覧ください。

統合のデモンストレーションを行うために、Visual Studio で基本的なアプリを作成します。

###新しい Windows Phone Silverlight プロジェクトを作成する

1. Visual Studio を起動し、ホーム画面で [**新しいプロジェクト...**] を選択します。

2. ポップアップで、[`Store Apps`] -> [`Windows Phone Apps`] -> [`Blank App (Windows Phone Silverlight)`] の順に選択します。アプリの [`Name`] と [`Solution name`] に入力し、[**OK**] をクリックします。

   	![][13]

3. **Windows Phone 8.0** と **Windows Phone 8.1** のいずれかのバージョンを対象として選択できます。

これで、Azure モバイル エンゲージメント SDK を統合する新しい Windows Phone Silverlight アプリが作成されました。

###アプリをモバイル エンゲージメントのバックエンドに接続する

1. プロジェクトに[モバイル サービス Windows Phone SDK] nuget パッケージをインストールします。

2. `WMAppManifest.xml` ([プロパティ] フォルダーの下) を開き、`<Capabilities />` タグで次が宣言されていることを確認します (宣言されていない場合、追加します)。

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

	![][20]

3. 先にコピーしたモバイル エンゲージメント アプリの接続文字列を `Resources\EngagementConfiguration.xml` ファイルの `<connectionString>` タグと `</connectionString>` タグの間に貼り付けます。

	![][21]

4. `App.xaml.cs` ファイルで次の操作を行います。

	a.`using` ステートメントを追加します。

			using Microsoft.Azure.Engagement;

	b.`Application_Launching` メソッドで SDK を初期化します。

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c.`Application_Activated` に次を挿入します。

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>リアルタイム監視の有効化

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) をモバイル エンゲージメントのバックエンドに送信する必要があります。そのために、モバイル エンゲージメント SDK が提供する `EngagementPage` を使用して、`MainPage` をサブクラス化します。

1. `using` ステートメントを追加します。

		using Microsoft.Azure.Engagement;

2. 以下のように、`PhoneApplicationPage` の前にある `MainPage` のスーパー クラスを `EngagementPage` で置き換えます。

	![][22]

3. `MainPage.xml` ファイルで、次の操作を行います。a.次の内容を名前空間宣言に追加します。

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b.xml タグ名の `phone:PhoneApplicationPage` を `engagement:EngagementPage` で置き換えます。

###アプリがリアルタイム監視に接続していることを確認する

このセクションでは、モバイル エンゲージメントのリアルタイム監視機能を使用して、アプリがモバイル エンゲージメントのバックエンドに接続していることを確認する方法を説明します。

1. モバイル エンゲージメント ポータルに移動します。

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の [**関与**] ボタンをクリックします。

	![][26]

2. アプリのエンゲージメント ポータルの設定ページに移動します。ここで、以下に示す [**モニター**] タブをクリックします。![][30]

3. アプリを起動するデバイスをリアルタイムでモニターに表示する準備ができました。

4. Visual Studio に戻り、エミュレーターまたは接続されたデバイスのいずれかでアプリを起動します。

5. 正常に動作している場合、モニターに 1 つのセッションが表示されます。![][33]

**ご利用ありがとうございます。** これで終了です。このチュートリアルの最初の手順が正常に完了し、アプリがモバイル エンゲージメントのバックエンドに接続して、データが送信されました。

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

モバイル エンゲージメントにより、ユーザーと通信を行い、キャンペーンのコンテキストに関するプッシュ通知とアプリ内メッセージングを届けることができます。このモジュールは、モバイル エンゲージメント ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するためにアプリをセットアップします。

###アプリが WNS プッシュ通知を受信できるようにする

`WMAppManifest.xml` ファイルに新しい Capabilities を追加します。

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

###REACH SDK を初期化する

1. `App.xaml.cs` で、エージェントの初期化の直後に、`Application_Launching` 関数で `EngagementReach.Instance.Init();` を呼び出します。

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. `App.xaml.cs` で、エージェントの初期化の直後に、`Application_Activated` 関数で `EngagementReach.Instance.OnActivated(e);` を呼び出します。

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

これができたら、次はこの基本統合が正常に行われているかを確認します。

##<a id="send"></a>アプリへ通知を送信する

アプリにプッシュ通知を送信する単純なプッシュ通知キャンペーンを作成します。

1. Mobile Engagement ポータルで [**リーチ**] タブに移動します。

2. [**新しいお知らせ**] をクリックして、プッシュ キャンペーンを作成します。![][35]

3. 次の手順に従って、キャンペーンの最初のフィールドをセットアップします。![][36]

	1. キャンペーンに希望する任意の名前を付けます。
	2. [**配信時刻**] で [*指定なし*] を選択し、アプリが起動されているかどうかに関係なく、アプリが通知を受信できるようにします。
	3. 通知テキストに、プッシュ通知内で太字で表示されるタイトルを入力します。
	4. 次に、メッセージを入力します。

4. スクロール ダウンし、コンテンツ セクションで [**通知のみ**] を選択します。![][37]

5. 最も基本的なキャンペーンの設定が完了したので、もう一度下にスクロールし、[**作成**] ボタンをクリックしてキャンペーンを保存します。

6. 最後の手順として、[**アクティブ化**] をクリックしてキャンペーンをアクティブにし、プッシュ通知を送信します。![][39]

7. デバイスで通知を確認したら、**これで終了です。**![][40]

<!-- URLs. -->
[モバイル エンゲージメント Windows Phone SDK]: http://go.microsoft.com/?linkid=9874664
[モバイル サービス Windows Phone SDK]: http://go.microsoft.com/?linkid=9874664
[モバイル エンゲージメント Windows Phone SDK ドキュメント]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
 

<!---HONumber=July15_HO4-->