---
title: Azure SQL Edge のリリース ノート
description: Azure SQL Edge イメージの新機能または変更点の詳細について説明したリリース ノート
keywords: SQL Edge のリリース ノート
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 543a71bc2a77f0a31f8ddaa0de13135b93db48ec
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395038"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge のリリース ノート 

この記事では、Azure SQL Edge の新しいビルドごとに、新機能と変更点について説明します。

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge - 1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>SQL エンジンのビルド番号 - 15.0.2000.1552

### <a name="whats-new"></a>新機能
1. Ubuntu 18.04 ベースのコンテナー イメージ。 
2. `LAST_VALUE()` および `FIRST_VALUE()` 関数を使用した `IGNORE NULL` および `RESPECT NULL` 構文のサポート。 
3. ONNX を使用した PREDICT の信頼性の向上。
4. データ保持ポリシー ベースのクリーンアップのサポート。
   - トラブルシューティングの保持クリーンアップ タスクのリング バッファー サポート。
5. 新機能のサポート 
   - 高速復旧
   - クエリの自動チューニング
   - 並列実行を有効にするシナリオ
6. 省電力モードの電力節約機能強化
7. ストリーミングの新機能のサポート 
   - [スナップショット ウィンドウ](/stream-analytics-query/snapshot-window-azure-stream-analytics) : 新しい種類のウィンドウでは、イベント別のグループがまったく同時に到着するようにできます。 
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) と [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) を分析機能として有効にします。それにより、自分で選んだ列を基準にレコードを並べて返すことができます。ウィンドウの一部にする必要がありません。 
   - [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics) の機能強化。 

### <a name="fixes"></a>修正
1. TSQL ストリーミング操作のトラブルシューティングに関する追加のエラー メッセージと詳細。 
2. アイドル モードでバッテリー寿命を維持するための機能強化。 
3. TSQL ストリーミング エンジンの修正: 
   - 停止したストリーミング ジョブのクリーンアップ 
   - ローカライズの修正と Unicode 処理の機能強化
   - Edge TSQL ストリーミングのデバッグ機能を強化します。ユーザーは get_streaming_job からジョブ失敗エラーにクエリを実行できます。
4. データ保持ポリシー ベースのクリーンアップ
   - アイテム保存ポリシーの作成とクリーンアップのシナリオの修正。
5. 低電力モードで省電力を向上させるためのバックグラウンド タイマー タスクの修正。

### <a name="known-issues"></a>既知の問題 
1. Date_Bucket T-SQL 機能では計算列で使用できません。


## <a name="ctp-23"></a>CTP 2.3
### <a name="sql-engine-build-number---15020001549"></a>SQL エンジンのビルド番号 - 15.0.2000.1549
### <a name="whats-new"></a>新機能
1. Date_Bucket() 関数でのカスタム オリジンのサポート。 
2. SQL 展開の一部としての BacPac ファイルのサポート。
3. データ保持ポリシー ベースのクリーンアップのサポート。      
   - アイテム保持ポリシーを有効にするための DDL のサポート 
   - ストアド プロシージャのクリーンアップとバックグラウンド クリーンアップ タスク
   - クリーンアップ タスクを監視する拡張イベント

### <a name="fixes"></a>修正
1. TSQL ストリーミング操作のトラブルシューティングに関する追加のエラー メッセージと詳細。 
2. アイドル モードでバッテリー寿命を維持するための機能強化。 
3. TSQL ストリーミング エンジンの修正: 
   - サブストリーム化されたホッピング ウィンドウでの透かしのスタックに関する問題の修正 
   - ユーザーが対処できるエラーとして収集されるようにフレームワークの例外処理を修正


## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>SQL エンジンのビルド番号 - 15.0.2000.1546
### <a name="whats-new"></a>新機能
1. ルート以外のコンテナーのサポート 
2. 使用状況と診断データの収集のサポート 
3. T-SQL ストリーミングの更新
   - ストリーム オブジェクト名の Unicode 文字のサポート

### <a name="fixes"></a>修正
1. T-SQL ストリーミングの更新
   - プロセス クリーンアップの機能強化
   - ログ記録と診断の機能強化
2. データ インジェストのパフォーマンスの向上

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>SQL エンジンのビルド番号 - 15.0.2000.1545
### <a name="fixes"></a>修正
1. ARM で CPUID の問題を処理するように ONNX モデルを使用した PREDICT を修正します 
2. TSQL ストリーミングの起動時のエラー パスの処理を改善するように修正します 
3. データが存在しない場合のジョブ メトリックの透かしの遅延の誤った値を修正します 
4. アダプターにバッチ間の可変スキーマがある場合の出力アダプターの問題を修正します。  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>SQL エンジンのビルド番号 - 15.0.2000.1401
### <a name="whats-new"></a>新機能
1.  製品名が 'Azure SQL Edge' に更新されました
1.  Date_bucket 関数

    i.  Date、Time、DateTime 型のサポート
3.  ONNX を使用した PREDICT
    
    i.  ONNX の場合に RUNTIME パラメーターが必須になりました 
    
4.  TSQL ストリーミングのサポート (制限付きプレビュー) 
 
### <a name="known-issues"></a>既知の問題

1. <b>問題:</b>タイミングの問題が原因で、起動時に dacpac を適用すると失敗する可能性があります。

    <b>対処法:</b>SQL Server またはコンテナーを再起動すると、dacpac の適用が再試行されます。これで、おそらく問題が解決します。
### <a name="request-support"></a>サポートを依頼する
1. [サポート ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)でサポートを依頼することができます。

4. 次のフィールドが選択されていることを確認します。 
    * 問題の種類 - 技術 
    * サービス - IoT Edge
    * 問題の種類 - IoT Edge モジュールに関連する問題
    * 問題のサブタイプ - Azure SQL Edge

   ![サポート チケットのサンプル](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1.5
### <a name="sql-engine-build-number---15020001331"></a>SQL エンジンのビルド番号 - 15.0.2000.1331
### <a name="whats-new"></a>新機能
1. Date_bucket 関数
    
    i. DateTimeOffset 型のサポート
2. ONNX モデルを使用した PREDICT

    i. nvarchar のサポート
 
## <a name="ctp-14"></a>CTP 1.4
### <a name="sql-engine-build-number---15020001247"></a>SQL エンジンのビルド番号 - 15.0.2000.1247
### <a name="whats-new"></a>新機能
1.  ONNX モデルを使用した PREDICT
 
    i.  Varchar のサポート
    
    ii. ONNX ランタイム バージョン 1.0 への移行 
2.  機能のサポート - 次の機能が有効になっています。

    i.   CDC のサポート

    ii.  圧縮される履歴テーブル

    iii. 先読みログのより高い倍率

    iv.  バッチ モード ES フィルターのプッシュダウン

    v.   先読みの最適化
 
## <a name="ctp-13"></a>CTP 1.3
### <a name="sql-engine-build-number---15020001147"></a>SQL エンジンのビルド番号 -15.0.2000.1147
### <a name="whats-new"></a>新機能
1. Azure IOT ポータルのデプロイ 

    i.   AMD64 および ARM の各イメージのデプロイのサポート

    ii.  ストリーミング ジョブの作成のサポート

    iii. Dacpac のデプロイ
2. ONNX モデルを使用した PREDICT

    i. 数値型のサポート
3. 機能のサポート - 次の機能が有効になっています。

    i.  列ストア スキャンのプッシュダウン集計

    ii. メリーゴーランド スキャン
4. メモリ占有領域とメモリ消費の削減作業