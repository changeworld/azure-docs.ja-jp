---
title: Azure HDInsight のクラスターの容量計画
description: Azure HDInsight クラスターの容量とパフォーマンスの計画に関する重要な質問を特定します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886995"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight クラスターの容量計画

HDInsight クラスターをデプロイする前に、必要なパフォーマンスとスケールを判定することによって目的のクラスター容量を計画します。 この計画により、使いやすさとコストの両方を最適化できます。 クラスターの容量に関する決定事項の中には、デプロイ後に変更できないものもあります。 パフォーマンス パラメーターが変わった場合は、保存されているデータを失うことなく、クラスターを解体して再作成できます。

容量計画における重要な検討事項は次のとおりです。

* クラスターをどの地理的リージョンにデプロイすればよいか。
* どれくらいのストレージ容量が必要か。
* どのような種類のクラスターをデプロイすればよいか。
* クラスター ノードでは、どのサイズおよび種類の仮想マシン (VM) を使用すればよいか。
* クラスターにはワーカー ノードがいくつ必要か。

## <a name="choose-an-azure-region"></a>Azure リージョンの選択

Azure リージョンによって、クラスターを物理的にプロビジョニングする場所が決まります。 読み取りと書き込みの待機時間を最小限に抑えるには、クラスターをデータの近くに配置する必要があります。

HDInsight は多数の Azure リージョンで利用できます。 最も近いリージョンを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)」を参照してください。

## <a name="choose-storage-location-and-size"></a>ストレージの場所とサイズの選択

### <a name="location-of-default-storage"></a>既定のストレージの場所

既定のストレージ (Azure ストレージ アカウントまたは Azure Data Lake Storage) は、クラスターと同じ場所に存在する必要があります。 Azure Storage はあらゆる場所で利用できます。 Data Lake Storage Gen1 は一部のリージョンで利用できます。[Data Lake Storage の現在の提供状況](https://azure.microsoft.com/global-infrastructure/services/?products=storage)を参照してください。

### <a name="location-of-existing-data"></a>既存のデータの場所

既存のストレージ アカウントまたは Data Lake Storage をクラスターの既定のストレージとして使用する場合は、その同じ場所にクラスターをデプロイする必要があります。

### <a name="storage-size"></a>ストレージ サイズ

デプロイされたクラスターで、追加の Azure Storage アカウントを接続したり、他の Data Lake Storage にアクセスしたりできます。 すべてのストレージ アカウントがクラスターと同じ場所に存在する必要があります。 Data Lake Storage は別の場所にあってもかまいませんが、その距離が長いと、ある程度の待ち時間が発生する可能性があります。

Azure Storage には何らかの[容量制限](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)がありますが、Data Lake Storage Gen1 はほぼ無制限です。

クラスターは、さまざまなストレージ アカウントの組み合わせにアクセスできます。 一般的な例を次に示します。

* データ量が、1 つの Blob Storage コンテナーのストレージ容量を超える可能性がある場合。
* BLOB コンテナーへのアクセス レートがしきい値を超え、調整が発生する可能性がある場合。
* データを作成する必要があるときに、クラスターで利用できる BLOB コンテナーに既にアップロードしている場合。
* セキュリティ上の理由からストレージのさまざまな部分を分離する場合、または管理を簡素化する場合。

パフォーマンスを向上させるために、ストレージ アカウントごとにコンテナーを 1 つだけ使用します。

## <a name="choose-a-cluster-type"></a>クラスターの種類の選択

クラスターの種類によって、HDInsight クラスターが実行するように構成されるワークロードが決定されます。 種類には、[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)、[Apache Storm](./storm/apache-storm-overview.md)、[Apache Kafka](./kafka/apache-kafka-introduction.md)、または [Apache Spark](./spark/apache-spark-overview.md) が含まれます。 使用できるクラスターの種類の詳細については、[Azure HDInsight の概要](hdinsight-overview.md#cluster-types-in-hdinsight)に関するページを参照してください。 クラスターの種類ごとに、サイズとノード数の要件を含む特定のデプロイ トポロジがあります。

## <a name="choose-the-vm-size-and-type"></a>VM のサイズと種類の選択

各クラスターの種類には一連のノード タイプがあり、各ノード タイプには VM のサイズと種類の固有のオプションがあります。

アプリケーションに最適なクラスター サイズを決定するために、クラスターの容量のベンチマークを実行し、その結果に従ってサイズを増やすことができます。 たとえば、シミュレートされたワークロードや "*カナリア クエリ*" をご利用いただけます。 シミュレートされたワークロードをさまざまなサイズのクラスターで実行します。 目的のパフォーマンスに到達するまで、サイズを徐々に増やします。 他の実稼働クエリの間にカナリア クエリを定期的に挿入することで、クラスターに十分なリソースがあるかどうかを示すことができます。

ワークロードのための適切な VM ファミリを選択する方法の詳細については、[クラスターの適切な VM サイズの選択](hdinsight-selecting-vm-size.md)に関するページを参照してください。

## <a name="choose-the-cluster-scale"></a>クラスターのスケールの選択

クラスターのスケールは、VM ノードの数によって決まります。 どのクラスターの種類にも、特定のスケールのノード タイプと、スケールアウトをサポートするノード タイプがあります。たとえば、クラスターで 3 つの [Apache ZooKeeper](https://zookeeper.apache.org/) ノードまたは 2 つのヘッド ノードが必要な場合があります。 分散方式でデータ処理を実行するワーカー ノードは、追加のワーカー ノードのメリットが得られます。

クラスターの種類によっては、ワーカー ノードの数を増やすと、コンピューティング容量が追加されます (コア数の増加など)。 ノードの数が増加すると、処理されるデータのインメモリ ストレージをサポートするためにクラスター全体に必要なメモリの合計が増えます。 VM のサイズと種類の選択と同様に、クラスターの適切なスケールの選択も、通常は経験的に実現されます。 シミュレートされたワークロードまたはカナリア クエリを使用します。

ピーク時の負荷要求を満たすようにクラスターをスケールアウトできます。 その後、これらの追加のノードが必要なくなったら、再びスケールダウンします。 [自動スケーリング機能](hdinsight-autoscale-clusters.md)を使用すると、事前に定義されたメトリックとタイミングに基づいて、クラスターを自動的にスケーリングできます。 クラスターの手動スケーリングについて詳しくは、「[HDInsight クラスターのスケーリング](hdinsight-scaling-best-practices.md)」をご覧ください。

### <a name="cluster-lifecycle"></a>クラスターのライフサイクル

ユーザーは、クラスターの有効期間に対して課金されます。 特定の時間だけクラスターが必要な場合は、[Azure Data Factory を使用してオンデマンド クラスターを作成](hdinsight-hadoop-create-linux-clusters-adf.md)します。 また、クラスターのプロビジョニングと削除を実行する PowerShell スクリプトを作成し、[Azure Automation](https://azure.microsoft.com/services/automation/) を使用してそれらのスクリプトのスケジュールを設定することもできます。

> [!NOTE]  
> クラスターが削除されると、既定の Hive metastore も削除されます。 クラスターを次回再作成するために metastore を保持するには、Azure Database や [Apache Oozie](https://oozie.apache.org/) などの外部メタデータ ストアを使用します。
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>クラスターのジョブ エラーの分離

マルチノード クラスターでの複数の map および reduce コンポーネントの並列実行のためにエラーが発生する場合があります。 この問題を分離しやすくするために、分散テストを試してください。 1 つのワーカー ノード クラスターで複数の同時実行ジョブを実行します。 次に、このアプローチを拡張して、複数のノードを含むクラスターで複数のジョブを同時に実行します。 Azure で単一ノードの HDInsight クラスターを作成するには、 *`Custom(size, settings, apps)`* オプションを使用し、ポータルで新しいクラスターをプロビジョニングするときに **[クラスター サイズ]** セクションの *[Number of Worker nodes] (ワーカー ノードの数)* として 1 の値を使用します。

## <a name="quotas"></a>Quotas (クォータ)

ターゲット クラスターの VM サイズ、スケール、種類を決定したら、サブスクリプションの現在のクォータの容量制限を確認します。 クォータ制限に達した場合は、新しいクラスターをデプロイできません。 または、ワーカー ノードを追加することによって、既存のクラスターをスケールアウトします。 唯一のクォータ制限は CPU コア クォータに関するものであり、サブスクリプションごとにリージョン レベルで存在します。 たとえば、ご利用のサブスクリプションは、米国東部リージョンにおいてコア数が 30 に制限されます。

使用可能なコアを確認するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. HDInsight クラスターの **[概要]** ページに移動します。
3. 左側のメニューで、 **[クォータ制限]** を選択します。

   このページには、使用中のコア数、使用可能なコア数、およびコアの合計が表示されます。

クォータの増加を要求する必要がある場合は、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ページの左下にある **[ヘルプとサポート]** を選択します。
1. **[新しいサポート リクエスト]** を選択します。
1. **[新しいサポート要求]** ページの **[基本]** タブで、次のオプションを選択します。

   - **問題の種類**: **サービスとサブスクリプションの制限 (クォータ)**
   - **サブスクリプション**: 使用するサブスクリプション
   - **クォータの種類**: **HDInsight**

     ![HDInsight コア クォータを増やすためのサポート要求を作成します。](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. **ソリューション >>** を選択します。
1. **[詳細]** ページで、問題に関する説明を入力し、問題の重大度、希望する連絡方法、およびその他の必須フィールドを選択します。
1. **確認と作成 >>** を選択します。
1. **[確認および作成]** タブで、 **[作成]** を選択します。

> [!NOTE]  
> プライベート リージョンで HDInsight コア クォータを増やす必要がある場合は、[ホワイト リストの要求を送信](https://aka.ms/canaryintwhitelist)してください。

[サポートに連絡してクォータの引き上げを要求](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)できます。

固定のクォータ制限がいくつか存在します。 たとえば、1 つの Azure サブスクリプションに許可されるコアの数は最大 10,000 です。 これらの制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Apache Hadoop、Spark、Kafka などを使用して HDInsight でクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md): HDInsight でクラスターをセットアップして構成する方法について説明します。
* [クラスター パフォーマンスを監視する](hdinsight-key-scenarios-to-monitor.md): クラスターの容量に影響を及ぼす可能性のある HDInsight クラスターを監視するための主なシナリオについて説明します。
