<properties 
	pageTitle="プレビュー開発ガイドライン (MongoDB のプロトコル対応の DocumentDB アカウント) | Microsoft Azure" 
	description="現在プレビューとして提供されている MongoDB のプロトコル対応の DocumentDB アカウントについて、プレビュー段階での開発指針について取り上げます。" 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# プレビュー開発ガイドライン (MongoDB のプロトコル対応の DocumentDB アカウント)

Azure DocumentDB との通信は、オープン ソースで公開されている任意の MongoDB クライアント [ドライバー](https://docs.mongodb.org/ecosystem/drivers/)を使って行うことができます。MongoDB のプロトコル対応は、MongoDB クライアント ドライバーの通信対象が MongoDB 2.6 以降のサーバー エンドポイントであることを前提としています。DocumentDB による同プロトコル対応は、MongoDB [Wire Protocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol/) Version 2.6 に準拠することで実現されています。Wire Protocol Version 3.2 もほぼ完全にサポートされていますが、特定のクライアントの操作 (バージョン 3.2 MongoDB のシェル セッションなど) で "レガシ" モードに退化する事象が確認されています。

DocumentDB は、データの CRUD (Create、Read、Update、Delete) やデータベースの照会など、MongoDB の主要な API 関数に対応しています。実装対象の機能は、一般的なプラットフォーム、フレームワーク、ツール、アプリケーション パターンの要件に基づく優先度に従って決められました。

## コレクション

> [AZURE.IMPORTANT] DocumentDB は、予約されたスループットをコレクション レベルで使用することにより、保証済みかつ確実なパフォーマンスを実現します。そのため DocumentDB では、コレクションが課金対象となっています。

システム内の最も低いレベルのデータ コンテナーでアプリケーションがパフォーマンスを調整できるよう、パフォーマンスの予約はコレクション レベルで適用されます。そのためコレクションのコストは、1 秒あたりの要求ユニット数と合計使用ストレージ (GB 単位) とによって測定される、プロビジョニングされたコレクションのスループットで決まります。プロビジョニングしたスループットは、そのコレクションの有効期間にわたって、アプリケーションの処理ニーズの変化やアクセス パターンに合わせて調整できます。詳細については、[DocumentDB のパフォーマンス レベル](documentdb-performance-levels.md)に関する記事を参照してください。

MongoDB コレクションのプロトコルに対応した DocumentDB は、既定では、Standard 価格レベル (プロビジョニングされるスループットは 1,000 RU/秒) で作成されます。各コレクションにプロビジョニングされるスループットは調整することができます。「[Azure ポータルを使用するパフォーマンス レベルの変更](documentdb-performance-levels.md#changing-performance-levels-using-the-azure-portal)」を参照してください。

## CRUD 操作

MongoDB の Insert、Read、Update、Replace、Delete の各操作が完全にサポートされています。MongoDB の [Update 演算子仕様](https://docs.mongodb.org/manual/reference/operator/update/)に記載されている Field Update、Array Update、Bitwise Update、Isolation Update もサポートされています。複数のドキュメント操作を必要とする Update 演算子に関して、DocumentDB は ACID の完全なセマンティクスとスナップショット分離に対応しています。

## クエリ操作

DocumentDB は、いくつかの例外を除いて MongoDB のクエリ文法に完全対応しています。MongoDB の date time 形式に加えて、JSON 互換の [BSON 型](https://docs.mongodb.org/manual/reference/bson-types/)に対するクエリ操作がサポートされます。非 JSON 型固有の演算子を必要とするクエリに関して、DocumentDB は、GUID データ型をサポートしています。MongoDB クエリ文法の対応状況については以下の表に記載してあります。

## 集計

DocumentDB は、MongoDB の集計パイプラインや Map-Reduce 操作をサポートしません。集計パイプラインは通常、多段適用される一連のフィルターと変換を介したドキュメント処理に使用されます (結果のグループ化やマッチングなど)。DocumentDB は、JavaScript のユーザー定義関数とストアド プロシージャの両方にネイティブで対応します。加えて、Azure HDInsight を使用し、DocumentDB [Hadoop コネクタ](documentdb-run-hadoop-with-hdinsight.md)を介すことで、Hive ジョブや Pig ジョブ、MapReduce ジョブのソースとして、またはシンクとして DocumentDB を容易に利用することができます。

## ポータルでの操作
MongoDB プロトコル対応アカウントでは、Azure ポータルの操作性が、標準的な DocumentDB アカウントと若干異なります。マイクロソフトでは今後、操作性の向上に努めていくことになりますが、ユーザーにとって最も使いやすいポータルの機能は何かという点で、お客様からの[フィードバック](mailto:askdocdb@microsoft.com?subject=DocumentDB%20Protocol%20Support%20for%20MongoDB%20Preview%20Portal%20Experience)がたいへん重要となります。

## サポート マトリックス


### CRUD とクエリの操作

機能|サポートされています|サポート予定|サポートされていません 
---|---|---|---
挿入|InsertOne| | 
 |InsertMany| | 
 |挿入| | 
更新|UpdateOne| | 
 |UpdateMany| | 
 |更新| | 
フィールド更新|$inc、$mul、$rename、$set、$unset、$min、$max|$currentDate| 
配列更新| |すべて| 
ビット| |すべて| 
分離:| |すべて| 
Replace|ReplaceOne| |
削除|DeleteOne | |
 |DeleteMany| | 
 |削除| | 
一括書き込み| |bulkWrite()| 
比較|すべて| | 
論理|すべて| | 
要素クエリ| |すべて| 
評価|$mod|$regex、$text、$where| 
GeoSpatial|2dsphere、2d、polygon|その他すべて| 
Array|$all、$size|$elemMatch| 
ビット| |すべて| 
Comment (コメント)|すべて| | 
プロジェクション| |すべて| 


### データベース コマンド

機能|サポートされています|サポート予定|サポートされていません 
---|---|---|---
集計|カウント| |aggregate、distinct、group、mapreduce
GeoSpatial| |すべて| 
クエリと書き込み|find、insert、update、delete、getLastError、getMore、findAndModify| |Eval、parallelCollectionScan、getPrevError、resetError
クエリ プラン キャッシュ| | |すべて
認証|getnonce、logout、authenticate| |Copydbgetnone、authschemaUpgrade
ユーザー管理| | |すべて
役割管理| | |すべて
レプリケーション| | |すべて
管理|createIndex、listIndexes、dropIndexes、connectionStatus、reIndex| |その他のコマンド。インデックスに関して、Unique、expireAfterSeconds、storageEngine、weights、default\_language、textIndexVersion、min、max、bucketSize はサポートされません。
診断|listDatabases、collStats、dbStats| |その他すべて

## 次のステップ

- MongoDB のプロトコル対応の DocumentDB アカウントで [MongoChef を使用](documentdb-mongodb-mongochef.md)する方法を確認します。
- MongoDB のプロトコル対応 DocumentDB の[サンプル](documentdb-mongodb-samples.md)を体験します。

 

<!---HONumber=AcomDC_0720_2016-->