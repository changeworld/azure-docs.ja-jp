<properties
	pageTitle="Xamarin iOS アプリケーション用 Mobile Services の使用 | Microsoft Azure"
	description="次のチュートリアルに従って、Xamarin iOS 開発用の Azure Mobile Services を使用します。"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="11/05/2015"
	ms.author="donnam"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

>[AZURE.TIP]Microsoft Azure を使用したモバイル開発を初めて行う場合は、Azure Mobile Services の代わりに [Azure Mobile Apps を使用](app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)してください。Mobile Apps を通じて[さらに多くのメリット](app-service-mobile-value-prop-migration-from-mobile-services-preview.md)が得られます。

このチュートリアルでは、Azure Mobile Services を使用して Xamarin iOS アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な _To do list_ アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン]」を参照してください。

>[AZURE.NOTE]このトピックでは、Microsoft Azure 管理ポータルを使用して、新しい Mobile Services プロジェクトを作成する方法について説明します。Visual Studio 2013 更新プログラム 2 を使用して、新しいモバイル サービス プロジェクトを既存の Visual Studio ソリューションに追加することもできます。詳細については、「[クイック スタート: モバイル サービスを追加する (.NET バックエンド)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]


このチュートリアルを完了することは、Xamarin iOS アプリケーションの他のすべての Mobile Services チュートリアルの前提条件です。

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、Azure 試用版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-JP%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">無料試用版のサイト</a>を参照してください。<br />このチュートリアルには <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> が必要です。無料試用版が利用できます。

## 新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 新しい Xamarin iOS アプリケーションを作成する

モバイル サービスを作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル サービスに接続することができます。

ここでは、モバイル サービス用の新しい Xamarin iOS アプリケーションとサービス プロジェクトをダウンロードします。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Xamarin]** を選択し、**[新しい Xamarin アプリケーションを作成する]** を展開します。

   	![][6]

   	これにより、モバイル サービスに接続された Xamarin iOS アプリケーションを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. まだインストールしていない場合は、<a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4. [Xcode] v4.4 以降および [Xamarin Studio] をダウンロードしてインストールします。また、Xamarin for Visual Studio を使用することもできます。

5. **[サービスのダウンロードとクラウドへの発行]** で **[iOS]** を選択し、**[ダウンロード]** をクリックします。

  	これにより、モバイル サービスとモバイル サービスに接続されている _To do list_ サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

6. 発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル サービスをテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## モバイル サービスを発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Xamarin iOS アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. Visual Studio または Xamarin Studio で、モバイル サービス ソリューション内のクライアント プロジェクトに移動します。

	![][8]

	![][9]

2. **[実行]** ボタンを押してクライアント プロジェクトをビルドし、iPhone エミュレーターでアプリケーションを開始します。

3. アプリケーションで、意味のあるテキスト (たとえば、「_チュートリアルの完了_」) を入力し、正符号 (**[+]**) アイコンをクリックします。

	![][10]

	これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。

>[AZURE.NOTE]モバイル サービスにアクセスして QSTodoService.cs C# ファイルにあるデータをクエリおよび挿入するコードを確認できます。


## 次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックを参照してください。

* [オフライン データの同期の使用]<br/>クイックスタートでオフライン データの同期を使用して、アプリの応答性と信頼性を高める方法について説明します。

* [認証の使用] <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services .NET バックエンドのトラブルシューティング] <br/>Mobile Services .NET バックエンドで発生する問題を診断して解決する方法について説明します。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[オフライン データの同期の使用]: mobile-services-xamarin-ios-get-started-offline-data.md
[認証の使用]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[プッシュ通知の使用]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[JavaScript バックエンド バージョン]: mobile-services-ios-get-started.md
[Mobile Services .NET バックエンドのトラブルシューティング]: mobile-services-dotnet-backend-how-to-troubleshoot.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!---HONumber=Nov15_HO3-->