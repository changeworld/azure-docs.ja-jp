<properties urlDisplayName="Get Started with Data" pageTitle="データの使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="Mobile Services を使用して Windows ストア アプリでデータを活用する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# 既存のアプリケーションへの Mobile Services の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure Mobile Services を Windows ストア アプリのバックエンド データ ソースとして使用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合し、アプリケーションの実行時にデータに加えられた変更を表示します。

このチュートリアルで作成するモバイル サービスは .NET バックエンド モバイル サービスです。.NET バックエンドによって、モバイル サービスにおけるサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できるようになります。さらに、ローカル コンピューターでモバイル サービスを実行し、デバックすることができます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「JavaScript バックエンド バージョン」を参照してください。

>[WACOM.NOTE]このトピックでは、Visual Studio Professional 2013 Update 3 でツーリングを使用して、新しいモバイル サービスをユニバーサル Windows アプリに接続する方法について説明します。Visual Studio Professional 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、トピックの「[このバージョン](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data)」を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows ストア アプリ プロジェクトのダウンロード]
2. [Visual Studio からの新しいモバイル サービスの作成]
3. [モバイル サービス プロジェクトをローカルにテストする]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスを Azure に発行する]
6. [Azure でホストされるサービスに対するアプリのテスト]
7. [SQL データベースに格納されているデータの表示]

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F)を参照してください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。無料評価版が利用できます。

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-javascript-download-project.md)]

##<a name="create-service"></a>Visual Studio からの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="8"><li><p>Solution Explorer で、<strong>services\mobileService\scripts</strong> サブフォルダーに移動し、service.js スクリプト ファイルを開き、次のような新しいグローバル変数があることを確認します。</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>このコードは、グローバル変数を使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。このスクリプトへの参照は default.html ファイルに追加されているため、この変数は、このページからも参照されるすべてのスクリプト ファイルで使用できます。</p>
</li>
<li><p>default.html プロジェクト ファイルを開き、新しい service.js スクリプト ファイルへの参照を見つけ、参照されたパスが次のようになっていることを確認します。</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>現在、Visual Studio では、誤ったフォルダー名がパスに生成されるバグがあります。</p></li>
<li><p>Windows Phone アプリ プロジェクトを右クリックし、<strong>[追加]</strong>、<strong>[接続済みサービス...]</strong> をクリックし、作成されたばかりのモバイル サービスを選択してから、<strong>[OK]</strong> をクリックします。 </p>
<p>これと同じ新しいコード ファイルが Windows Phone ストア アプリ プロジェクトに追加されます。default.html ファイルに追加された参照パスも必ず修正してください。</p></li>
</ol>

この時点で、Windows ストア アプリと Windows Phone ストア アプリのどちらも新しいモバイル サービスに接続されます。次の手順は、新しいモバイル サービス プロジェクトのテストです。

##<a name="test-the-service-locally"></a>モバイル サービス プロジェクトをローカルにテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

##<a name="update-app"></a>モバイル サービスを使用するためのアプリケーションの更新

このセクションでは、ユニバーサル Windows アプリを更新してモバイル サービスをアプリケーションのバックエンド サービスとして使用します。変更する必要があるのは、GetStartedWithData.Shared プロジェクト フォルダーの default.js プロジェクト ファイルだけです。 

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="publish-mobile-service"></a>モバイル サービスを Azure に発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="test-azure-hosted"></a>Azure でホストされているモバイル サービスをテストする

これで、Azure でホストされているモバイル サービスに対して両方のバージョンのユニバーサル Windows アプリをテストできます。

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

##<a name="view-stored-data"></a>SQL データベースに格納されているデータの表示

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトで Mobile Services のデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

* [認証の使用]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用] 
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>HTML および JavaScript で Mobile Services を使用する方法について説明します。
  
<!-- Anchors. -->

[Windows ストア アプリ プロジェクトのダウンロード]: #download-app
[Visual Studio からの新しいモバイル サービスの作成]: #create-service
[モバイル サービス プロジェクトをローカルにテストする]: #test-the-service-locally
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[ローカルでホストされるサービスに対するアプリケーションのテスト]: #test-locally-hosted
[モバイル サービスを Azure に発行する]: #publish-mobile-service
[Azure でホストされるサービスに対するアプリのテスト]: #test-azure-hosted
[SQL データベースに格納されているデータの表示]: #view-stored-data
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Validate and modify data with scripts (スクリプトを使用したデータの検証および変更)]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Refine queries with paging (ページングを使用したクエリの改善)]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-js
[Get started with Mobile Services (Mobile Services の使用)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Azure の管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference (Mobile Services .NET の使用方法の概念リファレンス)]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library/
[MobileServiceClient クラス]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  
