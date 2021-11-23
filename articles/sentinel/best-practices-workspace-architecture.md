---
title: Microsoft Sentinel のワークスペース アーキテクチャのベスト プラクティス
description: Microsoft Sentinel のワークスペースを設計する際のベスト プラクティスについて説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a7d4dd4c8bcda776b5fe4cea886b5e8a6bd87ea9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714620"
---
# <a name="microsoft-sentinel-workspace-architecture-best-practices"></a>Microsoft Sentinel ワークスペース アーキテクチャのベスト プラクティス

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel ワークスペース デプロイを計画する際、Log Analytics ワークスペースのアーキテクチャも設計する必要があります。 ワークスペースのアーキテクチャに関する決定は、通常、ビジネスと技術面の要件により主導されます。 この記事では、組織にとって適切なワークスペースのアーキテクチャを決定するのに役立つ、以下を含む主な決定要因について説明します。

- シングル テナントと複数のテナントのどちらを使用するか
- データの収集と保存に関するコンプライアンス要件
- Microsoft Sentinel データへのアクセスを制御する方法
- さまざまなシナリオのコストへの影響

詳細については、一般的なシナリオについては「[Microsoft Sentinel ワークスペース アーキテクチャを設計する](design-your-workspace-architecture.md)」と「[サンプル ワークスペースの設計](sample-workspace-designs.md)」、また「[Microsoft Sentinel のデプロイ前のアクティビティとデプロイの前提条件](prerequisites.md)」を参照してください。

詳細については、「[Architecting SecOps for Success: Best Practices for Deploying Microsoft Sentinel](https://youtu.be/DyL9MEMhqmI)」 (SecOps の設計を成功させる: Microsoft Sentinel をデプロイするためのベスト プラクティス) の動画をご覧ください


## <a name="tenancy-considerations"></a>テナントに関する考慮事項

ワークスペースが少ないほど管理は簡単ですが、複数のテナントとワークスペースに対する固有のニーズがある場合があります。 たとえば多くの組織が持つクラウド環境では、合併や買収の結果として、または ID 分離の要件に起因して、複数の [Azure Active Directory (Azure AD) テナント](../active-directory/develop/quickstart-create-new-tenant.md)が存在します。

使用するテナントとワークスペースの数を決定する際は、ほとんどの Microsoft Sentinel 機能が単一のワークスペースまたは Microsoft Sentinel インスタンスを使用して動作し、Microsoft Sentinel によってワークスペース内に保存されているすべてのログが取り込まれることを考慮してください。

> [!IMPORTANT]
> コストは、Microsoft Sentinel アーキテクチャを決定する際の主な考慮事項の 1 つです。 詳細については、[Microsoft Sentinel のコストと課金](billing.md)に関する記事をご覧ください。
>
### <a name="working-with-multiple-tenants"></a>複数のテナントの利用

マネージド セキュリティ サービス プロバイダー (MSSP) である場合など、複数のテナントがある場合は、Azure AD テナントごとに少なくとも 1 つのワークスペースを作成して、自身の Azure AD テナント内でのみ動作する組み込みの[サービス間データ コネクタ](connect-data-sources.md#service-to-service-integration)をサポートすることをお勧めします。

診断設定に基づくすべてのコネクタは、リソースが存在するのと同じテナントに配置されていないワークスペースには接続できません。 これは、[Azure Firewall](./data-connectors-reference.md#azure-firewall)、[Azure Storage](./data-connectors-reference.md#azure-storage-account)、[Azure アクティビティ](./data-connectors-reference.md#azure-activity)、または [Azure Active Directory](connect-azure-active-directory.md) などのコネクタが該当します。

[Azure Lighthouse](../lighthouse/how-to/onboard-customer.md) を使用すると、別々のテナント内の複数の Microsoft Sentinel インスタンスを管理するのに役立ちます。

> [!NOTE]
> [パートナー データ コネクタ](data-connectors-reference.md)は通常、API またはエージェント コレクションに基づいているため、特定の Azure AD テナントには接続されません。
>



## <a name="compliance-considerations"></a>コンプライアンスの考慮事項

データを収集、格納、および処理した後は、コンプライアンスが重要な設計要件になり、Microsoft Sentinel のアーキテクチャに大きな影響を与える可能性があります。 多くの国や地域では、あらゆる状況下で、だれがどのデータにアクセスできるかを検証および証明できる能力を持つことが重要なデータ主権要件であり、多くのお客様にとって、Microsoft Sentinel のワークフローでリスクを評価し、分析情報を得ることが優先事項です。

Microsoft Sentinel では、データは主に同じ地域またはリージョンに格納されて処理されますが、Microsoft の機械学習を利用する検出ルールを使用する場合など、一部の例外があります。 このような場合、データは処理のためにワークスペースの地理的な場所の外部にコピーされることがあります。

詳細については、次を参照してください。

- [リージョン別の提供状況とデータの保存場所](quickstart-onboard.md#geographical-availability-and-data-residency)
- [Azure におけるデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)
- [EU のデータを EU に格納して処理する - EU ポリシー ブログ](https://blogs.microsoft.com/eupolicy/2021/05/06/eu-data-boundary/)

コンプライアンスの検証を開始するには、データ ソースと、データを送信する方法と場所を評価します。

> [!NOTE]
> [Log Analytics エージェント](connect-windows-security-events.md)は FIPS 140 標準とともに、エージェントと Log Analytics サービスの間で転送中のデータ セキュリティを保証する TLS 1.2 をサポートします。 
>
> Microsoft Sentinel ワークスペースとは異なる地域またはリージョンにデータを送信する場合は、送信リソースが Azure に存在するかどうかに関係なく、同じ地域またはリージョンのワークスペースを使用することを検討してください。
>

## <a name="region-considerations"></a>リージョンに関する考慮事項

リージョンごとに別個の Microsoft Sentinel インスタンスを使用します。 Microsoft Sentinel は複数のリージョンで使用できますが、チーム、リージョン、サイトごとにデータを分ける要件があったり、規制や制御のために複数リージョン モデルが不可能だったり、必要以上に複雑になったりする場合があります。 リージョンごとに個別のインスタンスとワークスペースを使用すると、リージョン間でデータを移動するための帯域幅およびエグレスのコストを回避するのに役立ちます。

複数のリージョンを使用する場合は、次のことを考慮してください。

- エグレス コストは通常、仮想マシン上などでログを収集するために [Log Analytics または Azure Monitor エージェント](connect-windows-security-events.md)が必要な場合に適用されます。

- インターネット エグレスも課金されますが、Log Analytics ワークスペースの外部にデータをエクスポートしない限り、影響を受けない可能性があります。 たとえば、Log Analytics データをオンプレミス サーバーにエクスポートすると、インターネット　エグレス料金が発生する可能性があります。

- 帯域幅コストは、ソースと宛先のリージョンおよび収集方法によって異なります。 詳細については、次を参照してください。

    - [帯域幅の料金](https://azure.microsoft.com/pricing/details/bandwidth/)
    - [Log Analytics を使用したデータ転送の料金](../azure-monitor/logs/manage-cost-storage.md)。

- 分析ルール、カスタム クエリ、ブック、その他のリソースにテンプレートを使用して、デプロイをより効率的にします。 各リージョンに各リソースを手動でデプロイするのではなく、テンプレートをデプロイします。

- 診断設定に基づくコネクタでは、帯域幅内のコストは発生しません。 詳細については、「[Azure Monitor ログで使用量とコストを管理する](../azure-monitor/logs/manage-cost-storage.md#data-transfer-charges-using-log-analytics)」を参照してください。

たとえば、米国東部の Virtual Machines からログを収集して米国西部の Microsoft Sentinel ワークスペースに送信することにした場合、データ転送のイングレス コストが課金されます。 Log Analytics エージェントによって転送中のデータが圧縮されるため、帯域幅に対して課金されるサイズが、Microsoft Sentinel のログのサイズよりも小さくなる可能性があります。

世界中の複数のソースから Syslog および CEF のログを収集する場合、コンプライアンスが問題にならない限り、Microsoft Sentinel ワークスペースと同じリージョンに Syslog コレクターを設定して、帯域幅のコストを回避することをお勧めします。

帯域幅のコストが Microsoft Sentinel ワークスペースを分離することの正当な理由になるかどうかは、リージョン間の転送が必要なデータの量によって決まります。 コストの見積もりには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/details/bandwidth/)をご利用ください。

詳細については、「[Azure でのデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)」を参照してください。

## <a name="access-considerations"></a>アクセスに関する考慮事項

異なるチームが同じデータにアクセスする必要がある状況が計画されている場合があります。 たとえば、SOC チームはすべての Microsoft Sentinel データにアクセスできる必要がある一方、運用チームとアプリケーション チームは特定の部分にのみアクセスする必要があります。 また、独立したセキュリティ チームも Microsoft Sentinel の機能にアクセスする必要があるかもしれませんが、データ セットは異なります。

[リソース コンテキスト RBAC](resource-context-rbac.md) および[テーブル レベルの RBAC](../azure-monitor/logs/manage-access.md#table-level-azure-rbac) を組み合わせて、ほとんどのユース ケースをサポートする広範囲のアクセス オプションをチームに提供します。

詳細については、「[Microsoft Sentinel のアクセス許可](roles.md)」を参照してください。

### <a name="resource-context-rbac"></a>リソース コンテキスト RBAC

次の図は、セキュリティ チームと運用チームがさまざまなデータ セットにアクセスする必要があるワークスペース アーキテクチャの簡略化されたバージョンを示しており、必要なアクセス許可を提供するためにリソース コンテキスト RBAC が使用されています。


[![リソース コンテキスト RBAC のサンプル アーキテクチャの図。](media/resource-context-rbac/resource-context-rbac-sample.png)](media/resource-context-rbac/resource-context-rbac-sample.png#lightbox)

この図では、アクセス許可の分離性を高めるために、Microsoft Sentinel ワークスペースが別のサブスクリプションに配置されています。

> [!NOTE]
> もう 1 つのオプションは、Microsoft Sentinel をセキュリティ専用の別の管理グループの下に配置する方法です。これにより、最小限のアクセス許可の割り当てだけが確実に継承されます。 セキュリティ チーム内では、それぞれの機能に従って複数のグループにアクセス許可が割り当てられます。 これらのチームはワークスペース全体にアクセスできるので、完全な Microsoft Sentinel エクスペリエンスにアクセスでき、割り当てられている Microsoft Sentinel ロールによってのみ制限されます。 詳細については、「[Microsoft Sentinel のアクセス許可](roles.md)」を参照してください。
>

セキュリティ サブスクリプションに加えて、アプリケーション チームがワークロードをホストするために別のサブスクリプションが使用されます。 アプリケーション チームには、それぞれのリソース グループへのアクセス権が付与され、そこでリソースを管理できます。 この個別のサブスクリプションとリソース コンテキスト RBAC を使用すると、これらのチームは、直接アクセス "*できない*" ワークスペースにログが格納されている場合でも、アクセスできるすべてのリソースによって生成されたログを表示できます。 アプリケーション チームは、Azure portal の **[ログ]** 領域を使用して特定のリソースのログを表示したり、Azure Monitor を介してアクセスできるすべてのログを同時に表示したりして、ログにアクセスできます。

Azure リソースには、リソース コンテキスト RBAC のための組み込みサポートがありますが、Azure 以外のリソースを操作する場合は追加の微調整が必要な場合があります。 詳細については、「[リソースコンテキスト RBAC を明示的に構成する](resource-context-rbac.md#explicitly-configure-resource-context-rbac)」を参照してください。

### <a name="table-level-rbac"></a>テーブルレベルの RBAC

テーブル レベルの RBAC を使用すると、特定のデータ型 (テーブル) を定義して、指定したユーザーのセットにのみアクセス可能にすることができます。

たとえば、上の図で説明されているアーキテクチャを持つ組織が、内部監査チームに Office 365 ログへのアクセス権を付与する必要がある場合を考えてみます。 この場合、テーブル レベルの RBAC を使用して、他のテーブルへのアクセス許可を付与せずに、**OfficeActivity** テーブル全体へのアクセス権を監査チームに付与することができます。

### <a name="access-considerations-with-multiple-workspaces"></a>複数のワークスペースでのアクセスに関する考慮事項

組織内に異なるエンティティ、子会社、または地域があって、それぞれに Microsoft Sentinel へのアクセスを必要とする独自のセキュリティ チームが存在する場合は、エンティティまたは子会社ごとに個別のワークスペースを使用します。 1 つの Azure AD テナント内で、または Azure Lighthouse を使用する複数のテナントにわたって、別々のワークスペースを実装します。 

また中央の SOC チームが、オプションの追加の Microsoft Sentinel ワークスペースを使用して、分析ルールやブックなどの成果物を一元的に管理することもできます。

詳細については、「[複数のワークスペースの操作を簡略化する](#simplify-working-with-multiple-workspaces)」を参照してください。


## <a name="technical-best-practices-for-creating-your-workspace"></a>ワークスペースを作成するための技術的なベスト プラクティス

Microsoft Sentinel に使用する Log Analytics ワークスペースを作成するときに、次のベスト プラクティス ガイダンスを使用します。

- **ワークスペースの名前を指定するとき**、*Microsoft Sentinel* またはその他のインジケーターを名前に含め、他のワークスペース間で簡単に識別できるようにします。

- **Microsoft Sentinel と Microsoft Defender for Cloud の両方に同じワークスペースを使用** し、Microsoft Defender for Cloud で収集されるログもすべて Microsoft Sentinel に取り込み、使用できるようにします。 Microsoft Defender for Cloud によって作成された既定のワークスペースは、Microsoft Sentinel の利用可能なワークスペースとして表示されません。

- **予想されるデータ インジェストが 1 日あたり約 1 TB またはそれを超える場合、専用のワークスペース クラスターを使用します**。 [専用クラスター](../azure-monitor/logs/logs-dedicated-clusters.md)を使用すると、Microsoft Sentinel データのリソースをセキュリティ保護でき、大規模なデータ セットのクエリ パフォーマンスを向上させることができます。 また、専用クラスターには、組織のキーの暗号化と制御を強化するオプションも用意されています。

## <a name="simplify-working-with-multiple-workspaces"></a>複数のワークスペースの操作を簡略化する

複数のワークスペースを操作する必要がある場合は、[Microsoft Sentinel の各インスタンスのすべてのインシデントを 1 つの場所にまとめて一覧化](multiple-workspace-view.md)して、インシデントの管理と調査を簡略化します。

[クロスワークスペース ブック](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)など、Microsoft Sentinel の他のワークスペースに保持されているデータを参照するには、[クロスワークスペース クエリ](extend-sentinel-across-workspaces-tenants.md)を使用します。

クロスワークスペース クエリを使用する最適な状況は、貴重な情報が別のワークスペース、サブスクリプション、またはテナントに格納されていて、現在のアクションに価値を提供できる場合です。 たとえば、次のコードはクロスワークスペース クエリのサンプルを示しています。

```Kusto
union Update, workspace("contosoretail-it").Update, workspace("WORKSPACE ID").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

詳細については、「[ワークスペースおよびテナント全体での Microsoft Sentinel の拡張](extend-sentinel-across-workspaces-tenants.md)」を参照してください。
## <a name="next-steps"></a>次のステップ


> [!div class="nextstepaction"]
>[Microsoft Sentinel ワークスペース アーキテクチャを設計する](design-your-workspace-architecture.md)

> [!div class="nextstepaction"]
>[Microsoft Sentinel のサンプル ワークスペースの設計](sample-workspace-designs.md)

> [!div class="nextstepaction"]
>[Microsoft Sentinel をオンボードする](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](get-visibility.md)
