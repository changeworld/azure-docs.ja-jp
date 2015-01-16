<properties title="Azure Stream Analytics developer guide" pageTitle="Stream Analytics 開発者ガイド | Azure" description="Azure Stream Analytics アプリケーションを開発する方法について説明します。" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />


# Azure Stream Analytics 開発者ガイド 

[これはプレリリース資料であり、今後のリリースで変更されることがあります。] 

Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。プレビュー リリースでは、Stream Analytics により、ユーザーはデータ ストリームを分析するためにストリーミングのジョブをセットアップでき、ほぼリアルタイムで分析を実行できます。  

Stream Analytics の対象となるシナリオ:

- 大量の高速データに対する複雑なイベント処理の実行   
- 車やユーティリティ グリッドに接続されているような、世界各地に分散した資産や設備のイベント データの収集 
- ほぼリアルタイムで監視および診断するための遠隔測定データの処理 
- 後で処理するリアルタイムのイベントのキャプチャおよびアーカイブ

詳細については、「[Azure Stream Analytics の概要][stream.analytics.introduction]」を参照してください。 

Stream Analytics のジョブは、1 つ以上の入力ソース、受信ストリームのデータに対するクエリ、出力先として定義されます。  


##この記事の内容

+ [入力](#inputs) 
+ [クエリ](#query)
+ [出力](#output)
+ [ジョブの規模の設定](#scale)
+ [ジョブの監視およびトラブルシューティング](#monitor)
+ [ジョブの管理](#manage)
+ [次のステップ](#nextsteps)



##<a name="inputs"></a>入力

### データ ストリーム

Stream Analytics の各ジョブの定義には、ジョブで使用および変換される少なくとも 1 つのデータ ストリームの入力ソースを含める必要があります。[Azure BLOB ストレージ][azure.blob.storage]および [Azure Service Bus Event Hub][azure.event.hubs] は、データ ストリームの入力ソースとしてサポートされています。イベント ハブの入力ソースは、複数のさまざまなデバイスからイベント ストリームを収集するために使用されます。一方、BLOB ストレージは大量のデータのインジェストのために入力ソースとして使用できます。BLOB はデータをストリーミングしないため、BLOB 内のレコードにタイムスタンプが含まれていなければ、BLOB に対する Stream Analytics ジョブは実際には時間で分析されません。

### 参照データ

Stream Analytics は、次の 2 種類の入力ソースもサポートします。参照データ: 相関関係と参照を実行するために使用される補足のためのデータであり、通常静的または変更頻度の低いデータです。プレビュー リリースでは、BLOB ストレージは参照データをサポートする唯一の入力ソースです。

### シリアル化
クエリの正しい動作を確保するには、Stream Analytics で、受信データ ストリームに使用されているシリアル化形式に注意する必要があります。現在サポートされている形式は、JSON、CSV、ストリーミング データ用の Avro、参照データ用の CSV です。

### 生成されたプロパティ
ジョブで使用される入力型に応じて、イベント メタデータといくつかの追加フィールドが生成されます。これらのフィールドには他の入力列と同じようにクエリを実行できます。既存のイベントに次のいずれかのプロパティと同じ名前を持つフィールドがある場合、入力メタデータで上書きされます。

<table border="1">
	<tr>
		<th></th>
		<th>プロパティ</th>
		<th>説明</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>BLOB</strong></td>
		<td>BlobName</td>
		<td>イベントに起因する入力 BLOB の名前</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>BLOB レコードが処理された日時</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>BLOB が最後に変更された日時</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>入力アダプターの 0 から始まるパーティション ID</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>イベント ハブ</b></td>
		<td>EventProcessedUtcTime</td>
		<td>イベントが処理された日時</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>イベント ハブでイベントを受信した日時</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>入力アダプターの 0 から始まるパーティション ID</td>
	</tr>
</table>



###その他のリソース
入力ソースの作成方法の詳細については、「[Azure Event Hub 開発者ガイド][azure.event.hubs.developer.guide]」および「[Azure BLOB ストレージの使用][azure.blob.storage.use]」を参照してください。  



##<a name="query"></a>クエリ
受信データをフィルター処理、操作、および処理するロジックは、Stream Analytics ジョブのクエリで定義されます。クエリは、大部分が一時的なクエリの特定の拡張機能を使用した標準の T-SQL 構文のサブセットの SQL に似た言語である、Stream Analytics のクエリ言語を使用して作成します。

###ウィンドウ化
ウィンドウ化の拡張機能により、複数の期間内に発生したイベントのサブセットに対して集計と計算を実行できます。ウィンドウ化関数は、GROUP BY ステートメントを使用して呼び出されます。たとえば、次のクエリは 1 秒あたりの受信イベント数をカウントします。 

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

###実行ステップ
さらに複雑なクエリの場合は、標準的な SQL 句 WITH を使用して、一時的な名前付き結果セットを指定できます。たとえば、このクエリは次の 2 つの実行ステップで構成される変換を実行するために WITH を使用します。
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

クエリ言語の詳細については、「[Azure Stream Analytics Query Language Reference (Azure Stream Analytics のクエリ言語リファレンス)][stream.analytics.query.language.reference]」を参照してください。 

##<a name="output"></a>出力
出力ソースには、Stream Analytics のジョブの結果が書き込まれます。結果は、ジョブが入力イベントを処理すると、出力ソースに継続的に書き込まれます。次の出力ソースがサポートされています。

- Azure Service Bus Event Hub: デバイスへのコマンドの発行など、複数のストリーミング パイプラインを一緒に構成する必要がある場合は、イベント ハブをシナリオの出力ソースとして選択します。
- Azure ストレージ BLOB : 長期的な出力のアーカイブや、後で処理するためのデータの格納に、BLOB ストレージを使用します。
- Azure SQL データベース: この出力ソースは、実質的にリレーショナルであるデータや、データベースでホストされているコンテンツに依存するアプリケーションに適しています。


##<a name="scale"></a>ジョブの規模の設定

Stream Analytics ジョブは、ジョブを受信する処理能力の大きさを定義するストリーミング ユニットを構成することで拡張できます。各ストリーミング ユニットは、約 1 MB/秒のスループットに対応します。各サブスクリプションには、そのリージョン内のジョブ間で割り当てられた、リージョンあたり 12 のストリーミング ユニットのクォータがあります。

詳細については、「[Stream Analytics ジョブのスケーリング | Azure][stream.analytics.scale.jobs]」を参照してください。


##<a name="monitor"></a>ジョブの監視およびトラブルシューティング

###リージョンのストレージ アカウントの監視

ジョブの監視を有効にするには、Stream Analytics で、Stream Analytics のジョブが含まれている各リージョン内のデータを監視する Azure のストレージ アカウントを指定する必要があります。これは、ジョブの作成時に構成します。  

###メトリック
次のメトリックは、Stream Analytics の使用状況とジョブのパフォーマンスの監視に利用できます。

- 受信スループット: Stream Analytics のジョブが受信したデータのイベント数単位の量
- 送信メッセージ: Stream Analytics のジョブから出力ソースに送信されたデータのイベント数単位の量
- エラー数: Stream Analytics のジョブから発生したエラー メッセージの数

###操作ログ
Stream Analytics のジョブのデバッグやトラブルシューティングに最適な手段が Azure の操作ログです。操作ログは、ポータルの管理サービス セクションからアクセスできます。ジョブのログを調べるには、[サービスの種類] を「Stream Analytics」に、[サービス名] をジョブの名前に設定します。


##<a name="manage"></a>ジョブの管理 

###ジョブの開始と停止
Stream Analytics のプレビュー リリースでは、このジョブを停止しても、ジョブによって使用された最後のイベントに関してどのような状態も保持されません。このため、停止されたジョブを再起動すると、イベントの削除やデータの複製が発生します。ジョブを一時的に停止する必要がある場合のベスト プラクティスに、出力を調べ、ジョブが停止された時刻に近づけるために最後のレコードの挿入時刻を使用する方法があります。ジョブを再開するとき、[構成] タブで [出力の開始] の設定でこの時刻を指定します。
一時的にこのように制限されていますが、データを失うことなくジョブを開始および停止する機能は、将来のリリースでの修正対象として優先度が高く設定されています。  

###ジョブの構成
Stream Analytics では、次ジョブの最上位レベルの設定を調整することができます。

- 出力の開始: このジョブが結果の出力を生成し始めるタイミングを指定します。関連付けられたクエリに期間が使用されている場合は、指定したときに最初の出力イベントが生成されるように、所定の期間の開始時に入力ソースから入力を取得するジョブが開始されます。ジョブの開始時刻とユーザー設定の、2 つのオプションがあります。既定の設定はジョブの開始時刻です。ユーザー設定のオプションでは、日付と時刻を指定する必要があります。この設定は、使用する入力ソースの履歴データの量の指定や、ジョブが最後に停止されたときなど、特定の時点からのデータのインジェストの取り込みに便利です。 
- 順不同の場合のポリシー: Stream Analytics のジョブに順番に配信されないイベントを処理するための設定です。許容範囲を指定してイベントの順序を変更するための時間のしきい値を指定でき、この範囲外のイベントに実行するアクションとして、削除または調整を決定することもできます。削除は、順不同に受信したすべてのイベントを削除し、調整は、順不同のイベントの System.Timestamp を、最近正しい順序で受信したイベントのタイムスタンプに変更します。  
- ロケール: Stream Analytics のジョブの国際化の基本設定を指定するには、この設定を使用します。データのタイムスタンプはロケールに依存しませんが、この設定は、ジョブの解析、比較、およびデータの並べ替えの方法に影響を与えます。プレビュー リリースでは、en-US のみがサポートされています。


##<a name="support"></a>サポートを受ける
その他のサポートについては、「[Azure Stream Analytics のフォーラム][stream.analytics.forum]」を参照してください。 


##<a name="nextsteps"></a>次のステップ

- [Azure Stream Analytics の概要][stream.analytics.introduction]
- [Azure Stream Analytics の使用][stream.analytics.get.started]
- [Azure Stream Analytics ジョブのスケーリング][stream.analytics.scale.jobs]
- [Azure Stream Analytics の制限事項と既知の問題][stream.analytics.limitations]
- [Azure Stream Analytics クエリ言語リファレンス][stream.analytics.query.language.reference]
- [Azure Stream Analytics management REST API reference (Azure Stream Analytics の管理 REST API リファレンス)][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/ja-jp/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/ja-jp/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/ja-jp/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
