<properties 
	pageTitle="Azure Search 関連の更新履歴情報 | Microsoft Azure" 
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
	ms.date="07/08/2015" 
	ms.author="heidist"/>

#Azure Search 関連の最新の更新履歴情報#

Azure Search の一般提供が開始されています。これは [API バージョン 2015-02-28](https://msdn.microsoft.com/library/azure/dn798935.aspx) でサポートされる構成に対して、99.9% の可用性を保証するサービス レベル アグリーメント (SLA) を提供します。

##機能のバージョン管理とロール アウト

機能は、単独または他の機能と組み合わされて、[REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)、[.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216)、または [Azure ポータル](https://portal.azure.com)のサービス ダッシュ ボードを通してリリースされます。

.NET ライブラリおよび REST API の両方に、複数のバージョンがあります。新機能がロール アウトされた後も、従来の API は引き続き動作します。バージョン管理ポリシーの詳細については、「[Azure Search サービスのバージョン](https://msdn.microsoft.com/library/azure/dn864560.aspx)」を参照してください。


##.NET SDK 0.10.0-Preview

これは、.NET クライアント ライブラリ Microsoft.Azure.Search.dll の、2 番目のイテレーションです。  
このバージョンでは、.NET クラスを使用してインデクサーを作成、管理、および使用できるようになりました。また、Azure SQL インデクサーの場合、地理ポイントのインデックス作成が新しくサポートされます。  


- [Indexers クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [DataSource クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##.NET SDK 0.9.6-Preview
**リリース日: 2015 年 3 月 5 日**

これは、Azure Search 用 .NET SDK の最初のパブリック リリースです。これには、以下の 2 つの名前空間を持つ、クライアント ライブラリ Microsoft.Azure.Search.dll が含まれています。

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

以下については除外されています。

- [インデクサー](http://go.microsoft.com/fwlink/p/?LinkId=528173) (この機能は、0.10.0-Preview バージョンでは除外されなくなりました)
- [管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)
- [2015-02-28-Preview](search-api-2015-02-28-Preview.md) 機能 (現時点では、プレビュー専用機能は Microsoft 自然言語プロセッサおよび `moreLikeThis` で構成される)。

SDK のインストールおよび使用のガイダンスについては、「[How to use Azure Search from a .NET Application](http://go.microsoft.com/fwlink/p/?LinkId=528088)」を参照してください。

##API バージョン 2015-02-28-Preview
**リリース日: 2015 年 4 月 22 日**

- インデクサーは、fieldMapping コンストラクトをサポートするようになりました。これは実際のフィールド名が外部データベースと Azure Search インデックスとの間で異なる場合に、フィールド割り当てを提供します。`2015-02-28-preview` バージョンのインデクサーの資料については、「[Indexer Operations](search-api-indexers-2015-02-28-Preview.md)」を参照してください。

- さらにこのプレビューの更新では、インデクサーはフィールドの型変換をサポートするようになったため、ソース フィールドが JSON 配列を表すと仮定して、SQL テーブル内の文字列フィールドを、検索インデックス内の文字列コレクション フィールドにマッピングできます。  


**リリース日: 2015 年 3 月 5 日**

- [Microsoft 自然言語プロセッサ](search-api-2015-02-28-Preview.md)は、さらに多くの言語のサポートと、Office と Bing でサポートされるすべての言語の広範な語幹検索のサポートを提供します。

- [moreLikeThis=](search-api-2015-02-28-Preview.md) は、`search=` とは相互排他の検索パラメーターであり、クエリの代替実行パスをトリガーします。`moreLikeThis=` は、検索用語の入力に基づく `search=` のフルテキスト検索ではなく、指定されたドキュメントに類似のドキュメントを、検索可能フィールドの比較によって検索します。

##API バージョン 2015-02-28
**リリース日: 2015 年 3 月 5 日**

- [インデクサー](http://go.microsoft.com/fwlink/p/?LinkID=528210)は、Azure VM 上の Azure SQL Database、Azure DocumentDB、および SQL Server にあるデータ ソースからのインデックス作成を大幅に簡略化する新機能です。

- [サジェスター](https://msdn.microsoft.com/library/azure/dn798936.aspx)は、挿入辞一致をサポートするようになり、以前の実装の、より制限が大きい (プレフィックスでの一致のみを実行する) 先行入力クエリ サポートを置き換えています。この実装では、語中のどの位置にある一致でも検出できます。さらに、あいまい一致もサポートしています。

- [タグ ブースト](http://go.microsoft.com/fwlink/p/?LinkId=528212)により、スコアリング プロファイルのための新しいシナリオが可能になります。具体的には、持続データ (ショッピングの嗜好など) を活用し、パーソナライズされた情報に基づいて、個々のユーザーに対する検索結果の精度を向上させることができます。

サービスの発表については、これらすべての機能について説明する Azure ブログの「[Azure Search is now Generally Available](http://go.microsoft.com/fwlink/p/?LinkId=528211)」を参照してください。

##API バージョン 2014-10-20-Preview
**リリース日付: 2014 年 11 月、2015 年 1 月**

- [Lucene 言語アナライザー](search-api-2014-10-20-preview.md)が、Lucene と共に配布されるカスタム言語アナライザーで多言語サポートするために追加されました。 

- [Azure 管理ポータル](https://portal.azure.com)には、スコアリング プロファイルを含め、インデックス作成のためのツール サポートが導入されました。

##API バージョン 2014-07-31-Preview
**リリース日: 2014 年 8 月 21 日**

このバージョンは、次のコア機能を提供する、Azure Search のパブリック プレビュー リリースでした。

- インデックスとドキュメントの操作のための REST API。この API バージョンの大半は、2015-02-28 バージョンでも変更されていません。バージョン `2014-07-31-Preview` のドキュメントは、「[Azure Search Service REST API Version 2014-07-31-Preview](search-api-2014-07-31-preview.md)」にあります。

- 検索結果のチューニングのためのスコアリング プロファイル。スコアリング プロファイルは、検索スコアの計算に使用する条件を追加します。この機能のドキュメントは、「[Scoring Profiles (Azure Search Service REST API Version 2014-07-31-Preview)](search-api-scoring-profiles-2014-07-31-preview.md)」にあります。

- 地理空間サポートは当初より利用可能です。これは最初から Azure Search の一部であった `Edm.GeographyPoint` データ型により提供されます。

- [Azure 管理ポータル](https://portal.azure.com)のプレビュー バージョンでのプロビジョニング。Azure Search は、新しいポータルでのみ使用できるいくつかのサービスの 1 つでした。

##管理 API バージョン 2015-08-19
**リリース日: 2015 年 9 月 11 日**

[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx) には次の更新が含まれています。

- checkNameAvailability は、所与のサービス名が既に使用されていないか確認します。
- レプリカの範囲は以前は 1 ～ 6 でしたが、現在は 1 ～ 12 です。
- SKU プロパティはプロパティ バッグからサービス ペイロードの最上位に移動されました。
- 検索サービスの作成操作の要求本文が更新され、SKU 設定の再配置が追加されました。

##管理 API バージョン 2015-02-28
**リリース日: 2015 年 3 月 5 日**

[管理 REST API](search-management-api-2014-02-28.md) は、Azure Search の一般提供のリリースに属している、管理 API の最初のバージョンです。以前のプレビューとこのプレビューでは、機能の相違はありません。

##管理 API バージョン 2014-07-31-Preview
**リリース日: 2014 年 10 月**

プレビュー リリースの[管理 REST API](search-management-api-2014-07-31-preview.md) は、プログラムからサービスを管理できるようにするために追加されました。これは、サービス REST API とは独立してバージョン管理されます。


 

<!---HONumber=Sept15_HO3-->