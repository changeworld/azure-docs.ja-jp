<properties 
	pageTitle="スナップショットの課金方法について" 
	description="Azure Storage BLOB スナップショットの課金方法に関するガイド" 
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

# スナップショットの課金方法について

スナップショット (BLOB の読み取り専用コピー) を作成すると、別途データ ストレージ料金がアカウントに課金される場合があります。不要なコストを抑えるためにも、アプリケーションを設計する際は、この料金が発生するしくみを理解しておくことが重要です。

## 課金に関する重要な考慮事項

スナップショットの作成時に考慮する必要のある重要なポイントを以下に示します。

- 料金はブロックまたはページに対して一意的に発生します。そのブロックまたはページが BLOB であるかスナップショットであるかは関係ありません。BLOB に関連付けられているスナップショットについてアカウントに追加料金が発生するのは、そのベースになっている BLOB を更新したときです。いったんベース BLOB を更新すると、ベース BLOB はそのスナップショットから分化し、それぞれの BLOB またはスナップショットの中で、一意のブロックまたはページに対する料金が発生するようになります。

- ブロック BLOB 内のブロックを置き換えた場合、以後そのブロックは一意のブロックとして課金されます。そのブロックに割り当てられているブロック ID とデータが、スナップショット側と同じであっても変わりません。ブロックが再度コミットされると、そのブロックはスナップショット内の対応するブロックから分化し、そのデータに対する料金が発生します。これは、ページ BLOB 内のページを、まったく同じデータで更新した場合にも当てはまります。

- UploadFile、UploadText、UploadStream、UploadByteArray のいずれかのメソッドを呼び出してブロック BLOB を置き換えると、その BLOB 内のすべてのブロックが置き換えられます。その BLOB に関連付けられているスナップショットがある場合、ベース BLOB とスナップショット内のすべてのブロックが分化し、両方の BLOB のすべてのブロックが課金対象となります。これは、ベース BLOB 内のデータとスナップショット内のデータとがまったく同一であっても変わりません。

- 2 つのブロックに格納されているデータが同一であるかどうかを判断する方法は、Azure BLOB サービスには用意されていません。アップロードされてコミットされたブロックは、同じデータや同じブロック ID がある場合でも、それぞれが一意のものとして扱われます。料金は一意のブロックに対して発生するため、スナップショットがある BLOB を更新すると、一意のブロックが増え、追加料金が発生することを考慮することが重要です。

> [AZURE.NOTE] 不要な料金を避けるためにも、スナップショットは慎重に管理してください。スナップショットは次のように管理することをお勧めします。

> - まったく同じデータで更新する場合も含め、BLOB を更新するときは必ずその BLOB に関連付けられているスナップショットを削除してから作成し直すようにします (アプリケーションの設計上、スナップショットを維持しなければならない場合を除く)。BLOB のスナップショットを削除してから作成し直すことにより、BLOB とスナップショットの分化を確実に防ぐことができます。

> - BLOB のスナップショットを維持する場合は、UploadFile、UploadText、UploadStream、UploadByteArray を呼び出して BLOB を更新することは避けてください。これらのメソッドを実行すると、BLOB 内のすべてのブロックが置き換えられます。代わりに、PutBlock メソッドと PutBlockList メソッドを使用して、最小数のブロックのみを更新するようにしてください。


## スナップショットの課金シナリオ


ブロック BLOB とそのスナップショットについて料金が発生するしくみを次のシナリオで説明します。 

シナリオ 1 は、スナップショットが作成されてからベース BLOB は更新されていません。したがって、料金の発生対象は、一意のブロックである 1、2、3 のみです。

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-1.png)

シナリオ 2 は、ベース BLOB が更新されています。スナップショットは更新されていません。更新されたのはブロック 3 です。格納されているデータと ID が同じでも、スナップショット内のブロック 3 とは異なります。このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-2.png)

シナリオ 3 は、ベース BLOB が更新されています。スナップショットは更新されていません。ベース BLOB のブロック 3 がブロック 4 に置き換わっていますが、スナップショット側はブロック 3 のままです。このため、4 ブロック分の料金がアカウントに課金されます。
 
![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-3.png)

シナリオ 4 では、ベース BLOB が完全に更新されており、元のブロックは 1 つも含まれていません。このため、8 つある一意のブロックすべてについての料金がアカウントに課金されます。このシナリオは、UploadFile、UploadText、UploadFromStream、UploadByteArray などの更新メソッド (これらのメソッドは BLOB の内容をすべて置き換えるため) を使用した場合に発生する可能性があります。

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-4.png)
<!--HONumber=47-->
