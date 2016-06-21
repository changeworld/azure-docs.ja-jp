<properties
   pageTitle="SQL Data Warehouse プレビューで予期される内容 | Microsoft Azure"
   description="SQL Data Warehouse のパブリック プレビュー機能と一般公開の目標の概要"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/11/2016"
   ms.author="nicw;barbkess;sonyama"/>


# SQL Data Warehouse プレビューで予期される内容

この記事では、SQL Data Warehouse のプレビュー機能と、サービスの一般公開 (GA) の目標について説明します。パブリック プレビューの機能を向上させながら、この情報を継続的に更新します。

SQL Data Warehouse の目標

- 予測可能なパフォーマンスと、ペタバイト単位まで達するデータの直線的拡張性
- すべてのデータ ウェアハウス操作の高い信頼性
- リレーショナル データと非リレーショナル データ全体で、データの読み込みからデータ分析までを短時間で処理

SQL Data Warehouse のプレビュー期間中も引き続き、これらの目標に向けて取り組んでいきます。

## 予測可能でスケーラブルなパフォーマンス

SQL Data Warehouse では、データ ウェアハウスに使用可能なコンピューティング リソース (CPU、メモリ、ストレージ I/O) を測定する方法の 1 つとして、Data Warehouse ユニット (DWU) が導入されています。DWU の数が増えると、リソースの数も増えます。DWU の数が増えるほど、SQL Data Warehouse はより広範囲にわたって分散されたリソース間で操作 (データのロードやクエリなど) を並列で実行するようになります。これにより、遅延が削減され、パフォーマンスが向上します。

すべてのデータ ウェアハウスには、2 つの基本的なパフォーマンス メトリックがあります。

- 読み込みレート。1 秒あたりにデータ ウェアハウスに読み込むことができるレコードの数。具体的には、Azure BLOB ストレージから、クラスタ化された列ストア インデックスを使用するテーブルに PolyBase を介してインポートできるレコード数を測定しています。
- スキャン レート。1 秒あたりにデータ ウェアハウスから順番に取得できるレコードの数。具体的には、クラスタ化された列ストア インデックスに基づいたクエリによって返されるレコード数を測定しています。

現在、いくつかの重要なパフォーマンス上の向上を測定しており、期待されるレートをまもなくお伝えします。プレビュー中にも、これらのレートを高めて予想どおりに拡張できるように、継続的な拡張 (圧縮およびキャッシュ機能の向上) を行います。

## データ保護

SQL Data Warehouse は、Azure のローカル冗長ストレージにすべてのデータを格納します。複数の同期されたデータ コピーがローカル データ センターに保持され、ローカルで障害が発生した場合には透過的なデータ保護が保証されます。さらに、SQL Data Warehouse では、Azure Storage Snapshots を使用して、アクティブな (一時停止されていない) データベースが定期的に自動でバックアップされます。バックアップと復元のしくみについては、[バックアップと復元の概要][]に関するページを参照してください。

## クエリの信頼性

SQL Data Warehouse は、大量並列処理 (MPP) アーキテクチャに基づいています。SQL Data Warehouse は、コンピューティング ノードと制御ノードでの障害を自動的に検出し、移行します。ただし、ノードの障害や移行の結果として、操作 (データの読み込みやクエリなど) に失敗する場合があります。プレビュー中、ノードに障害が発生しても操作を正常に完了できるように、継続的に機能強化を行います。

## アップグレードとダウンタイム

SQL Data Warehouse は、新機能の追加と重要な修正プログラムのインストールのために定期的にアップグレードされます。このアップグレードでダウンタイムが発生する可能性があり、現時点では定期的なスケジュールでアップグレードが行われていません。このプロセスで中断が多すぎると思われる場合は、このプロセスを回避できるように[サポート チケットを作成][]することをお勧めします。

## 次のステップ

パブリック プレビューで[作業を開始][]します。

<!--Image references-->

<!--Article references-->
[サポート チケットを作成]: ./sql-data-warehouse-get-started-create-support-ticket.md
[作業を開始]: ./sql-data-warehouse-get-started-provision.md
[バックアップと復元の概要]: ./sql-data-warehouse-restore-database-overview.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0615_2016-->