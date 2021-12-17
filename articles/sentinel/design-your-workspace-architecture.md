---
title: Microsoft Sentinel ワークスペース アーキテクチャを設計する | Microsoft Docs
description: デシジョン ツリーを使用して、Microsoft Sentinel ワークスペース アーキテクチャを設計する方法を理解します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0fccafee7a645be13ae17eca98d99305dd2b4a10
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521550"
---
# <a name="design-your-microsoft-sentinel-workspace-architecture"></a>Microsoft Sentinel ワークスペース アーキテクチャを設計する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel ワークスペース アーキテクチャの設計方法に関する重要な決定を下す際に役立つデシジョン ツリーを提供します。 詳細については、「[Microsoft Sentinel のサンプル ワークスペースの設計](sample-workspace-designs.md)」および「[Microsoft Sentinel アーキテクチャのベスト プラクティス](best-practices-workspace-architecture.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

デシジョン ツリーを使用する前に、次の情報がそろっていることを確認してください。

|前提条件  | 説明 |
|---------|---------|
|**Azure でのデータ所在地に関連する規制要件**     |  Microsoft Sentinel は、[Log Analytics の GA でサポートされている](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)、ほとんどのリージョンのワークスペースで実行できます。ただし、実行できないリージョンもあります。 Log Analytics の新しくサポートされたリージョンでは、Microsoft Sentinel サービスのオンボードに時間がかかる場合があります。 <br><br> インシデント、ブックマーク、分析ルールなど、Microsoft Sentinel によって生成されたデータには、顧客の Log Analytics ワークスペースをソースとする顧客データが含まれている可能性があります。<br><br> 詳細については、「[リージョン別の提供状況とデータの保存場所](quickstart-onboard.md#geographical-availability-and-data-residency)」を参照してください。|
|**[データ ソース]**     |   Microsoft と Microsoft 以外の両方のソリューションへの組み込みコネクタを含め、どの[データ ソース](connect-data-sources.md)に接続する必要があるかを確認します。 Common Event Format (CEF)、Syslog、または REST-API を使用して、データ ソースを Microsoft Sentinel に接続することもできます。 <br><br>複数の Azure の場所にログ収集の必要な Azure VM があり、データ エグレス コストの節約が重要な場合は、[帯域幅価格計算ツール](https://azure.microsoft.com/pricing/details/bandwidth/#overview)を使用して、Azure の場所ごとのデータ エグレス コストを計算する必要があります。      |
|**ユーザー ロールとデータ アクセス レベルまたはアクセス許可**     |    Microsoft Sentinel では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) を使用して、Azure のユーザー、グループ、サービスに割り当てることができる[組み込みロール](../role-based-access-control/built-in-roles.md)が提供されています。 <br><br>Microsoft Sentinel のすべての組み込みロールでは、Microsoft Sentinel ワークスペース内のデータに対して読み取りアクセス権が付与されます。 したがって、データ ソースまたは行レベルごとにデータ アクセスを制御する必要があるかどうかを確認する必要があります。このことはワークスペースの設計に関する決定に影響を与えるからです。 詳細については、「[カスタム ロールと高度な Azure RBAC](roles.md#custom-roles-and-advanced-azure-rbac)」を参照してください。     |
|**日次インジェスト率**     |  日次インジェスト率 (通常 GB/日単位) は、Microsoft Sentinel のコスト管理と計画に関する考慮事項およびワークスペースの設計における重要な要素の 1 つです。 <br><br>ほとんどのクラウドやハイブリッド環境では、取り込まれたデータのほとんどは、ファイアウォールやプロキシなどのネットワーク デバイスおよび Windows や Linux サーバーによって生成されます。 最も正確な結果を得るには、データ ソースを包括的にインベントリすることをお勧めします。 <br><br>また、Microsoft Sentinel の[コスト計算ツール](https://cloudpartners.transform.microsoft.com/download?assetname=assets%2FAzure_Sentinel_Calculator.xlsx&download=1)には、データ ソースの占有領域を見積もるのに役立つ表が含まれています。 <br><br>**重要**: これらの見積もりは出発点であり、ログの詳細設定やワークロードによって差異が生まれます。 実際のシステムを定期的に監視して、すべての変更を追跡することをお勧めします。 各自のシナリオに基づいて、定期的に監視することをお勧めします。 <br><br>詳細については、「[Azure Monitor ログで使用量とコストを管理する](../azure-monitor/logs/manage-cost-storage.md)」を参照してください。       |
|     |         |

## <a name="decision-tree"></a>デシジョン ツリー

次の画像は、ワークスペースを最適に設計する方法を理解するのに役立つ完全なデシジョン ツリー フロー チャートを示しています。

[![Microsoft Sentinel ワークスペースの設計デシジョン ツリー。](media/best-practices/workspace-decision-tree.png)](media/best-practices/workspace-decision-tree.png#lightbox)

次のセクションでは、このデシジョン ツリーのフルテキスト バージョンを提供します。画像内で参照されている次の注記が含まれています。

[注 #1](#note1) | [注 #2](#note2)  | [注 #3](#note3)  | [注 #4](#note4)  | [注 #5](#note5)  | [注 #6](#note6)  | [注 #7](#note7)  | [注 #8](#note8)  | [注 #9](#note9) | [注 #10](#note10)

### <a name="step-1-new-or-existing-workspace"></a>手順 1: ワークスペースは新規か既存のものか?

Microsoft Sentinel に使用できる既存のワークスペースがありますか?

- [なくて、いずれにしても新しいワークスペースを作成する場合](#step-2-keeping-data-in-different-azure-geographies)は、**手順 2** に直接進みます。

- 使用できる可能性がある **既存のワークスペースがある場合** は、取り込むデータの量を検討します。

  - **取り込む量が 100 GB/日を "*上回る*" 場合** は、コスト効率のために個別のワークスペースを使用することをお勧めします。

  - **取り込む量が 100 GB/日を "*下回る*" 場合** は、[手順 2](#step-2-keeping-data-in-different-azure-geographies) に進んで、さらなる評価を行います。 [手順 5](#step-5-collecting-any-non-soc-data) でこの問題が持ち上がった場合は、この質問を再度検討します。

### <a name="step-2-keeping-data-in-different-azure-geographies"></a>手順 2: 異なる Azure 地域にデータを保持するか?

- **異なる Azure 地域にデータを保持するという規制要件がある場合** は、コンプライアンス要件がある Azure リージョンごとに個別の Microsoft Sentinel ワークスペースを使用します。 詳細については、「[リージョンに関する考慮事項](best-practices-workspace-architecture.md#region-considerations)」を参照してください。

- **異なる Azure 地域にデータを保持する必要がない場合** は、[手順 3](#step-3-do-you-have-multiple-azure-tenants) に進みます。

### <a name="step-3-do-you-have-multiple-azure-tenants"></a>手順 3: 複数の Azure テナントがあるか?

- **テナントが 1 つしかない場合** は、[手順 4](#step-4-splitting-billing--charge-back) に直接進みます。

- **複数の Azure テナントがある場合** は、Office 365 や Microsoft 365 Defender など、テナントの境界に固有のログを収集するかどうかを検討します。

  - **テナント固有のログがない場合** は、[手順 4](#step-4-splitting-billing--charge-back) に直接進みます。

  - **テナント固有のログを収集 "*する*" 場合** は、Azure AD テナントごとに個別の Microsoft Sentinel ワークスペースを使用します。 [手順 4](#step-4-splitting-billing--charge-back) に進み、その他について検討します。

    <a name="note1"></a>[デシジョン ツリーの注 #1](#decision-tree): Office 365 や Microsoft Defender for Cloud など、テナントの境界に固有のログは同じテナント内のワークスペースにのみ格納できます。

    カスタム コレクターを使用して他のテナントのワークスペースからテナント固有のログを収集することは "*できます*" が、次のような短所があるため、お勧めしません。

    - カスタム コネクタによって収集されたデータは、カスタム テーブルに取り込まれます。 そのため、組み込みのルールとブックを一部使用することができません。
    - カスタム テーブルは、UEBA や機械学習ルールなど、一部の組み込み機能では考慮されません。
    - カスタム コネクタには、Azure Functions や Logic Apps を使用するなど、追加のコストと労力が必要です。

    これらの欠点が組織にとって問題とならない場合は、個別の Microsoft Sentinel ワークスペースを使用する代わりに、[手順 4](#step-4-splitting-billing--charge-back) に進みます。

### <a name="step-4-splitting-billing--charge-back"></a>手順 4: 請求またはチャージバックを分割するか?

請求またはチャージバックを分割する必要がある場合は、使用状況レポートまたは手動でのクロスチャージが役立つかどうかを検討します。

- **請求またはチャージバックを分割する必要が "*ない*" 場合** は、[手順 5](#step-5-collecting-any-non-soc-data) に進みます。

- **請求またはチャージバックを分割する必要が "*ある*" 場合** は、[使用状況レポートまたは手動でのクロスチャージ](azure-sentinel-billing.md)が役立つかどうかを検討します。

  - **使用状況レポートまたは手動のクロスチャージが役立つ場合** は、[手順 5](#step-5-collecting-any-non-soc-data) に進みます。

  - **使用状況レポートと手動のクロスチャージが "*どちらも役立たない*" 場合** は、コストの所有者ごとに個別の Microsoft Sentinel ワークスペースを使用します。

  <a name="note2"></a>[デシジョン ツリーの注 #2](#decision-tree): 詳細については、「[Microsoft Sentinel のコストと課金](azure-sentinel-billing.md)」を参照してください。

### <a name="step-5-collecting-any-non-soc-data"></a>手順 5: SOC 以外のデータを収集するか?

- オペレーショナル データなど、**SOC 以外のデータを収集しない場合** は、[手順 6](#step-6-multiple-regions) に直接進むことができます。

- **SOC 以外のデータを収集 "*する*" 場合** は、重複があるかどうかを検討します。ある場合は、SOC と SOC 以外の両方のデータのデータ ソースが同じである必要があります。

  **SOC と SOC 以外のデータが重複 "*している"* 場合** は、重複しているデータを SOC データとしてのみ扱います。 次に、SOC と SOC 以外の "*両方*" のデータのインジェストが個別では 100 GB/日を下回るものの、まとめると 100 GB/日を超えるかどうかを検討します。

  - **はい**: [手順 6](#step-6-multiple-regions) に進み、さらなる評価を行います。
  - **いいえ**: コスト効率の点で、同じワークスペースを使用しないことをお勧めします。 [手順 6](#step-6-multiple-regions) に進み、さらなる評価を行います。

  どちらの場合も、詳細については、[注 10](#note10) を参照してください。

  **重複データが "*ない*" 場合** は、SOC と SOC 以外の "*両方*" のデータのインジェストが個別では 100 GB/日を下回るが、まとめると 100 GB/日を超えるかどうかを検討します。

  - **はい**: [手順 6](#step-6-multiple-regions) に進み、さらなる評価を行います。 詳細については、[注 3](#combining-your-soc-and-non-soc-data) を参照してください。
  - **いいえ**: コスト効率の点で、同じワークスペースを使用しないことをお勧めします。 [手順 6](#step-6-multiple-regions) に進み、さらなる評価を行います。

#### <a name="combining-your-soc-and-non-soc-data"></a>SOC と SOC 以外のデータをまとめる

<a name="note3"></a>[デシジョン ツリーの注 #3](#decision-tree): 一般に、SOC 以外のデータが Microsoft Sentinel のコストの対象とならないように、SOC 以外のデータ用に個別のワークスペースを保持することをお勧めしますが、SOC と非 SOC データをまとめた方がそれらを分けるよりもコストが低くなる場合があります。

たとえば、セキュリティ ログのインジェストが 50 GB/日、オペレーション ログのインジェストが 50 GB/日で、ワークスペースが米国東部リージョンにある組織について考えてみます。

次の表では、個別のワークスペースがある場合とない場合のワークスペース オプションを比較しています。

> [!NOTE]
> 次の表に記載されているコストと条件は架空のものであり、説明のためにのみ使用されています。 最新のコスト情報については、Microsoft Sentinel 価格計算ツールを参照してください。
>

|ワークスペース アーキテクチャ  |説明 |
|---------|---------|
|SOC チームが、Microsoft Sentinel が有効になっている専用のワークスペースを利用する。 <br><br>運用チームが、Microsoft Sentinel が有効になっている専用のワークスペースを利用する。     |  **SOC チーム**: <br>50 GB/日の Microsoft Sentinel のコストは、1 か月あたり 6,500 ドルです。<br>最初の 3 か月のリテンション期間は無料です。 <br><br>**運用チーム**:<br>- 50 GB/日の Log Analytics のコストは、1 か月あたり約 3,500 ドルです。<br>- 最初の 31 日のリテンション期間は無料です。<br><br>両方の合計コストは、1 か月あたり 10,000 ドルです。       |
|SOC と運用の両方のチームが、Microsoft Sentinel が有効になっている同じワークスペースを共有する。 |両方のログをまとめると、インジェストは 100 GB/日になり、コミットメント レベルの資格 (Sentinel の場合は 50%、LA の場合は 15%) を満たします。       <br><br>100 GB/日の Microsoft Sentinel のコストは、1 か月あたり 9,000 ドルです。      |
|     |         |

この例では、両方のワークスペースをまとめることで、1 か月あたり 1,000 ドルのコストを削減し、さらに運用チームが無料で利用できるリテンション期間が、たった 31 日ではなく、3 か月間になります。

この例は、SOC と SOC 以外の両方のデータのインジェスト サイズが 50 GB/日以上かつ 100 GB/日未満である場合にのみ適用されます。

<a name="note10"></a>[デシジョン ツリーの注 #10](#decision-tree): SOC 以外のデータが Microsoft Sentinel のコストの対象とならないように、SOC 以外のデータ用に個別のワークスペースを使用することをお勧めします。

ただし、SOC 以外のデータに対する個別のワークスペースについてのこの推奨は純粋なコストベースの観点からのものであり、使用するワークスペースの数を 1 つにするか複数にするかを決定する際に確認する重要な設計要素は他にもあります。 インジェスト コストが二重にかからないようにするには、テーブルレベルの Azure RBAC を使用して、重複するデータを 1 つのワークスペースのみに収集することを検討してください。

### <a name="step-6-multiple-regions"></a>手順 6: リージョンは複数か?

- **"*単一*" のリージョン内にある Azure VM からのみログを収集する場合** は、[手順 7](#step-7-segregating-data-or-defining-boundaries-by-ownership) に直接進みます。

- **"*複数*" のリージョン内にある Azure VM からログを収集する場合** は、データ エグレス コストはどの程度問題となりますか?

  <a name="note4"></a>[デシジョン ツリーの注 #4](#decision-tree):データ エグレスは、Azure データセンターからデータを移動する際の[帯域幅コスト](https://azure.microsoft.com/pricing/details/bandwidth/)を指します。 詳細については、「[リージョンに関する考慮事項](best-practices-workspace-architecture.md#region-considerations)」を参照してください。

  - 個別のワークスペースを維持するために必要な作業量を減らすことが優先事項である場合は、[手順 7](#step-7-segregating-data-or-defining-boundaries-by-ownership) に進みます。

  - 個別のワークスペースを維持する価値を見いだすうえでデータ エグレス コストが十分に懸念される場合は、データ エグレス コストを削減する必要があるリージョンごとに個別の Microsoft Sentinel ワークスペースを使用します。

    <a name="note5"></a>[デシジョン ツリーの注 #5](#decision-tree): ワークスペースの数はできる限り少なくすることをお勧めします。 [Azure 価格計算ツール](azure-sentinel-billing.md#estimate-microsoft-sentinel-costs)を使用してコストを見積もり、実際に必要なリージョンを特定し、エグレス コストが低いリージョンのワークスペースをまとめます。 帯域幅コストは、Microsoft Sentinel と Log Analytics の個々のインジェスト コストと比較すると、Azure の請求額のごく一部でしかない場合があります。

    たとえば、次の場合のコストを見積もります。

    - VM が 1,000 台あり、それぞれ 1 GB/日を生成する
    - 米国リージョンからヨーロッパ リージョンにデータを送信する
    - エージェントで 2:1 の圧縮率を使用する

    このコスト見積もりの計算は `1000 VMs * (1GB/day ÷ 2) * 30 days/month * $0.05/GB = $750/month bandwidth cost` となります

    このサンプル コストは、Microsoft Sentinel と Log Analytics の個々のワークスペースの月額コストと比較すると、はるかに安価です。

    > [!NOTE]
    > 記載されているコストは架空のものであり、説明のためにのみ使用されています。 最新のコスト情報については、Microsoft Sentinel 価格計算ツールを参照してください。
    >

### <a name="step-7-segregating-data-or-defining-boundaries-by-ownership"></a>手順 7: データを分離したり、所有権による境界を定義したりするか?

- **データを分離したり、所有権による境界を定義したりする必要が "*ない*" 場合** は、[手順 8](#step-8-controlling-data-access-by-data-source--table) に直接進みます。

- **データを分離したり、所有権に基づいて境界を定義したりする必要が "*ある*" 場合**、各データ所有者が Microsoft Sentinel ポータルを使用する必要がありますか?

  - 各データ所有者が Microsoft Sentinel ポータルにアクセスできる必要がある場合は、所有者ごとに個別の Microsoft Sentinel ワークスペースを使用します。

    <a name="note6"></a>[デシジョン ツリーの注 #6](#decision-tree): Microsoft Sentinel ポータルにアクセスするには、各ユーザーが少なくとも [Microsoft Sentinel 閲覧者](../role-based-access-control/built-in-roles.md)のロールと、ワークスペース内のすべてのテーブルに対する **閲覧者** アクセス許可を持っている必要があります。 ワークスペース内のすべてのテーブルにアクセスできないユーザーは、Log Analytics を使用して検索クエリでログにアクセスする必要があります。

  - Microsoft Sentinel ポータルにアクセスできない所有者にとって Log Analytics を使用したログへのアクセスで十分な場合は、[手順 8](#step-8-controlling-data-access-by-data-source--table) に進みます。

  詳細については、「[Microsoft Sentinel のアクセス許可](roles.md)」を参照してください。

### <a name="step-8-controlling-data-access-by-data-source--table"></a>手順 8: データ ソースまたはテーブルごとにデータ アクセスを制御するか?

- **ソースまたはテーブルごとにデータ アクセスを制御する必要が "*ない*" 場合** は、1 つの Microsoft Sentinel ワークスペースを使用します。

- **ソースまたはテーブルごとにデータアクセスを制御する必要が "*ある*" 場合** は、次の状況で [リソースコンテキスト RBAC](resource-context-rbac.md) を使用することを検討します。

  - 行レベルでアクセスを制御する必要がある場合 (各データ ソースまたはテーブルに複数の所有者を設定する場合など)

  - 複数のカスタム データ ソースまたはテーブルがある場合 (それぞれに個別のアクセス許可が必要です)

  それ以外で、行レベルでアクセスを制御する必要が "*ない*" 場合は、複数のカスタム データ ソースまたはテーブルに個別のアクセス許可を付与し、1 つの Microsoft Sentinel ワークスペースを使用し、テーブルレベル RBAC を使用してデータ アクセスを制御します。

#### <a name="considerations-for-resource-context-or-table-level-rbac"></a>リソースコンテキストまたはテーブルレベルの RBAC に関する考慮事項

リソースコンテキストまたはテーブルレベルの RBAC の使用を計画している場合は、次を考慮します。

- <a name="note7"></a>[デシジョン ツリーの注 #7](#decision-tree): Azure 以外のリソースのためにリソースコンテキスト RBAC を構成する場合は、Microsoft Sentinel に送信するときにリソース ID をデータに関連付けることができます。これにより、リソースコンテキスト RBAC を使用してアクセス許可のスコープを指定できます。 詳細については、「[リソースコンテキスト RBAC を明示的に構成する](resource-context-rbac.md#explicitly-configure-resource-context-rbac)」および[デプロイ別のアクセス モード](../azure-monitor/logs/design-logs-deployment.md)に関する記事を参照してください。

- <a name="note8"></a>[デシジョン ツリーの注 #8](#decision-tree): [リソースのアクセス許可](../azure-monitor/logs/manage-access.md)または[リソースコンテキスト](../azure-monitor/logs/design-logs-deployment.md)を使用すると、ユーザーはアクセス権を持つリソースのログのみを表示できます。 ワークスペースのアクセス モードは、**ユーザー リソースまたはワークスペースのアクセス許可** に設定する必要があります。 Microsoft Sentinel の **[ログ]** ページの検索結果には、ユーザーがアクセス許可を持っているリソースに関連するテーブルのみが含まれます。

- <a name="note9"></a>[デシジョン ツリーの注 #9](#decision-tree): [テーブルレベルの RBAC](../azure-monitor/logs/manage-access.md) では、他のアクセス許可に加えて、Log Analytics ワークスペースのデータをさらにきめ細かく定義できます。 この制御を使用すると、特定のユーザーのグループのみがアクセスできる特定のデータ型を定義できます。 詳細については、「[Microsoft Sentinel のテーブルレベルの RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)」を参照してください。

## <a name="next-steps"></a>次の手順

このデシジョン ツリーの実際の例については、「[Microsoft Sentinel のサンプル ワークスペースの設計](sample-workspace-designs.md)」を参照してください。

詳細については、次を参照してください。

- [Microsoft Sentinel ワークスペース アーキテクチャのベスト プラクティス](best-practices-workspace-architecture.md)
- [ Microsoft Sentinel のベスト プラクティス](best-practices.md)
