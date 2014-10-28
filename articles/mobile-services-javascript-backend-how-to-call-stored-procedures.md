<properties pageTitle="Calling SQL stored procedures with a JavaScript back end" metaKeywords="stored procedures, SQL, mobile devices, Azure" description="explains how to use SQL stored procedures in a mobile services JavaScript backed." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Calling SQL stored procedures with a JavaScript back end" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# JavaScript バックエンドによる SQL ストアド プロシージャの呼び出し

SQL Server のストアド プロシージャを呼び出す必要があるのは、次のような場合です。

-   モバイル サービスで、アクセスする必要のある既存のストアド プロシージャが使用されている場合
-   アプリケーションのアーキテクチャで、データベースへのアクセス用にストアド プロシージャの使用が規定されている場合
-   個人的に使用する必要がある場合

このトピックでは、次の操作方法について説明します。

-   [単純なストアド プロシージャを呼び出す][単純なストアド プロシージャを呼び出す]
-   [パラメーターを使用してストアド プロシージャを呼び出す][パラメーターを使用してストアド プロシージャを呼び出す]
-   [詳細を確認する][詳細を確認する]

この記事では、モバイル サービスで JavaScript バックエンドを使用する場合のストアド プロシージャについて説明します。

ただし、.Net バックエンドを使用してモバイル サービスを作成することもできます。.Net バックエンドは、Entity Framework に基づく、まったく異なるアーキテクチャを使用しています。詳細については、「[How the Azure Mobile Services .Net Backend works (Azure Mobile Services .Net バックエンドの動作のしくみ)][How the Azure Mobile Services .Net Backend works (Azure Mobile Services .Net バックエンドの動作のしくみ)]」を参照してください。

## 単純なストアド プロシージャを呼び出す

**"データベースでストアド プロシージャを使用しています。それらをモバイル サービスから呼び出すにはどうすればよいですか?"**

これには、[mssql オブジェクト][mssql オブジェクト]を使用します。このオブジェクトを使用すると、プロシージャを呼び出す *Transact-SQL* ("T-SQL") ステートメントを実行できます。

### Transact-SQL コードの作成とテスト

ストアド プロシージャの名前を *GetAll*、モバイル サービスの名前を **todolist** と仮定します。

1.  Azure ポータルのサイド バーで**データベース** アイコンを選択し、データベースを選択します。データベースの名前は、モバイル サービス名の末尾に "\*\*\_db\*\*" を付けたものです。

2.  [データベースのクイック スタート] ページの下部にある **[管理]** リンクをクリックします。

3.  データベースにログインします。

4.  上部のバーで **[新しいクエリ]** をクリックし、次のコードを追加します。

            EXEC todolist.GetAll

5.  **[実行]** をクリックし、結果を確認します。

プロシージャ名の先頭にデータベース スキーマの名前が付加されていることに注意してください。これは既定ではモバイル サービスの名前です。

### mssql オブジェクトの呼び出しを配置する位置

ストアド プロシージャを直接呼び出す必要がある場合、最も柔軟な方法は、[カスタム API][カスタム API] を作成して呼び出すことです。

1.  モバイル サービスのダッシュボードで、**[API]** をクリックし、**[作成]** をクリックして、スクリプトに "***getall***" という名前を付け、ストアド プロシージャを呼び出すために次のコードを追加します。

        exports.get = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "EXEC todolist.GetAll";
            mssql.query(sql, {
                success: function(results) {                          
                        response.send(200, results); 
                }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

2.  ブラウザーのアドレス行に次の URL を入力して、スクリプトをテストします。

        https://todolist.azure-mobile.net/api/getall

または、テーブルの標準の *read*、*insert*、*update*、または *delete* サーバー スクリプト内に同様なコードを配置することもできます。それによって、コードは既定の動作をバイパスし、ストアド プロシージャを呼び出します。同様なコードを *Scheduler* スクリプト内に配置することもできます。ただし、カスタム API を作成するのが最も柔軟な方法です。

### クライアントからのコードの呼び出し

クライアント コードを変更して、**MobileServiceClient** オブジェクトの **invokeApi** メソッドを呼び出すようにします。コードの厳密な構文はクライアント デバイスごとに固有であり、以下のトピックで説明されています。

-   [Windows ストア C#][Windows ストア C#]
-   [Windows ストア JavaScript][Windows ストア JavaScript]
-   [Windows Phone][Windows Phone]
-   [iOS][iOS]
-   [Android][Android]
-   [HTML][HTML]

## <a name="parameters"></a>パラメーターを使用してストアド プロシージャを呼び出す

ストアド プロシージャの名前が *ItemsByStatus* で、*Status* という名前の 1 個のパラメーターがあり、これをカスタム API から呼び出すと仮定します。

1.  前のセクションの手順に従い、Azure データベース ポータルを使用して T-SQL コードを作成し、テストします。ただし、次の T-SQL コードを使用します。

        EXEc todolist.ItemsByStatus @Status = 1

2.  **[実行]** シンボルをクリックし、結果を確認します。

3.  前の手順と同様に、ストアド プロシージャを呼び出す次のコードを使用して、**completeall** という名前の新しいカスタム API を作成します。次に示すコードでは、テストに使用した <**@Status**> パラメーターの実際の値を **"?"** で置き換えています。これには、実行時に指定されたパラメーターが代入されます。

        exports.get = function(request, response) { 
            var mssql = request.service.mssql;
            var param1 = parseInt(request.query.status);
            var sql = "EXEC todolist.ItemsByStatus @Status = ?";
            mssql.query(sql, [param1], {
                success: function(results) { 
                         response.send(200, results[0]); 
                    }, error: function(err) {
                        response.send(400, { error: err });        
                }
            })
        };

4.  ブラウザーに次の URL を入力して、API をテストします。

        https://todolist.azure-mobile.net/api/completeall?status=1

5.  前のセクションで説明したとおりに、クライアントから API を呼び出します。

### より複雑なパラメーター シグネチャ

前のセクションのストアド プロシージャでは、パラメーターが 1 個だけでした。より長いシグネチャに対して使用する構文を次に示します。

        function read(query, user, request) {
            var sql = 'EXEC MySProc @Param1 = ?, @Param2 = ?, @Param3 = ?';
            var param1 = request.parameters.first;
            var param2 = request.parameters.second;
            var param3 = request.parameters.third;
            mssql.query(sql, [param1, param2, param3], {
                success: function(results) {
                    request.respond(200, results);
                }
            });
        }

**EXEC** ステートメントでは、パラメーターが場所ではなく名前によって呼び出されていることに注意してください。これが推奨される構文であり、パラメーターの順序の変更やオプションのパラメーターの追加など、ストアド プロシージャに加えられたわずかな変更からコードが影響を受けるのを防ぐことができます。

## <a name="more"></a>詳細を確認する

このトピックでは、おおまかな内容だけを示しています。

使用されているコードの詳細については、次のトピックを参照してください。

-   [mssql オブジェクト]: モバイル サービスでストアド プロシージャを呼び出す
ために使用される一元的なオブジェクトに関する参照情報
-   [JavaScript バックエンド モバイル サービスの使用]: mssql オブジェクトに
関する資料を含む、Javascript バックエンドについてのより一般的な情報


次のようなシナリオも考えられます。

-   [How do I do more than 1 read operation by scripting (スクリプトで複数の読み取り操作を実行する方法)][How do I do more than 1 read operation by scripting (スクリプトで複数の読み取り操作を実行する方法)]: 条件に従ってテーブルを読み取る方法、またはストアド プロシージャを呼び出す方法について説明しています。このブログの著者である Carlos Figueira 氏は、Azure Mobile Services やデータベースに関する記事を頻繁に投稿しています。

-   [Accessing a Stored Procedure from a different Schema (異なるスキーマからストアド プロシージャにアクセスする)][Accessing a Stored Procedure from a different Schema (異なるスキーマからストアド プロシージャにアクセスする)]: 同じモバイル サービス データベースの異なるスキーマに含まれるストアド プロシージャにアクセスする際に発生する可能性のある問題について、解決策を説明しています。

また、Azure ポータルを使用して新しいストアド プロシージャを管理したり作成したりすることもできます。

<!-- Anchors. --> 
<!-- URLs. -->

  [単純なストアド プロシージャを呼び出す]: #simple
  [パラメーターを使用してストアド プロシージャを呼び出す]: #parameters
  [詳細を確認する]: #more
  [How the Azure Mobile Services .Net Backend works (Azure Mobile Services .Net バックエンドの動作のしくみ)]: http://curah.microsoft.com/64518/how-the-azure-mobile-services-net-backend-works
  [mssql オブジェクト]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554212.aspx
  [カスタム API]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn280974.aspx
  [Windows ストア C#]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Windows ストア JavaScript]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-windows-store-javascript-call-custom-api/
  [Windows Phone]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-windows-phone-call-custom-api/
  [iOS]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-ios-call-custom-api/
  [Android]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-android-call-custom-api/
  [HTML]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-html-call-custom-api/
  [How do I do more than 1 read operation by scripting (スクリプトで複数の読み取り操作を実行する方法)]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/fccf4ae7-f43c-4c2d-8518-32e2df84a824/how-do-i-do-more-than-1-read-operation-by-scripting?forum=azuremobile
  [Accessing a Stored Procedure from a different Schema (異なるスキーマからストアド プロシージャにアクセスする)]: http://blogs.msdn.com/b/jpsanders/archive/2013/05/02/windows-azure-mobile-services-accessing-a-stored-procedure-from-a-different-schema.aspx
  [JavaScript バックエンド モバイル サービスの使用]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-how-to-use-server-scripts/
  [mssql オブジェクト]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554212.aspx
