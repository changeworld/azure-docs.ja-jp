<properties 
	pageTitle="Stream Analytics のリリース ノート | Microsoft Azure" 
	description="Stream Analytics GA のリリース ノート" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/20/2015" 
	ms.author="jeffstok"/>

#Microsoft Stream Analytics リリース ノート

## Stream Analytics の 2015 年 8 月 20 日付けリリースに関するノート ##

このリリースには、次の更新プログラムが含まれています。

タイトル|説明
---|---
Last 関数の追加 |Stream Analytics ジョブで [Last](http://msdn.microsoft.com/library/mt421186.aspx) 関数が使用できるようになりました。これにより、特定の期間内のイベント ストリームで最新のイベントを取得することができます。
新しい配列関数|配列関数 [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx)、[GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx)、[GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) が使用できるようになりました。
新しいレコード関数|レコード関数 [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx)、[GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) が使用できるようになりました。

## Stream Analytics の 2015 年 7 月 30 日付けリリースに関するノート ##

このリリースには、次の更新プログラムが含まれています。

タイトル|説明
---|---
Azure ID から切り離された Power BI の組織 ID|この機能により、任意の Azure アカウント種類 (Live ID または組織 ID) で ASA ジョブの [Power BI 出力](stream-analytics-power-bi-dashboard.md)が可能です。さらに、Azure アカウントに対して 1 つの組織 ID を使用し、Power BI 出力の承認に対して別の ID を使用できます。
Service Bus キューの出力のサポート|[Service Bus キュー](stream-analytics-connect-data-event-outputs.md#service-bus-queues)の出力を、Stream Analytics ジョブで使用できるようになりました。
Service Bus トピックの出力のサポート|[Service Bus トピック](stream-analytics-connect-data-event-outputs.md#service-bus-topics)の出力を、Stream Analytics ジョブで使用できるようになりました。

## Stream Analytics の 2015 年 9 月 7 日付けリリースに関するノート ##

このリリースには、次の更新プログラムが含まれています。


タイトル|説明
---|---
カスタム Blob 出力のパーティション分割|Blob ストレージ出力で、出力 blob が書き込まれる頻度、よび出力データ パス フォルダーの構造と形式を指定するオプションが使用できるようになりました。 

## Stream Analytics の 2015 年 5 月 3 日付けリリースに関するノート ##

このリリースには、次の更新プログラムが含まれています。


タイトル|説明
---|---
順不同許容範囲ウィンドウの最大値の増加|順不同許容範囲 ウィンドウの最大サイズは 59:59 (MM:SS) になりました。
JSON 出力形式: 行区切りまたは配列|Blob ストレージまたは Event Hub に出力する際に、JSON オブジェクトの配列として、または JSON オブジェクトを改行で分割することにより、出力するオプションができました。 

## Stream Analytics の 2015 年 4 月 16 日付けリリースに関するノート ##


タイトル|説明
---|---
Azure Storage アカウント構成の遅延|Stream Analytics ジョブをリージョンで初めて作成すると、そのリージョンで Stream Analytics ジョブを監視するために、新しいストレージ アカウントを作成するか、既存のアカウントを指定するように求められます。監視の構成に待機時間が生じるせいで、同じリージョンで 30 分以内に別の Stream Analytics のジョブを作成すると、構成したばかりのアカウントが [監視ストレージ アカウント] ボックスの一覧に表示される代わりに、2 つ目のストレージ アカウントを指定するように求められます。不要なストレージ アカウントの作成を回避するには、そのリージョンで初めてジョブを作成した後に 30 分待ってから、追加のジョブをプロビジョニングしてください。
ジョブのアップグレード|現時点では、Stream Analytics は、実行中のジョブの定義または構成に対するライブ編集をサポートしていません。実行中のジョブに対する入力、出力、クエリ、スケール、または構成を変更するには、先にジョブを停止する必要があります。
入力ソースから推論されるデータ型|CREATE TABLE ステートメントを使用しない場合、入力の型は入力形式から派生します。たとえば、CSV からのフィールドはすべて文字列です。型の不一致エラーを回避するために、CAST 関数を使用して、フィールドを適切な型に明示的に変換する必要があります。
存在しないフィールドが null 値として出力される|入力ソースに存在しないフィールドを参照すると、出力イベントで null 値となります。
SELECT ステートメントの前に WITH ステートメントを配置する必要がある|クエリでは、SELECT ステートメントに続けて、WITH ステートメントで定義したサブクエリを指定する必要があります。
メモリ不足の問題|順序が適切でないイベントや複雑なクエリに対する Streaming Analytics ジョブの許容範囲が広く、保持している状態が大量になると、ジョブのメモリ不足が発生し、ジョブが再起動されることがあります。開始と停止の操作は、ジョブの操作ログに記録されます。この動作を回避するには、クエリを複数のパーティションにスケール アウトしてください。今後のリリースでは、影響を受けるジョブを再起動する代わりにパフォーマンスを低下させることで、この制限事項が解消される予定です。
ペイロード タイムスタンプのない大きな BLOB 入力によってメモリ不足の問題が発生する|TIMESTAMP BY でタイムスタンプ フィールドが指定されていない場合、BLOB ストレージから大きなファイルを使用すると、Stream Analytics ジョブがクラッシュすることがあります。この問題を回避するには、各 BLOB のサイズを 10 MB 以下にしてください。
SQL Database イベントのボリューム制限|出力ターゲットとして SQL Database を使用すると、大量の出力データによって Stream Analytics ジョブがタイムアウトになる場合があります。この問題を解決するには、集計またはフィルター演算子を使用して出力の量を削減するか、出力ターゲットとして Azure BLOB ストレージまたは Event Hubs を選択してください。
PowerBI データセットにはテーブルを 1 つしか含めることができない|PowerBI では、特定のデータセット内で複数のテーブルがサポートされていません。

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-jp/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](../stream.analytics.get.started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=August15_HO8-->