<properties 
	pageTitle="Stream Analytics ジョブ監視について | Microsoft Azure" 
	description="Stream Analytics ジョブ監視について" 
	keywords="クエリ モニター"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/19/2016" 
	ms.author="jeffstok"/>

# Stream Analytics ジョブ監視とクエリの監視方法の概要

## 概要: [監視] ページ

Microsoft Azure 管理ポータルと Azure ポータルは、両方とも、クエリとジョブ パフォーマンスの監視とトラブルシューティングに使用できる主要なパフォーマンス メトリックを明らかにします。

Microsoft Azure 管理ポータルでは、実行中の Stream Analytics ジョブの **[監視]** タブをクリックし、これらのメトリックを確認します。[監視] ページにパフォーマンス メトリックが表示されるまで、最大 1 分の遅延が生じます。

  ![監視ジョブ、ダッシュボード](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)

Azure ポータルでは、メトリックの確認に関心のある Stream Analytics ジョブを参照し、**[監視]** 画面を表示します。

  ![Azure ポータルのジョブ監視ダッシュボード](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)

Stream Analytics ジョブを初めてリージョンに作成したときに、そのリージョン用の診断を構成する必要があります。これを行うには、**[監視]** セクションのいずれかの場所をクリックし、**[診断]** ブレードを表示します。ここで、診断を有効にし、データを監視するためのストレージ アカウントを指定できます。

  ![Azure ポータルのクエリ構成診断](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)

## Stream Analytics に利用できるメトリック


| メトリック | 定義 |
|--------|-------------|
| SU % 使用率 | ジョブの [スケール] タブからジョブに割り当てられたストリーミング ユニットの使用率。このインジケーターが 80% 以上に達した場合、イベントの処理が遅れたり、進行が遅延または停止する可能性があります。 |
| 入力イベント | Stream Analytics のジョブが受信したデータのイベントの量。イベントが入力ソースに送信されることを検証するために使用できます。 |
| 出力イベント | Stream Analytics のジョブから出力ターゲットに送信されたデータのイベントの量。 |
| 異常イベント | イベント順序ポリシーに基づいて、削除された、または調整されたタイムスタンプが付与された、順不同で受信したイベントの数。誤順序の許容期間の設定の構成により、影響を受けることがあります。 |
| データ変換エラー | Stream Analytics のジョブによって発生した、データ変換エラーの数。 |
| 実行時エラー | Stream Analytics ジョブの実行中に発生したエラーの数。 |
| 遅延入力イベント | 到着遅延許容期間の設定のイベント順序ポリシーの構成に基づいて、削除された、または調整されたタイムスタンプが付与された、ソースから遅れて到着するイベントの数。 |

## Microsoft Azure 管理ポータルでの監視のカスタマイズ ##

最大 6 つのメトリックをグラフに表示することができます。

相対値 (最終値は各メトリックにだけ) と絶対値 (Y 軸を表示) の表示を切り替えるには、チャートの上部で [相対] または [絶対] をクリックします。

  ![クエリ モニター、相対絶対](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)

メトリックは、1 時間、12 時間、24 時間、7 日間で集計して監視グラフに表示できます。

メトリック チャートに表示する期間を変更するには、チャートの上部で 1 時間、24 時間、または 7 日を選択します。

  ![クエリ モニター、タイム スケール](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)

定義済みのしきい値をジョブが超えた場合に電子メールで通知されるように規則を設定することができます。

## Azure ポータルでの監視のカスタマイズ ##

グラフのタイプ、表示されるメトリック、および時間範囲を [グラフの編集] 設定で調整できます。詳細については、「[監視のカスタマイズ方法](../azure-portal/insights-how-to-customize-monitoring.md)」をご覧ください。

  ![Azure ポータルのクエリ モニター、タイム スケール](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)

## ジョブの状態

Stream Analytics のジョブの状態は、Azure クラシック ポータルのジョブの一覧で確認することができます。Azure クラシック ポータルで Stream Analytics アイコンをクリックすると、ジョブの一覧が表示されます。

| 状態 | 定義 |
|--------|------------|
| 作成日時 | ジョブが作成されましたが、まだ開始されていません。 |
| Starting | ユーザーが [ジョブの開始] をクリックしたため、ジョブが開始中です。 |
| 実行中 | ジョブは、割り当て済み、入力の処理中、または入力処理の待機中です。ジョブが出力を生成せずに実行中の状態のままになっている場合、データ処理期間が長期であるか、クエリのロジックが複雑である可能性があります。または、現在ジョブにデータが送信されていない可能性があります。 |
| 停止中 | ユーザーが [ジョブの停止] をクリックしたため、ジョブが停止中です。 |
| 停止済み | ジョブが停止されました。 |
| 低下しています | この状態は、Stream Analytics ジョブに一時的なエラー (入力/出力のエラー、処理エラー、変換エラーなど) が発生したことを示します。ジョブはまだ実行中ですが、多くのエラーが生成されています。ユーザーはこのジョブに注意する必要があります。操作ログでエラーを参照することができます。 |
| Failed | エラーのためジョブが失敗したことを示します。処理は停止されました。ユーザーは、エラーをデバッグするために、操作ログを調べる必要があります。 |
| 削除中 | ジョブが削除中であることを示します。 |

## 診断

Microsoft Azure 管理ポータルのジョブのダッシュボードには、入力、出力、操作ログ、またはこれらすべての診断情報を検索する場所の情報が表示されます。適切な場所へのリンクをクリックすると、診断情報を表示することができます。

  ![クエリ モニター、エラー](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)

入力リソースまたは出力リソースをクリックすると、詳細な診断情報が表示されます。この情報は、ジョブの実行中に最新の診断情報で更新されます。

  ![クエリ診断](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->