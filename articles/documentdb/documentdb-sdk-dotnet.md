<properties 
	pageTitle="DocumentDB .NET SDK | Microsoft Azure" 
	description="リリース日、提供終了日、DocumentDB .NET SDK の各バージョン間の変更など、.NET SDK に関するあらゆる詳細を提供します。" 
	services="documentdb" 
	documentationCenter=".net" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/14/2016" 
	ms.author="rnagpal"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB .NET SDK

<table>
<tr><td>**ダウンロード**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**ドキュメント**</td><td>[.NET SDK のリファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**サンプル**</td><td>[.NET コード サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)</td></tr>
<tr><td>**概要**</td><td>[DocumentDB .NET SDK の使用](documentdb-get-started.md)</td></tr>
<tr><td>**現在サポートされているフレームワーク**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## リリース ノート

### <a name="1.8.0"/>[1\.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - 複数リージョンのデータベース アカウントのサポートを追加しました。
  - 調整された要求での再試行のサポートを追加しました。ユーザーは、ConnectionPolicy.RetryOptions プロパティを構成することで、再試行の回数と最大待機時間をカスタマイズできます。
  - すべての DocumenClient プロパティおよびメソッドの署名を定義できる新しい IDocumentClient インターフェイスを追加しました。また、この変更の一環として、IQueryable と IOrderedQueryable を作成する拡張メソッドを DocumentClient クラス自体のメソッドに変更しました。
  - 特定の DocumentDB エンドポイント URI の ServicePoint.ConnectionLimit を設定する構成オプションを追加しました。ConnectionPolicy.MaxConnectionLimit を使用して既定値 (50) を変更できます。
  - IPartitionResolver とその実装を廃止しました。IPartitionResolver のサポートは廃止されました。大量のストレージとスループットを必要とする場合は、パーティション分割コレクションの使用をお勧めします。


### <a name="1.7.1"/>[1\.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - RequestOptions をパラメーターとして受け取る Uri ベースの ExecuteStoredProcedureAsync メソッドにオーバーロードを追加しました。
  
### <a name="1.7.0"/>[1\.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - ドキュメントの有効期限 (TTL) サポートを追加しました。

### <a name="1.6.3"/>[1\.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - .NET SDK を Azure クラウド サービス ソリューションの一部としてパッケージ化するための Nuget パッケージ化のバグを修正しました。
  
### <a name="1.6.2"/>[1\.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - [パーティション分割コレクション](documentdb-partition-data.md)と[ユーザー定義のパフォーマンス レベル](documentdb-performance-levels.md)を実装しました。 

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[修正済み]** DocumentDB エンドポイントのクエリを実行すると、"System.Net.Http.HttpRequestException: コンテンツをストリームにコピーしている間にエラーが発生しました" というエラーがスローされます。

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - ページング、条件付きの式、および範囲比較用の新しい演算子を使用できるように LINQ を拡張しました。
    - LINQ で SELECT TOP 動作を有効にする Take 演算子
    - 文字列の範囲の比較を有効にする CompareTo 演算子
    - 条件付き (?) および合体演算子 (??)
  - **[修正済み]** linq クエリでモデル プロジェクションを Where-In と組み合わせると ArgumentOutOfRangeException が発生します。[#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[修正済み]** Select が最後の式ではない場合、LINQ プロバイダーはプロジェクションがないものと想定し、SELECT * を不適切に生成しました。 [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - 実装された Upsert、追加された UpsertXXXAsync メソッド
 - すべての要求のパフォーマンス改善
 - LINQ プロバイダーによる文字列の条件、結合、CompareTo メソッドのサポート
 - **[修正済み]** LINQ プロバイダー --> List に Contains メソッドを実行し、IEnumerable や Arraymethod の場合と同じ SQL を生成
 - **[修正済み]** BackoffRetryUtility は、再試行時に新規作成する代わりに、同じ HttpRequestMessage をもう一度使用します
 - **[廃止]** UriFactory.CreateCollection --> 今後は UriFactory.CreateDocumentCollection を使用する必要があります
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[修正済み]** nl-NL など、非英語文化情報の使用時のローカリゼーション問題 
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - ID ベースのルーティング
    - ID ベースのリソース リンクの構築に役立つ新しい UriFactory ヘルパー
    - URI に取り入れる DocumentClient の新しいオーバーロード
  - 地理空間の LINQ で IsValid() と IsValidDetailed() を追加
  - LINQ プロバイダー サポートの機能強化
    - **数値演算** - Abs、Acos、Asin、Atan、Ceiling、Cos、Exp、Floor、Log、Log10、Pow、Round、Sign、Sin、Sqrt、Tan、Truncate
    - **文字列** - Concat、Contains、EndsWith、IndexOf、Count、ToLower、TrimStart、Replace、Reverse、TrimEnd、StartsWith、SubString、ToUpper
    - **配列** - Concat、Contains、Count
    - **IN** 演算子

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - インデックス作成ポリシーを変更するためのサポートを追加
    - DocumentClient の新しい ReplaceDocumentCollectionAsync メソッド
    - インデックス ポリシー変更の進捗状況 (%) を追跡するための ResourceResponse<T> の新しい IndexTransformationProgress プロパティ
    - DocumentCollection.IndexingPolicy は変化可能になりました
  - 空間インデックス作成とクエリのサポートを追加
    - 点や多角形など、空間型のシリアル化/逆シリアル化するための新しい Microsoft.Azure.Documents.Spatial 名前空間
    - DocumentDB に格納されている GeoJSON データにインデックスを作成するための新しい SpatialIndex クラス
  - **[修正済み]** : 間違った SQL クエリが linq 式から生成される [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Newtonsoft.Json v5.0.7 の依存関係 
- Order By 対応になりました
  - LINQ プロバイダーによる OrderBy() または OrderByDescending() のサポート
  - Order By をサポートするための IndexingPolicy 
  
		**NB: Possible breaking change** 
  
    	If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- 新しい HashPartitionResolver クラス、RangePartitionResolver クラス、IPartitionResolver を使用し、データをパーティショニング
- DataContract シリアル化
- LINQ プロバイダーの GUID サポート
- LINQ の UDF サポート

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- GA SDK

> [AZURE.NOTE]
プレビューと GA の間で NuGet パッケージ名の変更がありました。**Microsoft.Azure.Documents.Client** が **Microsoft.Azure.DocumentDB** になりました。 <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- プレビュー SDK [廃止]

## リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

提供終了の SDK で DocumentDB に要求した場合、サービスにより却下されます。

> [AZURE.WARNING]
バージョン **1.0.0** 以前のすべてのバージョンの Azure DocumentDB SDK for .NET は **2016 年 2 月 29 日**で提供が終了します。
 
<br/>
 
| バージョン | リリース日 | 提供終了日 
| ---	  | ---	         | ---
| [1\.8.0](#1.8.0) | 2016 年 6 月 14 日 |--- | [1\.7.1](#1.7.1) | 2016 年 5 月 06 日 |--- | [1\.7.0](#1.7.0) | 2016 年 4 月 26 日 |--- | [1\.6.3](#1.6.3) | 2016 年 4 月 08 日 |--- | [1\.6.2](#1.6.2) | 2016 年 3 月 29 日 |--- | [1\.5.3](#1.5.3) | 2016 年 2 月 19 日 |--- | [1\.5.2](#1.5.2) | 2015 年 12 月 14 日 |--- | [1\.5.1](#1.5.1) | 2015 年 11 月 23 日 |--- | [1\.5.0](#1.5.0) | 2015 年 10 月 05 日 |--- | [1\.4.1](#1.4.1) | 2015 年 8 月 25 日 |--- | [1\.4.0](#1.4.0) | 2015 年 8 月 13 日 |--- | [1\.3.0](#1.3.0) | 2015 年 8 月 05 日 |--- | [1\.2.0](#1.2.0) | 2015 年 7 月 06 日 |--- | [1\.1.0](#1.1.0) | 2015 年 4 月 30 日 |--- | [1\.0.0](#1.0.0) | 2015 年 4 月 08 日 |--- | [0\.9.3-prelease](#0.9.x-preview) | 2015 年 3 月 12 日 | 2016 年 2 月 29 日 | [0\.9.2-prelease](#0.9.x-preview) | 2015 年 1 月 | 2016 年 2 月 29 日 | [0\.9.1-prelease](#0.9.x-preview) | 2014 年 10 月 13 日 | 2016 年 2 月 29 日 | [0\.9.0-prelease](#0.9.x-preview) | 2014 年 8 月 21 日 | 2016 年 2 月 29 日|

## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 関連項目

DocumentDB に関する詳細は、[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。

<!---HONumber=AcomDC_0615_2016-->