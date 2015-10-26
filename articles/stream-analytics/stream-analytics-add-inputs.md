<properties 
	pageTitle="入力の追加 | Microsoft Azure" 
	description="Stream Analytics ジョブへの入力の追加 | ラーニング パス セグメント。"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/29/2015" 
	ms.author="jeffstok"/>


# 入力を追加する

Azure Stream Analytics ジョブを 1 つ以上の入力に接続できます。入力では、既存のデータ ソースへの接続が定義されています。データがそのデータ ソースに送信されると、Stream Analytics ジョブによって使用され、リアルタイムで処理されます。Stream Analytics は、ジョブ サブスクリプションの内部および外部で [Azure Event Hubs](http://azure.microsoft.com/services/event-hubs/) および [Azure BLOB](./storage/storage-dotnet-how-to-use-blobs.md) ストレージとの最上位の統合が行われます。Stream Analytics には、データ ストリームと参照データという 2 種類の入力があります。

- **データ ストリーム**: Stream Analytics のジョブには、ジョブで使用および変換される少なくとも 1 つのデータ ストリームの入力を含める必要があります。Azure BLOB ストレージと Azure Event Hubs は、データ ストリーム入力ソースとしてサポートされます。Azure Event Hubs は、接続されているデバイス、サービス、アプリケーションからイベント ストリームを収集するために使用されます。ストリームとしてバルク データを取り込むための入力ソースとして、Azure BLOB ストレージを使用できます。  
- **参照データ**: Stream Analytics は、参照データと呼ばれる第 2 の種類の補助入力をサポートしています。このデータは、動的なものではなく、静的またはあまり変更されないものです。通常は、高度なデータ セットを作成するための検索およびデータ ストリームとの関連付けを実行するために使用されます。現在、Azure BLOB ストレージは参照データをサポートする唯一の入力ソースです。  

Stream Analytics ジョブに入力を追加するには:

1. Azure ポータルの Stream Analytics ジョブで、**[入力]** をクリックし、**[入力の追加]** をクリックします。

    ![入力の追加](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)

    Azure プレビュー ポータルの Stream Analytics ジョブで、**[入力]** タイルをクリックします。

    ![Azure プレビュー ポータルでの入力の追加](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)

2. 入力の種類として、**[データ ストリーム]** または **[参照データ]** を指定します。

    ![データの追加](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)

    ![Azure プレビュー ポータルでのデータの追加](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)

3. データ ストリーム入力を作成する場合は、入力のソースの種類を指定します。参照データを作成する場合は、現時点でサポートされるのは BLOB ストレージだけなので、この手順はスキップできます。

    ![データ ストリームの追加](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)

    ![Azure プレビュー ポータルでのデータ ストリームの追加](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)

4. [入力のエイリアス] ボックスに、この入力のわかりやすい名前を入力します。この名前は、後で入力を参照するためにジョブのクエリで使用されます。

    データ ソースに接続するために必要な他の接続プロパティを入力します。これらのフィールドは入力の種類とソースの種類によって異なります。詳細は[ここ](stream-analytics-create-a-job.md)に定義されています。

    ![イベント ハブの追加](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)

5. 入力データのシリアル化の設定を指定します。
	- クエリを正しく動作させるには、受信データの **[イベントのシリアル化の形式]** を指定します。サポートされているシリアル化形式は、JSON、CSV、Avro です。
	- データの **[エンコード]** を確認します。現時点でサポートされているエンコード形式は UTF-8 だけです。

    ![データ シリアル化の設定](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)

    ![Azure プレビュー ポータルでのデータ シリアル化の設定](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)

6. 入力の作成が完了すると、Stream Analytics は入力ソースに接続できることを確認します。Notification Hub でテスト接続操作の状態を表示できます。

    ![接続テスト](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)

    ![Azure プレビュー ポータルでの接続テスト](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-jp/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure Stream Analytics の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO3-->