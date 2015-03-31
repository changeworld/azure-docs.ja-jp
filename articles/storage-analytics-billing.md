<properties 
	pageTitle="Storage Analytics と課金" 
	description="Storage Analytics を使用して、ストレージ サービスの課金体系をより詳しく把握する方法を説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>


# Storage Analytics と課金

このトピックでは、Storage Analytics のコストについて説明します。メトリック データとログ データを使用してストレージ サービスの課金体系を把握する方法についても説明します。


## Storage Analytics のコスト

Storage Analytics は、ストレージ アカウント所有者が有効にします。既定では有効になっていません。すべてのメトリック データは、ストレージ アカウントのサービスによって書き込まれます。したがって、Storage Analytics によって実行される個々の書き込み操作には料金が発生します。加えて、メトリック データに費やされるストレージの使用量も課金対象となります。

Storage Analytics によって実行される次の操作には料金が発生します。

- ログの BLOB の作成要求

- メトリックのテーブル エンティティの作成要求

データ保持ポリシーを構成した場合、Storage Analytics が古いログ データやメトリック データを削除しますが、その際の削除トランザクションに対する料金は発生しません。ただし、クライアントからの削除トランザクションは課金対象となります。保持ポリシーの詳細については、「[Storage Analytics のデータ保持ポリシーの設定](https://msdn.microsoft.com/library/azure/hh343263.aspx)」をご覧ください。

## 課金の対象となる要求について

アカウントのストレージ サービスに対して実行されるそれぞれの要求は、課金対象の要求とそうでない要求とに分けられます。Storage Analytics は、サービスに対するすべての要求をそれぞれログに記録します。たとえば、要求がどのように処理されたかを示すステータス メッセージも記録されます。同様に、Storage Analytics は、サービスに対するメトリックと、そのサービスの API 操作に対するメトリックを保存しています (特定のステータス メッセージの数やパーセンテージなど)。これらの機能をうまく組み合わせて利用すると、課金対象の要求を分析したり、アプリケーションを改善したり、サービスに対する要求の問題を診断したりできます。課金の詳細については、「[Azure Storage ストレージでの課金 - 帯域幅、トランザクション、容量 (このページは英語の場合があります)](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)」をご覧ください。

Storage Analytics のデータで課金対象の要求を調べる際は、「[Storage Analytics によって記録される操作やステータス メッセージ](https://msdn.microsoft.com/library/azure/hh343260.aspx)」の表が参考になります。手元のログ データとメトリック データをステータス メッセージと照らし合わせながら、特定の要求が課金対象であったかどうかを確認できます。この表は、ストレージ サービスまたは特定の API 操作の可用性を調べるときにも利用できます。

## 次のステップ
[Storage Analytics によって記録される操作やステータス メッセージ](https://msdn.microsoft.com/library/azure/hh343260.aspx)

[Storage Analytics Logging について](https://msdn.microsoft.com/library/azure/hh343262.aspx)

[Storage Analytics Metrics について](../storage-about-analytics-metrics.md) 

<!--HONumber=47-->
