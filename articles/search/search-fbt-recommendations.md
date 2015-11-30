<properties pageTitle="Azure Search の "よく一緒に購入されている商品" によるレコメンデーション | Microsoft Azure | Apache Mahout | Azure Machine Learning" description="Apache Mahout または Azure Machine Learning を使用して、よく一緒に購入されている商品、おすすめの新商品、他にも購入された商品のレコメンデーションを Azure Search アプリケーションに追加するサンプル コードです" services="search" documentationCenter="" authors="liamca" manager="pablocas" editor="" tags="machine learning"/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="11/12/2015"
   ms.author="liamca"/>

# Azure Search の "よく一緒に購入されている商品" によるレコメンデーション

オンラインで買い物をしたときに、"よく一緒に購入されている商品" リストや "この商品を購入したお客様はこのような商品も購入しています" リスト、または "おすすめの新商品" リストに気付いたことがあるでしょう。これらのリストには、購入を検討するようにその他の商品またはサービスが提示されています。通常、このデータは、多くの顧客の購入履歴に基づいてオンライン小売業者が集めたものか、単に消費者の好みに関する独自のデータになります。検索で関連する消費者向けのレコメンデーションを提示する機能は、商用アプリケーションだけでなく、マルチメディア コンテンツや情報を提供するサイトにとっても効果的です。

Azure Search アプリケーションでは、追加のコードを記述することでレコメンデーションを実装できます。このコードにより、データ内のパターンや関係を特定して、ユーザーに関連するレコメンデーションに変換するために使用されるデータとサービスが取り入れられます。

[このサンプル](https://github.com/liamca/azure-search-recommendations)では、レコメンデーション エンジンに [Apache Mahout]() を使用した方法について説明します。

この記事の残りの部分では、映画のレンタルに関するサンプル データを使用して "よく一緒にレンタルされている商品" のレコメンデーションを追加するサンプル コードについて説明します。

![1](./media/search-fbt-recommendations/product_recommendations.png)

## レコメンデーションとは

*レコメンデーション*とは、既存の検索ユーザーの行動 (Web ログなど) に基づいてカタログからより多くの商品を表示させる手法で、商品の推奨やコンバージョンの向上に使用されます。

多くの場合、レコメンデーション エンジンのトレーニングでは、過去の顧客行動を使用するか、デジタル ストアから直接データを収集します。このサンプルでは、Apache Mahout を使用してレコメンデーション データを収集します。

一般的なレコメンデーションは次のとおりです。 - よく一緒に購入されている商品: この商品を購入した顧客が他に購入した商品が示されます。 - この商品を購入したお客様はこの商品も購入しています: 自分に似た他の顧客が購入した商品が示されます。

## Azure Search インデックスの作成

- AzureSearchMovieRecommendations.sln ソリューションを開き、ImportAzureSearchIndexData を既定のプロジェクトとして設定します。  
- ImportAzureSearchIndexData 内の Program.cs ファイルを開き、お使いの Azure Search サービスを指すように SearchServiceName と SearchApiKey を変更します。
- http://grouplens.org/datasets/hetrec-2011/ から hetrec2011-movielens-2k.zip ファイルをダウンロードし、Movies.dat ファイルと user\_ratedmovies.dat ファイルを \\ImportAzureSearchIndexData\\data にコピーします。
- プロジェクトを実行し、インデックスを作成して映画データを読み込みます。 
- 最後に、アプリケーションでテスト検索を実行します。

## 映画を検索するための簡単な HTML アプリケーションの作成

Azure Search インデックスを照会できる完成済み JavaScript Web アプリケーションは、\\WebSite\\starter-template-complete にあります。

デモを最初から見る場合は、元の CSS が \\WebSite\\starter-template にあります。

\\WebSite\\starter-template-complete 内の search.js ファイルを開き、Azure Search サービスの詳細で apiKey と azureSearchService を更新します。

Chrome などのブラウザーでこのファイルを開き、検索ボックスで入力すると映画が表示されます。

## Mahout を使用したレコメンデーションの作成を実行するためのコマンド

- data\\movie\_usage.txt ファイルを Azure Blob Storage にアップロードします。 
- (リモート デスクトップを有効にして) HDInsight インスタンスを作成し、(Azure ポータルから利用できる) リモート デスクトップからコンピューターに接続します。
- HDInsight コンピューターから、"Hadoop コマンド ライン" を開きます。
- C:\\apps\\dist の Mahout bin ディレクトリに移動します。ここではこのようになりますが、C:\\apps\\dist\\mahout-1.0.0.2.3.3.0-2992\\bin でより新しいバージョンの Mahout を利用できます。
- [CONTAINER] と [STORAGEACT] を Azure Storage の詳細 (movie\_usage.txt ファイルを配置した場所) に置き換えて、次のコマンド ラインを実行します。

    mahout itemsimilarity -s SIMILARITY\_COSINE --input "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/movie\_usage.txt" --output "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/output/" --tempDir "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/temp" -m 5

これは完了するまでかなり時間がかかります。ただし、完了すると、ストレージ コンテナーには、映画のレコメンデーションを含む /movies/output/part-r-00000 ファイルが作成されます。

このファイルには、[Item ID of Movie]、[Item ID of Recommendations related to this Movie]、[Similarity Percentage] という 3 つの列があります。

## Mahout から Azure Search へのデータのインポート

Azure Search インデックスを作成したプログラムによって、`Recommendations` という、コレクション型のフィールド (コンマで区切られた、一連の文字列に似ています) も作成されます。ここでは、前の手順で作成されたデータをこの Azure Search インデックスとマージします。

- Visual Studio の AzureSearchMovieRecommendations.sln ソリューションから、MahoutOutputLoader 内の Program.cs ファイルを開きます。
- お使いの Azure Search サービスの詳細で SearchServiceName と SearchApiKey を更新します。
- Mahout の製品レコメンデーション ファイルを格納する Azure ストレージ アカウントの詳細で StorageApiKey と StorageAccountName を更新します。
- アプリケーションを実行してデータをマージします。
 
## レコメンデーションの視覚化
この時点で、Web アプリケーションに戻り、いずれかの映画をクリックしてレコメンデーションを表示できます。

このイメージをクリックしたときにレコメンデーションが返されるしくみを確認したい場合は、Search.js ファイルを開き、openMovieDetails() 関数を確認してください。

## クレジット

データは、GroupLens (http://grouplens.org/datasets/hetrec-2011/) からご提供いただきました。

このデータのライセンスの詳細については、こちらのページ (http://files.grouplens.org/datasets/hetrec2011/hetrec2011-movielens-readme.txt) を参照してください。

<!---HONumber=Nov15_HO4-->