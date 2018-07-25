---
title: ポータルを使用した Azure Search のインデックス作成、クエリ、フィルター処理に関するチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure portal で、定義済みのサンプル データを使用して Azure Search のインデックスを生成します。 フルテキスト検索、フィルター、ファセット、あいまい検索、地理空間検索などについて確認します。
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 124963359d0b2d4050156958de195e47b9331c92
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007986"
---
# <a name="tutorial-use-built-in-tools-for-azure-search-indexing-and-queries"></a>チュートリアル: ビルトイン ツールを使用した Azure Search のインデックス作成とクエリ

Azure portal の Azure Search サービス ページには、各種のビルトイン ツールが用意されており、ごくわずかな準備で概念テストを行ったり基本操作を実体験したりすることができます。 ポータルのツールには、.NET や REST API と完全に対応しているわけではありませんが、簡単な概念実証テストであれば、ウィザードやエディターを使って手軽に行うことができます。 この概要ではコーディングはせず、興味深いクエリをすぐに作成できるように、公開されている少量のデータを使用します。 

> [!div class="checklist"]
> * 最初に、公開されているサンプル データと**データのインポート** ウィザードを使用して Azure Search インデックスを自動生成します。 
> * Azure Search に公開されたインデックスについて、そのスキーマと属性を確認します。
> * **Search エクスプローラー**を使用して、フルテキスト検索、フィルター、ファセット、あいまい検索、地理空間検索について確認します。  

ポータルのツールは、Azure Search の全機能に対応しているわけではありません。 必要な機能が不足している場合は、[Azure Search の .NET プログラミングに関するコーディングを前提とした概要記事](search-howto-dotnet-sdk.md)を参照するか、または [REST API 呼び出しを行うための Web テスト ツール](search-fiddler.md)の利用を検討してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 また、「[Azure Search Overview (Azure Search の概要)](https://channel9.msdn.com/Events/Connect/2016/138)」ビデオで、このチュートリアルの手順のデモをご覧いただけます (約 3 分目から 6 分間)。

## <a name="prerequisites"></a>前提条件

[Azure Search サービスを作成](search-create-service-portal.md)するか、または現在のサブスクリプションから既存のサービスを見つけます。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure Search サービスのサービス ダッシュボードを開きます。 サービス タイルをダッシュボードにピン留めしていない場合、次の手順でサービスを検索します。 
   
   * ジャンプ バーで、左側のナビゲーション ウィンドウの **[すべてのサービス]** をクリックします。
   * 検索ボックスに「*search*」と入力し、サブスクリプションの検索関連サービスの一覧を取得します。 **[Search サービス]** をクリックします。 サービスが一覧に表示されます。 

### <a name="check-for-space"></a>領域の確認
多くのユーザーが最初に利用するのは、無料版のサービスです。 このバージョンは、インデックス、データ ソース、インデクサーがそれぞれ 3 つに限定されています。 十分な空き領域があることを確認してから開始してください。 このチュートリアルでは、それぞれのオブジェクトを 1 つ作成します。 

> [!TIP] 
> サービス ダッシュボードのタイルには、既に存在するインデックス、インデクサー、データ ソースの数が表示されます。 インデクサーのタイルには、成功と失敗のインジケーターが表示されます。 インデクサーの数を表示するには、タイルをクリックします。 
>
> ![インデクサーとデータソースのタイル][1]
>

## <a name="create-index"></a>インデックスの作成とデータの読み込み
検索クエリは、"[*インデックス*](search-what-is-an-index.md)" を反復処理します。インデックスには、検索可能なデータやメタデータのほか、特定の検索の動作を最適化することを目的とした構造が含まれています。

このタスクをすべてポータル上で実行するために、**データのインポート** ウィザードから "[*インデクサー*](search-indexer-overview.md)" を使用してクロールできる、組み込みのサンプル データセットを使用します。 インデクサーは、サポートされている Azure データ ソースからメタデータとコンテンツを読み取ることができるソース固有のクローラーです。 インデクサーは、独立したリソースとしてコードから作成し、管理することができます。 ポータルの各種インデクサーは、**データのインポート** ウィザードを通じて公開されています。 

#### <a name="step-1-start-the-import-data-wizard"></a>手順 1: データのインポート ウィザードを開始する
1. インデックスの作成とデータ投入はどちらもウィザードで実行できます。Azure Search サービス ダッシュボードで、コマンド バーの **[データのインポート]** をクリックしてウィザードを開始してください。
   
    ![[データのインポート] コマンド][2]

2. ウィザードで **[データに接続します]** > **[サンプル]** > **[realestate-us-sample]** の順にクリックします。 このデータ ソースは、名前、種類、接続情報が事前構成されています。 作成すると、他のインポート操作で再度使用できる "既存のデータ ソース" になります。

    ![サンプル データセットの選択][9]

3. **[OK]** をクリックして、このデータセットを使用します。

#### <a name="skip-cognitive-skills"></a>コグニティブ スキルのスキップ

**[データのインポート]** には、インデックス作成に AI のアルゴリズムを追加するコグニティブ スキル手順 (省略可能) があります。 このチュートリアルでは取り上げないので、この機能はスキップして **[対象インデックスをカスタマイズします]** に進んでください。 Azure Search の新しい Cognitive Search プレビュー機能に興味がある場合は、[Cognitive Search のクイック スタート](cognitive-search-quickstart-blob.md)または[チュートリアル](cognitive-search-tutorial-blob.md)をご覧ください。

   ![コグニティブ スキル手順のスキップ][11]

#### <a name="step-2-define-the-index"></a>手順 2: インデックスを定義する
インデックスは通常、コードベースで手動で作成しますが、ウィザードでは、クロール可能な任意のデータ ソースのインデックスを生成できます。 インデックスには少なくとも、名前とフィールド コレクションが必要です。さらに、各ドキュメントを一意に識別するためのドキュメント キーとして、1 つのフィールドがマークされている必要があります。

フィールドには、データ型と属性があります。 上部に並んだチェック ボックスは、フィールドがどのように使用されるかを制御する "*インデックスの属性*" です。 

* **[取得可能]** は、そのフィールドが検索結果のリストに現れることを意味します。 このチェック ボックスをオフにすることで、フィルター式でのみ使用されているフィールドなど、検索結果から抑制するフィールドを個別に指定できます。 
* **[フィルター可能]**、**[ソート可能]**、**[ファセット可能]** は、フィルタリング、並べ替え、ファセットのナビゲーション構造にフィールドを使用できるかどうかを決定します。 
* **[検索可能]** は、フィールドがフルテキスト検索の対象となることを意味します。 文字列は検索可能です。 数値フィールドとブール型フィールドは通常、検索対象外として指定されます。 

既定では、ウィザードは一意の識別子のデータ ソースをキー フィールドの基準としてスキャンします。 文字列は、取得可能かつ検索可能です。 整数は、取得可能、フィルター可能、ソート可能、ファセット可能です。

  ![生成された realestate インデックス][3]

**[OK]** をクリックすると、インデックスが作成されます。

#### <a name="step-3-define-the-indexer"></a>手順 3: インデクサーを定義する
引き続き、**データのインポート** ウィザードで、**[インデクサー]** > **[名前]** の順にクリックし、インデクサーの名前を入力します。 

このオブジェクトによって、実行可能なプロセスが定義されます。 定期的なスケジュールを設定することもできますが、ここでは、**[OK]** をクリックした直後にインデクサーを一度だけ実行する既定のオプションを使用します。  

  ![realestate インデクサー][8]

## <a name="check-progress"></a>進行状況の確認
データのインポートを監視するには、サービス ダッシュボードに戻って下へスクロールし、**[インデクサー]** タイルをダブルクリックしてインデクサーの一覧を開きます。 先ほど作成したインデクサーが一覧に表示されます。"進行中" または "成功" を示すステータスと、インデックスが作成されたドキュメントの数も表示されます。

   ![インデクサーの進行状況のメッセージ][4]

## <a name="view-the-index"></a>インデックスの表示

サービス ダッシュボードのタイルでは、概要情報を確認するだけでなく、そこから詳しい情報にアクセスできるようになっています。 たとえば **[インデックス]** タイルには、前の手順で作成した *realestate-us-sample* インデックスを含む既存のインデックスが一覧表示されます。

ここで *[realestate-us-sample]* インデックスをクリックすると、その定義に使用されるポータル オプションが表示されます。 **[フィールドの追加/編集]** オプションを選択すると、新しいフィールドを作成して詳細に属性を設定することができます。 既存のフィールドは、Azure Search における物理的表現を含んでいるため、コード内でも編集することはできません。 既存のフィールドを根本的に変えるには、フィールドを新たに作成して、元のフィールドは削除します。 

   ![サンプル インデックスの定義][10]

その他のコンストラクト (スコアリング プロファイル、CORS オプションなど) はいつでも追加することができます。 

インデックスの設計時に何を編集できて何を編集できないかを明確に理解するために、インデックスの定義オプションをじっくり見てみましょう。 淡色表示されているオプションは、値を編集することも削除することもできないことを表します。

## <a name="query-index"></a>インデックスのクエリを実行する
この時点で検索インデックスは、ビルトインの [**Search エクスプローラー**](search-explorer.md)のクエリ ページを使って照会する準備が整っています。 このページには、任意のクエリ文字列をテストできるよう検索ボックスが備わっています。 

> [!TIP]
> 「[Azure Search Overview (Azure Search の概要)](https://channel9.msdn.com/Events/Connect/2016/138)」ビデオの 6 分 8 秒から、次の手順のデモを見ることができます。
>

1. コマンド バーの **[Search エクスプローラー]** をクリックします。

   ![[Search エクスプローラー] コマンド][5]

2. コマンド バーで **[インデックスの変更]** をクリックし、*realestate-us-sample* に切り替えます。 コマンド バーで **[API バージョンを設定]** をクリックし、使用できる REST API を確認します。 以下のクエリでは、一般公開バージョン (2017-11-11) を使います。 

   ![インデックスと API のコマンド][6]

3. 検索バーで次のクエリ文字列を入力して、**[検索]** をクリックします。

    > [!NOTE]
    > **Search エクスプローラー**は [REST API 要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)を処理する機能のみを備えています。 Search エクスプローラーは、[単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)と[完全な Lucene クエリ パーサー](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)の両方の構文に加え、[ドキュメント検索](https://docs.microsoft.com/rest/api/searchservice/search-documents)操作で使用できるすべての検索パラメーターを受け取ります。
    > 


#### <a name="example-string-searchseattle"></a>例 (文字列): `search=seattle`

+ **search** パラメーターは、フルテキスト検索用のキーワード検索の入力に使用します。この例では、ドキュメントの検索可能フィールドに *Seattle* を含むワシントン州キング郡内の項目の一覧が返されます。 

+ **Search エクスプローラー**は JSON で結果を返しますが、ドキュメント構造が高密度な場合は冗長で読みづらくなります。 これは意図的なものです。特にテスト時には、ドキュメント全体の可視性は重要なユース ケースとなります。 ユーザー エクスペリエンスを高めるためには、[検索結果を処理](search-pagination-page-layout.md)して重要な要素を抽出するコードの作成が必要になるでしょう。

+ ドキュメントは、インデックスで "取得可能" としてマークされているすべてのフィールドで構成されます。 ポータルでインデックスの属性を表示するには、**[インデックス]** タイルで *[realestate-us-sample]* をクリックします。

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>例 (パラメーター): `search=seattle&$count=true&$top=100`

+ 検索パラメーターを追加するには **&** 記号を使用します。検索パラメーターは任意の順序で指定できます。 

+  **$count=true** パラメーターは、返されたすべてのドキュメントの合計数を返します。 この値は、検索結果の最上部付近に表示されます。 **$count=true** で報告される数の変化を監視することで、フィルター クエリを確認できます。 カウントが小さいほど、指定したフィルターが効果的に作用していることを意味します。

+ **$top=100** は、全体の中から上位 100 件のドキュメントを返します。 既定では、Azure Search によって上位 50 件が返されます。 **$top** を使用して、この数を増減できます。

## <a name="filter-query"></a> クエリのフィルター処理

検索要求に対するフィルターの追加は、**$filter** パラメーターを追加するときに行います。 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>例 (フィルター): `search=seattle&$filter=beds gt 3`

+ **$filter** パラメーターは、指定した条件に一致する結果を返します。 この例では、ベッドルーム数が 3 を超えるものが返されます。 

+ フィルター構文は、OData 構文です。 詳細については、[フィルターの OData 構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)に関するページを参照してください。

## <a name="facet-query"></a> クエリのファセット

ファセット フィルターは検索要求に追加されます。 指定したファセット値に一致するドキュメントの総数を facet パラメーターを使用して取得できます。 

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>例 (範囲の縮小によるファセット): `search=*&facet=city&$top=2`

+ **search=*** は空の検索です。 空の検索は、すべてを検索します。 空のクエリを送信する理由の 1 つは、ドキュメントのすべてのセットをフィルターまたはファセットすることです。 たとえば、インデックス内のすべての都市から成るファセット ナビゲーション構造が必要な場合などです。

+  **facet** は、UI コントロールに渡せるナビゲーション構造を返します。 返されるのはカテゴリと数です。 この例では、カテゴリは都市の数に基づいています。 Azure Search には集計機能はありませんが、各カテゴリのドキュメントの数を示す `facet` により、大まかな集計を行うことができます。

+ **$top=2** では 2 件のドキュメントが返されます。つまり、`top` を使用すると、結果を減らすことも増やすこともできます。

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>例 (数値でのファセット): `search=seattle&facet=beds`**

+ このクエリは、*Seattle* に対するテキスト検索をベッドルーム数でファセットするものです。 *beds* という語は、フィールドがインデックスで取得可能、フィルター可能、ファセット可能としてマークされているのでファセットとして指定できます。また、含まれる値 (1 ～ 5 の数値) は一覧をグループ (3 ベッドルームと 4 ベッドルームの一覧) に分類するのに適しています。 

+ ファセットできるのは、フィルター可能なフィールドのみです。 結果として返されるのは、取得可能なフィールドのみです。

## <a name="highlight-query"></a> 強調表示の追加

検索結果の強調表示は、特定のフィールドで一致があった場合の、キーワードに一致したテキストの書式設定を表します。 検索対象の用語が説明に深く埋もれている場合、検索結果の強調表示を追加してその用語を見つけやすくすることができます。 

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>例 (蛍光ペン): `search=granite countertops&highlight=description`

+ この例では、書式設定された語句 *granite countertops* が説明フィールドで見つけやすくなります。

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>例 (言語分析): `search=mice&highlight=description`

+ フルテキスト検索では、セマンティクスが似ている語形を検索します。 この例では、ネズミの侵入がある家についてのキーワード検索 "mice" に対して、強調表示された "mouse" が検索結果に含まれます。 語形変化していても、言語分析により同じ単語として結果に表示できます。 

+ Azure Search では、Lucene と Microsoft の 56 個のアナライザーをサポートしています。 Azure Search で既定で使用されるのは、Lucene の標準アナライザーです。 

## <a name="fuzzy-search"></a> あいまい検索を試す

既定では、シアトル地域の "Samammish" 高原を検索しようとして「*samamish*」と入力するなど、検索語のスペルを間違うと、通常の検索では一致しません。 次の例では、検索結果が 1 件も返されません。

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>例 (検索語のスペルミス非対応): `search=samamish`

スペルミスに対応するには、あいまい検索を使用します。 あいまい検索は、full タイプの Lucene クエリ構文を使用したときに有効になります。クエリで「**queryType=full**」を設定し、さらに検索文字列に「**~**」を追加するという 2 つの指定によって、この動作が引き起こされます。 

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>例 (検索語のスペルミス対応): `search=samamish~&queryType=full`

この例では、"Sammamish" に基づく一致を含んだドキュメントが返されます。

**queryType** が指定されていない場合、既定の単純なクエリ パーサーが使用されます。 単純なクエリ パーサーの方が高速ですが、あいまい検索、正規表現、近接検索などの高度な種類のクエリが必要な場合は、完全な構文が必要になります。 

あいまい検索とワイルドカード検索は、検索の出力に影響を及ぼします。 これらのクエリ形式に対しては言語分析が実行されません。 あいまい検索とワイルドカード検索を使用する際は、あらかじめ「[Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md#stage-2-lexical-analysis)」で、字句解析の例外についてのセクションを参照してください。

完全なクエリ パーサーによって有効になるクエリのシナリオの詳細については、「[Lucene query syntax in Azure Search (Azure Search での Lucene クエリ構文)](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)」を参照してください。

## <a name="geo-search"></a> 地理空間検索を試す

地理空間検索は、座標を格納しているフィールドの [edm.GeographyPoint データ型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)によってサポートされます。 地理空間検索はフィルターの種類で、[フィルターの OData 構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)で指定します。 

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>例 (地理座標フィルター): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

この例のクエリでは、位置データのすべての結果から、(緯度と経度の座標で指定された) 特定の地点から 5 km 未満のものをフィルター処理します。 **$count** を追加すると、距離と座標のいずれかを変更した場合に返される結果の数を確認できます。 

地理空間検索は、検索アプリケーションに "近くを検索" 機能がある場合、またはマップ ナビゲーションを使用している場合に有用です。 ただし、フルテキスト検索ではありません。 ユーザーの要件に都市名または国名での検索がある場合は、座標に加えて、都市名または国名を含むフィールドを追加します。

## <a name="takeaways"></a>重要なポイント

このチュートリアルでは、Azure portal の**データ インポート** ウィザードと **Search エクスプローラー**を使用する基本的な手順を紹介しています。

データ インポート ウィザードの原動力となる[インデクサー](search-indexer-overview.md)について取り上げると共に、インデックス設計の基本的なワークフローについて説明しました。その際、[公開済みのインデックスに対して行うことができる変更](ttps://docs.microsoft.com/rest/api/searchservice/update-index)についても触れています。 

フィルターや検索結果の強調表示、あいまい検索、地理空間検索など、主要な機能を紹介する実践的な例を通じて、クエリの構文を学びました。

最後に、ご利用のサブスクリプションに作成したインデックスやインデクサー、データ ソースの情報を、ダッシュボードのタイルをクリックすることによって把握する方法についても説明しました。 後日、自分や同僚が作成したインデックスを利用する際には、ポータルから手軽にデータ ソースの定義や一連のフィールドの構成を調べることができるので、不慣れなコードに目を通す必要はありません。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスが含まれているリソース グループを削除することです。 リソース グループを削除することで、そのすべての内容を完全に削除することができます。 ポータルでは、リソース グループ名は Azure Search サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

Azure Search に関して、ツールを使ったさらに詳しい考察については、Postman や Fiddler などの REST テスト ツールの使用をご検討ください。

> [!div class="nextstepaction"]
> [Azure Search の REST API を呼び出すための Web テスト ツール](search-fiddler.md)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png