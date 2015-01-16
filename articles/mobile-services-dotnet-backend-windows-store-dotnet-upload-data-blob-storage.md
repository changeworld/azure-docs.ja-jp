<properties pageTitle="Mobile Services を使用したイメージの BLOB ストレージへのアップロード (Windows ストア) | Mobile Services" metaKeywords="" description="Mobile Services を使用して画像を Azure BLOB ストレージにアップロードする方法を説明します。" metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# モバイル サービスを使用した Azure ストレージへのイメージのアップロード

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

このトピックでは、Azure Mobile Services を使用して、ユーザーが生成したイメージをアプリケーションが Azure Storage にアップロードおよび保存する方法を紹介します。モバイル サービスでは、SQL データベースを使用してデータを保存します。ただし、BLOB (Binary Large Object) データは、Azure BLOB ストレージ サービスに、より効率的に保存されます。 

Blob ストレージ サービスにデータを安全にアップロードするために必要な資格情報をクライアント アプリケーションで安全に配信できません。代わりに、これらの資格情報をモバイル サービスに保存し、それらを使用して、新しいイメージをアップロードするために使用される SAS (Shared Access Signature) を生成する必要があります。SAS は有効期間が短い資格情報 (この場合は 5 分間) であり、モバイル サービスによりクライアント アプリケーションに安全に返されます。アプリケーションは、この一時的な資格情報を使用してイメージをアップロードします。この例では、Blob サービスからのダウンロードはパブリックです。

このチュートリアルでは、モバイル サービスにより生成された SAS を使用して、写真を撮影してイメージを Azure にアップロードする機能を、モバイル サービス クイック スタート アプリケーションに追加します。このチュートリアルでは、モバイル サービス クイック スタートを更新してイメージを Blob ストレージ サービスにアップロードする、次のような基本的な手順について説明します。

1. [ストレージ クライアント ライブラリのインストール]
2. [イメージをキャプチャするためのクライアント アプリケーションの更新]
3. [モバイル サービス プロジェクトでのストレージ クライアントのインストール]
4. [データ モデルでの TodoItem 定義の更新]
5. [SAS を生成するテーブル コントローラーの更新]
6. [アプリケーションをテストするイメージのアップロード]

このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2013 以降。
+ Microsoft Visual Studio でインストールされる NuGet パッケージ マネージャー。
+ [Azure Storage アカウント][How To Create a Storage Account (ストレージ アカウントの作成方法)]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」を完了している必要があります。 

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Windows ストア アプリ用のストレージ クライアントのインストール

SAS を使用してアプリケーションから BLOB ストレージにイメージをアップロードできるようにするには、最初に、Windows ストア アプリ用のストレージ クライアント ライブラリをインストールする NuGet パッケージを追加する必要があります。

1. Visual Studio の**ソリューション エクスプローラー**で、クライアント アプリ プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、**[オンライン]** カテゴリを選択し、**[プレリリースを含める]** を選択して **WindowsAzure.Storage-Preview** を探します。**Azure Storage** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。 

  	![][2]

  	これにより、Azure のストレージ サービス用のクライアント ライブラリがプロジェクトに追加されます。

次に、イメージをキャプチャおよびアップロードするクイック スタート アプリケーションを更新します。

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
<!-- Anchors. -->
[ストレージ クライアント ライブラリのインストール]: #install-storage-client
[イメージをキャプチャするためのクライアント アプリケーションの更新]: #add-select-images
[モバイル サービス プロジェクトでのストレージ クライアントのインストール]: #storage-client-server
[データ モデルでの TodoItem 定義の更新]: #update-data-model
[SAS を生成するテーブル コントローラーの更新]: #update-scripts
[アプリケーションをテストするイメージのアップロード]: #test
[次のステップ]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[SendGrid を使用したモバイル サービスからの電子メールの送信]: /ja-jp/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[モバイル サービスでのバックエンド ジョブの計画]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started

[Azure 管理ポータル]: https://manage.windowsazure.com/
[ストレージ アカウントの作成方法]: /ja-jp/documentation/articles/storage-create-storage-account/
[ストア アプリケーション用の Azure ストレージ クライアント ライブラリ]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/ja-jp/download/details.aspx?id=35471


