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
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>

# Stream Analytics 入力の作成

## Stream Analytics 入力について
---
Stream Analytics 入力はデータ ソースへの接続として定義されます。Stream Analytics では、ジョブ サブスクリプションの内外からの Azure ソース Event Hub と BLOB ストレージとの最上位の統合が行われます。データがそのデータ ソースに送信されると、Stream Analytics ジョブによって使用され、リアルタイムで処理されます。入力は 2 つの個別のタイプであるデータ ストリーム入力と参照データ入力に分けられます。

## データ ストリーム入力
---
Stream Analytics のジョブには、ジョブで使用および変換される少なくとも 1 つのデータ ストリームの入力を含める必要があります。Azure BLOB ストレージと Azure Event Hubs は、データ ストリーム入力ソースとしてサポートされます。Azure イベント ハブは複数のデバイスとソースから、ソーシャル メディア アクティビティ フィード、株式取引情報、センサーからのデータなど、イベント ストリームを回収するために使用されます。または、バルク データを取り込むための入力ソースとして Azure BLOB ストレージを使用することもできます。

## 参照データ入力
---
Stream Analytics は、参照データと呼ばれる 2 番目のタイプの入力をサポートします。これは一般に関連付けと参照を実行するために使用される補足データであり、通常は静的データまたは変更頻度の低いデータです。現在、Azure BLOB ストレージは参照データをサポートする唯一の入力ソースです。参照データ ソースの BLOB のサイズは、最大で 50 MB に制限されています。

## Event Hub データ入力ストリームの作成
---
### Event Hubs の概要
イベント ハブは拡張性の高いイベント インジェスターであり、Stream Analytics ジョブにデータを取り込むための最も一般的な方法です。Event Hubs と Stream Analytics が組み合わされることで、リアルタイム分析を行うためのエンドツーエンド ソリューションが顧客に提供されます。Event Hubs により、顧客はイベントを Azure にリアルタイムでフィードできるため、Stream Analytics ジョブはそれらをリアルタイムで処理できるようになります。たとえば、顧客は、Web クリック、センサーの読み取り、オンライン ログ イベントを Event Hubs に送信し、リアルタイムのフィルター処理、集計、結合に入力データ ストリームとして Event Hubs を使用するために Stream Analytics ジョブを作成できます。また、イベント ハブはデータ送信にも使用できます。Event Hubs の詳細については、[Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs") のドキュメントをご覧ください。

### コンシューマー グループ
Stream Analytics イベント ハブの各入力は、独自のコンシューマー グループを持つように構成する必要があります。ジョブに自己結合または複数の入力が含まれる場合、一部の入力は複数の閲覧者ダウンストリームによって読み取られる可能性があります。これは 1 つのコンシューマー グループの閲覧者数に影響を与えます。閲覧者の数を各パーティションのコンシューマー グループ別に 5 人とするイベント ハブの上限を回避するには、Stream Analytics ジョブごとにコンシューマー グループを指定するのが最良事例となります。Event Hub ごとに 20 個のコンシューマー グループという制限もある点に注意してください。詳細については、「[イベント ハブのプログラミング ガイド](https://msdn.microsoft.com/library/azure/dn789972.aspx "Event Hubs のプログラミング ガイド")」をご覧ください。

## イベント ハブ 入力データ ストリームの作成
---
### データ ストリームの入力としてデータ ストリームを追加する  ###

1. Stream Analytics ジョブの入力タブで、**[入力の追加]** をクリックし、既定オプションである **[データ ストリーム]** を選択し、右ボタンをクリックします。

    ![Image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. 次に、**[イベント ハブ]** を選択します。

    ![image6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

3. 次のフィールドを入力するか、選択し、右ボタンをクリックします。

    - **入力のエイリアス**: この入力を参照するジョブ クエリで使用されるわかりやすい名前。  
    - **Service Bus 名前空間**: Service Bus 名前空間は一連のメッセージング エンティティのコンテナーです。新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。  
    - **イベント ハブ**: イベント ハブ入力の名前。  
    - **イベント ハブのポリシー名**: [イベント ハブの構成] タブで作成できる共有アクセス ポリシー。各共有アクセス ポリシーには、名前、設定したアクセス許可、およびアクセス キーが含まれます。  
    - **イベント ハブのコンシューマー グループ** (任意): イベント ハブからのデータを取り込むためのコンシューマー グループです。指定しないと、Stream Analytics ジョブは既定のコンシューマー グループを使用してイベント ハブからデータを取り込みます。Stream Analytics ジョブごとに個別のコンシューマー グループを使用することをお勧めします。  

    ![image7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

4. 次の設定を指定できます。

    - **イベントのシリアル化形式**: クエリを予想どおりに動作させるには、入ってくるデータ ストリームに使用しているシリアル化形式 (JSON、CSV、Avro) が Stream Analytics で認識される必要があります。  
    - **エンコード**: 現在のところ、UTF-8 が唯一サポートされているエンコード形式です。  

    ![image8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

5. チェック ボタンをクリックしてウィザードを完了し、Stream Analytics からイベント ハブに正常に接続できることを確認します。

## BLOB ストレージ データ ストリーム入力の作成
---
大量の非構造化データをクラウドに保存する場合は、BLOB ストレージを使用するとコスト効率の高いスケーラブルなソリューションが実現します。BLOB ストレージのデータは一般的に「休息中の」データとして見なされますが、Stream Analytics でデータ ストリームとして処理できます。BLOB ストレージと Stream Analytics の一般的なシナリオには、製品利用統計情報をシステムから取得し、解析し、処理し、意味のあるデータを抽出するログ処理があります。Stream Analytics の BLOB ストレージ イベントの既定のタイムスタンプは BLOB が作成されたタイムスタンプであることに注意してください。イベント ペイロードのタイムスタンプを利用してデータを処理するには、[TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。BLOB ストレージの詳細については、[BLOB ストレージ](http://azure.microsoft.com/services/storage/blobs/)のドキュメントをご覧ください。

### BLOB ストレージをデータ ストリーム入力として追加する  ###

1. Stream Analytics ジョブの入力タブで、**[入力の追加]** をクリックし、既定オプションである **[データ ストリーム]** を選択し、右ボタンをクリックします。

    ![Image1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. **[BLOB ストレージ]** を選択し、右ボタンをクリックします。

    ![Image2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

3. 次のフィールドを入力または選択します。

    - **入力のエイリアス**: この入力を参照するジョブ クエリで使用されるわかりやすい名前。  
    - **ストレージ アカウント**: ストレージ アカウントがストリーミング ジョブとは異なるサブスクリプションにある場合、ストレージ アカウント名とストレージ アカウント キーが必要です。  
    - **ストレージ コンテナー**: コンテナーにより、Microsoft Azure BLOB サービスに格納される BLOB が論理的にグループ化されます。BLOB を BLOB サービスにアップロードするとき、その BLOB のコンテナーを指定する必要があります。  

    ![Image3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

4. オプションの **[詳細設定の構成]** ボックスをクリックすると、カスタマイズされたパスで読み取り BLOB のパス プレフィックス パターンを設定できます。このフィールドが指定されていない場合、Stream Analytics はコンテナー内のすべての BLOB を読み取ります。

    ![image4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

5. 次の設定を選択します。

    - **イベントのシリアル化形式**: クエリを予想どおりに動作させるには、入ってくるデータ ストリームに使用しているシリアル化形式 (JSON、CSV、Avro) が Stream Analytics で認識される必要があります。  
    - **エンコード**: 現在のところ、UTF-8 が唯一サポートされているエンコード形式です。  


    ![image5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

6. チェック ボタンをクリックしてウィザードを完了し、Stream Analytics から BLOB ストレージ アカウントに正常に接続できることを確認します。

## BLOB ストレージ参照データの作成
---
BLOB ストレージを使用し、Stream Analytics ジョブの参照データを定義できます。これは検索またはデータの相互関連付けに使用される静的データまたはゆっくり変化するデータです。参照データの更新をサポートするには、{date} および {time} トークンを使用する入力構成でパス パターンを指定します。Stream Analytics はこのパス パターンに基づいて参照データ定義を更新します。たとえば、日付形式が「YYYY-MM-DD」で、時刻形式が「HH:mm」の `"/sample/{date}/{time}/products.csv"` は、更新された BLOB `"/sample/2015-04-16/17:30/products.csv"` を UTC 時間帯の 2015 年 4 月 16 日の午後 5:30 に回収するように Stream Analytics に通知します。

> [AZURE.NOTE]現在、Stream Analytics ジョブは、時刻が、BLOB 名でエンコードされた時刻と一致する場合にのみ参照 BLOB 更新データを検索します。たとえば、ジョブで、UTC タイム ゾーンの 2015 年 4 月 16 日の 5:30 PM から 5:30:59.999999999 PM の間に /sample/2015-04-16/17:30/products.csv が検索されます。クロックが 5:31PM になると、/sample/2015-04-16/17:30/products.csv の検索は停止され、/sample/2015-04-16/17:31/products.csv の検索が開始されます。

以前の参照データ BLOB が考慮される唯一の時間は、ジョブが開始される時点です。その時点では、ジョブの開始時刻よりも以前の値の、その名前でエンコードされた最新の日付/時刻を持つ BLOB がジョブによって検索されます (ジョブの開始時刻以前からの最新の参照データ BLOB)。これは、ジョブの開始時に空ではない参照データ セットが必ず存在するようにするために実行されます。それが見つからない場合、ジョブは失敗し、診断通知がユーザーに表示されます。

### BLOB ストレージを参照データとして追加する  ###

1. Stream Analytics ジョブの入力タブで、**[入力の追加]** をクリックし、**[参照データ]** を選択し、右ボタンをクリックします。

    ![image9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

2.	次のフィールドを入力または選択します。

    - **入力のエイリアス**: この入力を参照するジョブ クエリで使用されるわかりやすい名前。  
    - **ストレージ アカウント**: ストレージ アカウントがストリーミング ジョブとは異なるサブスクリプションにある場合、ストレージ アカウント名とストレージ アカウント キーが必要です。  
    - **ストレージ コンテナー**: コンテナーにより、Microsoft Azure BLOB サービスに格納される BLOB が論理的にグループ化されます。BLOB を BLOB サービスにアップロードするとき、その BLOB のコンテナーを指定する必要があります。  
    - **パス パターン**: 指定されたコンテナー内に BLOB を配置するために使用されるファイル パス。このパス内に、{date} と {time} の 2 つの変数のいずれかまたは両方のインスタンスを指定できます。  

    ![image10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

3. 次の設定を選択します。

    - **イベントのシリアル化形式**: クエリを予想どおりに動作させるには、入ってくるデータ ストリームに使用しているシリアル化形式 (JSON、CSV、Avro) が Stream Analytics で認識される必要があります。  
    - **エンコード**: 現在のところ、UTF-8 が唯一サポートされているエンコード形式です。  

    ![image12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

4.	チェック ボタンをクリックしてウィザードを完了し、Stream Analytics から BLOB ストレージ アカウントに正常に接続できることを確認します。

    ![image11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)


## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-jp/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO3-->