<properties
	pageTitle="Xamarin iOS アプリでの Mobile Services の使用"
	description="次のチュートリアルに従って、Xamarin iOS 開発用の Azure Mobile Services を使用します。"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="conceptdev"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/22/2014"
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

このチュートリアルでは、Azure の Mobile Services を使用して Xamarin.iOS アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しい Mobile Services と、新しい Mobile Services にアプリケーション データを保存する簡単な <em>To do list</em> アプリケーションの両方を作成します。

このチュートリアルのビデオを見る場合は、下のクリップでこのチュートリアルと同じ手順が表示されます。

ビデオ:"Getting Started with Xamarin and Azure Mobile Services" (Xamarin と Azure Mobile Services の使用)。Xamarin のデベロッパー エバンジェリスト Craig Dunn 氏 (再生時間:10:05 分)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了するには、XCode と、OS X 用 [Xamarin Studio] または Visual Studio 用 Xamarin Visual Studio プラグイン (Windows) が必要です。サンプルは iOS 5.0 以降で実行します。

> [AZURE.IMPORTANT] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料 Mobile Services を入手できます。このサービスは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F"%20target="_blank)をご覧ください。

## <a name="create-new-service"> </a>新しい Mobile Services を作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

<h2>新しい Xamarin.iOS アプリを作成する</h2>

Mobile Services を作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更して Mobile Services に接続できます。

ここでは、Mobile Services に接続された新しい Xamarin.iOS アプリを作成します。

1.  管理ポータルで、**[Mobile Services]** をクリックし、先ほど作成した Mobile Services をクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Xamarin.iOS]** を選択し、**[新しい Xamarin.iOS アプリを作成する]** を展開します。

	![][6]

	これにより、Mobile Services に接続された Xamarin.iOS アプリケーションを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. まだインストールしていない場合は、Xcode (最新バージョンの Xcode 6.0 以降をお勧めします) と [Xamarin Studio] をダウンロードしてインストールします。

4. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

5. **[アプリをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

	これにより、Mobile Services に接続されている _To do list_ サンプル アプリケーションのプロジェクトがダウンロードされ、Xamarin.iOS 向け Azure Mobile Services コンポーネントを参照します。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

<h2>新しい Xamarin.iOS アプリを実行する</h2>

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、Xamarin Studio または Visual Studio で **XamarinTodoQuickStart.iOS.sln** ソリューション ファイルを開きます。

	![][8]

	![][9]

2. **[実行]** をクリックしてプロジェクトをビルドし、このプロジェクトの既定である iPhone エミュレーターでアプリケーションを開始します。

3. アプリケーションで、意味のあるテキスト (たとえば、「チュートリアルの完了」) を入力し、プラス (**+**) アイコンをクリックします。

	![][10]

	これで、Azure でホストされている新しい Mobile Services に POST 要求が送信されます。要求のデータは TodoItems テーブルに挿入されます。テーブルに格納された項目が Mobile Services によって返され、データが一覧に表示されます。

	> [AZURE.NOTE] Mobile Services にアクセスして TodoService.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。

4. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

	![][11]

	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

	![][12]


## 次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。

* [オフライン データ同期の使用]
  <br/>クイック スタートでオフライン データの同期を使用して、アプリケーションの応答性と信頼性を高める方法について説明します。

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Mobile Services の使用]:#getting-started
[新しい Mobile Services を作成する]:#create-new-service
[Mobile Services インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[データの使用]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[オフライン データ同期の使用]: /develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理ポータル]: https://manage.windowsazure.com/


<!--HONumber=52--> 