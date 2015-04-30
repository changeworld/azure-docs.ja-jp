<properties 
	pageTitle="Xamarin Android アプリケーション用 Mobile Services の使用 - Azure Mobile Services" 
	description="次のチュートリアルに従って、Xamarin Android 開発用の Azure Mobile Services を使用します。" 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

このチュートリアルでは、Azure Mobile Services を使用して Xamarin Android アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。このチュートリアルでは、新しい Mobile Services と、新しい Mobile Services にアプリ データを保存する簡単な _To do list_ アプリの両方を作成します。作成する Mobile Services は、サーバー側ビジネス ロジックのために Visual Studio を使用してサポートされる .NET 言語を使用し、Mobile Services を管理します。JavaScript でサーバー側ビジネス ロジックを記述できる Mobile Services を作成する方法については、このトピックの「[JavaScript バックエンド バージョン]」をご覧ください。

>[AZURE.NOTE]このトピックでは、Azure 管理ポータルを使用して、新しい Mobile Services プロジェクトを作成する方法について説明します。Visual Studio 2013 更新プログラム 2 を使用して、新しい Mobile Services プロジェクトを既存の Visual Studio ソリューションに追加することもできます。詳細については、「[クイック スタート:Mobile Services の追加 (.NET バックエンド)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)」をご覧ください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、Xamarin Android アプリケーションの他のすべての Mobile Services チュートリアルの前提条件です。 

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料 Mobile Services を入手できます。このサービスは評価終了後も使用できます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started" target="_blank">Azure の無料評価版のサイト</a>をご覧ください。このチュートリアルには V<a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> が必要です。無料評価版が利用できます。

## 新しい Mobile Services を作成する

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 新しい Xamarin Android アプリケーションを作成する

Mobile Services を作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更して Mobile Services に接続できます。 

ここでは、Mobile Services 用の新しい Xamarin Android アプリケーションとサービス プロジェクトをダウンロードします。

1. 管理ポータルで、**[Mobile Services]** をクリックし、先ほど作成した Mobile Services をクリックします。
   
2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Xamarin]** を選択し、**[新しい Xamarin アプリケーションを作成する]** を展開します。

   	![][6]

   	これにより、Mobile Services に接続された Xamarin Android アプリケーションを作成するための簡単な 3 つの手順が表示されます。

  	![][7]

3. <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> をまだインストールしていない場合は、ダウンロードし、ローカル コンピューターまたは仮想マシンにインストールします。  

4. まだインストールしていない場合は、[Xamarin Studio] または Xamarin for Visual Studio をダウンロードしてインストールします。

5. **[サービスのダウンロードとクラウドへの発行]** で **[Android]** を選択し、**[ダウンロード]** をクリックします。 

  	これにより、Mobile Services と Mobile Services に接続されている _To do list_ サンプル アプリケーションの両方のプロジェクトを含むソリューションがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

6. 発行プロファイルをダウンロードしてローカル コンピューターに保存し、保存場所を書き留めておいてください。

## Mobile Services をテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Mobile Services を発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Xamarin Android アプリケーションを実行する

このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

1. Visual Studio または Xamarin Studio で、Mobile Services ソリューション内のクライアント プロジェクトに移動します。

	![][8]

	![][9]

2. **[実行]** ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。エミュレーターまたは接続されている USB デバイスを選択するよう求めるメッセージが表示されます。 

	> [AZURE.NOTE] プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。

3. アプリケーションで、意味のあるテキスト (たとえば、「チュートリアルの完了」) を入力し、プラス (**+**) アイコンをクリックします。

	![][10]

	これで、Azure でホストされている新しい Mobile Services に POST 要求が送信されます。要求のデータは TodoItems テーブルに挿入されます。テーブルに格納された項目が Mobile Services によって返され、データが一覧に表示されます。

	> [AZURE.NOTE] 
   	> Mobile Services にアクセスして ToDoActivity.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。
    
## 次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。 

* [オフライン データ同期の使用]
  <br/>クイック スタートでオフライン データの同期を使用して、アプリケーションの応答性と信頼性を高める方法について説明します。

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services .NET バックエンドのトラブルシューティング]
  <br/> Mobile Services .NET バックエンドで発生する問題を診断して解決する方法について説明します。 

<!-- Anchors. -->
[Mobile Services の使用]:#getting-started
[新しい Mobile Services を作成する]:#create-new-service
[次のステップ]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[オフライン データ同期の使用]: mobile-services-xamarin-android-get-started-offline-data.md
[認証の使用]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[プッシュ通知の使用]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript と HTML]: mobile-services-win8-javascript/
[管理ポータル]: https://manage.windowsazure.com/
[JavaScript バックエンド バージョン]: partner-xamarin-mobile-services-android-get-started.md
[Visual Studio 2012 を使用した Mobile Services でのデータの使用]: mobile-services-windows-store-dotnet-get-started-data-vs2012.md
[Mobile Services .NET バックエンドのトラブルシューティング]: mobile-services-dotnet-backend-how-to-troubleshoot.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Windows 用 Xamarin]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=52-->