<properties
	pageTitle="iOS アプリ用 Azure Mobile Services の使用"
	description="次のチュートリアルに従って、iOS 開発用の Azure Mobile Services を使用します。"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

>[AZURE.TIP] Microsoft Azure を使用したモバイル開発を初めて行う場合は、Azure Mobile Services の代わりに [Azure Mobile Apps を使用](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)してください。Mobile Apps を通じて[さらに多くのメリット](../app-service-mobile/app-service-mobile-value-prop-migration-from-mobile-services.md)が得られます。

このチュートリアルでは、Azure Mobile Services を使用して iOS アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な _To do list_ アプリケーションの両方を作成します。Mobile Services はサーバー側ビジネス ロジックの .NET と Visual Studio を使用します。JavaScript のサーバー側ビジネス ロジックを使用してモバイル サービスを作成する方法については、このトピックの[ JavaScript バックエンド バージョン]をご覧ください。

> [AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、[無料 Mobile Services を入手できます。このサービスは評価終了後も使用できます](https://azure.microsoft.com/pricing/details/mobile-services/)。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-ios-get-started%2F)を参照してください。

## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## ローカル コンピューターに、モバイル サービスとアプリケーションをダウンロードします。

モバイル サービスが作成されたので、ローカルで実行できるプロジェクトをダウンロードします。

1. 先ほど作成したモバイル サービスをクリックし、[クイック スタート] タブの [**プラットフォームの選択**] で [**iOS**] をクリックし、[**新しい iOS アプリを作成する**] を展開します。

2. Windows PC で、[**サービスのダウンロードとクラウドへの発行**] の下の [**ダウンロード**] をクリックします。これにより、モバイル サービスを実装する Visual Studio プロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

3. Mac で、[**アプリケーションをダウンロードして実行する**] の下の [**ダウンロード**] をクリックします。これにより、モバイル サービスに接続されている _To do list_ サンプル アプリケーションのプロジェクトが、Mobile Services iOS SDK と共にダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル サービスをテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## モバイル サービスを発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


## 新しい iOS アプリを実行する

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]


## <a name="next-steps"> </a>次のステップ

これで、Azure で実行されているモバイル サービスに対して新しいクライアント アプリケーションを実行する方法が示されます。iOS アプリをローカル コンピューターで実行しているモバイル サービスでテストする前に、iOS 開発コンピューターからのアクセスを許可するように Web サーバーおよびファイアウォールを構成する必要があります。詳細については、[ローカル モバイル サービスへの接続を可能にするローカル Web サーバーの構成](mobile-services-dotnet-backend-how-to-configure-iis-express.md)を参照してください。

Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。

* [オフライン データの同期の使用]<br/> オフライン データの同期を使用して、アプリの応答性と信頼性を高める方法について説明します。

* [既存の Azure Mobile Services アプリへの認証の追加]<br/> ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [Mobile Services アプリへのプッシュ通知の追加] <br/>アプリにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services .NET バックエンドのトラブルシューティング] <br/>Mobile Services .NET バックエンドで発生する問題を診断して解決する方法について説明します。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[オフライン データの同期の使用]: mobile-services-ios-get-started-offline-data.md
[既存の Azure Mobile Services アプリへの認証の追加]: mobile-services-dotnet-backend-ios-get-started-users.md
[Mobile Services アプリへのプッシュ通知の追加]: mobile-services-dotnet-backend-ios-get-started-push.md
[Mobile Services .NET バックエンドのトラブルシューティング]: mobile-services-dotnet-backend-how-to-troubleshoot.md
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[ JavaScript バックエンド バージョン]: mobile-services-ios-get-started.md

<!----HONumber=AcomDC_0211_2016-->