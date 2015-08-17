<properties 
	pageTitle="Stream Analytics 出力の作成 | Microsoft Azure" 
	description="Stream Analytics ソリューションの出力ターゲットに接続して構成する方法について説明します。" 
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

# Stream Analytics 出力の作成

## Stream Analytics 出力について ##
---
Stream Analytics ジョブを作成するときの考慮事項として、ジョブの出力がどのように使用されるかということが挙げられます。データ変換のコンシューマーには、Stream Analytics ジョブの結果がどのように表示されますか。 出力の解析にどのツールを使用しますか。 データの保存またはウェアハウジングは必要ですか。

Azure Stream Analytics には、ジョブ出力を保存および表示するための 7 つの異なるメソッドが用意されています。SQL Database、BLOB ストレージ、Event Hubs、Service Bus キュー、Service Bus トピック、Power BI、およびテーブル ストレージです。これにより、データのウェアハウジングやその他の目的のためのジョブ出力の使用や保存において柔軟性を得たり、ジョブ出力を簡単に表示したりすることができます。

## 出力としての SQL Database の使用 ##
---
SQL Database は、本質的にリレーショナルであるデータや、リレーショナル データベースにホストされているコンテンツに依存するアプリケーションの出力として使用できます。Azure SQL Databases の詳細については、[Azure SQL Database](http://azure.microsoft.com/services/sql-database/) に関するページをご覧ください。

### パラメーター ###

SQL Database を使用するには、SQL Database に関する次の情報が必要です。

1. サーバー名
2. データベース名
3. 有効なユーザー名とパスワードの組み合わせ
4. 出力テーブル名このテーブルは既に存在している必要があります。ジョブでは作成されません。

### 出力としての SQL Database の追加 ###

![図 1][graphic1]

ジョブの [出力] タブに移動し、**[出力の追加]** コマンドをクリックして [次へ] をクリックします。

![図 2][graphic2]

出力として **[SQL Database]** を選択します。

![図 3][graphic3]

次のページで、データベースの情報を入力します。出力エイリアスは、クエリの出力をこのデータベースに出力するためにクエリで使用されるわかりやすい名前です。単一の出力しかない場合の既定のエイリアスは "output" です。

![図 4][graphic4]

データベースが Stream Analytics ジョブと同じサブスクリプション内に存在する場合、現在のサブスクリプションから SQL Database を使用するように選択するオプションを使用できます。次に、ドロップダウン リストからデータベースを選択します。

![図 5][graphic5]

[次へ] をクリックして、この出力を追加します。2 つの操作が開始されます。最初の操作で、出力が追加されます。

![図 6][graphic6]

2 番目の操作で、接続がテストされます。Azure Stream Analytics は、その SQL Database に接続し、入力された資格情報が正しく、テーブルにアクセス可能であることを確認しようとします。

## 出力としての BLOB ストレージの使用 ##
---
Azure BLOB ストレージとその使用法の説明については、[BLOB ストレージの使用方法](./articles/storage/storage-dotnet-how-to-use-blobs.md)に関するページをご覧ください。

### パラメーター ###

BLOB ストレージ出力を使用するには、次の情報が必要です。

1. ストレージ アカウントがストリーミング ジョブとは異なるサブスクリプションにある場合、ストレージ アカウント名とストレージ アカウント キーを入力するためのフィールドが表示されます。
2. コンテナーの名前。
3. ファイル名のプレフィックス。
4. データに使用されるシリアル化形式 (Avro、CSV、JSON)。

### 出力としての BLOB ストレージの追加 ###

BLOB ストレージへの出力を選択します。

![図 20][graphic20]

次のように、詳細を指定します。

![図 21][graphic21]

## 出力としての Event Hub の使用 ##
---
### 概要 ###
 
イベント ハブは、拡張性の高いイベント インジェスターで、通常、Stream Analytics データ受信の最も一般的な方法です。また、多数のイベントを確実に処理できるため、完璧なジョブ出力でもあります。Event Hub を出力として使用する一例として、Stream Analytics ジョブの出力が別のストリーミング ジョブの入力となる場合があります。Event Hubs の詳細については、[Event Hubs](https://azure.microsoft.com/services/event-hubs/ "Event Hubs") のポータルにアクセスしてください。
 
### パラメーター ###

Event Hub のデータ ストリームを構成するために必要なパラメーターがいくつかあります。

1. Service Bus 名前空間: イベント ハブの Service Bus 名前空間。Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。新しい Event Hub を作成すると、Service Bus の名前空間も作成されます。 
2. イベント ハブ名: イベント ハブの名前。これは、新しい Event Hub の作成時に指定した名前です。 
3. イベント ハブ ポリシー名: イベント ハブにアクセスするための共有アクセス ポリシーの名前。共有アクセス ポリシーは、[構成] タブでイベント ハブに対して構成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、および生成されたアクセス キーが含まれます。
4. イベント ハブ ポリシー キー: イベント ハブにアクセスするための共有アクセス ポリシーのプライマリ キーとセカンダリ キー。  
5. パーティション キー列: イベント ハブ出力のオプションのパラメーター。この列には、Event Hub 出力のパーティション キーが含まれます。

### 出力としての Event Hub の追加 ###

1. ページの上部にある **[出力]** をクリックしてから、**[出力の追加]** をクリックします。Event Hub を出力オプションとして選択し、ウィンドウの下部にある右側のボタンをクリックします。

    ![図 38][graphic38]

2. 出力のフィールドに関連情報を入力し、完了したら、ウィンドウの下部にある右側のボタンをクリックして処理を続けます。

    ![図 36][graphic36]

3. イベントのシリアル化形式、エンコードの種類、および形式が適切な値に設定されていることを検証し、チェック ボックスをクリックしてアクションを完了します。

    ![図 37][graphic37]

## 出力としての Power BI の使用 ##
---
### 概要 ###
Stream Analytics ジョブの出力として Power BI を使用し、Stream Analytics ユーザーに豊富な視覚化エクスペリエンスを提供できます。この機能は、操作ダッシュボード、レポート生成、およびメトリック ドリブン レポート作成に使用できます。Power BI の詳細については、[Power BI](https://powerbi.microsoft.com/) のサイトをご覧ください。

### パラメーター ###

Power BI 出力を構成するために必要なパラメーターがいくつかあります。

1. 出力エイリアス – 簡単に参照できるわかりやすい名前の出力エイリアス。この出力のエイリアスは、ジョブに複数の出力を設定する場合に特に便利です。その場合、このエイリアスはクエリで参照されます。たとえば、出力エイリアス値として “OutPbi” を使用します。
2. データセット名 - Power BI 出力に使用するために必要なデータセット名を指定します。たとえば、“pbidemo” を使用します。
2. テーブル名 - Power BI 出力のデータセットの下にテーブル名を入力します。たとえば、“pbidemo” を使用します。**現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に 1 つのテーブルのみを保持できます。**

### 出力としての Power BI の追加 ###

1.  ページの上部にある **[出力]** をクリックしてから、**[出力の追加]** をクリックします。出力オプションとして Power BI を選択します。

    ![図 22][graphic22]

2.  次のような画面が表示されます。

    ![図 23][graphic23]

3.  このステップでは、Power BI の出力を承認するための、職場または学校のアカウントを指定します。Power BI を使用するためのサインアップをまだ行っていない場合は、**[今すぐサインアップ]** を選択します。
4.  次のような画面が表示されます。

    ![図 24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	**[OK]**、**[接続のテスト]** の順にクリックすると、出力の構成は完了です。


## 出力のための Azure テーブル ストレージの使用 ##
---
テーブル ストレージは高度な可用性と拡張性を備え、アプリケーションを需要に応じて自動的に拡張できます。テーブル ストレージは Microsoft の NoSQL キー/属性ストアであり、スキーマに対する制約を抑えながら、構造化されたデータに活用できます。Azure テーブル ストレージを使用すると、永続化と効率的な取得のためにデータを保持できます。Azure テーブル ストレージの詳細については、[Azure テーブル ストレージ](./articles/storage/storage-introduction.md)に関するページをご覧ください。

### パラメーター ###

Azure テーブル ストレージを使用するには、次の情報が必要です。

1. ストレージ アカウント名 (このストレージがストリーミング ジョブとは異なるサブスクリプションにある場合)。
2. ストレージ アカウント キー (このストレージがストリーミング ジョブとは異なるサブスクリプションにある場合)。
3. 出力テーブル名 (ない場合は作成されます)
4. パーティション キー (必須)
5. 行キー

パーティション キーと行キーの設計を向上するには、「[Azure テーブル ストレージのスケーラブルなパーティション分割方法の設計](https://msdn.microsoft.com/library/azure/hh508997.aspx)」を参照してください。

### 出力としての Azure テーブル ストレージの追加 ###

![図 11][graphic11]

ジョブの [出力] タブに移動し、**[出力の追加]** コマンドをクリックして [次へ] をクリックします。

![図 12][graphic12]

出力として **[テーブル ストレージ]** を選択します。

![図 13][graphic13]

次のページで、Azure テーブル情報を入力します。出力のエイリアスは、クエリの出力をこのテーブルに出力するために、クエリ内で使用できる名前です。

![図 14][graphic14]

![図 15][graphic15]

バッチ サイズは、バッチ操作のレコードの数です。通常、大部分のジョブには既定値で十分です。この設定の変更の詳細については、「[TableBatchOperation Class (TableBatchOperation クラス)](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)」をご覧ください。

Azure Storage アカウントが、ジョブの作成に使用されているものと同じサブスクリプション内に存在する場合、[現在のサブスクリプションのストレージ アカウントを使用] を選択し、ドロップダウン リストからストレージ アカウントを選択します。

[次へ] をクリックして、この出力を追加します。2 つの操作が開始されます。最初の操作で、出力が追加されます。

![図 16][graphic16]

次に、接続をテストします。Azure Stream Analytics は、そのストレージ アカウントに接続し、入力された資格情報が正しいことを確認しようとします。

## Service Bus キュー ##
---
### Service Bus キューの概念の概要 ###
Service Bus キューでは、コンシューマーが競合している場合のメッセージ配信に先入先出法 (FIFO) を使用します。通常、メッセージはキューに追加された順番に受信され、処理されます。このとき、メッセージを受信して処理できるメッセージ コンシューマーは、メッセージ 1 件につき 1 つだけです。

Service Bus キューの詳細については、[サービス バス キュー、トピック、およびサブスクリプション](https://msdn.microsoft.com/library/azure/hh367516.aspx "サービス バス キュー、トピック、およびサブスクリプション")および[Service Bus キューの概要](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "Service Bus キューの概要")をご覧ください。

### パラメーター ###

Service Bus キューを使用するには、次の情報が必要です。

1. 出力エイリアス – 簡単に参照できるわかりやすい名前の出力エイリアス。この出力のエイリアスは、ジョブに複数の出力を設定する場合に特に便利です。その場合、このエイリアスはジョブ クエリで参照されます。
2. 名前空間および Service Bus の名前。
3. キュー名 - キューは、Event Hubs やトピックと類似するメッセージ エンティティです。多数のさまざまなデバイスやサービスからイベント ストリームを収集するように設計されています。キューが作成されるときに、特定の名前も付けられます。
4. データに使用されるシリアル化形式 (Avro、CSV、JSON)。

### 出力としての Service Bus キューの追加 ###

![図 31][graphic31]

次のように詳細を指定して、[次へ] を選択します。

![図 32][graphic32]

データ形式とシリアル化が正しいことを確認します。

![図 33][graphic33]

## Service Bus トピック ##
---
### Service Bus トピックの概念の概要 ###
Service Bus キューには、送信者から受信者への 1 対 1 の通信メソッドが備わっていますが、Service Bus トピックでは 1 対多の形式の通信を行うことができます。

Service Bus トピックの詳細については、「[Service Bus のキュー、トピック、サブスクリプション](https://msdn.microsoft.com/library/azure/hh367516.aspx "サービス バス キュー、トピック、およびサブスクリプション")」および「[An Introduction to Service Bus Topics (Service Bus トピックの概要)](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "Service Bus トピックの概要")」をご覧ください。

### パラメーター ###

Service Bus トピックの出力を使用するには、次の情報が必要です。

1. 出力エイリアス – 簡単に参照できるわかりやすい名前の出力エイリアス。この出力のエイリアスは、ジョブに複数の出力を設定する場合に特に便利です。その場合、このエイリアスはクエリで参照されます。
2. 名前空間および Service Bus の名前。
3. トピック名 - トピックは、Event Hubs やトピックと類似するメッセージ エンティティです。多数のさまざまなデバイスやサービスからイベント ストリームを収集するように設計されています。トピックが作成されるときに、特定の名前も付けられます。トピックに送信されるメッセージはサブスクリプションが作成されなければ使用できないため、トピックの下に 1 つ以上のサブスクリプションがあることを確認してください。
4. データに使用されるシリアル化形式 (Avro、CSV、JSON)。

### 出力としての Service Bus トピックの追加 ###

Service Bus トピックへの出力を選択します。

![図 34][graphic34]

次のように詳細を指定して、[次へ] を選択します。

![図 35][graphic35]

データ形式とシリアル化が正しいことを確認します。

![図 33][graphic33]


## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=August15_HO6-->