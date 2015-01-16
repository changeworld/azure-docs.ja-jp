<properties urlDisplayName="Get Started with Data" pageTitle="データの使用 (iOS) | モバイル デベロッパー センター" metaKeywords="Azure iOS data, Azure mobile services data, " description="Mobile Services を使用して iOS アプリでデータを活用する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 既存のアプリケーションへの Mobile Services の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

このトピックでは、Azure Mobile Services を使用して iOS アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

<div class="dev-callout"><b>注</b>
<p>このチュートリアルの目的は、Mobile Services が iOS アプリのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「<a href="/ja-jp/develop/mobile/tutorials/get-started-ios">モバイル サービスの使用</a>」を完了することをお勧めします。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1. [iOS アプリ プロジェクトのダウンロード]
2. [モバイル サービスの作成]
3. [ストレージのデータ テーブルの追加]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスに対するアプリケーションのテスト]

このチュートリアルには、次のものが必要です。

+ [Mobile Services iOS SDK]、[XCode 4.5][Install Xcode]、iOS 5.0 以降のバージョン。
+ Microsoft Azure アカウント。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、iOS アプリの 1 つである [GetStartedWithData アプリ][GitHub]をベースにしています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービス iOS のクイック スタートで生成したアプリケーションと同じです。

1. GetStartedWithData [サンプル アプリ][GitHub]をダウンロードします。

2. Xcode で、ダウンロードしたプロジェクトを開き、TodoService.m ファイルを確認します。

   	8 つの **// TODO** コメントに、このアプリケーションをモバイル サービスで実行するために必要な手順が指定されている点に注目してください。

3. **[Run]** ボタン (または Command + R キー) を押してプロジェクトをリビルドし、アプリケーションを起動します。

4. アプリケーションで、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

   	![][0]  

   	保存されたテキストが下のリストに表示されます。

##<a name="create-service"></a>管理ポータルで新規モバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>新しいテーブルをモバイル サービスに追加する

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>モバイル サービスをデータ アクセスに使用するためにアプリケーションを更新する

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>新しいモバイル サービスに対するアプリケーションのテスト

1. Xcode で、iPhone または iPad にデプロイするエミュレーターを選択し、**[Run]** ボタン (または Command + R キー) を押してプロジェクトをリビルドし、アプリケーションを起動します。

   	これにより、モバイル サービスから項目にクエリを行う Azure のモバイル サービス クライアントが、iOS SDK でビルドされ、実行されます。

2. 前と同様に、テキスト ボックスにテキストを入力し、**[+]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]

   	**TodoItem** テーブルに、Mobile Services によって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、iOS 向けの**データの使用**に関するチュートリアルはおしまいです。

##<a name="next-steps"></a>次のステップ

このチュートリアルでは、iOS アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。

次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、iOS に関する他のチュートリアルを参照してください。

* [認証の使用]
	<br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>Mobile Services を使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[iOS アプリ プロジェクトのダウンロード]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージのデータ テーブルの追加]: #add-table
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-ios
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Xcode のインストール]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub のリポジトリ]: http://go.microsoft.com/fwlink/p/?LinkId=268784
