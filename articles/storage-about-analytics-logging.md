<properties 
	pageTitle="Storage Analytics Logging について" 
	description="Storage Analytics ログと認証済みの要求の使用方法、ログの保存方法について説明します。 " 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Storage Analytics Logging について

## 概要
Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。要求は、ベスト エフォートでログに記録されます。

Storage Analytics を使用するには、監視するサービスごとに Storage Analytics を個別に有効にする必要があります。Storage Analytics は、[Azure の管理ポータル](https://manage.windowsazure.com/)から有効にできます。詳細については、「[ストレージ アカウントの監視方法](../how-to-monitor-a-storage-account)」をご覧ください。また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。各サービスに対して Storage Analytics を有効にするには、Get Blob Service Properties、Get Queue Service Properties、Get Table Service Properties の各操作を使用します。

ログ エントリが作成されるのは、ストレージ サービス アクティビティが存在する場合に限られます。たとえば、ストレージ アカウントの BLOB サービスにはアクティビティが存在するが、Table サービスや Queue サービスにはアクティビティが存在しない場合、BLOB サービスに関連したログだけが作成されます。

## 認証済み要求のログ記録
次のタイプの認証済み要求が記録されます。

- 成功した要求

- 失敗した要求 (タイムアウト、帯域幅調整、ネットワーク、承認などに関する各種エラー)

- Shared Access Signature (SAS) を使用した要求 (失敗した要求と成功した要求を含む)

- データの分析要求

Storage Analytics そのものによる要求 (ログの作成/削除など) は記録されません。ログに記録されるデータの一覧については、「[Storage Analytics によって記録される操作やステータス メッセージ](https://msdn.microsoft.com/library/hh343260.aspx)」と「[Storage Analytics のログの形式](https://msdn.microsoft.com/library/hh343259.aspx)」トピックに記載されています。

## 匿名要求のログ記録
次のタイプの匿名要求が記録されます。

- 成功した要求

- サーバー エラー

- クライアントとサーバーの両方のタイムアウト エラー

- エラー コード 304 (Not Modified) で失敗した GET 要求

その他の失敗した匿名要求は一切記録されません。ログに記録されるデータの一覧については、「[Storage Analytics によって記録される操作やステータス メッセージ](https://msdn.microsoft.com/library/hh343260.aspx)」と「[Storage Analytics のログの形式](](https://msdn.microsoft.com/library/hh343259.aspx))」トピックに記載されています。

## ログの保存方法
すべてのログは、Storage Analytics をストレージ アカウントに対して有効にしたときに自動的に作成される $logs という名前のコンテナー内のブロック BLOB に格納されます。$logs コンテナーは、ストレージ アカウントの BLOB 名前空間に置かれています (例:  `http://<accountname>.blob.core.windows.net/$logs`)。Storage Analytics を有効にした後は、このコンテナーを削除することはできません。ただし、コンテナーの内容を削除することはできます。

>[Azure.NOTE] コンテナーの一覧作成操作 ([ListContainers](https://msdn.microsoft.com/library/ee758348.aspx) メソッドなど) を実行しても $logs コンテナーは表示されません。直接アクセスする必要があります。たとえば、`$logs` コンテナー内の BLOB には [ListBlobs](https://msdn.microsoft.com/library/ee772878.aspx) メソッドでアクセスできます。
要求がログに記録されると、Storage Analytics は、中間結果をブロックとしてアップロードします。これらのブロックを定期的にコミットし、BLOB として利用できるようにします。

同じ時間内に作成されたログについて、重複するレコードが存在する場合があります。レコードが重複しているかどうかは、**RequestId** と **Operation** の数をチェックすることによって確認できます。

## ログの名前付け規則
各ログは次の形式で出力されます。

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log 

次の表は、ログ名を構成する各属性の説明です。

| 属性      	| 説明                                                                                                                                                                                	|
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> 	| ストレージ サービスの名前。例: blob、table、または queue                                                                                                                        	|
| YYYY           	| ログの 4 桁の年。次に例を示します。2011                                                                                                                                         	|
| MM             	| ログの 2 桁の月。次に例を示します。07                                                                                                                                           	|
| DD             	| ログの 2 桁の日。次に例を示します。07                                                                                                                                           	|
| hh             	| ログの開始時刻 (時) を示す 2 桁の数字 (24 時間制 UTC 形式)。次に例を示します。18                                                                                   	|
| mm             	| ログの開始時刻 (分) を示す 2 桁の数字。 >[AZURE.NOTE]現在のバージョンの Storage Analytics ではこの値はサポートされず、常に 00 になります。 	|
| <counter>      	| 1 時間おきにストレージ サービスに対して生成されるログ BLOB の数を示す 0 から始まる 6 桁のカウンター。このカウンターの初期値は 000000 です。次に例を示します。000001   	|

以上の例をすべて組み合わせたサンプルのログ名は、次のようになります。

    blob/2011/07/31/1800/000001.log

このログにアクセスするための URI の例を次に示します。

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log 

ストレージ要求がログに記録されるとき、生成されるログの名前は、要求された操作が完了した時刻が基準となります。たとえば、GetBlob 要求が 2011 年 7 月 31 日の午後 6 時 30 分に完了した場合、書き込まれるログのプレフィックスは次のようになります。`blob/2011/07/31/1800/`

## ログのメタデータ
すべてのログ BLOB はメタデータと共に格納されます。このメタデータを使って、BLOB に含まれるログ データを特定できます。それぞれのメタデータ属性について次の表で説明します。

| 属性  	| 説明                                                                                                                                                                                                                                               	|
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType    	| 読み取り、書き込み、削除の各操作に関連した情報がログに含まれているかどうかを表します。この値には、操作の種類が 1 つだけ含まれている場合もあれば、3 つすべてがコンマ区切りで記録されている場合もあります。   例 1: write 例 2: read,write 例 3: read,write,delete 	|
| StartTime  	| ログに含まれる最も古いエントリの時刻です (YYYY-MM-DDThh:mm:ssZ 形式)。次に例を示します。2011-07-31T18:21:46Z                                                                                                                                          	|
| EndTime    	| ログに含まれる最も新しいエントリの時刻です (YYYY-MM-DDThh:mm:ssZ 形式)。次に例を示します。2011-07-31T18:22:09Z                                                                                                                                            	|
| LogVersion 	| ログのフォーマットのバージョン。現在サポートされている値: 1.0                                                                                                                                                                                 	|

以上の例を使用したサンプル メタデータを次に示します。

- LogType=write 

- StartTime=2011-07-31T18:21:46Z 

- EndTime=2011-07-31T18:22:09Z 

- LogVersion=1.0 

## ログ データへのアクセス

`$logs` コンテナー内のすべてのデータは、BLOB サービス API を使用してアクセスできます (Azure マネージ ライブラリで提供される .NET API など)。ストレージ アカウント管理者は、ログの読み取りと削除を行うことができますが、ログの作成または更新を行うことはできません。ログの照会には、ログのメタデータとログの名前の両方を使用できます。特定の時間のログが順番に並んでいない場合もありますが、メタデータには、ログに含まれているエントリのタイムスパンが必ず記録されています。したがって、ログの名前とメタデータを組み合わせれば、特定のログを検索できます。

## 次のステップ

[Storage Analytics のログの形式 ](https://msdn.microsoft.com/library/hh343259.aspx)

[Storage Analytics によって記録される操作やステータス メッセージ ](https://msdn.microsoft.com/library/hh343260.aspx)

[Storage Analytics Metrics について](https://msdn.microsoft.com/library/hh343258.aspx)
<!--HONumber=47-->
