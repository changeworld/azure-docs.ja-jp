<properties 
	pageTitle="データの使用 (Android) | モバイル デベロッパー センター" 
	description="モバイル サービスを使用して Android アプリでデータを活用する方法について説明します。" 
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
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="ricksal"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data-EC.md)]

##概要

このトピックでは、Azure Mobile Services を Android アプリのバックエンド データソースとして使用する方法について説明します。このチュートリアルでは、新しいモバイル サービスの作成、メモリにデータを格納するアプリケーション用の Eclipse Android のダウンロード、モバイル サービスとアプリケーションとの統合、アプリケーションの実行時にデータに加えられた変更の表示について説明します。

このチュートリアルで作成するモバイル サービスは、Mobile Services の .NET ランタイムをサポートします。これによって、モバイル サービスのサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン](mobile-services-android-get-started-data-EC.md)」を参照してください。

このチュートリアルを完了するには、以下が必要です。

+ <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 以降のバージョン)。 

+ Azure アカウント。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-android-get-started-data-EC%2F)を参照してください。

##<a name="create-service"></a>新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]


##<a name="download-the-service"></a>ローカル コンピューターにサービスをダウンロードする

[AZURE.INCLUDE [mobile-services-download-service-locally](../../includes/mobile-services-download-service-locally.md)]

##<a name="test-the-service"></a>モバイル サービスをテストする

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-the-service"></a>モバイル サービスを Azure に発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

###サンプル コードの取得

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/download-android-sample-code-EC.md)]

###Android SDK バージョンの検証

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version-EC.md)]


###サンプル コードの確認と実行

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code-EC.md)]

##<a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data-EC.md)]

##<a name="test-app"></a>発行されたモバイル サービスに対してアプリケーションをテストする

バック エンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、Android エミュレーターまたは Android フォンを使用して、モバイル サービスに対してアプリケーションをテストします。

1. **[Run]** メニューの **[Run]** をクリックして、プロジェクトを開始します。

	これにより、モバイル サービスから項目を取得するクエリを、クライアント ライブラリを使用して送信するアプリケーションが Android SDK でビルドされ、実行されます。

5. 前回と同様に、意味のあるテキストを入力し、**[Add]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

    アプリケーションを再起動して、変更内容が Azure のデータベースに保持されたことを確認できます。また、Azure の管理ポータルを使用して、データベースを確認することもできます。次の 2 つのステップでは、Azure の管理ポータルを使用してデータベースの変更を表示します。


4. Azure の管理ポータルで、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。

    ![](./media/mobile-services-dotnet-backend-android-get-started-data-EC/manage-sql-azure-database.png)

5. 管理ポータルで、クエリを実行して Windows ストア アプリによって加えられた変更を表示します。クエリは次のようになりますが、`todolist` の代わりにデータベースの名前を使用します。

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data-EC/sql-azure-query.png)

これで、Android 向けの**データの使用**に関するチュートリアルは終了です。


## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Android アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。

他のチュートリアルを 1 つ試してください。

* [認証の使用] <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services Android の使用方法の概念リファレンス](mobile-services-android-how-to-use-client-library.md) <br/>Android で Mobile Services を使用する方法について説明します。
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[認証の使用]: mobile-services-dotnet-backend-android-get-started-users.md
[プッシュ通知の使用]: mobile-services-dotnet-backend-android-get-started-push-EC.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030

<!---HONumber=July15_HO4-->