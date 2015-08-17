<properties 
   pageTitle="Azure Storage のスケーラビリティおよびパフォーマンスのターゲット | Microsoft Azure"
   description="Standard Storage アカウントと Premium Storage アカウントの両方の容量、要求レート、および送受信の帯域幅を含む、Azure Storage のスケーラビリティとパフォーマンスのターゲットについて説明します。各 Azure Storage サービス内にあるパーティションのパフォーマンス ターゲットを理解します。"
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="06/30/2015"
   ms.author="tamram" />

# Azure Storage のスケーラビリティおよびパフォーマンスのターゲット

このトピックでは、Microsoft Azure Storage のスケーラビリティとパフォーマンスについて説明します。その他の Azure の制約に関する概要は、「[Azure サブスクリプションとサービスの制限、クォータ、および制約](../azure-subscription-service-limits.md)」を参照してください。

>[AZURE.NOTE]すべてのストレージ アカウントは新しいフラット ネットワーク トポロジで実行され、ストレージ アカウントがいつ作成されたかにかかわらず、以下に示すスケーラビリティおよびパフォーマンスのターゲットがサポートされます。のフラット ネットワーク アーキテクチャおよびスケーラビリティの詳細については、[Microsoft Azure Storage の高い整合性を持つ高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)に関するページを参照してください。

<!-- -->

>[AZURE.IMPORTANT]ここで示すスケーラビリティおよびパフォーマンスのターゲットは、ハイエンドのターゲットですが、達成可能です。いかなる場合でも、ストレージ アカウントで達成される要求レートおよび帯域幅は、格納されたオブジェクトのサイズ、使用されているアクセス パターン、およびアプリケーションで実行されているワークロードの種類によって異なります。必ずサービスをテストして、パフォーマンスがユーザー要件を満たしているかどうかを確認してください。可能であれば、トラフィック量の急増を回避し、トラフィックがパーティション間でうまく分散されるようにしてください。

>ワークロードがアプリケーションのパーティションで処理できる上限に達すると、Azure Storage はエラー コード 503 (サーバーがビジー状態) またはエラー コード 500 (操作タイムアウト) の応答を返しはじめます。このような状況になった場合、アプリケーションで指数関数的バックオフによる再試行ポリシーを使用する必要があります。指数関数的バックオフによって、そのパーティションへの負荷が減少し、そのパーティションへのトラフィック量の増加が緩和されます。

アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合、複数のストレージ アカウントを使用し、それらのストレージ アカウント間でデータが分割されるようにアプリケーションを構築できます。1 つの Azure サブスクリプションには 100 のストレージ アカウントが使用できます。ボリューム ディスカウント料金の詳細については、「[Azure Storage 料金](http://azure.microsoft.com/pricing/details/storage/)」を参照してください。

## 標準的なストレージ アカウントのスケーラビリティ ターゲット

[AZURE.INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

## Premium Storage アカウントのスケーラビリティ ターゲット

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../../includes/azure-storage-limits-premium-storage.md)]

## ストレージの制限 - Azure リソース マネージャー

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## Azure Storage 内のパーティション

Azure Storage に格納されているデータを含むすべてのオブジェクト (BLOB、メッセージ、エンティティ、およびファイル) はパーティションに属し、パーティション キーによって識別されます。Azure Storage では、BLOB、メッセージ、エンティティ、およびファイルのトラフィック ニーズに合わせて、どのようにこれらのオブジェクトの負荷をサーバー間で分散するかがパーティションによって決まります。パーティション キーはストレージ アカウント内で一意であり、BLOB、メッセージ、またはエンティティを見つけるために使用されます。

上の「[標準的なストレージ アカウントのスケーラビリティ ターゲット](#scalability-targets-for-standard-storage-accounts)」の表は、サービスごとの 1 つのパーティションにおけるパフォーマンス ターゲットの一覧です。

パーティションは、各ストレージ サービスの負荷分散およびスケーラビリティに以下のような影響を与えます。

- **BLOB**: BLOB のパーティション キーは、コンテナー名と BLOB 名を組み合わせたものです。これは、各 BLOB にそれぞれのパーティションが存在するということです。これによって BLOB を多数のサーバーに分散できるため、BLOB へのアクセスのスケールアウトが可能になります。BLOB は BLOB コンテナーに論理的にグループ化できますが、このグループ化によってパーティション分割は影響を受けません。

- **メッセージ**: メッセージのパーティション キーはキュー名であるため、キュー内のすべてのメッセージは 1 つのパーティションにグループ化され、1 台のサーバーで処理されます。ストレージ アカウント内のキューがどんなに多くとも、負荷が分散されるように、異なるキューが別のサーバーによって処理されることがあります。

- **エンティティ**: エンティティのパーティション キーは、テーブル名とパーティション キーを組み合わせたものです。ここでパーティション キーは、エンティティに必要なユーザー定義された **PartitionKey** プロパティの値です。

	同じパーティション キー値を持つすべてのエンティティは、同じパーティションにグループ化され、同じパーティション サーバーに格納されます。これは、アプリケーションを設計する際に理解しておくべき重要なポイントです。アプリケーションでは、エンティティを複数のパーティションに分散することで得られるスケーラビリティ上のメリットと、エンティティを 1 つのパーティションにグループ化することで得られるデータ アクセスのメリットのバランスをとってください。

	テーブル内のエンティティのセットを単一のパーティションにグループ化する主な利点は、パーティションが 1 台のサーバー上にあるため、同じパーティション内のエンティティすべてにアトミック バッチ操作を実行できるということです。そのため、バッチ操作を実行する場合は、同じパーティション キーでエンティティをグループ化することを検討します。

	一方、同じテーブル内にあっても、異なるパーティションに属しているエンティティは、異なるサーバー間で負荷分散できるため、大きなテーブルの処理が可能になり、スケーラビリティもさらに大きくなります。

## 関連項目

- [Storage の料金詳細](http://azure.microsoft.com/pricing/details/storage/)
- [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)
- [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal/)
- [Azure ストレージのレプリケーション](storage-redundancy.md)
- [Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](storage-performance-checklist.md)
- [Microsoft Azure Storage: 強力な整合性を備えた高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)



 

<!---HONumber=August15_HO6-->