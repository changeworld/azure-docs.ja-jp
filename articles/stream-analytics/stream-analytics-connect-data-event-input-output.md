<properties 
	pageTitle="入力と出力への接続 | Microsoft Azure" 
	description="Stream Analytics ソリューションの入力ソースと出力ターゲットに接続して構成する方法について説明します。" 
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
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#入力と出力への接続
このドキュメントでは、Stream Analytics ソリューションの入力ソースと出力ターゲットを構成するさまざまな方法について説明します。

##SQL を出力として使用

実質的にリレーショナルであるデータや、データベースでホストされているコンテンツに依存するアプリケーションの出力として Azure SQL データベースを使用できます。

[http://azure.microsoft.com/services/sql-database/](http://azure.microsoft.com/services/sql-database/)

Azure SQL データベースの使用を開始するには、データベースに関する次の情報を用意する必要があります。

1. サーバー名
2. データベース名
3. 有効なユーザー名とパスワードの組み合わせ
4. 出力テーブル名

![図 1][graphic1]

ジョブの [出力] タブに移動し、[出力の追加] コマンドをクリックして [次へ] をクリックします。

![図 2][graphic2]


出力として [SQL データベース] を選択します。

![図 3][graphic3]

次のページで、データベースの情報を入力します。出力のエイリアスは、クエリの出力をこのデータベースにダイレクトするために、クエリ内で使用できる名前です。出力が 1 つの場合の既定のエイリアスは "output" です。

![図 4][graphic4]

使用しているものと同じサブスクリプション内に存在するデータベースを使用している場合は、[現在のサブスクリプションの SQL データベースを使用] を選択し、ドロップダウン リストからデータベースを選択できます。

![図 5][graphic5]

[次へ] をクリックして、この出力を追加します。2 つの操作が開始されます。最初に、出力を追加します。

![図 6][graphic6]

次に、接続をテストします。Azure Stream Analytics は、その SQL データベースに接続し、入力された資格情報が正しく、テーブルにアクセス可能であることを確認しようとします。それが完了すると、次の 2 つのメッセージのいずれかが表示されます。

![図 7][graphic7]

![図 8][graphic8]



後者の場合は、[詳細] をクリックすると、エラーの詳細が表示されます。

![図 9][graphic9]

この例では、入力された資格情報が正しくありませんでした。資格情報を修正し、[接続のテスト] ボタンをクリックしてもう一度テストを実行できます。

![図 10][graphic10]

##イベント ハブの使用

###概要
 
イベント ハブは、拡張性の高いイベント インジェスターで、通常、Stream Analytics データ受信の最も一般的な方法です。多数のさまざまなデバイスやサービスからイベント ストリームを収集するように設計されています。イベント ハブと Stream Analytics の連携により、顧客にはリアルタイム分析のためのエンド ツー エンド ソリューションが提供されます。イベント ハブを使用すると、顧客はイベントを Azure へリアルタイムにフィードでき、Stream Analytics ジョブがリアルタイムでそれらを処理できます。たとえば、顧客は、Web クリック、センサーの読み取り、オンライン ログ イベントをイベント ハブに発行し、リアルタイムのフィルター処理、集計、結合に入力データ ストリームとしてイベント ハブを使用するために Stream Analytics ジョブを作成できます。また、イベント ハブはデータ送信にも使用できます。イベント ハブを出力として使用する最も一般的な例として、Stream Analytics ジョブの出力が別のストリーミング ジョブの入力となる場合があります。

###コンシューマー グループ
Stream Analytics ジョブの各入力は、独自のイベント ハブ コンシューマー グループを持つように構成する必要があります。ジョブに自己結合または複数の出力が含まれる場合、一部の入力は複数の閲覧者によって読み取られる可能性があります。これにより、1 つのコンシューマー グループの総閲覧者数がコンシューマー グループあたり 5 人というイベント ハブの制限を超えます。この場合は、クエリを、追加のイベント ハブ経由でルーティングされる複数のクエリや中間結果に分割する必要があります。イベント ハブごとに 20 個のコンシューマー グループという制限もある点に注意してください。詳細については、「Event Hub プログラミング ガイド」を参照してください。

 
###パラメーター
 
イベント ハブのデータ ストリーム用に顧客が構成する必要のあるパラメーターがいくつかあります。特に記載のないかぎり、これらのパラメーターは、イベント ハブの入力と出力の両方のデータ ストリームに適用されます。

1. Service Bus 名前空間: イベント ハブの Service Bus 名前空間。Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。 
2. イベント ハブ名: イベント ハブの名前。これは、新しいイベント ハブの作成時に指定した名前です。 
3. イベント ハブ ポリシー名: イベント ハブにアクセスするための共有アクセス ポリシーの名前。共有アクセス ポリシーは、[構成] タブでイベント ハブに対して構成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、およびアクセス キーが含まれます。
4. イベント ハブ ポリシー キー: イベント ハブにアクセスするための共有アクセス ポリシーのプライマリ キーとセカンダリ キー。  
5. イベント ハブ コンシューマー グループ: イベント ハブ入力のオプション パラメーター。イベント ハブからのデータを取り込むためのコンシューマー グループです。指定しないと、Stream Analytics ジョブは既定のコンシューマー グループを使用してイベント ハブからデータを取り込みます。Stream Analytics ジョブごとに個別のコンシューマー グループを使用することをお勧めします。

パーティション キー列: イベント ハブ出力のオプションのパラメーター。イベント ハブ出力のパーティション キーとして使用されるデータ属性列です。

##Azure テーブル出力の使用

スキーマへの制約が少ない構造化データ向けに Azure テーブルを使用することができます。Azure テーブル ストレージを使用すると、永続化と効率的な取得のためにデータを保持できます。詳細については、[Azure Storage の概要](http://azure.microsoft.com/storage-introduction/)に関するページを参照してください。
 
Azure テーブル ストレージの使用を開始するには、テーブルに関する次の情報を用意する必要があります。

1. ストレージ アカウント名 (このストレージがストリーミング ジョブとは異なるサブスクリプションにある場合)
2. ストレージ アカウント キー (このストレージがストリーミング ジョブとは異なるサブスクリプションにある場合)
3. 出力テーブル名 (ない場合は作成されます)
4. パーティション キー (必須)
5.   行キー (現在これは必須ですが、お客様のフィードバックに従って省略可能にする予定です)

パーティション キーと行キーの設計を向上するには、「[Azure テーブル ストレージのスケーラブルなパーティション分割方法の設計](https://msdn.microsoft.com/library/azure/hh508997.aspx)」を参照してください。


![図 11][graphic11]


ジョブの [出力] タブに移動し、[出力の追加] コマンドをクリックして [次へ] をクリックします。


![図 12][graphic12]


出力として [テーブル ストレージ] を選択します。


![図 13][graphic13]


次のページで、Azure テーブル情報を入力します。出力のエイリアスは、クエリの出力をこのテーブルにダイレクトするために、クエリ内で使用できる名前です。


![図 14][graphic14] ![図 15][graphic15]

バッチ サイズは、バッチ操作の対象となるレコード数です。使い慣れていない場合は既定値のままにします。詳細については、[https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) を参照してください。


使用しているものと同じサブスクリプション内に存在する Azure Storage を使用している場合は、[現在のサブスクリプションのストレージ アカウントを使用] を選択し、ドロップダウン リストからストレージ アカウントを選択できます。

[次へ] をクリックして、この出力を追加します。2 つの操作が開始されます。最初に、出力を追加します。

![図 16][graphic16]

次に、接続をテストします。Azure Stream Analytics は、そのストレージ アカウントに接続し、入力された資格情報が正しいことを確認しようとします。それが完了すると、次の 2 つのメッセージのいずれかが表示されます。

![図 17][graphic17]

後者の場合は、[詳細] をクリックすると、エラーの詳細が表示されます。

![図 18][graphic18]

この例では、入力された資格情報が正しくありませんでした。資格情報を修正し、[接続のテスト] ボタンをクリックしてもう一度テストを実行できます。

![図 19][graphic19]

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-jp/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-input-output/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-input-output/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-input-output/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-input-output/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-input-output/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-input-output/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-input-output/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-input-output/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-input-output/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-input-output/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-input-output/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-input-output/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-input-output/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-input-output/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-input-output/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-input-output/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-input-output/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-input-output/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-input-output/19-stream-analytics-connect-data-event-input-output.png
 

<!---HONumber=62-->