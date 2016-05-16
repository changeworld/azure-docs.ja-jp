<properties
	pageTitle="Azure Mobile Engagement デモ アプリ"
	description="Azure Mobile Engagement デモ アプリのダウンロードする場所、使用方法、および使用する利点の確認について説明します"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="piyushjo" />

# Azure Mobile Engagement デモ アプリ

**iOS**、**Android**、および **Windows** プラットフォーム用のAzure Mobile Engagement デモ アプリは、公開されたことによって有用な情報を検索し、Azure Mobile Engagement について学習するのに役立ちます。

アプリは次の用途に役立ちます。

1. ビデオ、ドキュメント、サポート フォーラム、機能に関する要望の投稿先のような Azure Mobile Engagement 固有のリソースへの便利なリンクを簡単に検索します。 
2. Azure Mobile Engagement でサポートされているサンプルの通知を発生させ、独自のモバイル アプリケーションの概念を取得します。 
3. 独自のアプリに Mobile Engagement を実装する方法を学習するために使用できる参照実装を提供し、次の操作を実行します。 

	- Analytics のデータを収集する 
	- *全画面スポット ビュー*または*ポップアップ*などの種類の高度な通知シナリオを実装する
	- アンケート/ポーリングを実装する
	- サイレント プッシュ/データ プッシュのシナリオを実装する   

## アプリのインストール
このアプリは、次の各アプリ ストアで入手できます。

1. **Windows ユニバーサル デモ アプリ**

	- [Windows App Store のダウンロード リンク](https://www.microsoft.com/ja-JP/store/apps/azure-mobile-engagement/9nblggh4qmh2) 
	- アプリは、Windows 10 ユニバーサル アプリとして開発されました。ソース コードは [Github](https://github.com/Azure/azure-mobile-engagement-app-ios) から入手できます。

2. **iOS デモ アプリ**

	- [Apple ストアのダウンロード リンク](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090) 
	- アプリは、iOS Swift で開発されました。ソース コードは [Github](https://github.com/Azure/azure-mobile-engagement-app-ios) から入手できます。

3. **Android デモ アプリ**

	- [Google Play ストアのダウンロード リンク](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)
	- ソース コードは [Github](https://github.com/Azure/azure-mobile-engagement-app-android) から入手できます。

![][1]

![][2] ![][3]


## 使用法

次の方法でこれらのアプリを使用できます。

**1) 前述のアプリケーション ストアのリンクからデバイスにアプリをダウンロードします。**

>[AZURE.IMPORTANT] Azure アカウントは不要です。またバックエンドにアプリを接続する必要もありません。アプリは独立して動作します。

- デバイスにアプリを実装すると、左側のメニューにあるリンクにアクセスして、Azure Mobile Engagement に関する有用な情報をすべて検索できます。 
- 製品の最新の更新プログラムについては常に更新させるために、[サービスの RSS フィード](https://aka.ms/azmerssfeed)もこのアプリケーションに追加されています。
- また、サンプルの通知シナリオに進んで、各プラットフォームのAzure Mobile Engagement でサポートされている通知の種類を発生させることができます。これらの通知はローカルで発生させることができます。つまり、画面上のボタンをクリックして、Azure Mobile Engagement プラットフォームからの通知を送信する場合と同じ通知の発生を表示することができます。 

![][4]
    
![][5] ![][6]

**2) 前述の Github のリンクからソース コードをダウンロードします。**

- ソース コードをダウンロードしたら、XCode for iOS、Android Studio for Android、Visual Studio for Windows などそれぞれの開発環境で開きます。 
- 次に、[Basic SDK の統合手順](mobile-engagement-windows-store-dotnet-get-started.md)に従って、独自の Mobile Engagement バックエンド インスタンスにこのアプリを接続できるようにする必要があります。 
	- アプリで接続文字列を構成する必要があります。  
	- アプリのプッシュ通知のプラットフォームを構成する必要もあります。 
- このアプリ自体が Azure Mobile Engagement でインストルメント化されるため、バックエンドに接続した後にアプリを起動すると、[監視] タブでユーザー セッション、アクティビティ、イベントなどを表示できることがわかります。 
- ローカル通知を使用する代わりに、独自の Mobile Engagement インスタンスからこのアプリに通知を送信することもできます。 
	- ここでは、アプリの **[Get the Device ID (デバイス ID の取得)]** メニュー項目を使用して、テスト デバイスとしてデバイスを追加できます。これにより、 プラットフォーム バックエンド インスタンスでテスト デバイスとして登録するデバイス ID を取得できます。 

	![][7]
	    
	![][8] ![][9]

## デモ アプリの主な機能

1. 前述のように、このアプリでは、Azure Mobile Engagement のすべての主要リソースを理解できます。左側のメニューにあるリンクにアクセスしてください。 

2. 各プラットフォームでアプリ外通知を発生させます。これらの通知は、**[通知のみ]** として送信することができます。ここで、通知をクリックするとアプリケーションのネイティブ画面 (**[ディープ リンク]**を使用) が単に開きます。または、**[Web のアナウンス]** として送信することもできます。ここで、通知をクリックしたときに表示される Mobile Engagement バックエンドから追加の HTML コンテンツを送信できます。

	![][29]

	
	- iOS の場合は、アプリを閉じて、アプリ外またはシステムのプッシュ通知を確認する必要があります。ユーザーが通知自体からアクションを実行できるようにするために、*[フィードバック]* および *[共有]* に対して、ここでこのアプリ外通知に追加されたボタンと同様に追加する **[動作設定ボタン]** の実装をここで確認できます。 
	    
	![][11] ![][14]
	
	
	- Android の場合は、複数行のテキストを追加する形式 (**[テキスト大]**) で、または、iOS でサポートされているように **[動作設定ボタン]** と共に通知のイメージ (**[全体像]**) を通知に追加する形式で、Android でサポートされているオプションを確認します。 
	
	![][12] ![][15]
	
	
	- Windows 10 では、通知の外観を PC で確認できます。この通知も Windows 10 の**通知センター**に表示されます。現時点で、Windows SDK への **[動作設定ボタン]** の追加はサポートされていません。 
	
	![][10] ![][13]

3. 各プラットフォームで既定のアプリ内通知を発生させます。これは 2 段階の発生です。最初に **[通知]** ウィンドウが表示されます。これをクリックすると、全画面の **[アナウンス]** が開き、下に表示されます。このアナウンスの内容は、Mobile Engagement バックエンド インスタンスから提供されます。SDK には、ロゴおよび色の追加と同様に、このデモ アプリで簡単にカスタマイズできる通知とアナウンスの両方のテンプレートが用意されています。

	![][16]
	
	![][17] **iOS** ![][18] **Android**

4. Azure Mobile Engagement の **[カテゴリ]** 機能を使用して、この既定の発生をカスタマイズすることもできます。デモ アプリで、通知の発生を変更するための 2 つの一般的な方法について説明してきました。Windows SDK では、[カテゴリ] 機能がまだサポートされていないこと に注意してください。

	**全画面スポット ビュー**
	
	![][30]

	![][21] ![][22]

	**ポップアップ通知**

	![][31]
	
	![][19] ![][20]

5. Azure Mobile Engagement は、**[ポーリング]** と呼ばれるアプリ内通知の特殊な種類もサポートしています。これにより、簡易アンケートをセグメント化されたアプリのユーザーに送信できます。次のように、質問と各質問のオプションを追加することができます。これにより、アプリのユーザーにアプリ内通知として表示することができます。

	![][32]

	![][26]
	    
	![][27] **iOS** ![][28] **Android**

5. Azure Mobile Engagement は、サイレント **[データ プッシュ]** 通知の送信もサポートして います。アプリで処理し、いくつかのアクションを実行できる次の例の JSON データのようにサービスからデータを送信できます。たとえば、どのようにデータ プッシュ通知を使用して項目の価格を選択的に変更しているかを示します。

	![][33]

	![][23]
	    
	![][24] **iOS** ![][25] **Android**

> [AZURE.NOTE] サンプル通知画面の *[Click here for instructions on how to send these notifications from Mobile Engagement platform (Mobile Engagement プラットフォームからこれらの通知を送信する手順についてはここをクリック)]* をクリックして、これらの通知の詳細な手順を表示できることに注意してください。


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png

<!---HONumber=AcomDC_0504_2016-->