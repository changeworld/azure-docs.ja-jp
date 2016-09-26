<properties
   pageTitle="Azure Data Lake Analytics ドキュメントのバックログ | Microsoft Azure"
   description="Data Lake Analytics は Azure のビッグ データ コンピューティング サービスであり、クラウドのデータから得られた洞察を活用し、データを使用してビジネスを推進できます。その場所やサイズは関係ありません。Data Lake Analytics は、可能な限り最もシンプルで、スケーラブルかつ経済的な方法でこれを可能にします。このページはドキュメント作業のバックログです。 "
   documentationCenter="na"
   services="data-lake-analytics"
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Azure Data Lake Analytics ドキュメントのバックログ

サービス開発時に Azure Data Lake Analytics をサポートするために、また、一般的なユース ケースとオプションを明示するために、新しいドキュメントを継続的に追加していく予定です。このページには、現在計画されているドキュメントをリストします。焦点を当ててほしい内容がある場合は、この記事にコメントを追加するか、GitHub でプル要求を送信するか、あるいは電子メールでお知らせください。

## ドキュメントのバックログ

領域 |トピック | 優先順位 | 状態 | 割当先 | 要求者 
------------- | ------------- | -------------- | -------------- | --------------- | --------------
サービス | コンテンツの再設計 | 1 | 進行中 | [@edmacauley](https://github.com/edmacauley) |
U-SQL | 分散クエリ | 1 | 保留中 | [@edmacauley](https://github.com/edmacauley) |
U-SQL | WASB と ADLS 間のデータの移動 | 1 | Pending | 未定 | Ye Xing (ml)
U-SQL | UDO モデルの単体テストのガイダンス | 1 | Pending | 未定 | [@ddobric](https://github.com/ddobric) 
U-SQL | JSON の処理 | 2 | Pending | [@mumian](https://github.com/mumian) | [@mwinkle](https://github.com/mwinkle)
サービス | Curl の操作 | 3 |Pending |[@mumian](https://github.com/mumian)
U-SQL | パーティション テーブル |1 | Pending | [@edmacauley](https://github.com/edmacauley) | 
U-SQL | パフォーマンスのチューニング | 2 | Pending | [@MikeRys](https://github.com/mikerys) | 
U-SQL | U-SQL UDO プログラマー ガイド | 1 | Pending | [@edmacauley](https://github.com/edmacauley) | 
U-SQL | U-SQL プログラマー ガイド | 2 | Pending | [@edmacauley](https://github.com/edmacauley) | 
U-SQL | T-SQL バックグラウンドからの U-SQL の使用 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) | [@MikeRys](https://github.com/mikerys)
U-SQL | Hive バックグラウンドからの U-SQL の使用 | 1 | Pending | [@mumian](https://github.com/mumian)| [@MikeRys](https://github.com/mikerys)
U-SQL | 文法のレールロード ダイアグラム | 1 | 進行中 ([これ](http://bottlecaps.de/rr/ui)を使用) | [@mwinkle](https://github.com/mwinkle) | [@mwinkle](https://github.com/mwinkle)
サービス | Data Lake Analytics におけるジョブ、データ、およびテーブルのセキュリティ保護 |1| 進行中 | [@mumian](https://github.com/mumian) | [@MikeRys](https://github.com/mikerys)
サービス | Data Factory を使用する SQL Data Warehouse へのジョブ出力の移動 |1 | 進行中 | [@mumian](https://github.com/mumian) | 
ツール | 診断ツールの使用 | 1 | Pending | [@mumian](https://github.com/mumian) | 
U-SQL | 範囲基準の結合を行うための手法を説明します。 | 1 | Pending | [@MikeRys](https://github.com/mikerys) |[@saveenr](https://github.com/saveenr) 
U-SQL | ADLS 認証オプション | 1 | Pending | [@ArindamC](https://github.com/mikerys) |[@saveenr](https://github.com/saveenr) 
U-SQL | クロス プラットフォームの CLI ジョブの送信 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) |[@mwinkle](https://github.com/mwinkle) 
U-SQL | REDUCE と COMBINE のリファレンス ドキュメントの更新 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) |[@MikeRys](https://github.com/mikerys)
ツール | U-SQL UDFs Azure Data Lake Tools for Visual Studio の単体テスト | 1 | Pending | [@edmacauley](https://github.com/edmacauley) |[@xiaoyong](https://github.com/zxzxy1988)
ツール | Azure Data Lake Tools for Visual Studio のジョブ グラフと頂点実行ビュー | 1 | Pending | [@edmacauley](https://github.com/edmacauley) |[@xiaoyong](https://github.com/zxzxy1988)



## バックログからの完成したドキュメント

領域 |トピック | location
------------- | ------------- | -------------- 
サービス | ARM テンプレートを使用する他の Azure サービスとの Data Lake Analytics のデプロイメント調整 | [location](data-lake-analytics-manage-use-powershell.md)

## バックログに関するフィードバックの送信方法
バックログに関するフィードバックを送信する場合に使用できる以下のようないくつかのオプションがあります。

* 下にコメントを追加する。
* [Azure コンテンツ リポジトリ](https://github.com/Azure/azure-content/blob/master/articles/data-lake-analytics/data-lake-analytics-documentation-backlog.md)でこのドキュメントに関するプル要求を送信する。
* [adlafeedback at microsoft.com](mailto:adlafeedback@microsoft.com?subject=DocBacklog) に電子メールを送信する。

<!---HONumber=AcomDC_0914_2016-->