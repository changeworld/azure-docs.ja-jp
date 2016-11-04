このトピックでは、モバイル クライアントと Web クライアントの両方を使用するアプリを作成する方法を示します。モバイル アプリと Web アプリを作成し、その両方で、同じ基礎データベースを使用します。

最初に、新しいモバイル アプリ バックエンドと、その新しいモバイル アプリ バックエンドにアプリ データを保存する単純な To do list アプリの両方を作成します。モバイル アプリのバックエンドでは、サーバー側ビジネス ロジックでサポートされる .NET 言語を使用します。クライアント アプリでは、Mobile App によってサポートされるクライアント プラットフォーム (iOS、Windows、Xamarin iOS、Xamarin Android など) を使用できます。

次に、モバイル アプリと同じデータベースを使用する Web アプリを作成します。チュートリアルが終わるときには、同じデータで動作する Web クライアントとモバイル クライアントが作成されています。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## 新しいモバイル アプリ バックエンドとクライアントを作成する
* チュートリアル「[モバイル アプリを作成する]」の手順に従って、モバイル アプリ バックエンドとクライアントの両方を作成します。Mobile Apps によってサポートされるクライアント プラットフォーム (iOS、Windows、Xamarin iOS、Xamarin Android など) を使用できます。
* モバイル アプリ バックエンドを Azure にデプロイし、モバイル クライアント アプリケーションを、ホストされているバックエンドに接続したことを確認します。モバイル アプリのコード プロジェクトは "エンティティ フレームワーク コード ファースト" を使用し、モバイル クライアント アプリから最初の REST 要求を受け取ると、データベースを初期化します。

## Visual Studio から、TodoList Web API を発行します。
このセクションでは、サンプルの Web アプリケーション ソリューションを使用して新しい Web アプリケーションを作成します。サンプルで使用するデータベース スキーマ名と接続文字列を、モバイル アプリと同じものに変更します。

> [!NOTE]
> これらの手順を行う前に、クライアントをモバイル アプリ バックエンド データベースに接続してデータベースの初期化が完了していることを確認します。完了していないと、Web アプリケーションを同じデータベースに接続することはできません。
> 
> 

1. [Azure ポータル](https://portal.azure.com/)で、モバイル アプリと同じリソース グループとホスティング プランを使用して、新しい Web アプリを作成します。
2. サンプル ソリューション [MultiChannelToDo] をダウンロードし、Visual Studio で開きます。このソリューションには、Web クライアント UI 用の Web API プロジェクトと Web アプリケーション プロジェクトの両方が含まれています。
3. Web API プロジェクトで、MultiChannelToDoContext.cs を編集します。`OnModelCreating` で、スキーマ名を更新してモバイル アプリ名と同じにします。
   
        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore
4. 次に、Azure ポータルから、モバイル アプリの接続文字列を取得します。
   
   * ポータルでモバイル アプリを選択し、**User Code** というラベルの付いた部分をクリックします。
   * 開いたブレードで、**[すべての設定]**、**[アプリケーション設定]** の順にクリックします。
   * **[接続文字列]** の下の **[接続文字列の表示]** をクリックします。**MS\_TableConnectionString** 設定の値をコピーします。これは、モバイル アプリが SQL Database に接続するために使用する接続文字列です。
5. Visual Studio で、Web API プロジェクトを右クリックし、**[発行]** を選択します。発行先として **[Azure Web Apps]** を選択し、前の手順で作成した Web アプリを選択します。Web 発行ウィザードの **[設定]** セクションが表示されるまで **[次へ]** をクリックします。
6. **[データベース]** セクションで、モバイル アプリの接続文字列を **MultiChannelToDoContext** の値として貼り付けます。**[実行時にこの接続文字列を使用する]** チェックボックスのみをオンにします。
7. Web API が Azure に正常に発行されると、確認ページが表示されます。発行されたサービスの URL をコピーします。

## Visual Studio から TodoList Web クライアント UI を発行する
このセクションでは、AngularJS で実装されるサンプル Web クライアント アプリケーションを使用します。その後、Visual Studio を使用して、プロジェクトを Azure の新しいホストされる App Service Web アプリに対して発行します。

1. Visual Studio で、プロジェクト **MultiChannelToDo.Web** を開きます。`js/service/ToDoService.js` ファイルを編集して、発行したばかりの Web API への URL を追加します。
   
        var apiPath = "https://your-web-api-site-name.azurewebsites.net";
2. **MultiChannelToDo.Web** プロジェクトを右クリックし、**[発行]** を選択します。
3. **[Web の発行]** ウィザードで、発行先として **[Azure Web App]** を選択し、データベースを持たない新しい Web アプリを作成します。
4. プロジェクトが正常に発行されると、ブラウザーに Web UI が表示されます。

## モバイル アプリと Web アプリのテスト
1. Web UI で、いくつかの ToDo 項目を追加または編集します。
   
    ![ブラウザーでの Web アプリケーションの表示](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)
2. 「[モバイル アプリを作成する]」チュートリアルで作成したモバイル アプリを実行します。Web アプリと同じ ToDo 項目が表示されます。
   
    ![Xamarin モバイル アプリの表示](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 以前のポータルから新しいポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[モバイル アプリを作成する]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_1203_2015-->