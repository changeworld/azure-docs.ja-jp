<properties 
	pageTitle="Stream Analytics 入力の作成 | Microsoft Azure" 
	description="Stream Analytics ソリューションの入力ソースに接続して構成する方法について説明します。"
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
	ms.date="08/05/2015" 
	ms.author="jeffstok"/>

# Stream Analytics 入力の作成

## Stream Analytics 入力について
---
Stream Analytics 入力はデータ ソースへの接続として定義されます。データがそのデータ ソースに送信されると、Stream Analytics ジョブによって使用され、リアルタイムで処理されます。入力は 2 つの個別のグループであるデータ ストリーム入力と参照データ入力に分けられます。Stream Analytics では、ジョブ サブスクリプションの内外からの入力ソース Event Hub と BLOB ストレージとの最上位の統合が行われます。サポートされるデータ形式は、Avro、CSV、および JSON です。

## データ ストリーム入力
---
ベーシック レベルでは、Stream Analytics のジョブ定義には、ジョブで使用および変換される少なくとも 1 つのデータ ストリームの入力ソースを含める必要があります。Azure BLOB ストレージと Azure Event Hubs は、データ ストリーム入力ソースとしてサポートされます。Azure Event Hubs 入力ソースは、複数のデバイスやサービスからイベント ストリームを収集するために使用されます。たとえば、データ ストリームの例として、ソーシャル メディア アクティビティ フィード株式取引情報、またはセンサーからのデータなどがあります。

または、バルク データを取り込むための入力ソースとして Azure BLOB ストレージを使用することもできます。Azure BLOB を使用するときはデータは静止しているため、Stream Analytics は、BLOB に含まれているすべてのデータが、その BLOB 自体の作成時のタイムスタンプを持っていると解釈します。これは、BLOB 内のレコードにタイムスタンプが含まれていない場合、およびTIMESTAMP BY キーワードが使用されていない場合です。

## 参照データ入力
---
Stream Analytics は、参照データと呼ばれる 2 番目のタイプの入力ソースもサポートします。これは一般に関連付けと参照を実行するために使用される補足データであり、通常は静的データまたは変更頻度の低いデータです。現在、Azure BLOB ストレージは参照データをサポートする唯一の入力ソースです。参照データ ソースの BLOB のサイズは、最大で 50 MB に制限されています。

参照データの更新をサポートするには、{date} および {time} トークンを使用する入力構成でパス パターンを指定します。ジョブは、UTC タイム ゾーンを使用して BLOB 名にエンコードされた日時に基づいて、対応する BLOB を読み込みます。エンコードされた日付と時刻を含む参照データ BLOB のシーケンスは、結果の一貫性を保証するために必要です。たとえば、処理に遅延が生じ、BLOB ファイルを再読み込みする必要がある場合、そのファイルは変更されていない状態で同じ場所にある必要があります。そうでなければ、出力が異なる可能性があります。パス パターンでエンコードされた将来の日付と時刻を持つ新しい BLOB を追加するというシナリオのみがサポートされています。

たとえば、ジョブに /sample/{date}/{time}/products.csv というパスのパターンの、ポータルで構成された参照入力があるとします。ここで日付形式は "YYYY-MM-DD" であり、時刻形式は "HH:mm" です。この場合、ジョブは UTC タイム ゾーンで 2015 年 4 月 16 日 5:30 PM に、/sample/2015-04-16/17:30/products.csv というファイルを取得します。


> [AZURE.NOTE]現在、Stream Analytics ジョブは、時刻が、BLOB 名でエンコードされた時刻と一致する場合にのみ参照 BLOB 更新データを検索します。たとえば、ジョブで、UTC タイム ゾーンの 2015 年 4 月 16 日の 5:30 PM から 5:30:59.999999999 PM の間に /sample/2015-04-16/17:30/products.csv が検索されます。クロックが 5:31PM になると、/sample/2015-04-16/17:30/products.csv の検索は停止され、/sample/2015-04-16/17:31/products.csv の検索が開始されます。

以前の参照データ BLOB が考慮される唯一の時間は、ジョブが開始される時点です。その時点では、ジョブの開始時刻よりも以前の値の、その名前でエンコードされた最新の日付/時刻を持つ BLOB がジョブによって検索されます (ジョブの開始時刻以前からの最新の参照データ BLOB)。これは、ジョブの開始時に空ではない参照データ セットが必ず存在するようにするために実行されます。それが見つからない場合、ジョブは失敗し、診断通知がユーザーに表示されます。

    “Initializing input without a valid reference data blob for UTC time <job start time>.”

 
## データ ストリーム入力の作成
---
データ ストリーム入力を作成するには、Stream Analytics ジョブの **[入力]** タブに移動し、ページ下部の **[入力の追加]** をクリックします。

![Image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

 データ ストリーム入力を作成すると、[**Event Hub**](Creating-an-Event-hub-input-data-stream) または [**Blob storage**](Creating-a Blob-storage-input-data-stream) という 2 つの選択肢が表示されます。処理するストリームの種類に適した方を選択します。

![Image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

## Event Hub データ入力ストリームの作成
---
### Event Hubs の概要
イベント ハブは、拡張性の高いイベント インジェスターで、通常、Stream Analytics データ受信の最も一般的な方法です。多数のさまざまなデバイスやサービスからイベント ストリームを収集するように設計されています。Event Hubs と Stream Analytics が組み合わされることで、リアルタイム分析を行うためのエンドツーエンド ソリューションが顧客に提供されます。Event Hubs により、顧客はイベントを Azure にリアルタイムでフィードできるため、Stream Analytics ジョブはそれらをリアルタイムで処理できるようになります。たとえば、顧客は、Web クリック、センサーの読み取り、オンライン ログ イベントを Event Hubs に送信し、リアルタイムのフィルター処理、集計、結合に入力データ ストリームとして Event Hubs を使用するために Stream Analytics ジョブを作成できます。また、イベント ハブはデータ送信にも使用できます。Event Hubs を出力として使用する可能性のある例として、Stream Analytics ジョブの出力が別のストリーミング ジョブの入力になる場合があります。Event Hubs の詳細については、[Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs") のドキュメントをご覧ください。

### コンシューマー グループ
Stream Analytics ジョブの各入力は、独自の Event Hub コンシューマー グループを持つように構成する必要があります。ジョブに自己結合または複数の入力が含まれる場合、一部の入力は複数の閲覧者ダウンストリームによって読み取られる可能性があります。これにより、1 つのコンシューマー グループの総閲覧者数がコンシューマー グループあたり 5 人という Event Hub の制限を超えます。この場合は、クエリを、追加の Event Hubs 経由でルーティングされる複数のクエリや中間結果に分割する必要があります。Event Hub ごとに 20 個のコンシューマー グループという制限もある点に注意してください。詳細については、「[Event Hubs Programming Guide (Event Hubs のプログラミング ガイド)](https://msdn.microsoft.com/library/azure/dn789972.aspx "Event Hubs のプログラミング ガイド")」をご覧ください。

## Azure ポータルで Event Hub の入力を作成する例
---
次に、入力として Event Hub を構成する方法を順を追って説明します。Event Hub 入力の使用を開始するには、Event Hub に関する次の情報を収集する必要があります。

1. 入力の別名 – ジョブ クエリで参照されるわかりやすい名前の入力別名。
2. Service Bus 名前空間の名前。 
3. Event Hub の名前。
3. Event Hub のポリシー名。
4. (省略可能): Event Hub のコンシューマー グループ名。
	- イベント ハブからのデータを取り込むためのコンシューマー グループです。指定しないと、Stream Analytics ジョブは既定のコンシューマー グループを使用してイベント ハブからデータを取り込みます。Stream Analytics ジョブごとに個別のコンシューマー グループを使用することをお勧めします。
5. データ ストリームには、シリアル化形式が使用されます (Avro、CSV、JSON)。

最初に、Stream Analytics ジョブの [入力] ページから **[入力の追加]** を選択します。

![Image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

入力として Event Hub を選択します。

![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

次に、Event Hub に関して、以下に示されているように情報をフィールドに入力します。

![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

次に、データ ストリームに関してイベントのシリアル化形式が正しいかを検証します。

![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

**[完了]** を選択すると、Event Hub データ ストリーム入力が作成されます。

## BLOB ストレージ入力データ ストリームの作成
---
大量の非構造化データをクラウドに保存する場合は、BLOB ストレージを使用するとコスト効率の高いスケーラブルなソリューションが実現します。一般に、このデータは「保存された」データであると見なされます。データ ストリーム入力に対して BLOB が有用な例として、ログがシステムから既に取得され、解析される必要があり、それらから意味のあるデータを抽出する必要があるログ解析が挙げられます。また、保存された状態のデータ ウェアハウス データの解析などもあります。BLOB ストレージの詳細については、[BLOB ストレージ](http://azure.microsoft.com/services/storage/blobs/)のドキュメントをご覧ください。

次に、入力として BLOB ストレージを構成する方法を順を追って説明します。Azure BLOB ストレージの入力を使用するには、次の情報が必要です。

1. 入力の別名 – ジョブ クエリで参照されるわかりやすい名前の入力別名。
2. ストレージ アカウントがストリーミング ジョブとは異なるサブスクリプションにある場合、ストレージ アカウント名とストレージ アカウント キーが必要です。
3. コンテナーの名前。
4. ファイル名のプレフィックス。
5. データに使用されるシリアル化形式 (Avro、CSV、JSON)。

Stream Analytics ジョブの入力タブで、**[入力の追加]** をクリックし、既定オプションである **[データ ストリーム]** を選択します。![Image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

次に、**[BLOB ストレージ]** を選択します。

![Image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

次に、ストレージ アカウントに関して、以下に示されているように情報をフィールドに入力します。

![Image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

> [AZURE.NOTE][詳細設定の構成] ボックスをクリックすると、カスタマイズされたパスで読み取り BLOB のパス プレフィックス パターンを指定することができます。このフィールドが指定されていない場合、Stream Analytics はコンテナー内のすべての BLOB を読み取ります。

![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

次に、データの適切なシリアル化設定を選択します。JSON、CSV、および Avro から選択できます。

![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

**[完了]** を選択すると、BLOB ストレージ データ ストリーム入力が作成されます。

## BLOB ストレージ参照データ入力の作成
---
参照データ機能を使用するために、BLOB ストレージを利用できます。

次に、参照データ入力として BLOB ストレージを構成する方法を順を追って説明します。開始するには、次の情報が必要です。

1. 入力の別名 – ジョブ クエリで参照されるわかりやすい名前の入力別名。
2. ストレージ アカウントがストリーミング ジョブとは異なるサブスクリプションにある場合、ストレージ アカウント名とストレージ アカウント キーが必要です。
3. コンテナーの名前。
4. ファイル名のプレフィックス。
5. データに使用されるシリアル化形式 (CSV、JSON)。
6. パスのパターン。指定されたコンテナー内に BLOB を配置するために使用されるファイル パス。このパス内に、{date} と {time} の 2 つの変数の 1 つ以上のインスタンスを指定するように選択できます。


Stream Analytics ジョブの入力タブで、**[入力の追加]** をクリックし、既定オプションである **[参照データ]** を選択します。

![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

次に、BLOB ストレージとストレージ アカウントに関して、以下に示されているように情報をフィールドに入力します。

![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

下にスクロールして、BLOB を含むパス階層のプレフィックス パターンと、日付および時刻フィールドの形式を指定してください。

![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

データの適切なシリアル化設定を選択します。JSON、CSV、および Avro から選択できます。

![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)

**[完了]** を選択すると、BLOB ストレージ参照データ入力が作成されます。


## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO6-->