<properties 
	pageTitle="Storage Analytics" 
	description="BLOB、キュー、テーブル、およびファイル サービスの同時実行制御を管理する方法" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="tamram"/>

# Storage Analytics

Azure Storage Analytics では、ログが記録され、ストレージ アカウントのメトリック データを得ることができます。このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断を行うことができます。

Storage Analytics を使用するには、監視するサービスごとに Storage Analytics を個別に有効にする必要があります。Storage Analytics は、[Azure の管理ポータル](https://manage.windowsazure.com/)から有効にできます。詳細については、「[ストレージ アカウントの監視方法](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/)」をご覧ください。また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。各サービスに対して Storage Analytics を有効にするには、[Get Blob Service Properties](https://msdn.microsoft.com/ja-jp/library/hh452239.aspx)、[Get Queue Service Properties](https://msdn.microsoft.com/ja-jp/library/hh452243.aspx)、[Get Table Service Properties](https://msdn.microsoft.com/ja-jp/library/hh452238.aspx) の各操作を使用します。

集計データは、既知の BLOB (ログの場合) と既知のテーブル (メトリックの場合) に格納されます。集計データには、BLOB サービスとテーブル サービスの API を使用してアクセスできます。

ストレージ アカウントの合計の制限とは別に、Storage Analytics には、格納されたデータの量に関して 20 TB の制限があります。課金ポリシーとデータ保持ポリシーの詳細については、「[Storage Analytics と課金](https://msdn.microsoft.com/library/hh360997.aspx)」をご覧ください。ストレージ アカウントの上限の詳細については、「[Azure ストレージのスケーラビリティとパフォーマンスのターゲット](https://msdn.microsoft.com/library/dn249410.aspx)」をご覧ください。

Storage Analytics やその他のツールを使用した Azure Storage 関連の問題の特定、診断、トラブルシューティングに関する詳細なガイドについては、「[Microsoft Azure ストレージの監視、診断とトラブルシューティング](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/)」をご覧ください。



<!--HONumber=47-->
 