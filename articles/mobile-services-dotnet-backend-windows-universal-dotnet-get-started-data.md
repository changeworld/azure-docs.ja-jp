<properties 
	pageTitle="データの使用 (Windows ストア) | モバイル デベロッパー センター" 
	description="Mobile Services を使用して Windows ストア アプリでデータを活用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="wesmc"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure Mobile Services を Windows ストア アプリのバックエンド データ ソースとして使用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合し、アプリケーションの実行時にデータに加えられた変更を表示します。

このチュートリアルで作成するモバイル サービスは .NET バックエンド モバイル サービスです。.NET バックエンドによって、モバイル サービスにおけるサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できるようになります。さらに、ローカル コンピューターでモバイル サービスを実行し、デバックすることができます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「JavaScript バックエンド バージョン」を参照してください。

>[AZURE.NOTE]このトピックでは、Visual Studio Professional 2013 Update 3 でツーリングを使用して、新しいモバイル サービスをユニバーサル Windows アプリに接続する方法について説明します。同じ手順を使用して、モバイル サービスを Windows ストアまたは Windows Phone ストア 8.1 アプリに接続することができます。モバイル サービスを Windows Phone 8.0 アプリまたは Windows Phone Silverlight 8.1 アプリに接続するには、「[データの使用 (Windows Phone)](mobile-services-dotnet-backend-windows-phone-get-started-data.md)」を参照してください。

> Visual Studio Professional 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、トピックの[こちらのバージョン](mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md)を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows ストア アプリ プロジェクトのダウンロード]
2. [Visual Studio からの新しいモバイル サービスの作成]
3. [モバイル サービス プロジェクトのローカルでのテスト]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスを Azure に発行する]
6. [Azure でホストされるサービスに対するアプリのテスト]
7. [SQL Database に格納されているデータの表示]

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)を参照してください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 以降のバージョン)。 <br/>無料評価版が利用できます。 

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Visual Studio からの新しいモバイル サービスの作成

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>Solution Explorer で、GetStartedWithData.Shared プロジェクト フォルダーの App.xaml.cs コード ファイルを開き、Windows ストア アプリの条件付きコンパイル ブロック内の <strong>App</strong> クラスに追加された新しい静的フィールドを確認します。これは、次のように表示されます。</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>このコードは、<a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient class</a> のインスタンスを使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。この静的フィールドは、アプリケーションのすべてのページで使用できます。</p>
</li>
<li><p>Windows Phone アプリ プロジェクトを右クリックし、<strong>[追加]</strong>、<strong>[接続済みサービス...]</strong> をクリックし、作成されたばかりのモバイル サービスを選択してから、<strong>[OK]</strong> をクリックします。 </p>
<p>同じコードは共有 App.xaml.cs ファイルにも追加されますが、今回は Windows Phone アプリの条件付きコンパイル ブロック内に追加されています。</p></li>
</ol>

この時点で、Windows ストア アプリと Windows Phone ストア アプリのどちらも新しいモバイル サービスに接続されます。次の手順は、新しいモバイル サービス プロジェクトのテストです。


##<a name="test-the-service-locally"></a>モバイル サービス プロジェクトのローカルでのテスト

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##<a name="update-app"></a>モバイル サービスを使用するためのアプリケーションの更新

このセクションでは、ユニバーサル Windows アプリを更新してモバイル サービスをアプリケーションのバックエンド サービスとして使用します。変更する必要があるのは、GetStartedWithData.Shared プロジェクト フォルダーの MainPage.xaml.cs プロジェクト ファイルだけです。 

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]


##<a name="publish-mobile-service"></a>モバイル サービスを Azure に発行する

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Azure でホストされるモバイル サービスのテスト

これで、Azure でホストされているモバイル サービスに対して両方のバージョンのユニバーサル Windows アプリをテストできます。

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

##<a name="view-stored-data"></a>SQL Database に格納されているデータの表示

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ユニバーサル Windows アプリ プロジェクトで Mobile Services のデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [スクリプトを使用したデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したクエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

* [認証の使用]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services .NET の使用方法の概念リファレンス]
  <br/>.NET で Mobile Services を使用する方法について説明します。
  
<!-- Anchors. -->

[Windows ストア アプリ プロジェクトのダウンロード]: #download-app
[Visual Studio からの新しいモバイル サービスの作成]: #create-service
[モバイル サービス プロジェクトのローカルでのテスト]: #test-the-service-locally
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[ローカルでホストされているサービスに対するアプリのテスト]: #test-locally-hosted
[モバイル サービスを Azure に発行する]: #publish-mobile-service
[Azure でホストされるサービスに対するアプリのテスト]: #test-azure-hosted
[SQL Database に格納されているデータの表示]: #view-stored-data
[次のステップ]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[スクリプトを使用したデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[オフライン データ同期の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/

[Azure の管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!--HONumber=42-->
