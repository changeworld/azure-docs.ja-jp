<properties
	pageTitle="Xamarin Android アプリに Azure Mobile Apps を使用する"
	description="次のチュートリアルに従って、Xamarin Android 開発用の Azure Mobile Apps を使用します"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="normesta"
	manager="dwrede"
	editor="" />

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta" />

#Xamarin.Android アプリの作成

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]
 
##概要

このチュートリアルでは、Azure Mobile App バックエンドを使用して Xamarin.Android アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。新しい Mobile App バックエンドと、アプリのデータを Azure に格納する簡単な _Todo list_ Xamarin.Andorid アプリの両方を作成します。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

Xamarin Android アプリケーションの他のすべての Mobile Apps のチュートリアルを行う前に、このチュートリアルを完了してください。
 
##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。
 
* [Visual Studio Community 2013] 以降。Visual Studio Community 2013 をインストールする場合は、[Xamarin] を別個にインストールしてください。Visual Studio 2015 をインストールするときに、Xamarin ツールをインストールできます。
 
>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)に関するページにアクセスしてください。App Service で、有効期限付きのスターター Mobile App をすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。


## 新しい Azure Mobile App バックエンドの作成

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## サーバー プロジェクトのダウンロード

1. お使いの PC で [Azure ポータル]にアクセスします。**[すべて参照]**、**[Mobile Apps]** の順にクリックし、作成した Mobile App バックエンドをクリックします。

2. Mobile App ブレードで **[設定]** をクリックし、**[Mobile App]** の下で **[クイック スタート]**、**[Xamarin.Android]** の順にクリックします。
 
3. **[サーバー プロジェクトをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。圧縮されたプロジェクト ファイルを PC に抽出し、Visual Studio でソリューションを開きます。
 
## バックエンドのプロジェクトをローカルでテストします。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Azure へのサーバー プロジェクトの発行

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Xamarin.Android アプリのダウンロードと実行

1. **[Xamarin.Android プロジェクトをダウンロードして実行する]** の下の **[ダウンロード]** ボタンをクリックします。

  	これにより、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

	![][8]

	![][9]

2. **F5** キーを押してプロジェクトをビルドし、アプリケーションを開始します。

3. アプリケーションで、意味のあるテキスト (たとえば、「_チュートリアルの完了_」) を入力し、**[追加]** ボタンをクリックします。

	![][10]

	これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

	> [AZURE.NOTE]モバイル アプリ バックエンドにアクセスしてデータのクエリと挿入を行うコードを確認できます (ToDoActivity.cs C# ファイルにあります)。

##次のステップ

* [アプリへの認証の追加](app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)<br/> ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。


<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure ポータル]: https://azure.portal.com/
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=August15_HO8-->