<properties 
	pageTitle="Azure Search 関連の最新の更新履歴情報 | Microsoft Azure | ホスト型クラウド検索サービス" 
	description="サービスの最新の更新履歴情報を記述する Azure Search のリリース ノート" 
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
	ms.date="02/10/2016" 
	ms.author="heidist"/>

#Azure Search 関連の最新の更新履歴情報#

Azure Search は、Microsoft Azure のホスト型クラウド検索サービスです。このサービスは一般提供が開始されており、[Search サービス REST API バージョン 2015-02-28](https://msdn.microsoft.com/library/azure/dn798935.aspx) または [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) の構成に対して、99.9% の可用性を保証するサービス レベル アグリーメント (SLA) を提供します。

##2016 年の新機能

機能|リリース日|状態|詳細
-------|--------|------|-------
[.NET SDK 1.1](https://msdn.microsoft.com/library/azure/dn951165.aspx)|2016 年 2 月|一般公開|これは、.NET クライアント ライブラリ、`Microsoft.Azure.Search.dll` の最初の一般公開リリースです。このバージョンには重大な変更があります。移行のガイドラインについては、「[Azure Search .NET SDK バージョン 1.1 へのアップグレード](search-dotnet-sdk-migration.md)」をご覧ください。
[Lucene クエリ構文サポート](https://msdn.microsoft.com/library/azure/mt589323.aspx)|2016 年 2 月|[一般公開](search-api-2015-02-28.md)|Lucene クエリ構文を、REST API と .NET SDK の両方で利用できるようになりました。Lucene 構文を有効にするには、REST API では `queryType` パラメーターを `full` に設定し、.NET SDK では `SearchParameters.QueryType` プロパティを `QueryType.Full` に設定します。
[カスタム アナライザー](https://azure.microsoft.com/blog/custom-analyzers-in-azure-search/)|2016 年 1 月|[プレビュー](search-api-2015-02-28-preview.md)|トークナイザーとトークン フィルターのユーザー定義の構成MSDN の「[Analysis in Azure Search](https://msdn.microsoft.com/library/azure/mt605304.aspx)」をご覧ください。
[Azure BLOB Storage インデクサー](search-howto-indexing-azure-blob-storage.md)|2016 年 1 月|[プレビュー](search-api-2015-02-28-preview.md)|PDF、Office ドキュメント、XML、HTML、またはビデオやオーディオ ファイルを Azure Search インデックスにマージ、またはインジェスト。
[Search エクスプローラー](search-explorer.md)|2016 年 1 月|[ポータル](https://portal.azure.com)|インデックスに対するアドホック クエリ用の組み込みのクエリ ツール。
[Azure Search 用の Power BI コンテンツ パック](http://blogs.msdn.com/b/powerbi/archive/2016/01/19/visualizing-azure-search-data-with-power-bi.aspx)|2016 年 1 月|ツール|Azure Search 用の新しい Power BI コンテンツ パックを使用した、サービス データの視覚化と分析。検索分析によって使用可能。
[検索分析](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/)|2016 年 1 月|ポータル|ユーザーの検索行動を分析するためのデータ収集を有効にします。

##2015 年の新機能

機能|リリース日|状態|詳細
-------|--------|------|-------
Lucene 言語アナライザー|2015 年 10 月|一般公開|この機能は現在一般公開されており、サービス REST API と .NET SDK で使用できます。
[Microsoft 自然言語プロセッサ](search-api-2015-02-28-Preview.md)|2015 年 10 月|一般公開|この機能は現在一般公開されており、サービス REST API と .NET SDK で使用できます。 
[Lucene クエリ構文サポート](https://msdn.microsoft.com/library/azure/mt589323.aspx)|2015 年 9 月|[プレビュー](search-api-2015-02-28-preview.md)|Lucene のクエリ アナライザーを追加。新しい構文を使用するには、ドキュメント検索操作で `queryType` を指定する必要があります。
[自然言語プロセッサ](search-language-support.md)|2015 年 9 月|[プレビュー](search-api-2015-02-28-preview.md)|Microsoft の言語プロセッサを追加。全体的な言語の数を増やし、他にも代替の実装を提供。
検索、提案、および参照クエリの POST|2015 年 9 月|[プレビュー](search-api-2015-02-28-preview.md)|サービス REST API に適用。
[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015 年 9 月|一般公開|管理 REST API の 2 番目のバージョン。次の内容が含まれます。checkNameAvailability によって、指定されたサービス名が既に使用されていないか確認します。レプリカの範囲が 1 ～ 6 から 1 ～ 12 に変更されました。SKU プロパティがプロパティ バッグからサービス ペイロードの最上位に移動しました。検索サービスの作成操作の応答本文が SKU 設定の再配置に対応するように更新されました。
.NET SDK 0.10.0-Preview|2015 年 8 月|プレビュー|これは、.NET クライアント ライブラリ Microsoft.Azure.Search.dll の、2 番目のイテレーションです。  
このバージョンでは、.NET クラスを使用して [DataSource クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)と [Indexers クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)を作成、管理、および使用できるようになりました。また、Azure SQL インデクサーの場合、地理ポイントのインデックス作成が新しくサポートされます。  

fieldMapping コンストラクト|2015 年 4 月|プレビュー|インデクサーは、fieldMapping コンストラクトをサポートするようになりました。これは実際のフィールド名が外部データベースと Azure Search インデックスとの間で異なる場合に、フィールド割り当てを提供します。`2015-02-28-preview` バージョンのインデクサーの資料については、「[Indexer Operations](search-api-indexers-2015-02-28-Preview.md)」を参照してください。
フィールドの型変換|2015 年 4 月|プレビュー|インデクサーはフィールドの型変換をサポートするようになったため、ソース フィールドが JSON 配列を表すと仮定して、SQL テーブル内の文字列フィールドを、検索インデックス内の文字列コレクション フィールドにマッピングできます。
[サービス REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015 年 3 月|一般公開|Azure Search サービス REST API の最初の一般公開バージョン。このバージョンには、以前の機能が含まれています。これには[インデクサー](http://go.microsoft.com/fwlink/p/?LinkID=528210)も含まれています。[サジェスター](https://msdn.microsoft.com/library/azure/dn798936.aspx)は、挿入辞一致をサポートするようになり、以前の実装の、より制限が大きい (プレフィックスでの一致のみを実行する) 先行入力クエリ サポートを置き換えています。この実装では、語中のどの位置にある一致でも検出できます。さらに、あいまい一致もサポートしています。[タグ ブースト](http://go.microsoft.com/fwlink/p/?LinkId=528212)により、スコアリング プロファイルのための新しいシナリオが可能になります。具体的には、持続データ (ショッピングの嗜好など) を活用し、パーソナライズされた情報に基づいて、個々のユーザーに対する検索結果の精度を向上させることができます。 
.NET SDK 0.9.6-Preview|2015 年 3 月|プレビュー|これは、Azure Search 用 .NET SDK の最初のパブリック リリースです。これには、[Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) と [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx) の 2 つの名前空間を持つクライアント ライブラリ Microsoft.Azure.Search.dll が含まれています。
管理 REST API|2015 年 3 月|一般公開|Azure Search の一般公開のリリースに属している、管理 API の最初のバージョンです。以前のプレビューとこのプレビューでは、機能の相違はありません。
[Microsoft 自然言語プロセッサ](search-api-2015-02-28-Preview.md)|2015 年 3 月|プレビュー|さらに多くの言語が追加され、Office と Bing でサポートされるすべての言語に広範なステミングを提供します。
[moreLikeThis =](search-api-2015-02-28-Preview.md)|2015 年 3 月|プレビュー|`search=` とは相互排他の検索パラメーターであり、クエリの代替実行パスをトリガーします。`moreLikeThis=` は、検索用語の入力に基づく `search=` のフルテキスト検索ではなく、指定されたドキュメントに類似のドキュメントを、検索可能フィールドの比較によって検索します。

##2014 年の新機能

機能|リリース日|状態|詳細
-------|--------|------|-------
Lucene 言語アナライザー|2014 年 11 月|プレビュー|Lucene と共に配布されるカスタム言語アナライザーで多言語をサポートするために追加されました。 
インデックス作成のためのポータル サポートを導入|2014 年 11 月|[ポータル](https://portal.azure.com)|ポータルで、インデックス、アナライザー、およびスコアリング プロファイルを作成できます。
管理 API バージョン 2014-07-31-Preview|2014 年 10 月|プレビュー|管理 REST API の最初のパブリック プレビュー リリースです。この api バージョンのドキュメントは、必要に応じて利用可能です。
Search サービス REST API|2014 年 8 月|プレビュー|サービス REST API の最初のパブリック プレビュー リリースです (api-version-2014-07-31-Preview)。これは、インデックス操作とドキュメント操作、検索結果のチューニングのためのスコアリング プロファイル、地理空間サポートのための REST API です。このリリースでは、Azure ポータルでのサービス プロビジョニングをサポートします。この api バージョンのドキュメントは、必要に応じて利用可能です。これは、サービス REST API とは独立してバージョン管理されます。

##機能のバージョン管理とロール アウト

機能は、単独または他の機能と組み合わされて、[REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)、[.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216)、または [Azure ポータル](https://portal.azure.com)のサービス ダッシュボードを通してリリースされます。.NET ライブラリおよび REST API の両方に、複数のバージョンがあります。新機能がロール アウトされた後も、従来の API は引き続き動作します。バージョン管理ポリシーの詳細については、「[Azure Search サービスのバージョン](https://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。

プレビューと一般公開 (GA) の機能は、同じカテゴリの API のバージョンに関連付けられます。

- プレビューの機能は試験的であり、一般公開になる前に変更されたり、削除されたりすることがあります。プレビューの機能は、[REST API のプレビュー バージョン](search-api-2015-02-28-preview.md)では常に利用でき、場合によっては [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) で利用できます。機能のドキュメントでは、常に対象の機能にアクセスする方法について説明します。
- 一般公開の機能は安定しており、変更されることはあまりありません。一般公開の機能に変更がある場合は、重大な変更として発表されます。

ポータルそのものの機能またはツール ベースの機能は、時間の経過とともに変更され、プレビューまたは一般公開としては分類されません。














 

<!---HONumber=AcomDC_0211_2016-->