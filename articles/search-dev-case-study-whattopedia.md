<properties 
	pageTitle="Azure Search 開発者向けのケース スタディ: Microsoft Azure で WhatToPedia が情報メディア ポータルをどのように構築したか" 
	description="Microsoft Azure で Search サービスを使用して情報ポータルおよびメタ検索エンジンを構築する方法について説明します。" 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="04/13/2015" 
	ms.author="heidist"/>

<h1>Azure Search 開発者向けのケース スタディ</h1>

<font size="5">[WhatToPedia.com](http://whattopedia.com/) が Microsoft Azure で情報メディア ポータルをどのように構築したか</font>

 ![][6] &nbsp;&nbsp;&nbsp; <font size="9">大きな構想</font>


私たちの構想は、関連性の高い範囲指定検索エクスペリエンスを通して、買い物客と小売業者との接続をサポートする情報ポータルを構築することにあります。これは、旅行ポータルによって旅行者を見知らぬ土地でホテル、レストラン、エンターテイメントと結び付ける方法に似ています。

想定されているポータルは、特定の市場の小売業者データについて非常に質の高い検索エクスペリエンスを提供し、小売業者が提供する場所やその他のアメニティに基づいて買い物客が店舗を見つけるのに役立ちます。初期データセットを含む検索エンジンから開始しますが、ビジネスに関する情報を掲載する小売業者サブスクライバーからのサポートにより、時間の経過と共により高い価値が構築されます。プロモーション、新しい商品、人気のブランド、社内の専門サービスなどはすべて、サイトに価値を付加するデータの例です。小売業者がサブスクライバーとしてサインアップすると、こうしたデータが自主的に報告され、検索コーパスに統合されます。アドバタイズは、サブスクリプション モデルと共に、新しいビジネスに対して収益ストリームを提供します。

検索は、純粋なクラウド プラットフォームで主要なユーザー対話モデルとなります。スケール拡張と低コストを目的として、ポータル エクスペリエンスからソース管理に至るまで、私たちのほとんどすべての作業はオンライン サービスを介して実行されます。必要な機能を追加設定なしで提供する検索エンジンを使用して、検索エンジンを自分で構築および管理する必要なく、検索アプリケーションをすばやく作成できます。

## 構築した内容

WhatToPedia は新興の情報メディア企業です。私たちは [WhatToPedia.com](http://whattopedia.com/) を構築し、現在、2015 年 2 月 2 日を起動日として北ヨーロッパで市場試験を行っています。その顧客ベースは、主に、容易に管理および維持できる手頃な価格のオンライン状態を必要とする実在の店舗です。

私たちのタスクは、優れたオンライン検索エクスペリエンスを通して買い物客を惹きつけ、市区町村や近隣地域、ブランド、店舗名、店舗の種類に基づいて検索順位を上げることです。買い物客を惹きつけることによって波及効果が生まれ、小売業者がこのポータル サイトをサブスクライブしようとする意識が高められます。サブスクリプションは有料で、手頃な価格となっています。

 ![][7]

サブスクリプションのサインアップ後、小売業者は既存のプロファイル (購入したデータから私たちが最初に作成したもの) を引き継ぎ、プロモーション、お勧めのブランド、お知らせに関する追加データによって更新します。話されている言語、受け入れられる通貨、非課税での買い物などの社内の機能は、これらのアメニティを求めているユーザーをより惹きつけるために自主的に報告することができます。

## 自己紹介

私は Microsoft コンサルティングの Thomas Segato で、WhatToPedia の主任開発者である Jesper Boelling 氏と連携してソリューションを設計しています。

WhatToPedia は、スウェーデンで新しいポータル ビジネスの市場試験を行う新興企業です。その 60,000 の小売業者のほとんどは実在の SME (中小企業) です。ヨーロッパで買い物をする人は複数の言語を話し、複数の通貨を用いることがわかっているので、多言語の買い物客に対応するソリューションを構築しています。多言語の要件をサポートする検索エンジンが必要となり、Azure Search によって実現するに至りました。

Azure Search はプロジェクトに大きな変革をもたらしました。Azure Search が使用可能になる前は、独自の検索エンジンの構築にあたって問題に対処するために多大なエネルギーを費やしました。オンライン サービスとして Azure Search を採用することで、ソリューションの技術上および管理上の最大の問題を解消しました。つまり、より堅牢な検索エクスペリエンスを実現し、より迅速に市場に参入できました。

## 構築方法

私たちのビジョンは、クラウド サービスのみに基づく完全なインフラストラクチャを構築することでした。必要なサービス (コラボレーションと開発の両方)、オンデマンドでのスケール拡張、および手頃な価格設定を提供するプロバイダーである Microsoft が、戦略的プラットフォームとして選択されました。
 
### 高レベルのコンポーネント

サイトだけでなく、ビジネスを構築しました。作業全体をサポートするには、広範なツールとアプリケーションを要しました。開発用に Visual Studio および Visual Studio Online、ソース管理とスクラム管理用に Team Foundation Service (TFS) Online、 通信とコラボレーション用に Office 365、そして当然ながら、サイトに関連するすべての操作とストレージ用に Microsoft Azure を採用しました。Visual Studio により、IDE は Azure への直接プロビジョニングを提供し、TFS Online への統合により、さらなる生産性向上を実現しました。

次の図は、WhatToPedia インフラストラクチャで使用される高レベルのコンポーネントを示しています。

   ![][8]

### Microsoft Azure の使用方法

前の図の緑色のボックスは、WhatToPedia ソリューションが次のサービスに基づいて構築されていることを示しています。

- [Azure Search](http://azure.microsoft.com/services/search/)
- [MVC 4 を使用する Azure Websites ](http://azure.microsoft.com/services/websites/)
- [スケジュールされたタスク用の Azure Web ジョブ](app-service-web/websites-webjobs-resources.md)
- [Azure SQL データベース](http://azure.microsoft.com/services/sql-database/)
- [Azure BLOB ストレージ](http://azure.microsoft.com/services/storage/)
- [SendGrid 電子メール配信](http://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

ソリューションの中核は、データと検索です。Reseller プロバイダーからエンドユーザーへのデータ フローを次に示します。

  ![][9]

プライマリ データ ストレージは、Azure SQL データベース内のリセラーおよびアカウンティング データです。このデータは、初期データセットに加えて、時間の経過と共に追加された小売業者固有のデータで構成されています。SQL データベースから Azure Search での検索コーパスへの更新をポストするために、Azure Web ジョブを使用しています。

### プレゼンテーション層

ポータルは、MVC 4 および [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29) で実装された Azure Web サイトです。ASP.NET フォーム ベースの開発よりもはるかにわかりやすい HTML へのアプローチが提供されているので、MVC を選択しました。複数のデバイス用のアプリを作成して複数のモバイル プラットフォームを維持する必要がないよう、すべてのデバイスおよびプラットフォームをサポートするために Twitter Bootstrap が選択されました。

### 認証、アクセス許可、および機密データ

買い物客は匿名でサイトを参照します。そのため、買い物客に対するログイン要件がなく、コンシューマー データを格納することもできません。

小売業者の場合は異なります。ここでは、サイトで公開する公開プロファイル情報、課金情報、およびメディア コンテンツを格納します。サイトをサブスクライブしているすべての小売業者は、サブスクライバーのプロファイルを更新する前に、ユーザーの認証に使用されるユーザー ログインを取得します。すべてのサブスクライバー データを Azure SQL データベースおよび Azure BLOB ストレージに安全に格納します。.NET のフォーム ベースの認証に基づく認証モデルを選択しました。このアプローチはシンプルで、他のアプローチに付随するロール、UI サポート、およびその他の外部機能が不要なので選択しました。

小売業者に対してその業者に属するデータのみが表示されるように、各小売業者に対して小売業者 ID を作成しました。この ID は、小売業者固有のデータに関連するすべての読み取りおよび書き込み操作で今後使用されます。このアプローチでは、個別の小売業者にデータベースのアクセス許可を付与する必要がないことがわかりました。すべての小売業者は、単一のデータベース ロール下で、データ分離手法として小売業者 ID を用いてシステムと対話します。

私たちのビジネスはダウンストリーム効果に関するもの (ビジネスを小売業者にさらに推進し、アドバタイズとサブスクライブのインセンティブを作成する) なので、Web 経由での購入の処理は行いません。そのため、私たちのサイトにはショッピング カートがなく、セキュリティ要件が簡略化されます。

採用したもう 1 つの簡略化として、課金およびアカウントの支払業務を委託しています。顧客支払情報をサードパーティ ([SveaWebPay](http://www.sveawebpay.se/)) に直接ルーティングすることで、データ ストア内の機密情報の格納と保護に関連するリスクを軽減します。

### 検索エンジン

私たちのソリューションの中核となるのは、Azure Search サービス上に構築された検索エンジンです。最初にカスタムの検索エンジンを構築しましたが、そのプロセス中、実際にどれほど複雑で困難であるかがわかり、代替策を検討するようになりました。

私たちにとって最も重要な基本機能は、以下のとおりです。

- フィルター
- ファセット ナビゲーション
- 検索順位の引き上げ
- AJAX のページング

インターネット検索によってビデオ「[Deep Dive at TechEd Europe (TechEd Europe の詳細)](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410)」にアクセスし、Azure Search を試してみることになりました。

ビデオを見た後、その内容に基づいてプロトタイプを構築する準備を整えました。既に MVC でのデータ モデルを備えていたので、プロトタイプの作成は簡単でした。データには検索可能な語句が含まれており、データの並べ替え、ファセット、およびフィルター処理の目的の方法について既に要件に対処していたからです。

どのようにプロトタイプを作成したかを次に示します。

**Azure Search サービスの構成**

1. Azure ポータルにログインし、サブスクリプションに Search サービスを追加しました。共有バージョン (サブスクリプションは無料) を使用しました。
2. インデックスを作成します。プロトタイプについて、ポータル UI を使用して検索フィールドを定義し、スコアリング プロファイルを作成しました。このスコアリング プロファイルは、場所のデータ: 国 | 市区町村 | 住所に基づいています (スコアリング プロファイルの追加に関するページを参照)。
3. サービスの URL と管理者 API キーを構成ファイルにコピーします。このキーは、ポータルの Search サービス  ページにあり、サービスに対する認証に使用されます。
	
**検索インデクサー ジョブの開発 – Windows コンソール**

1. データベースからすべてのリセラーを読み取ります。
2. Azure Search サービス API を呼び出して、リセラーを 1 つずつアップロードします (http://msdn.microsoft.com/library/azure/dn798930.aspx) を参照)。
3. 増分インデックス用にリセラーがインデックス付けされるよう、データベースでプロパティを設定します。各プロファイルのインデックス状態 (インデックス付きかどうか) を格納する 'インデクサー' フィールドを追加することで、この処理を行いました。 

インデクサー ジョブを構築するコード スニペットに関する付録を参照してください。

**Search Web ポータルの開発 – MVC**

1. Azure Search サービスを呼び出して、検索からすべてのドキュメントを取得します (http://msdn.microsoft.com/library/azure/dn798927.aspx を参照)。
2. 検索サービスの応答から次のものを抽出します (json.net http://james.newtonking.com/json を使用)。
   - 結果
   - ファセット
   - 結果カウント
   - 検索結果、ファセット、およびカウントを表示するためのユーザー インターフェイスを開発します (これは既に備えていました)。

Azure Search からの結果の取得に使用したコードを次に示します。

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**場所による検索順位の引き上げ**

おそらく、プロトタイプで確認すべき最も重要な要件の 1 つは、クエリに場所の検索キーワードを追加することでした。ユーザーが検索クエリに市区町村名を入力した場合に、指定の市区町村にあるリセラーのランクが、説明に市区町村のキーワードを含むリセラーよりも高くなることが、私たちのポータルにとって不可欠です。この要件を満たすために、スコアリング プロファイルを使用して、市区町村フィールドのランクを他のフィールドよりも高くしました。

**多言語のサポート**

適切な言語で適切な検索結果を表示し、異なる言語で同じ結果を検索するためのオプションを提供する必要がありました。この問題の 2 つの側面は次のとおりです。

- 多言語での単語の検索
- 適切な言語での検索結果の表示

表示の問題は、ローカライズされたテキストを含む各言語のドキュメントと、その言語によるプロパティを追加することによって解決しました。ユーザーが検索語句を入力したら、`$filter` 式を使用して、ユーザーが選択した言語をフィルター処理します。

各ドキュメントには、コレクション型で構築された "cities" という非表示のプロパティがあります。このプロパティは、すべての言語で市区町村名を格納するので、ユーザーが多言語で検索することが可能になります。

###データ ストレージ

すべてのデータ (プロファイル、サブスクリプション、およびアカウンティング) は、SQL データベースに格納されます。小売業者によって提供されるイメージやビデオなどのすべてのメディア ファイルは、Azure BLOB ストレージに格納されます。個別の BLOB ストレージを使用すると、ファイルのアップロード効果が分離され、ファイルが Web サイトに混合されることはありません。したがって、ファイルを追加するたびにサイトを再構築する必要はありません。

このストレージ設計の重要な利点は、複数の開発者が 1 つの開発ストレージを共有できることです。WhatToPedia プロジェクトの要件の 1 つは、リセラーのデータ、イメージ、ビデオを含む開発環境を 15 分以内に作成できることでした。TFS Online から最新のデータを取得し、SQL スクリプトを実行して、インポート ジョブを実行することで、まったく時間をかけずに完全な環境を構築できます。この実習では、ステージング プロセスも向上させます。

###Web ジョブ

Azure Web ジョブを使用して、インデックスに対してデータを更新します。検索インデクサー ジョブを作成することで、インデックス作成部分をソリューションに非常に簡単に統合できました。インデクサー ジョブに対応するために行った唯一のコード変更として、インデックス状態を示すためにデータ モデルに `Indexed` フィールドを追加しました。新しいプロファイルが追加または更新されるたびに、`Indexed` フィールドが false に設定されます。小売業者がポータルを介して自分のプロファイル データを変更した場合も同様です。

ジョブは、`Indexed` が false に設定されたすべての行を探します。行が見つかると、ドキュメントが Azure Search にポストされてから、`Indexed` フィールドが true に設定されます。データの更新と追加は実際には Azure Search サービスによって行われるため、これらの処理を計画する必要はありませんでした。既に存在するドキュメントを追加する場合、サービスによって自動的に更新が行われます。

すべての Web ジョブは、コンソール アプリケーションとして開発されており、ZIP ファイルとして Azure Web サイトにアップロードし、展開してから、スケジュールすることができます。

ジョブは、スケジュール済みの Web タスクとして 5 分ごとに実行するようにスケジュールされます。計算によると、サービス タスクは 3 分間に約 3,000 のドキュメントをアップロードしますが、これは要件の範囲内でした。

> [AZURE.NOTE]Azure Search には、最近導入されたプロトタイプ インデクサー機能があります。この機能は、最初のリリースで使用するには間に合いませんでしたが、データ読み込み操作を自動化するためのインデクサー ジョブを使用したのと同じ問題を解決すると考えられます。


###バックアップ戦略

重大エラーから個々のトランザクションの回復まで、さまざまなシナリオから回復するための多層バックアップ戦略を設計しました。保護する資産には、3 種類データ (Web サイト、サブスクライバー データ、およびメディア ファイル) が含まれます。

まず、TFS Online で Web サイトのソース コードを保持することにより、サイトがダウンした場合は TFS から再パブリッシュすることで再構築できることがわかっています。

Azure SQL データベース内のサブスクライバー データは、最も重要な資産です。このデータは、組み込み機能を使用してバックアップします (「[Azure SQL データベースのバックアップと復元](http://msdn.microsoft.com/library/azure/jj650016.aspx)」を参照)。バックアップ スケジュールは、週 1 回のデータベースの完全バックアップ、1 日 1 回のデータベースの差分バックアップ、および 5 分ごとのトランザクション ログ バックアップです。データのサイズを考えれば、このソリューションは、直接のデータ ボリュームおよびプロジェクションされたデータ ボリュームにとって十分です。

3 つ目として、イメージおよびビデオ ファイルを Azure BLOB ストレージに格納します。このデータの最終的なバックアップ計画をまだ評価中であり、潜在的なソリューションとして Cloudberry Explorer for Azure を検討しています。ここでは、Web ジョブを使用してイメージやビデオを別の場所にコピーします。

##学習内容

既にデータがあったので、概念実証を確立するのは簡単でした。数時間のうちに、ファセットやカウンター、ページング、ランク プロファイル、および検索結果を含むプロトタイプを用意できました。検索結果が非常に正確なので、エンドユーザーに表示されるフィルターの一部を削除することにしました。

最大の驚きは、Azure Search についていかにすばやく学習でき、どれほど多くの利点を得られたかという点でした。文字通り、数時間のうちに概念実証を確立し (次の注を参照)、フロント エンド アプリケーション (および新しい Web ジョブ) で 500 行のコードを 3 行のコードに置き換えて、より望ましい結果を得ることができました。

以前、私たちのコードは、検索に標準的なページングやカウントなどの動作を実装していました。Azure Search を使用した場合に返される結果には、検索ヒット数、ファセット、ページング データ、カウントなどが含まれます。これらはすべて、私たちが必要とし、自分たちで提供する必要があったものです。また、元のソリューションにはなかったブースティングおよび組み込みファセット ナビゲーションも含まれました。

実装中の最大の課題は、プレビュー バージョンであるため、情報および共有エクスペリエンスを見つけるのが困難であった点です。全容の一端が明らかになると、REST API および JSON データ形式によって Azure Search サービスは使用がごく簡単であることがわかりました。JQuery JSON.Net などのほとんどのオープン ソース プラグインからフレームワークを直接呼び出すことがき、また、実験やデバッグを迅速に行うために Fiddler などのツールを使用できました。

> [AZURE.NOTE]これにより、データを用意するだけでなく、プロトタイプの構築担当者はテクノロジがどのように動作するかを早期に理解できました。また、生産性が向上するとともに、組み込み機能の真価を享受することができました。検索クエリの構造、ファセット ナビゲーション、フィルターなどを強化する必要がある場合は、プロトタイピングに時間がかかることが予想されます。

###検索プレゼンテーション ページでのファセットの制御

概念実証中に学習したことの 1 つは、ファセットを前もって慎重に計画することでした。大量のデータをソリューションに読み込むと、ファセットの量が膨大すぎて、ユーザーに対して表示しきれないことがわかりました。

この問題は、ファセット カウント パラメーターを制限することで解決しました。カウント パラメーターは、ユーザーに返されるファセットの数にハードリミットを課します。カウント パラメーターの説明を含むリンクは、[ここ](search-faceted-navigation.md)にあります。

###タスクのスケジュール設定用の Web ジョブ

変革をもたらしたのは、Azure Search だけではありません。Web ジョブを使用して Azure Search へのデータ読み込み操作を自動化する方法は、私たちの以前のアプローチよりもはるかに優れたものでした。以前のアプローチでは、検索インデックスを更新するためにスケジュールされたタスクと共に、Windows スケジューラを実行する専用の VM を使用する必要がありました。Web ジョブは、構成とデバッグが簡単で、当然ながら専用の VM にかかる料金よりもはるかに安価でした。

###イメージを更新するための Azure BLOB ストレージ エクスプローラー

[Azure BLOB ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/) (Codeplex で入手可能) を使用すると、サイトへのイメージおよびビデオの更新を管理するのに非常に有用であることがわかりました。メイン サイトに含まれるイメージおよびビデオを手動で更新するための開発者ツールとして使用しています。この方法はポータルに変更を配置するよりも柔軟性が高く、イメージを更新する必要が生じるたびに完全なテスト イテレーションを行う必要もなくなりました。

##まとめ

経験を共有してくださった WhatToPedia のみなさまに感謝いたします。

このケース スタディがお役に立てれば幸いです。Azure Search の使用を開始する場合は、作業の迅速化のために次のいくつかのリソースを推奨します。

- [Azure Search 専用の MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow にもタグが付けられています](http://stackoverflow.com/questions/tagged/azure-search)
- [Azure.com のドキュメント ページ](http://azure.microsoft.com/documentation/services/search/)
- [MSDN の Azure Search ドキュメント](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##付録: 検索インデクサー Web ジョブ

次のコードは、プロトタイプの構築に関するセクションで説明したインデクサーを構築します。

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!--HONumber=54-->