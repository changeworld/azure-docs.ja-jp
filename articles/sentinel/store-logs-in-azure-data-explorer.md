---
title: 長期的なログ保持のために Azure Data Explorer を統合する |Microsoft Docs
description: Azure Sentinel ログを長期保持のために Azure Data Explorer に送信し、データ ストレージのコストを削減します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2021
ms.author: bagol
ms.openlocfilehash: 12a6ae76b1eb4dced661b811d8f6e9030f74b4f1
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108795728"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>長期的なログ保持のために Azure Data Explorer を統合する

既定では、Azure Sentinel に取り込まれたログは Azure Monitor Log Analytics に格納されます。 この記事では、長期保持のために Azure Data Explorer に送信することで Azure Sentinel の保持コストを削減する方法について説明します。

ログを Azure Data Explorer に格納することで、データのクエリの機能を維持しながらコストを削減することができ、特にデータが増大する場合に役立ちます。 たとえば、セキュリティ データは時間の経過とともに価値が失われる可能性がありますが、規制上の要件のためにログを保持したり、古いデータに対して定期的な調査を実施したりしなければならない場合があります。

## <a name="about-azure-data-explorer"></a>Azure Data Explorer について

Azure Data Explorer は、ログおよびデータ分析に高度に最適化されたビッグ データ分析プラットフォームです。 Azure Data Explorer は、Kusto クエリ言語 (KQL) をクエリ言語として使用するため、Azure Sentinel データ ストレージの代替として適しています。 Azure Data Explorer をデータ ストレージに使用することで、Azure Data Explorer と Azure Sentinel の両方で、クロスプラットフォーム クエリを実行し、データを視覚化できます。

詳細については、Azure Data Explorer の[ドキュメント](/azure/data-explorer/)と[ブログ](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/)をご覧ください。

### <a name="when-to-integrate-with-azure-data-explorer"></a>Azure Data Explorer と統合するタイミング

Azure Sentinel は、SIEM および SOAR のフル機能を搭載し、迅速にデプロイと構成ができるだけでなく、SOC チームのための高度な組み込みのセキュリティ機能を提供します。 しかし、Azure Sentinel にセキュリティ データを格納することの有用性は、SOC ユーザーがそこにアクセスする頻度が新しいデータにアクセスするほどでなくなると、数か月後には低下する可能性があります。

定期的な調査や監査など、特定のテーブルに少ない頻度でアクセスするだけでよい場合は、データを Azure Sentinel に保持してもコスト効率がよくないと考えることができます。 この時点で、データを Azure Data Explorer に格納することをお勧めします。より低コストでありながら、Azure Sentinel で実行するのと同じ KQL クエリを使用して探索できます。

[Log Analytics Azure Data Explorer プロキシ機能](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy)を使用して、Azure Sentinel から Azure Data Explorer のデータに直接アクセスできます。 これを行うには、ログ検索またはブックでクロスクラスター クエリを使用します。

> [!IMPORTANT]
> 分析ルール、UEBA、調査グラフなどの SIEM コア機能は、Azure Data Explorer に格納されているデータをサポートしていません。
>

> [!NOTE]
> Azure Data Explorer と統合することで、データの制御と細分性の設定も可能になります。 詳細については、「[設計上の考慮事項](#design-considerations)」を参照してください。
>
## <a name="send-data-directly-to-azure-sentinel-and-azure-data-explorer-in-parallel"></a>データを直接 Azure Sentinel と Azure Data Explorer に並行して送信する

"*セキュリティ価値のある*" データを Azure Sentinel に保持して、検出、インシデント調査、脅威ハンティング、UEBA などで使用したい場合があります。 このデータを Azure Sentinel に保持することは、通常 3 か月から 12 か月のストレージで要件が十分満たされる、主にセキュリティ オペレーション センター (SOC) のユーザーにメリットがあります。

すべてのデータを "*そのセキュリティ価値に関係なく*" Azure Data Explorer に同時に送信するよう構成することもでき、そこでより長期間保存できます。 Azure Sentinel と Azure Data Explorer の両方にデータを同時に送信すると多少の重複が生じますが、Azure Sentinel の保持コストが減るので大きなコスト節約になります。

> [!TIP]
> また、このオプションでは、Azure Sentinel に格納されているセキュリティ データを Azure Data Explorer に格納されている運用データや長期的なデータでエンリッチするなど、データ ストアに分散しているデータを関連付けることができます。 詳細については、「[Azure Monitor を使用した Azure Data Explorer のクロスリソース クエリ](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy)」をご覧ください。
>

次の画像は、すべてのデータを Azure Data Explorer に保持しながら、日常的に使用するためにセキュリティ データだけを Azure Sentinel に送信する方法を示しています。

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="データを Azure Data Explorer と Azure Sentinel に並行して格納します。":::

このアーキテクチャ オプションを実装する場合の詳細については、「[Azure Data Explorer による監視](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer)」を参照してください。

## <a name="export-data-from-log-analytics-into-azure-data-explorer"></a>データを Log Analytics から Azure Data Explorer にエクスポートする

Azure Data Explorer にデータを直接送信する代わりに、データを Log Analytics から Azure Data Explorer に、Azure Event Hub または Azure Data Factory を介してエクスポートすることも選択できます。

### <a name="data-export-architecture"></a>データ エクスポートのアーキテクチャ

次の画像は、Azure Monitor インジェスト パイプラインを介してエクスポートされたデータのサンプル フローを示しています。 データは既定では Log Analytics に送られますが、Azure ストレージ アカウントまたはイベント ハブにエクスポートするように構成することもできます。

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Azure Monitor からのデータのエクスポート - アーキテクチャ。":::

データ エクスポートの規則を構成するときに、エクスポートするログの種類を選択します。 いったん構成すると、Log Analytics インジェスト エンドポイントに到着し、選択したテーブルのワークスペースをターゲットとしている新しいデータが、ストレージ アカウントまたはイベント ハブにエクスポートされます。

エクスポートするデータを構成する際には、次の点に注意してください。

|考慮事項  | 詳細 |
|---------|---------|
|**エクスポートするデータのスコープ**     |  特定のテーブルに対するエクスポートが構成されると、そのテーブルに送信されたすべてのデータが、例外なくエクスポートされます。 データのサブセットをフィルタリングしてエクスポートしたり、特定のイベントにエクスポートを制限したりすることはできません。       |
|**場所の要件**     |   Azure Monitor または Azure Sentinel ワークスペースと目的の場所 (Azure ストレージ アカウントまたはイベント ハブ) の両方が、同じ地理的リージョンにある必要があります。      |
|**サポート対象のテーブル**     | すべてのテーブルがエクスポートでサポートされているわけではありません。たとえば、カスタム ログ テーブルはサポート対象ではありません。 <br><br>詳細については、[Azure Monitor での Log Analytics ワークスペースのデータ エクスポート](/azure/azure-monitor/logs/logs-data-export)および[サポートされるテーブルの一覧](/azure/azure-monitor/logs/logs-data-export#supported-tables)に関するページを参照してください。         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>データ エクスポートの方法と手順

Azure Sentinel から Azure Data Explorer にデータをエクスポートするには、次のいずれかの手順を使用します。

- **Azure イベント ハブを使用する**。 データを Log Analytics からイベント ハブにエクスポートして、そこから Azure Data Explorer に取り込むことができます。 この方法では、一部のデータ (最初の X か月) が Azure Sentinel と Azure Data Explorer の両方に格納されます。

- **Azure Storage と Azure Data Factory を使用する**。 データを Log Analytics から Azure Blob Storage にエクスポートして、Azure Data Factory を使用して定期的なコピー ジョブを実行し、さらに Azure Data Explorer にデータをエクスポートします。 この方法では Azure Data Factory からデータをコピーできるのが Azure Sentinel または Log Analytics の保持期間の上限に近づいたときだけなので、重複が回避されます。

### <a name="azure-event-hub"></a>[Azure Event Hub](#tab/adx-event-hub)

このセクションでは、Azure Sentinel のデータを Log Analytics からイベント ハブにエクスポートし、そこで Azure Data Explorer に取り込む方法について説明します。 [データを直接 Azure Sentinel と Azure Data Explorer に並行して送信](#send-data-directly-to-azure-sentinel-and-azure-data-explorer-in-parallel)する場合と同様に、この方法では、データは Log Analytics に到着したときに Azure Data Explorer にストリーミングされるので、多少のデータ重複が伴います。

次の画像は、データがイベント ハブへエクスポートされて、そこから Azure Data Explorer に取り込まれるフローの例を示しています。

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Azure イベント ハブ経由でデータを Azure Data Explorer に エクスポートします。":::

前の画像で示されているアーキテクチャでは、*X* か月ごとに頻繁にアクセスしなければならないデータに対して、インシデント管理、視覚的調査、脅威ハンティング、高度な視覚化、UEBA など、Azure Sentinel の SIEM の完全なエクスペリエンスが提供されます。 同時に、このアーキテクチャでは、長期的データに対するクエリを、Azure Data Explorer で直接アクセスして行ったり、Azure Data Explorer のプロキシ機能を使って Azure Sentinel 経由で行ったりすることもできます。 Azure Data Explorer の長期的データ ストレージに対するクエリは、Azure Sentinel から Azure Data Explorer に、変更を加えることなく移植できます。

> [!NOTE]
> イベント ハブを介して複数のデータ テーブルを Azure Data Explorer にエクスポートするときは、Log Analytics データのエクスポートで、名前空間あたりのイベント ハブの最大数に制限があることに注意してください。 データ エクスポートの詳細については、[Azure Monitor での Log Analytics ワークスペースのデータ エクスポート](/azure/azure-monitor/logs/logs-data-export?tabs=portal)に関するページを参照してください。
>
> ほとんどのお客様には、Event Hubs Standard レベルを使用することをお勧めします。 エクスポートする必要があるテーブルの量とそれらのテーブルに対するトラフィックの量によっては、Event Hubs Dedicated レベルを使用する必要がある場合があります。 詳細については、[イベント ハブに関するドキュメント](/azure/event-hubs/event-hubs-quotas)を参照してください。
>

> [!TIP]
> この手順の詳細については、「[チュートリアル: Azure Data Explorer で監視データを取り込んでクエリを実行する](/azure/data-explorer/ingest-data-no-code)」を参照してください。
>

**イベント ハブ経由でデータを Azure Data Explorer に エクスポートするには**:

1. **イベント ハブへの Log Analytics データのエクスポートを構成します**。 詳細については、[Azure Monitor での Log Analytics ワークスペースのデータ エクスポート](/azure/azure-monitor/platform/logs-data-export)に関するページを参照してください。

1. **Azure Data Explorer クラスターとデータベースを作成します**。 詳細については、次をご覧ください。

    - [Azure Data Explorer クラスターとデータベースを作成します](/azure/data-explorer/create-cluster-database-portal)
    - [Azure Data Explorer クラスターに適したコンピューティング SKU を選択する](/azure/data-explorer/manage-cluster-choose-sku)

1. **ターゲット テーブルを作成します**。 生データは最初に中間テーブルに取り込まれて、そこで生データは格納、操作、および展開されます。

    すべての新しいデータに適用される関数に類似した更新ポリシーを使用して、展開されたデータを、Azure Sentinel の元のテーブルと同じスキーマを持つ最終的なテーブルに取り込みます。

    生テーブルの保持期間を **0** 日に設定します。 データは、正しく書式設定されたテーブルだけに格納され、変換されるとすぐに生テーブルで削除されます。

    詳細については、[Azure Data Explorer での監視データの取り込みとクエリの実行](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)に関するページを参照してください。

1. <a name="mapping"></a>**テーブル マッピングを作成します**。 JSON テーブルをマップして、イベント ハブから送られてきたレコードが生イベント テーブルにどのように配置されるかを定義します。 詳細については、[メトリックおよびログ データの更新ポリシーの作成](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)に関するページを参照してください。

1. **更新ポリシーを作成して、それを生レコード テーブルにアタッチします**。 この手順では、更新ポリシーと呼ばれる関数を作成して、それをターゲット テーブルにアタッチして、データがインジェスト時に変換されるようにします。

    > [!NOTE]
    > この手順が必要になるのは、Azure Data Explorer のデータ テーブルを Azure Sentinel と同じスキーマと形式にしたい場合だけです。
    >

    詳細については、「[イベント ハブを Azure Data Explorer に接続する](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)」を参照してください。

1. **イベント ハブと Azure Data Explorer の生データ テーブルの間にデータ接続を作成します**。 データをイベント ハブにエクスポートする方法の詳細を指定して Azure Data Explorer を構成します。

    [Azure Data Explorer に関するドキュメント](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)の手順に従い、次の詳細を指定します。

    - **[ターゲット]** で、 生データを含む特定のテーブルを指定します。
    - **形式**。 `.json` をテーブル形式として指定します。
    - **適用されるマッピング**。 上の[手順 4](#mapping) で作成したマッピング テーブルを指定します。


1. **ターゲット テーブルの保持期間を変更します**。 [既定の Azure Data Explorer 保持ポリシー](/azure/data-explorer/kusto/management/retentionpolicy)は、必要な期間よりもはるかに長い場合があります。

    保持ポリシーを 1 年に更新するには、次のコマンドを使用します。

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure Storage または Azure Data Factory](#tab/azure-storage-azure-data-factory)

このセクションでは、Azure Sentinel データを Log Analytics から Azure Storage にエクスポートして、そこで Azure Data Factory が通常のジョブを実行してデータを Azure Data Explorer にエクスポートできるようにする方法について説明します。

Azure Storage と Azure Data Factory を使用すると、Azure Storage のデータを Azure Sentinel または Log Analytics の保持期間の上限に近くなったときだけコピーすることができます。 データの重複はなく、Azure Data Explorer を使用するのは、 Azure Sentinel の保持期間の上限より古いデータにアクセスする場合 "*だけ*" になります。

> [!TIP]
> Azure Storage と Azure Data Factory をレガシ データで使用するためのアーキテクチャはより複雑になりますが、この方法は全体的に大きなコスト削減を実現できます。
>
次の画像は、データを Azure Storage にエクスポートして、そこから Azure Data Factory が通常のジョブを実行して、さらにAzure Data Explorer にエクスポートするフローの例を示しています。

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Azure Storage と Azure Data Factory を介してデータを Azure Data Explorer にエクスポートします。":::

**Azure Storage と Azure Data Factory を介してデータを Azure Data Explorer にエクスポートするには**:

1. **イベント ハブへの Log Analytics データのエクスポートを構成します**。 詳細については、[Azure Monitor での Log Analytics ワークスペースのデータ エクスポート](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export)に関するページを参照してください。

1. **Azure Data Explorer クラスターとデータベースを作成します**。 詳細については、次をご覧ください。

    - [Azure Data Explorer クラスターとデータベースを作成します](/azure/data-explorer/create-cluster-database-portal)
    - [Azure Data Explorer クラスターに適したコンピューティング SKU を選択する](/azure/data-explorer/manage-cluster-choose-sku)

1. **ターゲット テーブルを作成します**。 生データは最初に中間テーブルに取り込まれて、そこで生データは格納、操作、および展開されます。

    すべての新しいデータに適用される関数に類似した更新ポリシーを使用して、展開されたデータを、Azure Sentinel の元のテーブルと同じスキーマを持つ最終的なテーブルに取り込みます。

    生テーブルの保持期間を **0** 日に設定します。 データは、正しく書式設定されたテーブルだけに格納され、変換されるとすぐに生テーブルで削除されます。

    詳細については、[Azure Data Explorer での監視データの取り込みとクエリの実行](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)に関するページを参照してください。

1. <a name="mapping"></a>**テーブル マッピングを作成します**。 JSON テーブルをマップして、イベント ハブから送られてきたレコードが生イベント テーブルにどのように配置されるかを定義します。 詳細については、[メトリックおよびログ データの更新ポリシーの作成](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)に関するページを参照してください。

1. **更新ポリシーを作成して、それを生レコード テーブルにアタッチします**。 この手順では、更新ポリシーと呼ばれる関数を作成して、それをターゲット テーブルにアタッチして、データがインジェスト時に変換されるようにします。

    > [!NOTE]
    > この手順が必要になるのは、Azure Data Explorer のデータ テーブルを Azure Sentinel と同じスキーマと形式にしたい場合だけです。
    >

    詳細については、「[イベント ハブを Azure Data Explorer に接続する](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)」を参照してください。

1. **イベント ハブと Azure Data Explorer の生データ テーブルの間にデータ接続を作成します**。 データをイベント ハブにエクスポートする方法の詳細を指定して Azure Data Explorer を構成します。

    [Azure Data Explorer に関するドキュメント](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)の手順に従い、次の詳細を指定します。

    - **[ターゲット]** で、 生データを含む特定のテーブルを指定します。
    - **形式**。 `.json` をテーブル形式として指定します。
    - **適用されるマッピング**。 上の[手順 4](#mapping) で作成したマッピング テーブルを指定します。

1. **Azure Data Factory パイプラインを設定します**。

    - Azure Storage および Azure Data Explorer のリンク サービスを作成します。 詳細については、次をご覧ください。

        - [Azure Data Factory を使用して Azure Blob Storage のデータをコピーおよび変換する](../data-factory/connector-azure-blob-storage.md)
        - [Azure Data Factory を使用して Azure Data Explorer をコピー先またはコピー元としてデータをコピーする](../data-factory/connector-azure-data-explorer.md)

    - Azure Storage からデータセットを作成する 詳細については、「[Azure Data Factory のデータセット](../data-factory/concepts-datasets-linked-services.md)」を参照してください。

    - コピー操作を使用したデータ パイプラインを、**LastModifiedDate** プロパティに基づいて作成します。

        詳細については、「[Azure Data Factory で **LastModifiedDate** に基づいて新規および変更済みのファイルをコピーする](../data-factory/solution-template-copy-new-files-lastmodifieddate.md)」を参照してください。

---

## <a name="design-considerations"></a>設計上の考慮事項

Azure Sentinel データを Azure Data Explorer に格納するときは、次の点を考慮してください。

|考慮事項  |説明  |
|---------|---------|
|**クラスター サイズと SKU**     | クラスター内のノード数と VM SKU については、慎重に計画してください。 これらの要因によって、処理能力の量とホット キャッシュ (SSD とメモリ) のサイズが決まります。 キャッシュが大きければ大きいほど、より多くのデータを高いパフォーマンスでクエリできるようになります。 <br><br>[Azure Data Explorer のサイズ計算ツール](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)にアクセスすることをお勧めします。ここでは、さまざまな構成を試して、結果としてのコストを確認できます。 <br><br>Azure Data Explorer には、クラスターの負荷に応じて必要なノードの追加または削除をインテリジェントに判断する自動スケーリング機能も用意されています。 詳細については、「[需要の変化に対応するために Azure Data Explorer のクラスターの水平スケーリング (スケールアウト) を管理する](/azure/data-explorer/manage-cluster-horizontal-scaling)」を参照してください。      |
|**ホットまたはコールド キャッシュ**     | Azure Data Explorer では、ホット キャッシュ内のデータ テーブルを制御し、結果をより迅速に返すことができます。 大量のデータが Azure Data Explorer クラスターにある場合は、テーブルを月別に分割して、ホット キャッシュ内にあるデータの細分性を高めることができます。 <br><br>詳細については、「[キャッシュ ポリシー (ホットおよびコールド キャッシュ)](/azure/data-explorer/kusto/management/cachepolicy)」を参照してください。       |
|**保持**     |   Azure Data Explorer では、データがいつデータベースまたは個々のテーブルから削除されるかを構成できます。これは、ストレージ コストを抑えるための重要な要素でもあります。 <br><br> 詳細については、「[Retention policy](/azure/data-explorer/kusto/management/retentionpolicy)」 (アイテム保持ポリシー) を参照してください。       |
|**Security**     |  Azure Data Explorer には、ID 管理や暗号化など、データを保護するために役立ついくつかの設定があります。 特にロールベースのアクセス制御 (RBAC) では、データベース、テーブル、またはテーブル内の行へのアクセスを制限するように Azure Data Explorer を構成できます。 詳細については、「[Azure Data Explorer のセキュリティ](/azure/data-explorer/security)」と「[行レベルのセキュリティ](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy)」を参照してください。|
|**データ共有**     |   Azure Data Explorer では、データの一部をパートナーやベンダーなどの他の関係者が使用できるようにしたり、他の関係者からデータを購入したりすることができます。 詳細については、「[Azure Data Share を使用して Azure Data Explorer とデータを共有する](/azure/data-explorer/data-share)」を参照してください。      |
| **その他のコスト要素** | 次の方法については、その他のコスト要素を検討してください。 <br><br>**Azure Event Hub 経由でのデータのエクスポート**: <br>- Log Analytics データのエクスポート コスト。エクスポートされた GB 数ごとに課金されます。 <br>- イベント ハブのコスト。スループット単位で課金されます。  <br><br>**Azure Storage と Azure Data Factory を介したデータのエクスポート**: <br>- Log Analytics データのエクスポート。エクスポートされた GB 数ごとに課金されます。 <br>-Azure Storage。保存された GB 数ごとに課金されます。 <br>-Azure Data Factory。実行されるアクティビティのコピーごとに課金されます。
|     |         |

## <a name="next-steps"></a>次のステップ

データの保存場所に関係なく、Azure Sentinel を使用してハンティングと調査を続けます。

詳細については、次をご覧ください。

- [チュートリアル:Azure Sentinel でインシデントを調査する](tutorial-investigate-cases.md)
- [Azure Sentinel で脅威を検出する](hunting.md)
