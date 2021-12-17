---
title: 一元管理された Azure ハイブリッド特典への移行
description: この記事では、一元管理された Azure ハイブリッド特典へ移行した際に変わることと、いくつかの移行シナリオについて説明します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.openlocfilehash: be701e090a7fd993f476cd45194b73368c589dc5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223994"
---
# <a name="transition-to-centrally-managed-azure-hybrid-benefit"></a>一元管理された Azure ハイブリッド特典への移行

一元管理された Azure ハイブリッド特典に移行すると、リソース レベルで特典を構成する必要がなくなります。 この記事では、結果を示すため、変更点と、いくつかの移行シナリオについて説明しています。 新しいスコープレベルのライセンス管理エクスペリエンスにおいて、リソースにどのようにライセンスや割引が適用されるかの詳細については、「[一元管理された Azure ハイブリッド特典とは](overview-azure-hybrid-benefit-scope.md)」を参照してください。

## <a name="changes-to-individual-resource-configuration"></a>個々のリソース構成の変更

新しいエクスペリエンスを使用してサブスクリプションにライセンスを割り当てると、変更は Azure portal に表示されます。 その後、リソース レベルで特典を管理することはできません。 スコープ レベルで行った変更は、個々のリソース レベルでの設定よりも優先されます。

:::image type="content" source="./media/transition-existing/sql-db-configure.png" alt-text="SQL ライセンスの上書き情報を示すスクリーンショット。" lightbox="./media/transition-existing/sql-db-configure.png" :::

> [!NOTE]
> PowerShell、API を使用して、または Azure portal の外部からリソースに対して Azure ハイブリッド特典の設定を変更した場合、設定の変更は保存されます。 ただし、そのリソースのサブスクリプションまたは課金アカウントが、スコープに割り当てられたライセンスによってカバーされている限り、その設定は適用されません。 すべてのライセンス割り当てを削除して、Azure ハイブリッド特典のスコープレベルの管理から脱退した場合、ライセンスの割引は、各リソースの Azure ハイブリッド特典の設定によって決定されるように戻ります。

## <a name="check-how-many-sql-licenses-you-use-before-transition"></a>使用している SQL ライセンス数を移行前に確認する

Azure ハイブリッド特典エクスペリエンスのスコープレベルの管理に登録すると、個々のリソースに対して有効になっている現在の Azure ハイブリッド特典の使用状況が表示されます。 全体的なエクスペリエンスの詳細については、「[Azure ハイブリッド特典用に SQL Server ライセンス割り当てを作成する](create-sql-license-assignments.md)」を参照してください。 サブスクリプションの共同作成者であり、必要な課金管理者ロールをお持ちでない場合は、PowerShell スクリプトを使用して、Azure のさまざまな種類の SQL Server ライセンスの使用状況を分析できます。 このスクリプトを実行すると、複数のサブスクリプションまたはアカウント全体の使用状況のスナップショットが生成されます。 スクリプトの使用に関する詳細と例については、[sql-license-usage PowerShell スクリプト](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit)のサンプル スクリプトを参照してください。 スクリプトを実行したら、課金管理者を特定して連絡を取り、サブスクリプションまたは課金アカウントのスコープ レベルに Azure ハイブリッド特典管理を移行する機会がないか相談します。

> [!NOTE]
> このスクリプトには、正規化されたコア ライセンス (NCL) のサポートが含まれています。 

## <a name="hadr-benefit-for-sql-server-vms"></a>SQL Server VM の HADR の特典

新しい Azure portal エクスペリエンスでは、SQL Server VM の高可用性とディザスター リカバリー (HADR) の特典が完全にサポートされています。 SQL Server VM が HADR レプリカとして構成されている場合、他の操作は必要ありません。 SQL Server VM の HADR 特典のしくみについては、「[SQL Server HADR と中央管理型の Azure ハイブリッド特典の共存](sql-server-hadr-licenses.md)」を参照してください。

## <a name="transition-scenario-examples"></a>移行シナリオの例

以下の移行シナリオの内、ご自身の状況に最も近い例を確認してください。

### <a name="migrate-sql-workloads-from-on-premises-to-the-cloud-using-sql-database-with-azure-hybrid-benefit"></a>Azure ハイブリッド特典で SQL Database を使用して、オンプレミスからクラウドに SQL ワークロードを移行する

- **オンプレミスの SQL Server ワークロード -** ミッション クリティカルな SQL Server Enterprise エディションのデータベースをホストする 2 つの 16 コアのマシンをクラウドに移行します。
- **ワークロードを移行する Azure 上の移動先 -** 分析を行った結果、ワークロードを 2 つの 16 コアの Azure SQL Managed Instance で実行することが決まりました。
- **Azure に割り当てるライセンス -** 前述の点を考慮して、32 個の SQL Server Enterprise エディションのコア ライセンスをソフトウェア アシュアランス付きで Azure に割り当てます。
- **推奨されるアクション -** 新しい Azure portal エクスペリエンスを使用して、32 個の SQL Server Enterprise エディションのコア ライセンスを適切なサブスクリプションまたは課金アカウント全体に割り当てます。
- **結果 -** 管理しやすく、コスト最適化された、オンプレミスからクラウドへの SQL Server のワークロードの移行。

> [!NOTE] 
> Azure ハイブリッド特典を使用すると、ワークロードの移行、テスト、およびデプロイを行っている間、Azure に割り当てられたライセンスを最大 180 日間、オンプレミスでも継続して使用することができます。

### <a name="simplify-license-management-by-transitioning-to-centralized-scope-level-management-of-azure-hybrid-benefit"></a>Azure ハイブリッド特典のスコープレベルでの一元的な管理に移行してライセンス管理を簡略化する

- **実行中の SQL Server リソース -** 64 コアの Azure SQL Database Business Critical が 1 つ実行されており、Azure ハイブリッド特典が選択されています。
- **Azure に割り当てることができるライセンス -** 調達チームは、オンプレミスでは使用されていないソフトウェア アシュアランス付きの SQL Server Enterprise エディションのコア ライセンスが 64 個以上あることを確認しています。
- **推奨されるアクション -** Azure ハイブリッド特典エクスペリエンスの新しいスコープ レベル管理を使用して、64 個の SQL Server Enterprise エディションのコア ライセンスを Azure に割り当てます。 または、使用量が近々増えることが予想される場合は、必要に応じてさらにライセンスを割り当てます。
- **結果 -** 移行により SQL Server ソフトウェアのコストがカバーされるため、関連する請求に変更はありません。

### <a name="save-more-by-assigning-more-sql-server-licenses-to-cover-more-azure-sql-resources"></a>より多くの Azure SQL リソースをカバーするために、より多くの SQL Server ライセンスを割り当て、より多く節約する

- **実行中の SQL Server リソース -** 16 コアの Azure SQL Database Business Critical が 2 つ実行されており、Azure ハイブリッド特典がその内 1 つで選択されています。
- **Azure に割り当てることができるライセンス -** 調達チームによると、オンプレミスまたは Azure では使用されていないソフトウェア アシュアランス付きの SQL Server Enterprise エディションのコア ライセンスが 48 個あります。
- **推奨されるアクション -** Azure ハイブリッド特典エクスペリエンスのスコープ レベル管理を使用して、48 個すべての SQL Server Enterprise エディションのコア ライセンスを割り当てます。 これは、リソースレベルの Azure ハイブリッド特典を選択した場合と比較して、16 個の増加となります。
- **結果-** Azure 外で購入したライセンスがより多く使用されているため、Azure での請求額は減少するはずです。 また、ソフトウェア アシュアランスの 1 年あたりのコストは、SQL Server を従量課金で使用した場合の 1 年あたりのコストよりも低いため、SQL Server の利用にかかる総コストが削減されます。

### <a name="restore-compliance-when-excessive-azure-hybrid-benefit-usage-is-found"></a>Azure ハイブリッド特典の過剰使用が判明した場合のコンプライアンスの回復

- **実行中の SQL Server リソース -** 8 コアの SQL データベース General Purpose が 3 つ、および 16 コアの SQL Server VM Enterprise が 1 つ実行されており、そのすべてで Azure ハイブリッド特典が選択されています。 これをカバーするために必要な Azure ハイブリッド特典ライセンスの数は、24 個の Standard エディション コア + 16 個の SQL Server Enterprise コアです。 または、88 個の SQL Server Standard エディション (+ 0 個の SQL Server Enterprise エディション) または 22 個の SQL Server Enterprise (+ 0 個の SQL Server Standard エディション)でもカバーすることが可能です。 これは、1 つの SQL Server Enterprise エディションのコア ライセンスと、4 つの SQL Server Standard エディションのコア ライセンスは、すべての Azure SQL リソースの種類に対して、同じ Azure ハイブリッド特典の使用量をカバーできるからです。 この柔軟性の詳細については、「[一元管理された Azure ハイブリッド特典とは](overview-azure-hybrid-benefit-scope.md)」の記事に記載されている Azure ハイブリッド特典の規則を確認してください。
- **Azure に割り当てることができるライセンス -** 調達チームによると、オンプレミスまたは Azure では使用されていないソフトウェア アシュアランス付きの SQL Server Standard エディションのコア ライセンスが 64 個あります。 これは、必要な量である 22 個の SQL Server Enterprise または 88 個の SQL Server Standard エディションのコア ライセンスよりも少ないです。
- **推奨されるアクション -** コンプライアンスを回復するために、ソフトウェア アシュアランス付きの 6 つの SQL Server Enterprise または 24 個の SQL Server Standard エディションのコア ライセンスを特定し、それらと既に確認済みの 64 個の SQL Server Standard コア ライセンスを、Azure ハイブリッド特典エクスペリエンスのスコープレベルの管理を使用して Azure に割り当てます。
- **結果 -** コンプライアンス違反が解消され、コストを最小限に抑えるよう Azure ハイブリッド特典が最適に使用されます。
- **代替アクション -** 利用可能な 64 個の SQL Server Standard エディションのコア ライセンスのみを Azure に割り当てます。 準拠することにはなりますが、これらのライセンスでは Azure SQL の使用量をすべてカバーするには不十分なため、一部は従量課金されることになります。
## <a name="next-steps"></a>次のステップ

- 「[SQL Server に対して一元的に管理された Azure ハイブリッド特典を最適化する](tutorial-azure-hybrid-benefits-sql.md)」チュートリアルを確認します。
- [SQL Server ライセンスの割り当てを作成](create-sql-license-assignments.md)して、スコープレベルのライセンス管理に移動します。
- 「[一元管理された Azure ハイブリッド特典の FAQ](faq-azure-hybrid-benefit-scope.yml)」を確認します。