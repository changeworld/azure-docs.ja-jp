<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# モバイル サービスを使用した Azure ストレージへのイメージのアップロード

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Windows Phone">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title=".NET バックエンド" class="current">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、Azure モバイル サービスを使用して、ユーザーが生成したイメージをアプリケーションが Azure ストレージにアップロードおよび保存する方法を紹介します。モバイル サービスでは、SQL データベースを使用してデータを保存します。ただし、BLOB (Binary Large Object) データは、Azure BLOB ストレージ サービスに、より効率的に保存されます。

BLOB ストレージ サービスにデータを安全にアップロードするために必要な資格情報をクライアント アプリケーションで安全に配信できません。代わりに、これらの資格情報をモバイル サービスに保存し、それらを使用して、新しいイメージをアップロードするために使用される SAS (Shared Access Signature) を生成する必要があります。SAS は有効期間が短い資格情報 (この場合は 5 分間) であり、モバイル サービスによりクライアント アプリケーションに安全に返されます。アプリケーションは、この一時的な資格情報を使用してイメージをアップロードします。この例では、BLOB サービスからのダウンロードはパブリックです。

このチュートリアルでは、モバイル サービスにより生成された SAS を使用して、写真を撮影してイメージを Azure にアップロードする機能を、モバイル サービス クイック スタート アプリケーションに追加します。このチュートリアルでは、モバイル サービス クイック スタートを更新してイメージを BLOB ストレージ サービスにアップロードする、次のような基本的な手順について説明します。

1.  [ストレージ クライアント ライブラリのインストール][ストレージ クライアント ライブラリのインストール]
2.  [イメージをキャプチャするためのクライアント アプリケーションの更新][イメージをキャプチャするためのクライアント アプリケーションの更新]
3.  [モバイル サービス プロジェクトでのストレージ クライアントのインストール][モバイル サービス プロジェクトでのストレージ クライアントのインストール]
4.  [データ モデルでの TodoItem 定義の更新][データ モデルでの TodoItem 定義の更新]
5.  [SAS を生成するテーブル コントローラーの更新][SAS を生成するテーブル コントローラーの更新]
6.  [アプリケーションをテストするイメージのアップロード][アプリケーションをテストするイメージのアップロード]

このチュートリアルには、次のものが必要です。

-   Microsoft Visual Studio 2013 以降。
-   Microsoft Visual Studio でインストールされる NuGet パッケージ マネージャー。
-   [Azure ストレージ アカウント][Azure ストレージ アカウント]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

## <a name="install-storage-client"></a>Windows Store アプリ用のストレージ クライアントのインストール

SAS を使用してアプリケーションから BLOB ストレージにイメージをアップロードできるようにするには、最初に、Windows ストア アプリ用のストレージ クライアント ライブラリをインストールする NuGet パッケージを追加する必要があります。

1.  Visual Studio の**ソリューション エクスプローラー**で、クライアント アプリケーション プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

2.  左側のウィンドウで、**[オンライン]** カテゴリ、**[プレリリースを含める]** の順に選択し、**WindowsAzure.Storage-Preview** を探します。**Azure Storage** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

    ![][0]

    これにより、Azure のストレージ サービス用のクライアント ライブラリがプロジェクトに追加されます。

次に、イメージをキャプチャおよびアップロードするクイック スタート アプリケーションを更新します。

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
 


  [ストレージ クライアント ライブラリのインストール]: #install-storage-client
  [イメージをキャプチャするためのクライアント アプリケーションの更新]: #add-select-images
  [モバイル サービス プロジェクトでのストレージ クライアントのインストール]: #storage-client-server
  [データ モデルでの TodoItem 定義の更新]: #update-data-model
  [SAS を生成するテーブル コントローラーの更新]: #update-scripts
  [アプリケーションをテストするイメージのアップロード]: #test
  [Azure ストレージ アカウント]: /ja-jp/documentation/articles/storage-create-storage-account/
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
