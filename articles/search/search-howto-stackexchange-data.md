<properties
	pageTitle="Azure Search を使用して StackExchange のデータを検索する方法 | Microsoft Azure"
	description="Azure Search 使用して REST 検索を実行する方法について説明します。"
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="10/15/2015"
	ms.author="liamca"/>

# Azure Search を使用して StackExchange のデータを検索する方法

この記事は、[Azure Search](https://azure.microsoft.com/ja-JP/services/search/) で実行できる主なフルテキスト検索機能のいくつかに重点を置いたチュートリアルです。ここでは、Creative Commons 用に Stack Exchange が[利用可能](https://archive.org/details/stackexchange)にしたデータを、次の[属性](http://blog.stackoverflow.com/2009/06/attribution-required/)で利用します。

## 使用の開始

これらの機能の一部を強調するために、ユーザーが操作するための Azure Search インデックスを作成しました。このインデックスには、2015 年 10 月 14 日の時点での Programmer StackExchange のデータが含まれています。注: ここでは、このデータを使用して独自の Azure Search インデックスを作成する方法についても後で説明します。

まず、非常に簡単なフルテキスト検索クエリから始めましょう。この検索クエリでは、ユーザーが「azure」という単語を入力すると、Azure に関連する StackExchange の投稿が見つかります。次のリンクをクリックしてこれを試し、実際の動作を確認してください。

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

この例では、"azure" という単語を検索パラメーターとして渡すだけで、返された結果が JSON 形式で表示されます。その他のクエリの例を次に示すので、お試しください。

-	`Faceting`: ユーザーがデータセットを検索すると、データをフィルター処理できるようになります。これは、結果を確認する際に役立つ優れた方法です。これを実装するには、通常、ユーザーに表示される一連のカテゴリ (ファセット) を利用します。次に、ここで利用できるファセットの例をいくつか示します。
  -	**Tags**: 質問の多くにはタグが関連付けられているため、ユーザーは特定のカテゴリを掘り下げることができます。
  -	**Dates**: ユーザーは、特定の期間に投稿または回答された質問のみを表示することができます。
  -	**User**: 特定のユーザーの結果を表示または制限できます。この例では、"azure" を検索しますが、tagsCollection および acceptedAnswerDisplayName というユーザー名のファセット数が返されます。

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering`: ユーザーがファセットを選択した後に、別の検索を実行します。ただし、フィルターを利用して、選択したファセット値に結果を限定します。たとえば、"Azure" の検索結果を "architecture" というタグが付いた結果に限定し、viewCount の降順で並べ替えます。

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Spelling Mistakes`: [Lucene クエリ式](https://msdn.microsoft.com/library/mt589323.aspx)が新しく (プレビュー) サポートされるようになったため、結果のあいまい一致や特定のフィールドへの検索制限など、高度なクエリも実行できるようになりました。次の例では、タイトル フィールドで "visualize" という単語を検索しますが、~ はあいまい一致を示すため、"visualise" や "visualizing" などの結果も返されます。

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning`: 検索サービスによって返される結果を調整する場合は、[スコアリング プロファイル](https://msdn.microsoft.com/library/azure/dn798928.aspx)が非常に便利です。実際に、ここでは、[Lucene クエリ式](https://msdn.microsoft.com/library/mt589323.aspx)を使用して、その場で個々のフィールドと用語にスコアリングを適用することもできます。たとえば、タイトル フィールドで "visualize" または "chart" という単語を検索したいが、その中でも "chart" という単語が含まれる項目により重みを付ける場合は、次のようになります。

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  このデータセットには、ユーザーに関連した結果を増やすために使用できるフィールドがほかにも多数あります。たとえば、次のフィールドを使用できます。

  -	**Magnitude** は、answerCount、commentCount、favoriteCount、viewCount などの数値フィールドでスコアリングし、数が多くなった場合に検索結果が増加します。
  -	**Freshness** は、creationDate や lastActivityDate などの日時フィールドでスコアリングし、作成された項目や最近アクティブになった項目をブーストします。
  -	**フィールドの重み**は、質問の本文内で検索テキストが見つかった場合、回答で見つかった場合よりも関連性が高くなることを示します。

ほかに使用できるものを次に示します。

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx): ユーザーが検索ボックスに入力する場合、タイトル、タグ、ユーザー名などのフィールドをオートコンプリートに使用すると便利です。  

-	`Recommendations`: 推奨事項を作成して、ユーザーが閲覧したいと考えるような類似の質問を表示できるようにするのに、タイミングよくこのデータベースに既にいくつかの推奨事項が含まれる場合に、Apache Mahout や Azure Machine Learning などのツールが必要になることがよくあります。

さまざまな種類のクエリを試すには、次の JSFiddle ページを自由にお使いください。このインデックスの作成方法の詳細については、以下を参照してください。また、筆者の Twitter アカウント ([@liamca](https://twitter.com/liamca)) にもお気軽に直接ご連絡ください。

## この Azure Search インデックスの作成方法

Brent は、SQL データベースにデータをステージングする方法を示すことで、面倒な作業の多くを既に行いました。ご自身でこのプロセスを実行してデータをステージングする場合は、[こちらのチュートリアル](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/)をご確認ください。それ以外の場合は、2015 年 10 月 15 日以降に、一部のデータが事前に設定された Azure SQL データベースをご利用いただくか、ここで作成した Azure Search インデックスをお試しいただくことができます。この詳細については、ステージングされた Azure SQL データベースからのデータのインポートに関するセクションで後述します。ここでは、Brent が説明した以外に、Azure SQL データベースにビューを作成しただけです。これは、使用するテーブルのグループからデータをクロールして取り込むために、[Azure Search Indexer](https://msdn.microsoft.com/library/azure/dn946891.aspx) で使用されます。このビューがどのように定義されているかを次に示します。

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

これが完了すると、[Azure ポータル](https://portal.azure.com)を使用して、上記の Azure SQL のビューから "データをインポート" できます。これにより、このビューのフィールドのスキーマに基づいて Azure Search インデックスが作成されます。ステージングした Azure SQL データベースを使用する場合に利用できる読み取り専用の接続文字列を次に示します。

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=Oct15_HO4-->