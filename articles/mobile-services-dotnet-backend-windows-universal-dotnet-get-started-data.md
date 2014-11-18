<properties linkid="mobile-services-dotnet-backend-windows-store-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# モバイル サービスでのデータの使用

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure のモバイル サービスを Windows ストア アプリのバックエンド データソースとして使用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合し、アプリケーションの実行時にデータに加えられた変更を表示します。

このチュートリアルで作成するモバイル サービスは .NET バックエンド モバイル サービスです。.NET バックエンドによって、モバイル サービスにおけるサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できるようになります。さらに、ローカル コンピューターでモバイル サービスを実行し、デバックすることができます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「JavaScript バックエンド バージョン」を参照してください。

> [WACOM.NOTE] このトピックでは、Visual Studio Professional 2013 with Update 3 でツーリングを使用して、新しいモバイル サービスをユニバーサル Windows アプリに接続する方法について説明します。同じ手順を使用して、モバイル サービスを Windows ストアまたは Windows Phone ストア 8.1 アプリに接続することができます。モバイル サービスを Windows Phone 8.0 または Windows Phone Silverlight 8.1 アプリに接続するには、「[Windows Phone 向けデータの使用](/ja-JP/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)」を参照してください。

> Visual Studio Professional 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、トピックの「[このバージョン](/ja-JP/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data)」を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Windows ストア アプリ プロジェクトのダウンロード][Windows ストア アプリ プロジェクトのダウンロード]
2.  [Visual Studio からの新しいモバイル サービスの作成][Visual Studio からの新しいモバイル サービスの作成]
3.  [モバイル サービス プロジェクトをローカルにテストする][モバイル サービス プロジェクトをローカルにテストする]
4.  [モバイル サービスを使用するためのアプリケーションの更新][モバイル サービスを使用するためのアプリケーションの更新]
5.  [モバイル サービスを Azure に発行する][モバイル サービスを Azure に発行する]
6.  [Azure でホストされるサービスに対するアプリのテスト][Azure でホストされるサービスに対するアプリのテスト]
7.  [SQL データベースに格納されているデータの表示][SQL データベースに格納されているデータの表示]

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)を参照してください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 以降のバージョン)。<br/>無料評価版が利用できます。

## <a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

## <a name="create-service"></a>Visual Studio からの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>Solution Explorer で、GetStartedWithData.Shared プロジェクト フォルダーの App.xaml.cs コード ファイルを開き、Windows ストア アプリの条件付きコンパイル ブロック内の <strong>App</strong> クラスに追加された新しい静的フィールドを確認します。これは、次のように表示されます。</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
            </code></pre>

	<p>このコードは、<a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient クラス</a>のインスタンスを使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。この静的フィールドは、アプリケーションのすべてのページで使用できます。</p>
</li>
<li><p>Windows Phone アプリ プロジェクトを右クリックし、<strong>[追加]</strong>、<strong>[接続済みサービス...]</strong> をクリックし、作成されたばかりのモバイル サービスを選択してから、<strong>[OK]</strong> をクリックします。</p>
<p>同じコードは共有 App.xaml.cs ファイルにも追加されますが、今回は Windows Phone アプリの条件付きコンパイル ブロック内に追加されています。</p></li>
</ol>

この時点で、Windows ストア アプリと Windows Phone ストア アプリのどちらも新しいモバイル サービスに接続されます。次の手順は、新しいモバイル サービス プロジェクトのテストです。

## <a name="test-the-service-locally"></a>モバイル サービス プロジェクトをローカルにテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## <a name="update-app"></a>モバイル サービスを使用するためのアプリケーションの更新

このセクションでは、ユニバーサル Windows アプリを更新してモバイル サービスをアプリケーションのバックエンド サービスとして使用します。変更する必要があるのは、GetStartedWithData.Shared プロジェクト フォルダーの MainPage.xaml.cs プロジェクト ファイルだけです。

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

## <a name="publish-mobile-service"></a>モバイル サービスを Azure に発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Azure でホストされているモバイル サービスをテストする

これで、Azure でホストされているモバイル サービスに対して両方のバージョンのユニバーサル Windows アプリをテストできます。

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

## <a name="view-stored-data"></a>SQL データベースに格納されているデータの表示

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
<br/>Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]
<br/>クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

* [認証の使用][認証の使用]
<br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用][プッシュ通知の使用]
<br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]
<br/>.NET で Mobile Services を使用する方法について説明します。


  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Windows Phone 向けデータの使用]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [このバージョン]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Windows ストア アプリ プロジェクトのダウンロード]: #download-app
  [Visual Studio からの新しいモバイル サービスの作成]: #create-service
  [モバイル サービス プロジェクトをローカルにテストする]: #test-the-service-locally
  [モバイル サービスを使用するためのアプリケーションの更新]: #update-app
  [モバイル サービスを Azure に発行する]: #publish-mobile-service
  [Azure でホストされるサービスに対するアプリのテスト]: #test-azure-hosted
  [SQL データベースに格納されているデータの表示]: #view-stored-data
  [Azure の無料評価版サイト]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-windows-universal-dotnet-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-dotnet-backend-create-new-service-vs2013]: ../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md
  [MobileServiceClient クラス]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-dotnet-backend-test-local-service-api-documentation]: ../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md
  [mobile-services-windows-dotnet-update-data-app]: ../includes/mobile-services-windows-dotnet-update-data-app.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [mobile-services-dotnet-backend-view-sql-data]: ../includes/mobile-services-dotnet-backend-view-sql-data.md
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [ページングを使用したモバイル サービス クエリの改善]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
  [認証の使用]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [プッシュ通知の使用]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [モバイル サービス .NET の使用方法の概念リファレンス]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
