<properties pageTitle="モバイル サービスを使用した BLOB ストレージへのイメージのアップロード (Windows Phone) | モバイル サービス" metaKeywords="" description="モバイル サービスを使用して、イメージを Azure BLOB ストレージにアップロードする方法について説明します。" metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="モバイル サービスを使用した Azure のストレージへのイメージのアップロード" authors="glenga" writer="glenga" />

# モバイル サービスを使用した Azure のストレージへのイメージのアップロード
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Windows Phone" class="current">Windows Phone</a>
</div>

このトピックでは、Azure のモバイル サービスを使用して、ユーザーが生成したイメージを Azure のストレージにアプリケーションでアップロードおよび保存する方法を紹介します。モバイル サービスでは、SQL データベースを使用してデータを保存します。ただし、BLOB (Binary Large Object) データは、Azure BLOB ストレージ サービスにより効率的に保存されます。

BLOB ストレージ サービスにデータを安全にアップロードするために必要な資格情報をクライアント アプリケーションで安全に配信できません。代わりに、これらの資格情報をモバイル サービスに保存し、それらを使用して、新しいイメージをアップロードするために使用される SAS (Shared Access Signature) を生成する必要があります。SAS は有効期間が短い資格情報 (この場合は 5 分間) であり、モバイル サービスによりクライアント アプリケーションに安全に返されます。アプリケーションは、この一時的な資格情報を使用してイメージをアップロードします。この例では、BLOB サービスからのダウンロードはパブリックです。

このチュートリアルでは、モバイル サービスにより生成された SAS を使用して、写真を撮影してイメージを Azure にアップロードする機能を、モバイル サービス クイック スタート アプリケーションに追加します。このチュートリアルでは、モバイル サービス クイック スタートを更新してイメージを BLOB ストレージ サービスにアップロードする、次のような基本的な手順について説明します。

1. [ストレージ クライアント ライブラリのインストール]
2. [SAS を生成する挿入スクリプトの更新]
3. [イメージをキャプチャするためのクライアント アプリケーションの更新]
4. [アプリケーションをテストするイメージのアップロード]

このチュートリアルには、次のものが必要です。

+ Microsoft Visual Studio 2012 Express for Windows 8 以降のバージョン
+ [Windows Phone SDK 8.0] 以上
+ Microsoft Visual Studio でインストールされる NuGet パッケージ マネージャー
+ [Azure ストレージ アカウント][How To Create a Storage Account]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」を完了している必要があります。

##<a name="install-storage-client"></a>Windows Store アプリ用のストレージ クライアントのインストール

SAS を使用して Blob ストレージにイメージをアップロードできるようにするには、最初に、Windows ストア アプリ用のストレージ クライアント ライブラリをインストールする NuGet パッケージを追加する必要があります。

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、**[オンライン]** カテゴリ、**[プレリリースを含める]** の順にクリックし、**WindowsAzure.Storage-Preview** を探します。**Azure ストレージ** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

  	![][2]

  	これにより、Azure のストレージ サービス用のクライアント ライブラリがプロジェクトに追加されます。

次に、イメージをキャプチャおよびアップロードするクイック スタート アプリケーションを更新します。

##<a name="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


## <a name="next-steps"> </a>次のステップ

モバイル サービスを Blob サービスと統合することによりイメージを安全にアップロードできました。他のバックエンド サービスおよび統合のトピックを参照してください。

+ [SendGrid を使用したモバイル サービスからの電子メールの送信]
 
  SendGrid 電子メール サービスを使用して、モバイル サービスに電子メール機能を追加する方法について説明します。このトピックでは、サーバー側スクリプトを追加し、SendGrid を使用して電子メールを送信する方法を示します。

+ [モバイル サービスでのバックエンド ジョブの計画]

  モバイル サービスのジョブ スケジューラ機能を使用して、定義したスケジュールに従って実行されるサーバー スクリプト コードを定義する方法について説明します。

+ [モバイル サービスのサーバー スクリプト リファレンス]

  サーバー側のタスクを実行するサーバー スクリプトの使用および他の Azure コンポーネントおよび外部リソースとの統合に関するトピックを参照してください。
 
+ [モバイル サービス .NET の使用方法の概念リファレンス]

  .NET でモバイル サービスを使用する方法について説明します
  
 
<!-- Anchors. -->
[ストレージ クライアント ライブラリのインストール]: #install-storage-client
[イメージをキャプチャするためのクライアント アプリケーションの更新]: #add-select-images
[SAS を生成する挿入スクリプトの更新]: #update-scripts
[アプリケーションをテストするイメージのアップロード]: #test
[次のステップ]:#next-steps

<!-- Images. -->


[2]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


[5]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png
[6]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-wp8.png
[7]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-wp8.png
[8]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-save-wp8.png
[9]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-final-wp8.png

[11]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png

<!-- URLs. -->
[SendGrid を使用したモバイル サービスからの電子メールの送信]: /ja-jp/develop/mobile/tutorials/send-email-with-sendgrid/
[モバイル サービスでのバックエンド ジョブの計画]: /ja-jp/develop/mobile/tutorials/schedule-backend-tasks/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started

[Azure 管理ポータル]: https://manage.windowsazure.com/
[ストレージ アカウントの作成方法]: /ja-jp/manage/services/storage/how-to-create-a-storage-account
[ストア アプリケーション用の Azure ストレージ クライアント ライブラリ]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/ja-jp/download/details.aspx?id=35471



