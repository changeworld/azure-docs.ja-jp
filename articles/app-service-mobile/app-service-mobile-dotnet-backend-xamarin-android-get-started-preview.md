<properties
	pageTitle="Xamarin Android アプリに Azure モバイル アプリを使用する - Azure モバイル アプリ"
	description="次のチュートリアルに従って、Xamarin Android 開発用の Azure モバイル アプリを使用します"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrande"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/11/2014"
	ms.author="chrande"/>

# <a name="getting-started"> </a>Xamarin Android アプリを作成する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

このチュートリアルでは、Azure モバイル アプリを使用して Xamarin Android アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しい .NET サービスと、.NET バックエンドにアプリケーション データを格納する簡単な _To do list_ アプリケーションの両方を作成します。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、Xamarin Android アプリケーションの他のすべての Azure モバイル アプリのチュートリアルの前提条件です。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)に関するページにアクセスしてください。App Service で、有効期限付きのスターター モバイル アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 新しいモバイル アプリ バックエンドを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 新しい Xamarin Android アプリケーションを作成する

モバイル アプリ バックエンドを作成すると、[Azure ポータル]の簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更してモバイル アプリ バックエンドに接続することができます。

このチュートリアルでは、モバイル アプリ用の新しい Xamarin Android アプリケーションと .NET バックエンド サービス プロジェクトをダウンロードします。

1. Azure ポータルで、**[参照]**、**[モバイル アプリ]** の順にクリックします。次に、作成したモバイル アプリをクリックします。

2. ブレードの上部にある、**[クライアントの追加]** をクリックし、 **[Xamarin.Android]** を展開します。

    ![][6]

    これにより、モバイル アプリ バックエンドに接続された Xamarin Android アプリケーションを作成するための簡単な 3 つの手順が表示されます。


3. まだインストールしていない場合は、<a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> をダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。  

4. [Xamarin Studio] をまだインストールしていない場合は、ダウンロードしてインストールします。また、Xamarin for Visual Studio を使用することもできます。

5. **[サービスのダウンロードとクラウドへの発行]** で **[ダウンロード]** をクリックします。

  これにより、モバイル アプリ バックエンド コードと、モバイル アプリ バックエンドに接続されているサンプル_To do list_ クライアント アプリケーションの両方のプロジェクトが含まれるソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

6. 発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル アプリ バックエンドをテストする

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## モバイル アプリ バックエンドを発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Xamarin Android アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. Visual Studio または Xamarin Studio で、モバイル アプリ ソリューション内のクライアント プロジェクトに移動します。

	![][8]

	![][9]

2. **[Run]** ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。エミュレーターまたは接続されている USB デバイスを選択するよう求めるメッセージが表示されます。

	> [AZURE.NOTE]プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。

3. アプリケーションで、意味のあるテキスト (たとえば、「_チュートリアルの完了_」) を入力し、プラス (**+**) アイコンをクリックします。

	![][10]

	これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

	> [AZURE.NOTE]モバイル アプリ バックエンドにアクセスして ToDoActivity.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure ポータル]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=52-->
 