<properties 
	pageTitle="Storage Analytics Metrics について" 
	description="トランザクション メトリックと容量メトリックの両方を含む Storage Analytics の使用方法、メトリックの格納方法、メトリック データへのアクセス方法を説明します。" 
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

# Storage Analytics Metrics について

## 概要

Storage Analytics では、ストレージ サービスに対する要求に関する集計されたトランザクション統計情報と容量データを含むメトリックを格納できます。トランザクションに関しては、API 操作レベルとストレージ サービス レベルの両方でレポートされます。容量に関しては、ストレージ サービス レベルでレポートされます。メトリック データは、ストレージ サービスの使用状況の分析、ストレージ サービスに対する要求に関する問題の診断、サービスを使用するアプリケーションのパフォーマンスの向上に利用できます。

Storage Analytics を使用するには、監視するサービスごとに Storage Analytics を個別に有効にする必要があります。Storage Analytics は、[Azure の管理ポータル](https://manage.windowsazure.com/)から有効にできます。詳細については、「[ストレージ アカウントの監視方法](../how-to-monitor-a-storage-account)」をご覧ください。また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。[各サービスに対して Storage Analytics を有効にするには、Get Blob Service Properties、Get Queue Service Properties](https://msdn.microsoft.com/library/hh452239.aspx)、[Get Table Service Properties](https://msdn.microsoft.com/library/hh452238.aspx) の各操作を使用します。

## トランザクション メトリック

各ストレージ サービスおよび要求された API 操作について、受信/送信、可用性、エラー、分類された要求のパーセンテージを含む信頼性の高いデータのセットが 1 時間または 1 分間隔で記録されます。トランザクションの詳細の完全な一覧については、「[Storage Analytics Metrics のテーブル スキーマ](https://msdn.microsoft.com/library/hh343264.aspx)」をご覧ください。

トランザクション データは、サービス レベルと API 操作レベルの 2 つのレベルで記録されます。サービス レベルでは、サービスに対して要求が行われなかった場合でも、すべての要求された API 操作を要約した統計情報が 1 時間ごとにテーブル エンティティに書き込まれます。API 操作レベルでは、統計情報は、その時間内に操作が要求された場合にのみエンティティに書き込まれます。

たとえば、**GetBlob** 操作を BLOB サービスで実行する場合、Storage Analytics Metrics は要求を記録し、BLOB サービスと **GetBlob** 操作の両方の集計データに要求を含めます。ただし、**GetBlob** 操作がその時間に要求されない場合、その操作に対してエンティティが `$MetricsTransactionsBlob` に書き込まれることはありません。

トランザクション メトリックは、ユーザー要求と Storage Analytics 自体によって発行された要求の両方に関して記録されます。たとえば、Storage Analytics からのログとテーブル エンティティの書き込み要求は記録されます。これらの要求の課金方法の詳細については、「[Storage Analytics と課金](https://msdn.microsoft.com/library/hh360997.aspx)」をご覧ください。

## 容量メトリック

>[AZURE.NOTE] 現在、容量メトリックは、BLOB サービスに対してのみ利用できます。Table サービスと Queue サービスに関する容量メトリックは、Storage Analytics の将来のバージョンでの提供を予定しています。

容量データはストレージ アカウントの BLOB サービスに対して毎日記録され、2 つのテーブル エンティティが書き込まれます。一方のエンティティは、ユーザー データの統計情報です。もう一方のエンティティは、Storage Analytics によって使用される `$logs` BLOB コンテナーに関する統計情報です。この `$MetricsCapacityBlob` テーブルには、次の統計情報が含まれています。

- **容量**: ストレージ アカウントの BLOB サービスによって使用されているストレージの量 (バイト単位)。

- **ContainerCount**: ストレージ アカウントの BLOB サービス内の BLOB コンテナーの数。

- **ObjectCount**: ストレージ アカウントの BLOB サービス内のコミット済みとコミット前のブロックまたはページ BLOB の数。

容量メトリックの詳細については、「Storage Analytics Metrics のテーブル スキーマ」をご覧ください。

## メトリックの保存

各ストレージ サービスのすべてのメトリック データはそのサービスに予約された次の 3 つのテーブルに格納されます。トランザクション情報用に 1 つ、分単位のトランザクション情報用に 1 つ、容量情報用に 1 つのテーブルです。トランザクション情報と分単位のトランザクション情報は、要求データと応答データから構成されます。容量情報は、ストレージ使用量データから構成されます。ストレージ アカウントの BLOB サービスに関する時間単位のメトリック、分単位のメトリック、容量は、次の表に示した名前のテーブルで利用できます。

| メトリック レベル                      	| テーブル名                                                                                                                 	| サポートされているバージョン                                                                                                                       	|
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| 時間単位のメトリック、1 次拠点   	|  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                              	| 2013-08-15 よりも前のバージョンのみ。これらの名前はまだサポートされていますが、下に示すテーブルを使用するように切り替えることをお勧めします。 	|
| 時間単位のメトリック、1 次拠点   	| $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue             	| 2013-08-15 を含むすべてのバージョン                                                                                                           	|
| 分単位のメトリック、1 次拠点   	| $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue       	| 2013-08-15 を含むすべてのバージョン                                                                                                           	|
| 時間単位のメトリック、2 次拠点 	| $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue       	| 2013-08-15 を含むすべてのバージョン。読み取りアクセスの地理冗長レプリケーションを有効にする必要があります。                                                   	|
| 分単位のメトリック、2 次拠点 	| $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue 	| 2013-08-15 を含むすべてのバージョン。読み取りアクセスの地理冗長レプリケーションを有効にする必要があります。                                                   	|
| 容量 (BLOB サービスのみ)       	| $MetricsCapacityBlob                                                                                                        	| 2013-08-15 を含むすべてのバージョン                                                                                                           	|



これらのテーブルは、Storage Analytics がストレージ アカウントに対して有効化されたときに自動的に作成されます。これらのテーブルには、次のようにストレージ アカウントの名前空間を介してアクセスします (例:  `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`)。


## メトリック データへのアクセス

メトリック テーブル内のすべてのデータは、テーブル サービス API を使用してアクセスできます (Azure マネージ ライブラリで提供される .NET API など)。ストレージ アカウント管理者は、テーブル エンティティの読み取りと削除を行うことができますが、テーブル エンティティの作成または更新を行うことはできません。

## 次のステップ
### 概念
[Storage Analytics の有効化と構成](https://msdn.microsoft.com/library/hh360996.aspx)

[Storage Analytics Metrics のテーブル スキーマ](https://msdn.microsoft.com/library/hh343264.aspx)

[Storage Analytics によって記録される操作やステータス メッセージ](https://msdn.microsoft.com/library/hh343260.aspx)

[Storage Analytics Logging について](https://msdn.microsoft.com/library/hh343262.aspx)

### その他のリソース

[ストレージ アカウントの監視方法](../how-to-monitor-a-storage-account) 
<!--HONumber=47-->
