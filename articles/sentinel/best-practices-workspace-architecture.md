---
title: Azure Sentinel のワークスペース アーキテクチャのベスト プラクティス
description: Azure Sentinel のワークスペースを設計する際のベスト プラクティスについて説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/28/2021
ms.openlocfilehash: 7a477122e9a0642233a5bac7a1884818f216d2f3
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252325"
---
# <a name="azure-sentinel-workspace-architecture-best-practices"></a>Azure Sentinel ワークスペース アーキテクチャのベスト プラクティス

Azure Sentinel ワークスペース デプロイを計画するときに、Log Analytics ワークスペースのアーキテクチャも設計する必要があります。 ワークスペースのアーキテクチャに関する決定は、通常、ビジネスと技術面の要件により主導されます。 この記事では、組織にとって適切なワークスペースのアーキテクチャを決定するのに役立つ、以下を含む主な決定要因について説明します。

- シングル テナントと複数のテナントのどちらを使用するか
- データの収集と保存に関するコンプライアンス要件
- Azure Sentinel データへのアクセスを制御する方法
- さまざまなシナリオのコストへの影響

詳細については、「[Azure Sentinel ワークスペース アーキテクチャを設計する](design-your-workspace-architecture.md)」および一般的なシナリオの「[サンプル ワークスペースの設計](sample-workspace-designs.md)」、および「[デプロイ前のアクティビティと Azure Sentinel のデプロイの前提条件](prerequisites.md)」を参照してください。

## <a name="tenancy-considerations"></a>テナントに関する考慮事項

ワークスペースが少ないほど管理は簡単ですが、複数のテナントとワークスペースに対する固有のニーズがある場合があります。 たとえば多くの組織が持つクラウド環境では、合併や買収の結果として、または ID 分離の要件に起因して、複数の [Azure Active Directory (Azure AD) テナント](../active-directory/develop/quickstart-create-new-tenant.md)が存在します。

使用するテナントとワークスペースの数を決定する際は、ほとんどの Azure Sentinel 機能が 1 つのワークスペースまたは Azure Sentinel インスタンスを使用して動作し、Azure Sentinel によってワークスペース内に保存されているすべてのログが取り込まれることを考慮してください。

> [!IMPORTANT]
> コストは Azure Sentinel アーキテクチャを決定する際の主な考慮事項です。 詳細については、「[Azure Sentinel のコストと課金](azure-sentinel-billing.md)」を参照してください。
>
### <a name="working-with-multiple-tenants"></a>複数のテナントの利用

マネージド セキュリティ サービス プロバイダー (MSSP) である場合など、複数のテナントがある場合は、Azure AD テナントごとに少なくとも 1 つのワークスペースを作成して、自身の Azure AD テナント内でのみ動作する組み込みの[サービス間データ コネクタ](connect-data-sources.md#service-to-service-integration)をサポートすることをお勧めします。

診断設定に基づくすべてのコネクタは、リソースが存在するのと同じテナントに配置されていないワークスペースには接続できません。 これは、[Azure Firewall](connect-azure-firewall.md)、[Azure Storage](connect-azure-storage-account.md)、[Azure アクティビティ](connect-azure-activity.md)、または [Azure Active Directory](connect-azure-active-directory.md) などのコネクタが該当します。

[Azure Lighthouse](../lighthouse/how-to/onboard-customer.md) を使用すると、別々のテナント内の複数の Azure Sentinel インスタンスを管理するのに役立ちます。

> [!NOTE]
> [パートナー データ コネクタ](partner-data-connectors.md)は通常、API またはエージェント コレクションに基づいているため、特定の Azure AD テナントには接続されません。
>



## <a name="compliance-considerations"></a>コンプライアンスの考慮事項

データを収集、格納、および処理した後は、コンプライアンスが重要な設計要件になり、Azure Sentinel のアーキテクチャに大きな影響を及ぼす可能性があります。 すべての条件下でどのユーザーがどのデータにアクセスできるか検証して証明する能力を持つことは、多くの国や地域における重要なデータ主権要件であり、Azure Sentinel ワークフローでリスクを評価し、分析情報を得することは、多くのお客様にとって優先事項となります。

Azure Sentinel では、データは主に同じ地域またはリージョンに格納されて処理されますが、Microsoft の機械学習を利用する検出ルールを使用する場合などの一部の例外があります。 このような場合、データは処理のためにワークスペースの地理的な場所の外部にコピーされることがあります。

詳細については、次を参照してください。

- [リージョン別の提供状況とデータの保存場所](quickstart-onboard.md#geographical-availability-and-data-residency)
- [Azure におけるデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)
- [EU のデータを EU に格納して処理する - EU ポリシー ブログ](https://blogs.microsoft.com/eupolicy/2021/05/06/eu-data-boundary/)

コンプライアンスの検証を開始するには、データ ソースと、データを送信する方法と場所を評価します。

> [!NOTE]
> [Log Analytics エージェント](connect-windows-security-events.md)は FIPS 140 標準とともに、エージェントと Log Analytics サービスの間で転送中のデータ セキュリティを保証する TLS 1.2 をサポートします。 
>
> 送信リソースが Azure に存在するかどうかに関係なく、Azure Sentinel ワークスペースとは異なる地域またはリージョンにデータを送信する場合は、同じ地域またはリージョン内のワークスペースの使用を検討してください。
>

## <a name="region-considerations"></a>リージョンに関する考慮事項

リージョンごとに別個の Azure Sentinel インスタンスを使用します。 Azure Sentinel は複数のリージョンで使用できますが、チーム、リージョン、またはサイトによってデータを分離する要件があったり、複数リージョン モデルを不可能にしたり必要以上に複雑にしたりする規制や制御があったりする場合もあります。 リージョンごとに個別のインスタンスとワークスペースを使用すると、リージョン間でデータを移動するための帯域幅およびエグレスのコストを回避するのに役立ちます。

複数のリージョンを使用する場合は、次のことを考慮してください。

- エグレス コストは通常、仮想マシン上などでログを収集するために [Log Analytics または Azure Monitor エージェント](connect-windows-security-events.md)が必要な場合に適用されます。

- インターネット エグレスも課金されますが、Log Analytics ワークスペースの外部にデータをエクスポートしない限り、影響を受けない可能性があります。 たとえば、Log Analytics データをオンプレミス サーバーにエクスポートすると、インターネット　エグレス料金が発生する可能性があります。

- 帯域幅コストは、ソースと宛先のリージョンおよび収集方法によって異なります。 詳細については、次を参照してください。

    - [帯域幅の料金](https://azure.microsoft.com/pricing/details/bandwidth/)
    - [Log Analytics を使用したデータ転送の料金](../azure-monitor/logs/manage-cost-storage.md)。

- 分析ルール、カスタム クエリ、ブック、その他のリソースにテンプレートを使用して、デプロイをより効率的にします。 各リージョンに各リソースを手動でデプロイするのではなく、テンプレートをデプロイします。

- 診断設定に基づくコネクタでは、帯域幅内のコストは発生しません。 詳細については、「[Azure Monitor ログで使用量とコストを管理する](../azure-monitor/logs/manage-cost-storage.md#data-transfer-charges-using-log-analytics)」を参照してください。

たとえば、米国東部の Virtual Machines からログを収集し、それらを米国西部の Azure Sentinel ワークスペースに送信する場合、データ転送のイングレス コストが課金されます。 Log Analytics エージェントでは転送中のデータが圧縮されるため、帯域幅に対して課金されるサイズは、Azure Sentinel のログのサイズよりも小さい場合があります。

世界中の複数のソースから Syslog および CEF のログを収集する場合、コンプライアンスが問題でなければ、帯域幅のコストを回避するために、Azure Sentinel ワークスペースと同じリージョンに Syslog コレクターを設定することができます。

帯域幅のコストが Azure Sentinel ワークスペースを分離することの正当な理由になるかどうかの判断は、リージョン間で転送する必要があるデータの量によって異なります。 コストの見積もりには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/details/bandwidth/)をご利用ください。

詳細については、「[Azure でのデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)」を参照してください。

## <a name="access-considerations"></a>アクセスに関する考慮事項

異なるチームが同じデータにアクセスする必要がある状況が計画されている場合があります。 たとえば、SOC チームはすべての Azure Sentinel データにアクセスできる必要がある一方、運用チームとアプリケーション チームは特定の部分にのみアクセスする必要があります。 独立したセキュリティ チームも、データ セットは異なりますが、Azure Sentinel 機能へのアクセスが必要な場合があります。

[リソース コンテキスト RBAC](resource-context-rbac.md) および[テーブル レベルの RBAC](../azure-monitor/logs/manage-access.md#table-level-azure-rbac) を組み合わせて、ほとんどのユース ケースをサポートする広範囲のアクセス オプションをチームに提供します。

詳細については、「[Azure Sentinel のアクセス許可](roles.md)」を参照してください。

### <a name="resource-context-rbac"></a>リソース コンテキスト RBAC

次の図は、セキュリティ チームと運用チームがさまざまなデータ セットにアクセスする必要があるワークスペース アーキテクチャの簡略化されたバージョンを示しており、必要なアクセス許可を提供するためにリソース コンテキスト RBAC が使用されています。


[![リソース コンテキスト RBAC のサンプル アーキテクチャの図。](media/resource-context-rbac/resource-context-rbac-sample.png)](media/resource-context-rbac/resource-context-rbac-sample.png#lightbox)

この図では、アクセス許可の分離性を高めるために、Azure Sentinel ワークスペースが別のサブスクリプションに配置されています。

> [!NOTE]
> もう 1 つのオプションは、Azure Sentinel をセキュリティ専用の別の管理グループの下に配置する方法です。これにより、最小限のアクセス許可の割り当てだけが継承されます。 セキュリティ チーム内では、それぞれの機能に従って複数のグループにアクセス許可が割り当てられます。 これらのチームはワークスペース全体にアクセスできるので、完全な Azure Sentinel エクスペリエンスにアクセスでき、割り当てられている Azure Sentinel ロールによってのみ制限されます。 詳細については、「[Azure Sentinel のアクセス許可](roles.md)」を参照してください。
>

セキュリティ サブスクリプションに加えて、アプリケーション チームがワークロードをホストするために別のサブスクリプションが使用されます。 アプリケーション チームには、それぞれのリソース グループへのアクセス権が付与され、そこでリソースを管理できます。 この個別のサブスクリプションとリソース コンテキスト RBAC を使用すると、これらのチームは、直接アクセス "*できない*" ワークスペースにログが格納されている場合でも、アクセスできるすべてのリソースによって生成されたログを表示できます。 アプリケーション チームは、Azure portal の **[ログ]** 領域を使用して特定のリソースのログを表示したり、Azure Monitor を介してアクセスできるすべてのログを同時に表示したりして、ログにアクセスできます。

Azure リソースには、リソース コンテキスト RBAC のための組み込みサポートがありますが、Azure 以外のリソースを操作する場合は追加の微調整が必要な場合があります。 詳細については、「[リソースコンテキスト RBAC を明示的に構成する](resource-context-rbac.md#explicitly-configure-resource-context-rbac)」を参照してください。

### <a name="table-level-rbac"></a>テーブルレベルの RBAC

テーブル レベルの RBAC を使用すると、特定のデータ型 (テーブル) を定義して、指定したユーザーのセットにのみアクセス可能にすることができます。

たとえば、上の図で説明されているアーキテクチャを持つ組織が、内部監査チームに Office 365 ログへのアクセス権を付与する必要がある場合を考えてみます。 この場合、テーブル レベルの RBAC を使用して、他のテーブルへのアクセス許可を付与せずに、**OfficeActivity** テーブル全体へのアクセス権を監査チームに付与することができます。

### <a name="access-considerations-with-multiple-workspaces"></a>複数のワークスペースでのアクセスに関する考慮事項

組織内に異なるエンティティ、子会社、または地域があって、それぞれに Azure Sentinel へのアクセスを必要とする独自のセキュリティ チームが存在する場合は、エンティティまたは子会社ごとに個別のワークスペースを使用します。 1 つの Azure AD テナント内で、または Azure Lighthouse を使用する複数のテナントにわたって、別々のワークスペースを実装します。 

また中央の SOC チームが、オプションの追加の Azure Sentinel ワークスペースを使用して、分析ルールやブックなどの成果物を一元的に管理することもできます。

詳細については、「[複数のワークスペースの操作を簡略化する](#simplify-working-with-multiple-workspaces)」を参照してください。


## <a name="technical-best-practices-for-creating-your-workspace"></a>ワークスペースを作成するための技術的なベスト プラクティス

Azure Sentinel のために使用する Log Analytics ワークスペースを作成するときに、次のベスト プラクティス ガイダンスを使用します。

- **ワークスペースの名前を指定するとき**、*Azure Sentinel* またはその他のインジケーターを名前に含め、別のワークスペースから容易に識別できるようにします。

- **Azure Sentinel と Azure Security Center の両方に同じワークスペースを使用して**、Azure Security Center によって収集されたすべてのログを、Azure Sentinel に取り込んで使用できるようにします。 Azure Security Center によって作成された既定のワークスペースは、Azure Sentinel の使用可能なワークスペースとして表示されません。

- **予想されるデータ インジェストが 1 日あたり約 1 TB またはそれを超える場合、専用のワークスペース クラスターを使用します**。 [専用クラスター](../azure-monitor/logs/logs-dedicated-clusters.md)を使用すると、Azure Sentinel データのリソースをセキュリティ保護でき、大規模なデータ セットのクエリ パフォーマンスを向上させることができます。 また、専用クラスターには、組織のキーの暗号化と制御を強化するオプションも用意されています。

## <a name="simplify-working-with-multiple-workspaces"></a>複数のワークスペースの操作を簡略化する

複数のワークスペースを操作する必要がある場合は、[各 Azure Sentinel インスタンスのすべてのインシデントを 1 つの場所にまとめて一覧化する](multiple-workspace-view.md)ことで、インシデントの管理と調査を簡略化します。

[クロスワークスペース ブック](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)など、Azure Sentinel の他のワークスペースに保持されているデータを参照するには、[クロスワークスペース クエリ](extend-sentinel-across-workspaces-tenants.md)を使用します。

クロスワークスペース クエリを使用する最適な状況は、貴重な情報が別のワークスペース、サブスクリプション、またはテナントに格納されていて、現在のアクションに価値を提供できる場合です。 たとえば、次のコードはクロスワークスペース クエリのサンプルを示しています。

```Kusto
union Update, workspace("contosoretail-it").Update, workspace("WORKSPACE ID").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

詳細については、「[ワークスペースおよびテナント全体での Azure Sentinel の拡張](extend-sentinel-across-workspaces-tenants.md)」を参照してください。
## <a name="next-steps"></a>次のステップ


> [!div class="nextstepaction"]
>[Azure Sentinel ワークスペース アーキテクチャを設計する](design-your-workspace-architecture.md)

> [!div class="nextstepaction"]
>[Azure Sentinel のワークスペース設計サンプル](sample-workspace-designs.md)

> [!div class="nextstepaction"]
>[Azure Sentinel をオンボードする](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](get-visibility.md)