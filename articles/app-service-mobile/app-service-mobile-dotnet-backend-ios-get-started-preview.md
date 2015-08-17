<properties
	pageTitle="Azure App Service のモバイル iOS アプリの使用"
	description="次のチュートリアルに従って、iOS 開発に Azure App Service を使用します。"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="06/18/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>iOS アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

このチュートリアルでは、Azure App Service の Mobile Apps を使用して iOS アプリを作成する方法について説明します。新しいモバイル アプリ バックエンドと、アプリのデータを格納する簡単な _To do list_ アプリを作成します。このチュートリアルでは、サーバー側ロジックとして .NET と Visual Studio を使用します。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)に関するページにアクセスしてください。App Service で、有効期限付きのスターター モバイル アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## <a name="create-new-service"> </a>新しいモバイル アプリ バックエンドを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 新しい iOS アプリを作成する

モバイル バックエンドを作成したら、Azure プレビュー ポータルの簡単なクイック スタートに従って、新しいアプリを作成するか、既存のアプリを変更してモバイル アプリ バックエンドに接続することができます。

1. Azure ポータルで、**[モバイル アプリ]**、作成したモバイル アプリ バックエンドの順にクリックします。

2. ブレードの上部にある、[クライアントの追加] をクリックし、[iOS] を展開します。

	![][6]

	これにより、モバイル アプリ バックエンドに接続された iOS アプリを作成する手順が表示されます。

3. まだインストールしていない場合は、<a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。

4. [Xcode] v4.4 以降および [Xamarin Studio] をダウンロードしてインストールします。また、Xamarin for Visual Studio を使用することもできます。

5. **[サービスのダウンロードとクラウドへの発行]** で **[ダウンロード]** をクリックします。

 これにより、モバイル アプリ バックエンドと、モバイル アプリ バックエンドに接続されている _To do list_ サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

6. 発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル アプリをテストする

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## モバイル アプリを発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## iOS アプリを実行する

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 

<!---HONumber=August15_HO6-->