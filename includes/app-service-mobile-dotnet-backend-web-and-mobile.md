# モバイル クライアントと Web クライアントを使用するアプリを Azure App Service で作成する

このトピックでは、モバイル クライアントと Web クライアントの両方を使用するアプリを作成する方法を示します。モバイル アプリと Web アプリを作成し、その両方で同じ基になるデータベースを使用します。

最初に、新しいモバイル アプリのバックエンドと、その新しいモバイル アプリのバックエンドにアプリ データを保存する単純な *To do list* アプリの両方を作成します。モバイル アプリのバックエンドでは、サーバー側ビジネス ロジックでサポートされる .NET 言語を使用します。クライアント アプリでは、モバイル アプリによってサポートされるクライアント プラットフォーム (iOS、Windows、Xamarin iOS、Xamarin Android など) を使用できます。

次に、モバイル アプリと同じデータベースを使用する Web アプを作成します。チュートリアルの最後に、同じデータで動作する Web クライアントとモバイル クライアントが用意されます。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[App Service を試す](http://go.microsoft.com/fwlink/?LinkId=523751)」に移動してください。App Service で有効期間が短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 新しいモバイル アプリのバックエンドとクライアントを作成する

* チュートリアル「[モバイル アプリを作成する]」の手順に従って、モバイル アプリのバックエンドとクライアントの両方を作成します。モバイル アプリによってサポートされるクライアント プラットフォーム (iOS、Windows、Xamarin iOS、Xamarin Android など) を使用できます。

* モバイル アプリのバックエンドを Azure に展開し、モバイル クライアント アプリケーションをホストされるバックエンドに接続していることを確認します。モバイル アプリのコード プロジェクトは "エンティティ フレームワーク コード ファースト" を使用して、モバイル クライアント アプリから最初の REST 要求の後、データベースを初期化します。

## Visual Studio から、TodoList Web API を発行します。

このセクションでは、サンプルの Web アプリケーション ソリューションを使用して新しい Ｗeb アプリケーションを作成します。サンプルを、モバイル アプリと同じデータベース スキーマ名と同じ接続文字列を使用するように変更します。

    > [AZURE.NOTE] これらの手順を完了する前に、クライアントを接続することでモバイル アプリのバックエンド データベースを初期化したことを確認します。それ以外の場合、Ｗeb アプリケーションは同じデータベースに接続することはできません。

1. [Azure ポータル](https://portal.azure.com/)で、モバイル アプリと同じリソース グループとホスティング プランを使用して、新しい Web アプリを作成します。

2. サンプル ソリューション [MultiChannelToDo] をダウンロードし、Visual Studio で開きます。このソリューションには、Web クライアント UI 用の Web API プロジェクトと Web アプリケーション プロジェクトの両方が含まれています。

3. Web API プロジェクトで、MultiChannelToDoContext.cs を編集します。 `OnModelCreating` で、スキーマ名を更新してモバイル アプリ名と同じにします。

        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore

4. 次に、Azure ポータルから、モバイル アプリの接続文字列を取得します。

    - ポータルでモバイル アプリを選択し、**ユーザー コード**というラベルの付いた部分をクリックします。

    - 開いたブレードで、**[すべて設定]**、**[アプリケーション設定]** の順にクリックします。

    - **[接続文字列]** の下の **[接続文字列の表示]** をクリックします。**MS_TableConnectionString** 設定の値をコピーします。これは、モバイル アプリが SQL データベースに接続するために使用する接続文字列です。

5. Visual Studio で、Web API プロジェクトを右クリックし、**[発行]** を選択します。発行先として **[Azure Web アプリ]** を選択し、前の手順で作成した Web アプリを選択します。Web 発行ウィザードの **[設定]** セクションが表示されるまで **[次へ]** をクリックします。

6. **[データベース]** セクションで、モバイル アプリの接続文字列を **MultiChannelToDoContext** の値として貼り付けます。**[実行時にこの接続文字列を使用する]** チェックボックスのみをオンにします。

7. Web API が Azure に正常に発行されると、確認ページが表示されます。発行されたサービスの URL をコピーします。

## Visual Studio から TodoList Web クライアント UI を発行する

このセクションでは、AngularJS で実装されるサンプル Web クライアント アプリケーションを使用します。その後、Visual Studio を使用して、プロジェクトを Azure の新しいホストされる App Service Web アプリに対して発行します。 

1. Visual Studio で、プロジェクト **MultiChannelToDo.Web** を開きます。ファイル `js/service/ToDoService.js` を編集して、発行したばかりの Web API への URL を追加します。

        var apiPath = "https://your-web-api-site-name.azurewebsites.net";

2. **MultiChannelToDo.Web** プロジェクトを右クリックし、**[発行]** を選択します。

3. **[Web の発行]** ウィザードで、発行先として **[Azure Web アプリ]** を選択し、データベースを持たない新しい Web アプリを作成します。 

4. プロジェクトが正常に発行されると、ブラウザーに Web UI が表示されます。

## モバイル アプリと Web アプリのテスト 

1. Web UI で、いくつかの ToDo 項目を追加または編集します。

    ![ブラウザーでの Web アプリケーションの表示](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)

2. 「[モバイル アプリを作成する]」チュートリアルで作成したモバイル アプリを実行します。Web アプリと同じ ToDo 項目が表示されます。 

    ![Xamarin モバイル アプリの表示](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## 次のステップ

この例では、Ｗeb サイト クライアントとモバイル クライアントの両方を持つアプリで同じ基になるデータベースを使用する方法について紹介しました。ここでは、2 つのクライアント間で再利用するビジネス ロジックがバックエンドになかったため、単純に同じデータベースを共有するだけで十分でした。今後のチュートリアルで、Web API にビジネス ロジックを追加し、そのロジックをモバイル アプリのバックエンド コードで再利用する方法を学習します。

## 変更点
* Web サイトから App Service への変更ガイドについては、次を参照してください。[Azure App Service と既存の Azure サービスに与える影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更ガイドについては、次の参照してください。[プレビュー ポータルを移動するためのリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[モバイル アプリを作成する]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

<!--HONumber=49-->