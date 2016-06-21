<properties 
	pageTitle="Azure Search でのスコアリング プロファイルの使用方法 | Microsoft Azure | ホスト型クラウド検索サービス" 
	description="Microsoft Azure のホスト型のクラウド検索サービスである Azure Search のスコアリング プロファイルで検索ランキングを調整します。" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="06/04/2016" 
	ms.author="heidist"/>

# Azure Search でのスコアリング プロファイルの使用方法

スコアリング プロファイルは、検索結果一覧で項目がどう順位付けされるかに影響を及ぼす検索スコアの計算をカスタマイズする Microsoft Azure Search の機能です。スコアリング プロファイルは、定義済みの条件を満足する項目の優先順位を高めることで関連性をモデル化する方法と言えます。たとえば、オンラインのホテル予約サイト アプリケーションを考えてみます。`location` フィールドの優先順位を高めることによって、"シアトル" などの用語を含む検索では、`location` フィールドに "シアトル" を含む項目のスコアが高くなります。アプリケーションに対して複数のスコアリング プロファイルを使用することもできます。また、既定のスコアリングで十分な場合はまったく使用しないこともできます。

スコアリング プロファイルでの実験を簡単にするために、スコアリング プロファイルを使用して検索結果の順位を変更するサンプル アプリケーションをダウンロードすることができます。このサンプルは、コンソール アプリケーションであり、おそらく実際のアプリケーション開発では非現実的なものですが、学習ツールとしては便利です。

サンプル アプリケーションでは、`musicstoreindex` と呼ばれる、架空のデータを使用するスコア付けの動作を示します。サンプル アプリケーションは単純化されているため、スコアリング プロファイルおよびクエリの変更が容易で、プログラムを実行した際に、順位に対する影響を即座に確認できます。

<a id="sub-1"></a>
## 前提条件

サンプル アプリケーションは Visual Studio 2013 を使って C# で記述されています。Visual Studio のコピーをお持ちでない場合は、無料の [Visual Studio 2013 Express Edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) をお試しください。

このチュートリアルを完了するには、Azure サブスクリプションと Azure Search サービスが必要になります。サービスの設定のヘルプについては、「[ポータルでの Azure Search サービスの作成](search-create-service-portal.md)」を参照してください。

[AZURE.INCLUDE [このチュートリアルを完了するには、Azure アカウントが必要です。](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## サンプル アプリケーションのダウンロード

codeplex の [Azure Search スコアリング プロファイルのデモ](https://azuresearchscoringprofiles.codeplex.com/)に移動して、このチュートリアルで説明しているサンプル アプリケーションをダウンロードします。

[Source Code] タブで **[Download]** をクリックして、ソリューションの zip ファイルを取得してください。

 ![][12]

<a id="sub-3"></a>
## app.config の編集

1. ファイルを抽出した後で、Visual Studio でソリューションを開き構成ファイルを編集します。
1. ソリューション エクスプローラーで、**[app.config]** をダブルクリックします。このファイルは、要求を認証するためのサービス エンドポイントと `api-key` を指定します。これらの値はクラシック ポータルで取得できます。
1. [Azure ポータル](https://portal.azure.com)にサインインします。
1. Azure Search のサービス ダッシュボードに移動します。
1. **[プロパティ]** タイルをクリックして、サービスの URL をコピーします。
1. **[キー]** タイルをクリックして、`api-key` をコピーします。

app.config に URL と `api-key` を追加すると、アプリケーションの設定は次のようになります。

   ![][11]


<a id="sub-4"></a>
## アプリケーションの探索

アプリケーションの構築と実行の準備はこれでほぼ整いましたが、その前に、インデックスを作成し設定する JSON ファイルを見てみましょう。

**Schema.json** は、このデモで重視しているスコアリング プロファイルを含む、インデックスを定義します。スキーマは、インデックスで使用するすべてのフィールドを定義します。スコアリング プロファイルで使用できる `margin` など検索できないフィールドも含まれることに注意してください。スコアリング プロファイルの構文は、[Azure Search インデックスへのスコアリング プロファイルの追加](http://msdn.microsoft.com/library/azure/dn798928.aspx)に関するページに記載されています。

**Data1 3.json** は、データとして、いくつかのジャンルの 246 件のアルバムを提供します。このデータは、実際のアルバムとアーティストの情報を組み合わせたもので、検索操作を示すための `price` と `margin` のような架空のフィールドがあります。データ ファイルは、インデックスに準拠し、Azure Search サービスにアップロードされます。データがアップロードされ、インデックスが作成されると、そのデータに対するクエリを実行することができます。

**Program.cs** は、次の操作を実行します。

- コンソール ウィンドウを開きます。

- サービスの URL と `api-key` を使用して Azure Search に接続します。

- `musicstoreindex` が存在する場合は削除します。

- schema.json ファイルを使用して、新しい `musicstoreindex` を作成します。

- データ ファイルを使用して、インデックスを設定します。

- 4 つのクエリを使用してインデックスを照会します。スコアリング プロファイルがクエリ パラメーターとして指定されていることに注意してください。すべてのクエリで同じ用語 'best' を検索します。最初のクエリは、既定のスコア付けを示します。残りの 3 つのクエリでは、スコアリング プロファイルを使用します。

<a id="sub-5"></a>
## アプリケーションの構築と実行

接続またはアセンブリ参照の問題を除外するために、アプリケーションを構築、実行して、最初に対処する必要のある問題がないことを確認します。バックグラウンドで、コンソール アプリケーションが実行されます。4 つのクエリはすべて、一時停止することなく順番に実行されます。多くのシステムでは、プログラム全体が 15 秒以内で実行されます。コンソール アプリケーションに "完了しました。続行するには、Enter キーを押してください" というメッセージが表示されたら、プログラムが正常に完了しています。

クエリの実行を比較するには、コンソールからクエリの結果にコピーして Excel ファイルに貼り付けます。

次の図は、最初の 3 つのクエリの結果を並べて示しています。すべてのクエリで同じ検索用語 'best' が使用され、これは多数のアルバム タイトルに出現しています。

   ![][10]

最初のクエリは、既定のスコア付けを使用しています。検索用語がアルバム タイトルにのみ表示され、その他の条件が指定されていないため、アルバム タイトルに 'best' を含む項目が検索サービスが検出した順に返されます。

2 番目のクエリは、スコアリング プロファイルを使用していますが、プロファイルが結果に影響していないことに注意してください。結果は最初のクエリと同じです。これは、スコアリング プロファイルが、クエリに関係ないフィールド ('genre') を優先しているためです。検索用語 'best' は、どのドキュメントのどの 'genre' フィールドにも存在していません。スコアリング プロファイルが影響しない場合は、結果は既定のスコア付けと同じです。

3 番目のクエリは、スコアリング プロファイルの影響を示している最初の例です。同じセットのアルバムを対象にしているため検索用語は 'best' のままですが、スコアリング プロファイルが 'rating' と 'last-updated' を優先する条件を追加しているため、一覧では一部の項目のスコアが高くなっています。

次の図は、'margin' を優先した 4 番目と最後のクエリを示しています。'margin' フィールドは検索できないフィールドで、検索結果には返されません。'margin' の値は、マージンの高い項目が検索結果一覧の上位に表示されることを示すために、手動でスプレッドシートに追加されたものです。

   ![][9]

これでスコアリング プロファイルの実験が完了しました。さまざまなクエリ構文やスコアリング プロファイル、または大量のデータを使用するようにプログラムを変更してみてください。詳細については、次のセクションのリンクを参照してください。

<a id="next-steps"></a>
## 次のステップ

スコアリング プロファイルについて詳細に学習します。詳細については、[Azure Search インデックスへのスコアリング プロファイルの追加](http://msdn.microsoft.com/library/azure/dn798928.aspx)を参照してください。

検索構文とクエリ パラメーターについて詳細に学習します。詳細については、「[ドキュメントの検索 (Azure Search Service REST API)](http://msdn.microsoft.com/library/azure/dn798927.aspx)」を参照してください。

インデックスの作成について前に戻って詳しく学習する必要がありますか。 [このビデオ](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)で、基本を理解してください。

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG

<!---HONumber=AcomDC_0608_2016-->