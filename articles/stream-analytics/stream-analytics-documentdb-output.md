<properties 
	pageTitle="DocumentDB 出力の詳細 | Microsoft Azure" 
	description="Azure Stream Analytics の出力としての DocumentDB"
	keywords="DocumentDB 出力,DocDB 出力,Stream Analytics と DocumentDB"
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
	ms.date="11/23/2015" 
	ms.author="jeffstok"/>

# Azure Stream Analytics の出力としての DocumentDB について

Azure Stream Analytics では、[Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) が出力としてサポートされるようになったため、構造化されていない JSON データに対してデータ アーカイブや待機時間の少ないクエリを実行できます。このドキュメントでは、この統合を最適に実装する方法について説明します。DocumentDB を詳しく理解していない場合は、開始する前に [DocumentDB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)を参照してください。

Stream Analytics で Azure DocumentDB 出力を使用すると、ストリーム処理の結果を DocumentDB コレクションに書き込むことができます。Stream Analytics は、データベース内にコレクションを作成せず、代わりにユーザーが前もってコレクションを作成するように要求します。これは、DocumentDB コレクションの課金によるコストをユーザーに対して透明化し、コレクションのパフォーマンス、一貫性、容量などを [DocumentDB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して直接調整できるようにするためです。ストリーミング ジョブのコレクションを論理的に分離するには、ストリーミング ジョブごとに 1 つの DocumentDB データベースを使用することをお勧めします。

DocumentDB コレクションの一部のオプションの詳細を以下に示します。

## 整合性

DocumentDB では、アプリケーション要件を満たすために、データベースやコレクションを微調整し、一貫性、可用性、待機時間の間で妥協点を見つけることができます。読み取りおよび書き込みの待機時間に対してシナリオで求められる読み取りの一貫性レベルに応じて、データベース アカウントでの一貫性レベルを選択することができます。また、DocumentDB では、コレクションへの各 CRUD 操作に対する同期インデックス作成も、既定で有効になっています。この機能も、DocumentDB で書き込みと読み取りのパフォーマンスを制御するための便利なオプションの 1 つです。このトピックの詳細については、[データベースとクエリの一貫性レベルの変更](../articles/documentdb-consistency-levels.md)に関する記事を参照してください。

## パフォーマンス

DocumentDB コレクションは、3 種類のパフォーマンス レベル (S1、S2、S3) で作成できます。このレベルによって、そのコレクションへの CRUD に利用できるスループットが決まります。さらに、コレクションのインデックス作成レベルや一貫性レベルも、パフォーマンスに影響します。このようなパフォーマンス レベルの詳細を理解するには、[こちらの記事](../articles/documentdb-performance-levels.md)を参照してください。

## アップサート

Stream Analytics を DocumentDB と統合することで、特定のドキュメント ID 列に基づき、レコードを DocumentDB コレクションに挿入または更新できるようになります。この機能は、*アップサート*とも呼ばれています。

Stream Analytics では、オプティミスティック アップサート手法を採用しています。この手法では、ドキュメント ID の競合が原因で挿入に失敗した場合にのみ更新が実行されます。この更新は Stream Analytics によって PATCH として実行されるため、ドキュメントに対する部分更新が可能になります。つまり、新しいプロパティの追加や既存のプロパティの置き換えは段階的に実行されます。JSON ドキュメント内の配列プロパティの値を変更すると、配列全体が上書きされることになるので注意してください。つまり、配列はマージされません。

## パーティション分割

DocumentDB のコレクションを使用すると、クエリ パターンとアプリケーションのパフォーマンス ニーズの両方に応じてデータをパーティション分割することができます。各コレクションに格納できるデータは最大 10 GB で、現時点ではコレクションをスケールアップ (またはオーバーフロー) する方法はありません。スケールアウトの場合、Stream Analytics では、特定のプレフィックスを持つ複数のコレクションに書き込むことができます (使用方法の詳細については以下を参照)。Stream Analytics では、ユーザー指定の PartitionKey 列に基づく一貫性のある[ハッシュ パーティション リゾルバー](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)方式を使用し、出力レコードをパーティションに分割します。ストリーミング ジョブの開始時における特定のプレフィックスを持つコレクションの数が出力パーティションの数として使用され、ジョブはその出力パーティションに並行して書き込みを行います (DocumentDB コレクション = 出力パーティション)。1 つの S3 コレクションと遅延インデックス作成で挿入のみを実行する場合、予想される書き込みスループットは約 0.4 MB/秒です。複数のコレクションを使用することで、スループットの向上と容量の増加が実現できます。

将来パーティション数を増やす予定がある場合は、ジョブを停止し、既存のコレクションから新しいコレクションへとデータの再パーティション分割を行った後、Stream Analytics ジョブを再開することが必要になる場合があります。PartitionResolver の使用方法、再パーティション分割、サンプル コードなどの詳細は、フォローアップ記事に含まれる予定です。この詳細については、[DocumentDB でのパーティション分割](../articles/documentdb-partition-data.md#developing-a-partitioned-application)に関する記事でも説明しています。

## DocumentDB 設定

Stream Analytics で DocumentDB を出力として作成すると、以下に示すように、情報を求めるプロンプトが表示されます。このセクションでは、各プロパティの定義について説明します。
  
![documentdb stream analytics 出力画面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)

-   **出力のエイリアス** – ASA クエリ内でこの出力を意味するエイリアス。  
-   **アカウント名** – DocumentDB アカウントの名前またはエンドポイント URI。  
-   **アカウント キー** – DocumentDB アカウントの共有アクセス キー。  
-   **データベース** – DocumentDB データベース名。  
-   **コレクション名のパターン** – 使用するコレクションのコレクション名のパターン。コレクション名の形式は、オプションの {partition} トークンを使用して構成できます。この場合、パーティションは 0 から開始します。有効な入力値のサンプルを次に示します。1) MyCollection – "MyCollection" という名前のコレクションが 1 つ必要です。2) MyCollection{partition} – "MyCollection0"、"MyCollection1"、"MyCollection2" などのコレクションが必要です。  
-   **パーティション キー** – コレクション間で出力をパーティション分割するためのキーの指定に使用される、出力イベント内のフィールドの名前。コレクションの出力が 1 つの場合は、PartitionId など、任意の出力列を使用できます。  
-   **Document ID** – 省略可能です。挿入操作または更新操作の基準となるプライマリ キーを指定するために使用される、出力イベント内のフィールドの名前。  

<!---HONumber=AcomDC_1203_2015-->