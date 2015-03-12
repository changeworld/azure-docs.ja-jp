<properties 
	pageTitle="データの使用 (Windows ユニバーサル) | モバイル デベロッパー センター" 
	description="Mobile Services を使用してユニバーサル Windows アプリでデータを活用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure Mobile Services を使用してユニバーサル Windows アプリのデータを活用する方法について説明します。ユニバーサル Windows アプリ ソリューションには、Windows ストア 8.1 と Windows Phone ストア 8.1 の両方のアプリのプロジェクトと、共通の共有プロジェクトが含まれます。詳細については、「[Windows と Windows Phone を対象とするユニバーサル Windows アプリの構築](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx)」を参照してください。

このチュートリアルでは、メモリにデータを格納するユニバーサル Windows アプリの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにサインインして、アプリケーションの実行中にデータに加えられた変更を表示します。

>[AZURE.NOTE]このトピックでは、Visual Studio Professional 2013 Update 3 でツーリングを使用して、新しいモバイル サービスをユニバーサル Windows アプリに接続する方法について説明します。同じ手順を使用して、モバイル サービスを Windows ストアまたは Windows Phone ストア 8.1 アプリに接続することができます。モバイル サービスを Windows Phone 8.0 または Windows Phone Silverlight 8.1 アプリに接続するには、[Windows Phone 向けデータの使用]に関するトピック (/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data) を参照してください。

> Visual Studio Professional 2013 Update 3 にアップグレードできない場合や、モバイル サービス プロジェクトを手動で Windows ストア アプリ ソリューションに追加する場合は、[このバージョン]のトピック (/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data) を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows ストア アプリ プロジェクトのダウンロード][Windows ストア アプリの取得]
2. [Visual Studio からのモバイル サービスの作成]
3. [ストレージへのデータ テーブルの追加]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスに対するアプリケーションのテスト]
6. [Azure の管理ポータルでのアップロード済みデータの表示]

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F)を参照してください。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 for Windows</a> (Update 2 以降のバージョン)。


##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Visual Studio からの新しいモバイル サービスの作成

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8"><li><p>ソリューション エクスプローラーで、GetStartedWithData.Shared プロジェクト フォルダーの App.xaml.cs コード ファイルを開き、Windows ストア アプリの条件付きコンパイル ブロック内の <strong>App</strong> クラスに追加された新しい静的フィールドを確認します。これは、次のように表示されます。</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>このコードは、<a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient class</a> クラスのインスタンスを使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。この静的フィールドは、アプリケーションのすべてのページで使用できます。</p>
</li>
<li><p>Windows Phone アプリ プロジェクトを右クリックし、<strong>[追加]</strong>、<strong>[接続済みサービス...]</strong> をクリックし、作成されたばかりのモバイル サービスを選択してから、<strong>[OK]</strong> をクリックします。 </p>
<p>同じコードは共有 App.xaml.cs ファイルにも追加されますが、今回は Windows Phone アプリの条件付きコンパイル ブロック内に追加されています。</p></li>
</ol>

この時点で、Windows ストア アプリと Windows Phone ストア アプリのどちらも新しいモバイル サービスに接続されます。次の手順は、モバイル サービスで新しい TodoItem テーブルを作成することです。

##<a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Id、__createdAt、__updatedAt、および __version の列を含む新しいテーブルが作成されます。動的スキーマが有効な場合、挿入または更新の要求に含まれる JSON オブジェクトに基づいて、モバイル サービスによって自動的に新しい列が生成されます。詳細については、[動的スキーマ](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx) を参照してください。

#<a name="update-app"></a>モバイル サービスを使用するためのアプリケーションの更新

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Azure でホストされるモバイル サービスのテスト

これで、Azure でホストされているモバイル サービスに対して両方のバージョンのユニバーサル Windows アプリをテストできます。

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p><a href="https://manage.windowsazure.com/" target="_blank">管理ポータル</a>で、<strong>[モバイル サービス]</strong> をクリックし、対象のモバイル サービスをクリックします。<p></li>
<li><p><strong>[データ]</strong> タブをクリックし、<strong>[参照]</strong> をクリックします。</p>
<p><strong>TodoItem</strong> テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ユニバーサル Windows アプリで Mobile Services のデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

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
  <br/>.NET でモバイル サービスを使用する方法について説明します
  
<!-- Anchors. -->

[Windows ストア アプリの入手]: #download-app
[Visual Studio からのモバイル サービスの作成]: #create-service
[ストレージへのデータ テーブルの追加]: #add-table
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[Azure の管理ポータルでのアップロード済みデータの表示]: #view-data
[次のステップ]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[スクリプトを使用したデータの検証および変更]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
[ページングを使用したクエリの改善]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

[Azure の管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?LinkID=510826

[MobileServiceClient クラス]: http://go.microsoft.com/fwlink/p/?LinkId=302030


<!--HONumber=42-->
