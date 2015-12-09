
<properties
	pageTitle="Android アプリ用 Azure Mobile Services の使用"
	description="次のチュートリアルに従って、Android 開発用の Azure Mobile Services を使用します。"
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="11/05/2015"
	ms.author="ricksal"/>


# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

このチュートリアルでは、Azure Mobile Services を使用して Android アプリにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しいモバイル サービスと、新しいモバイル サービスにアプリケーション データを保存する簡単な _To do list_ アプリケーションの両方を作成します。作成するモバイル サービスは、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、モバイル サービスを管理します。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン](mobile-services-android-get-started.md)」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![](./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png)

このチュートリアルを完了するには、Android Studio 統合開発環境と最新の Android プラットフォームを含む [Android Developer Tools][Android Studio] が必要です。このほか、Android のバージョン 4.2 以降が必要になります。

ダウンロードしたクイック スタート プロジェクトには、Android 向けの Mobile Services SDK が含まれています。

> [AZURE.IMPORTANT]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、Azure 試用版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28)を参照してください。


## <a name="create-new-service"> </a>新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## ローカル コンピューターにモバイル サービスをダウンロードする

モバイル サービスを作成したので、個人用のモバイル サービス プロジェクトをダウンロードして、ローカル コンピューターまたは仮想マシンで動かすことができます。

1. 作成したモバイル サービスをクリックし、[クイック スタート] タブの **[プラットフォームの選択]** で **[Android]** をクリックして、**[新しい Android アプリを作成する]** を展開します。

	![][1]

2. [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 以降をダウンロードしてインストールします (まだインストールしていない場合)。

3. 手順 2 の **[サービスのダウンロードとクラウドへの発行]** の下の **[ダウンロード]** をクリックします。

	これにより、モバイル サービスを実装する Visual Studio プロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## モバイル サービスをテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## モバイル サービスを発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## 新しい Android アプリを作成する

ここでは、モバイル サービスに接続された新しい Android アプリを作成します。

1. [Azure クラシック ポータル]で、**[Mobile Services]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Android]** を選択し、**[新しい Android アプリを作成する]** を展開します。

	![][2]

3. まだインストールしていない場合は、[Android Developer Tools][Android SDK] をダウンロードしてローカル コンピューターまたは仮想マシンにインストールします。

4. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。

  	これにより、モバイル サービスに接続する、_To do list_ サンプル アプリケーションのプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Android アプリを実行する

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]

## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックを参照してください。

* [アプリケーションにプッシュ通知を追加する] <br/>アプリにごく基本的なプッシュ通知を送信する方法について説明します。

* [アプリケーションに認証を追加する] <br/>アプリの特定の登録ユーザーに対して、バックエンド データへのアクセスを制限する方法について説明します。

* [Mobile Services .NET バックエンドのトラブルシューティング] <br/>Mobile Services .NET バックエンドで発生する問題を診断して解決する方法について説明します。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-AS.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-AS.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started (Eclipse)]: mobile-services-dotnet-backend-android-get-started-ec.md
[アプリケーションにプッシュ通知を追加する]: mobile-services-dotnet-backend-android-get-started-push.md
[アプリケーションに認証を追加する]: mobile-services-dotnet-backend-android-get-started-auth.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Mobile Services .NET バックエンドのトラブルシューティング]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Azure クラシック ポータル]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_1203_2015-->