<properties pageTitle="Code First Migrations .NET バックエンドの使用方法 (モバイル サービス)" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="1 つのモバイル サービスから複数のクライアントをサポートするための考慮事項" authors="glenga" solutions="" writer="glenga" manager="dwrede" editor="" />

# データ モデルの変更を .NET バックエンド モバイル サービスに加える方法

.NET バックエンド モバイル サービス プロジェクトで、既定の Entity Framework Code First データベース初期化子は、[DropCreateDatabaseIfModelChanges] クラスから派生しています。この初期化子は、使用中の [DbContext] によって発行されたデータ モデルに対する変更が検出されるたびに、データベースを削除して再作成するように Entity Framework に指示します。モバイル サービス プロジェクトをローカル開発している間は、この初期化子を継続して使用する必要があり、.NET バックエンドに関するチュートリアルでは、この初期化子を使用していることを想定します。ただし、データ モデルに変更を加え、データベース内で既存のデータを保持しようとする場合は、Code First Migrations を使用する必要があります。Azure に対してデータ モデルの変更を発行する場合は、SQL Database を削除できないため、この場合も Code First Migrations を使用する方法が適切なソリューションになります。

このトピックでは Code First Migrations を使用して、既存のデータを失うことなく、既存の SQL データベースにデータ モデルの変更を加える方法を示します。この手順では、モバイル サービス プロジェクトを既に Azure に発行したこと、データベース内に既存のデータがあること、およびリモートとローカルのデータ モデルが同期状態にあることを想定します。

>[WACOM.NOTE]Azure にデータ モデル発行する前に、ローカル コンピューターでデータ モデルの開発をできるだけ多く完了させておくことをお勧めします。.NET バックエンド モバイル サービス プロジェクトを既に Azure に発行し、SQL データベースのテーブル スキーマが、プロジェクトの現在のデータ モデルと一致していない場合は、テーブルを削除するか、それ以外の場合は Code First Migrations を使用して発行を試みる前にスキーマとデータ モデルを手動で同期させる必要があります。

.NET バックエンド モバイル サービス プロジェクトをローカル コンピューター上で開発している場合に、データ モデルの変更に対処する最も簡単な方法は、既定の初期化子の使用を継続することです。この初期化子は、データ モデルの変更が検出されるたびに、データベースを削除して再作成します。Azure に対してプロジェクトを再発行するときは、これと同じアプローチは機能しません。ランタイムには、Azure 内の SQL データベースを削除するアクセス許可がありません。この仕様は適切ですが、その結果、初期化子は削除に失敗します。

>[WACOM.NOTE]Azure ライブ サービスに対してモバイル サービス プロジェクトを開発し、テストをする場合は、必ずテスト専用のモバイル サービス インスタンスを使用する必要があります。現時点で運用されているモバイル サービスや、クライアント アプリケーションによって使用されているモバイル サービスに対して、開発またはテストを決して実施しないでください。

## SQL データベース内のテーブルの削除

Azure 内で SQL データベースに対して Migrations を実行する前に、モバイル サービスで使用しているデータベース スキーマの中に存在する既存のすべてのテーブルを手動で削除する必要があります。SQL データベースから既存のテーブルを削除するには、次の手順を使用します。データベース スキーマが現在のデータ モデルと既に同期している場合は、この手順をスキップして「[Migrations]」に進んでください。

1. [Azure 管理ポータル]にログインし、モバイル サービスを選択して、**[構成]** タブ、**[SQL データベース]** リンクの順にクリックします。

	![][0]

	この結果、モバイル サービスで使用しているデータベースに対応するポータル ページに移動します。

2. **[管理]** をクリックし、SQL データベース サーバーにログインします。

	![][1]

3. SQL データベース マネージャーで、**[設計]**、**[テーブル]** の順にクリックし、モバイル サービスのスキーマ内にあるテーブルを選択して、**[テーブルの削除]** をクリックしてから、**[OK]** をクリックして削除してよいことを確認します。

	![][2]
   
4. モバイル サービスのスキーマ内にあるテーブルごとに、前の手順を繰り返します。

	既存のテーブルを削除した後、SQL データベースに対して Code First Migrations を初期化できます。モバイル サービスのスキーマに属していないテーブルは、モバイル サービスには影響を及ぼさず、削除することは望ましくありません。

## <a name="migrations"></a>Code First Migrations の有効化

Code First Migrations は、実行されたときにスナップショットの手法を使用して、データベースに対してスキーマの変更を加えるコードを生成します。Migrations を使用する場合は、データ モデルに対して増分の変更を加え、データベース内で既存のデータを保持することができます。次の手順では Migrations を有効にし、プロジェクト、ローカル データベース、Azure のそれぞれに対してデータ モデルの変更を適用します。

1. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
 
2. **[ツール]** メニューで **[NuGet パッケージ マネージャー]** を展開し、**[パッケージ マネージャー コンソール]** をクリックします。

	この結果、パッケージ マネージャー コンソールが表示され、このコンソールを使用して Code First Migrations を管理することができます。

3. パッケージ マネージャー コンソールで、次のコマンドを実行します。

		PM> Enable-Migrations

	この結果、プロジェクトに対して Code First Migrations が有効になります。

4. コンソールで、次のコマンドを実行します。

		PM> Add-Migration Initial

	この結果、*Initial* という名前の新しい移行が作成されます。移行用コードは、Migrations プロジェクト フォルダーに格納されます。

5. [App_Start] フォルダーを展開し、WebApiConfig.cs プロジェクト ファイルを開き、次の **using** ステートメントを追加します。

		using System.Data.Entity.Migrations;
		using todolistService.Migrations;

	上記のコードで、_todolistService_ 文字列を、プロジェクトで使用している名前空間に置換する必要があります。ダウンロードしたクイックスタート プロジェクトの場合は、<em>mobile&#95;service&#95;name</em>Service です。
 
6. この同じコード ファイル内で、**Database.SetInitializer** メソッドの呼び出しをコメント アウトし、その後に次のコードを追加します。

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

	この結果、データベースの削除と再作成を実行する Code First database の既定の初期化子が無効になり、最新の移行を適用する明示的な要求に置換されます。この時点で、データに対する移行がまだ作成されていない場合は、データにアクセスしたときに、InvalidOperationException 内でデータ モデルに対するすべての変更が発生する結果になります。この後、サービスで Code First Migrations を使用して、データ モデルに対する変更をデータベースに移行する必要があります。

7. F5 キーを押して、ローカル コンピューターでモバイル サービス プロジェクトを開始します。
 
	この時点で、データベースは、データ モデルと同期した状態にあります。登録されたデータを指定した場合は、**[Try it out]**、**[GET tables/todoitem]**、**[Try this out]**、**[Send]** の順にクリックして、このことを確認できます。詳細については、「[移行時のデータの登録]」を参照してください。

8.   ここで、TodoItem の種類に対して新しい UserId プロパティを追加するなど、データ モデルに変更を加え、プロジェクトをリビルドし、パッケージ マネージャーで次のコマンドを実行します。

		PM> Add-Migration NewUserId
                                                               
	この結果、*NewUserId* という名前の新しい移行が作成されます。この変更を実装する新しいコード ファイルが、Migrations フォルダーに追加されます。

9.  F5 キーを押して、ローカル コンピューターでモバイル サービス プロジェクトを再起動します。

	この移行がデータベースに適用され、データベースはもう一度データ モデルに同期されます。登録されたデータを指定した場合は、**[Try it out]**、**[GET tables/todoitem]**、**[Try this out]**、**[Send]** の順にクリックして、このことを確認できます。詳細については、「[移行時のデータの登録]」を参照してください。

10. Azure に対してモバイル サービスを再発行してから、クライアント アプリケーションを実行してデータにアクセスし、データが読み込まれて何もエラーが発生していないことを確認します。

13. (省略可能) [Azure 管理ポータル]にログインし、モバイル サービスを選択して、**[構成]** タブ、**[SQL データベース]** リンクの順にクリックします。

	![][0]

	この結果、モバイル サービスのデータベースに対応する SQL データベース ページに移動します。

14. (省略可能) **[管理]** をクリックし、SQL データベース サーバーにログインしてから、**[設計]** をクリックして、Azure にスキーマの変更が加えられたことを確認します。

    ![][1] 


##<a name="seeding"></a>移行時のデータの登録

移行を実行するときに、Migrations が登録データをデータベースに追加するように設定することもできます。Configuration クラスには、登録を行うための Seed メソッドがあり、このメソッドを使用してデータの挿入または更新をオーバーライドすることができます。Migrations を有効にするときに、Configuration.cs コード ファイルが Migrations フォルダーに追加されます。以下の例では、[Seed] メソッドをオーバーライドして、データを **TodoItems** テーブルに登録する方法を示します。最新のバージョンに移行した後で、[Seed] メソッドが呼び出されます。

###新しいテーブルの登録

次のコードでは、新しいデータ行を使用して **TodoItems** テーブルへの登録を実行します。

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

###テーブル内での新しい列の登録

次のコードでは、UserId 列のみを登録します。
 		    
        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

このコードは、[AddOrUpdate] ヘルパー拡張メソッドを呼び出し、登録データを新しい UserId 列に追加します。[AddOrUpdate] を使用する場合は、重複する行は作成されません。

<!-- Anchors -->
[Migrations]: #migrations
[移行時のデータの登録]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/query/dev12.query?appId=Dev12IDEF1&l=ja-jp&k=k("System.Data.Entity.DropCreateDatabaseIfModelChanges`1");k(TargetFrameworkMoniker-.NETFramework,Version%3Dv4.5);k(DevLang-csharp)&rd=true
[Seed]: http://msdn.microsoft.com/ja-jp/library/hh829453(v=vs.113).aspx
[Azure 管理ポータル]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/ja-jp/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/ja-jp/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx

