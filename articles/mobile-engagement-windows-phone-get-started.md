<properties 
	pageTitle="Windows Phone での Azure モバイル エンゲージメントの概要" 
	description="Windows Phone で Azure モバイル エンゲージメントの分析機能やプッシュ通知を使用する方法について説明します。"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="C#" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# モバイル エンゲージメントの概要

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Universal">Windows ストア</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

このトピックでは、Azure モバイル エンゲージメントを使用してアプリの使用状況を把握し、Windows Phone アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。 
このチュートリアルでは、モバイル エンゲージメントを使用した簡単なブロードキャスト シナリオを紹介します。チュートリアル内で空白の Windows アプリを作成し、このアプリで基本的なデータを収集して、Microsoft Push Notification Service (MPNS) を使用してプッシュ通知を受信します。チュートリアルを完了すると、すべてのデバイスにプッシュ通知を配信したり、デバイスのプロパティに基づいて特定のユーザーを対象に配信したりできます。次のチュートリアルで、モバイル エンゲージメントを使用して特定のユーザーやデバイス群を対象とする方法についてご確認ください。


このチュートリアルには、次のものが必要です。

+ Visual Studio 2013
+ [モバイル エンゲージメント Windows Phone SDK]

> [AZURE.IMPORTANT] Windows Phone アプリ向けの他のすべてのモバイル エンゲージメント チュートリアルは、このチュートリアルを完了していることが前提条件となっています。このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。

##<a id="setup-azme"></a>アプリ用のモバイル エンゲージメントをセットアップする

1. [Azure 管理ポータル]にログオンし、画面下にある **[+NEW (+新規)]** をクリックします。

2. **[アプリケーション サービス]**、**[Mobile Engagement]**、**[作成]** の順にクリックします。

   	![][7]

3. 表示されるポップアップ画面で、次の情報を入力します。
 
   	![][8]

	1. **アプリケーション名**: アプリケーションの名前を入力できます。使用する文字の種類に制限はありません。
	2. **プラットフォーム**:アプリの対象プラットフォームを選択します (アプリが複数のプラットフォームを対象としている場合は、各プラットフォーム用にこのチュートリアルを繰り返します)。
	3. **アプリケーション リソース名**:この名前を使用すると API や URL 経由でこのアプリにアクセスできます。標準の URL 文字のみを使用することをお勧めします。自動生成された名前を参考にしてください。また、名前の競合を避けるため、プラットフォーム名を追加して一意になるようにすることをお勧めします。
	4. **場所**:このアプリ (とそのコレクション - 詳細は後述) がホストされるデータ センターを選択します。
	5. **コレクション**:アプリケーションを既に作成済みの場合は以前に作成したコレクションを選択し、そうでない場合は新しいコレクションを選択します。
	6. **コレクション名**:アプリケーションのグループを示します。また、ここですべてのアプリが集計の計算が可能なグループにあることを確認できます。会社名や部門名を使用することを強くお勧めします。


	完了したら、チェック ボタンをクリックして、アプリの作成を終了します。

4. **[アプリケーション]** タブで先ほど作成したアプリをクリックして選択します。
 
   	![][9]

5. 次に、**[接続情報]** をクリックして、統合する SDK に組み込む接続設定を表示します。
 
   	![][10]

6. 最後に、**[接続文字列]** をメモに取ります。これは、アプリケーション コードからこのアプリを特定する際に必要になります。

   	![][11]

	>[AZURE.TIP] 接続文字列の右側にある [コピー] アイコンを使用すると、接続文字列をクリップボードにコピーできるので便利です。

##<a id="connecting-app"></a> アプリをモバイル エンゲージメントのバックエンドに接続する

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。完全な統合のドキュメントについては、「[モバイル エンゲージメント Windows Phone SDK ドキュメント]」をご覧ください。

統合のデモンストレーションを行うために、Visual Studio で基本的なアプリを作成します。

###新しい Windows Phone プロジェクトを作成します。

既にアプリが存在し、Windows Phone 開発に精通している場合は、この手順を省略できます。

1. Visual Studio を起動し、ホーム画面で **[新しいプロジェクト...]** を選択します。

   	![][12]

2. ポップアップで、(1)  `[Windows Phone]` を選択し、(2)  `[Windows Phone アプリ]` を選択します。また、(3) アプリ `名`と、(4) `ソリューション名`を入力して **[OK]** をクリックします。

   	![][13]

これで、デモ アプリ付きのプロジェクトが作成できました。これをモバイル エンゲージメントに統合します。

###プロジェクトに SDK ライブラリを含める

SDK ライブラリをダウンロードして統合する

1. [Mobile Engagement Windows Phone SDK] をダウンロードします。
2. .tar.gz ファイルをコンピューター上のフォルダーに展開します。
3.  `[PROJECT]` に移動して、[`Manage NuGet Packages...] を開きます。
4. ポップアップで、[設定] をクリックします。
5. [+] ボタンを押して新しいソースを作成します。
	![][17]

6. 下部にある [ `...` ] ボタンをクリックしてソースを選択し、展開した SDK (ステップ 2 を参照) の lib フォルダーに移動して、 `[選択]` をクリックします。
	![][18]

7. SDK がソースとして追加されます。 `[OK]` をクリックします。
	![][19]


###接続文字列を使用して、アプリをモバイル エンゲージメントのバックエンドに接続する

1. Engagement SDK では、正常に動作するために Windows Phone SDK の一部の機能が必要です。
WMAppManifest.xml ファイルを開いて、次の機能が Capabilities パネルで宣言されていることを確認します。
	- ID_CAP_NETWORKING
	- ID_CAP_IDENTITY_DEVICE


	![][20]

2. アプリの **[接続情報]** ページで Azure ポータルに戻り、 **[接続文字列]** をコピーします。

	![][11]

3. それを  `Resources\EngagementConfiguration.xml` ファイルの `<connectionString>` タグと `</connectionString>` タグの間に貼り付けます。

	![][21]

4.  `App.xaml.cs` ファイルで次の操作を行います。
	1.  `using` ステートメントを追加します。

			using Microsoft.Azure.Engagement;

	2. SDK を  `Application_Launching` メソッドで初期化します。
			
			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	3.  `Application_Activated` に次を挿入します。

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

###画面をモバイル エンゲージメントに送信します。

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) をモバイル エンゲージメントのバックエンドに送信する必要があります。これを行うには、Mobile Engagement SDK で提供される  `EngagementPage` を使用して  `MainPage` のサブクラスを作成します。

1. それには、次のように  `PhoneApplicationPage` の前の  `MainPage` のスーパー クラスを EngagementPage で置き換えます。

	![][22]

	>[AZURE.NOTE] クラスに赤の下線が付いている場合は、 `using Microsoft.Azure.Engagement;` を `using` 句に追加して解決するようにしてください。

2.  `MainPage.xml` ファイルで次の操作を行います。
	1. 名前空間宣言に追加する

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
	2. xml タグ名の  `phone:PhoneApplicationPage` を  `engagement:EngagementPage` で置き換えます。

##<a id="monitor"></a>リアルタイム モニタリングを使用してアプリが接続されていることを確認する方法

このセクションでは、モバイル エンゲージメントのリアルタイム モニタリング機能を使用して、アプリがモバイル エンゲージメントのバックエンドに接続されていることを確認する方法を説明します。

1. モバイル エンゲージメント ポータルに移動します。

	Azure ポータルで、このプロジェクトで使用しているアプリを開いていることを確認し、下部にある **[Engage (エンゲージ)]** ボタンをクリックします。

	![][26]

2. アプリのエンゲージメント ポータルの設定ページに移動します。次の図にある **[Monitor (モニター)]** タブをクリックします。
	![][30]

3. アプリを起動するデバイスをリアルタイムでモニターに表示する準備ができました。
	![][31]

4. Visual Studio に戻り、エミュレーターでアプリを起動します。または、緑の三角をクリックしてデバイスを選択し、接続済みのデバイスでアプリを起動します。

5. 正常に動作している場合、モニターに 1 つのセッションが表示されます。 
	![][33]

**これで終了です。**このチュートリアルの最初のステップを完了しました。アプリがモバイル エンゲージメントのバックエンドに接続され、データを送信できました。


##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

モバイル エンゲージメントにより、ユーザーと通信を行い、キャンペーンのコンテキストに関するプッシュ通知とアプリ内メッセージングを届けることができます。このモジュールは、モバイル エンゲージメント ポータルで REACH として呼び出されます。
次のセクションでは、アプリでそれらを受信するようセットアップします。

###アプリが WNS プッシュ通知を受信できるようにする

 `WMAppManifest.xml` ファイルに新しい Capabilities を追加します。

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

###REACH SDK を初期化する

1.  `App.xaml.cs`で、 `Application_Launching` 関数の エージェントの初期化直後の  `EngagementReach.Instance.Init();`  を呼び出します。

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2.  `App.xaml.cs`で、`Application_Activated` 関数のエージェントの初期化直後の  `EngagementReach.Instance.OnActivated(e);`  を呼び出します。

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

これができたら、次はこの基本統合が正常に行われているかを確認します。

##<a id="send"></a>プッシュ通知をアプリに送信する方法

簡単なプッシュ通知のキャンペーンを作成して、アプリにプッシュ通知を送信します。

1. モバイル エンゲージメント ポータルの **[REACH]** タブに移動します。
2. **[新しいお知らせ]** をクリックしてプッシュ キャンペーンを作成します。
	![][35]

3. 次の手順に従って、キャンペーンの最初のフィールドをセットアップします。
	![][36]

	1. キャンペーンに任意の名前を付けます。
	2. アプリが起動していても、していなくても通知を受け取れるよう **[Delivery time (配信時刻)]** を [ *Any time*] に設定します。
	3. 通知テキストに、プッシュ通知内で太字で表示されるタイトルを入力します。
	4. 次にメッセージを入力します。

4. 下へスクロールし、コンテンツ セクションで **[Notification only (通知のみ)]** を選択します。
![][37]

5. 最も基本的なキャンペーンの設定が完了したので、再度下にスクロールし、キャンペーンを作成して保存します。
![][38]

6. 最後の手順で、キャンペーンをアクティブ化します。
![][39]

デバイスで通知を確認したら、**これで終了です**。
![][40]


<!-- URLs. -->
[モバイル エンゲージメント Windows Phone SDK]: http://go.microsoft.com/?linkid=9874664

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-windows-phone-get-started/select-app.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-phone-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-phone-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-phone-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-phone-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-create.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!--HONumber=47-->
