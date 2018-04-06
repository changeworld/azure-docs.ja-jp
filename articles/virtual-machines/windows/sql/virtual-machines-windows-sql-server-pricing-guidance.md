---
title: Azure Virtual Machines における SQL Server の効率的なコスト管理 | Microsoft Docs
description: 適切な SQL Server 仮想マシンの料金モデルを選択するためのベスト プラクティスについて説明します。
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/20/2018
ms.author: jroth
ms.openlocfilehash: a275df84ce784147b5fd4f09afe4995417affffd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>SQL Server Azure VM の料金ガイダンス

この記事では、Azure における [SQL Server 仮想マシン](virtual-machines-windows-sql-server-iaas-overview.md)の料金ガイダンスを説明します。 コストに影響するいくつかのオプションがあるため、コストとビジネス要件が釣り合うように適切なイメージを選択することが重要です。

> [!TIP]
> SQL Server エディションと仮想マシン サイズの特定の組み合わせに対して、コストの見積もりを見定めることだけが目的の場合は、[料金ページ](https://azure.microsoft.com/pricing/details/virtual-machines/windows)をご覧ください。 **OS / ソフトウェア**の一覧から、お使いのオペレーティング システムと SQL Server エディションを選択します。
>
> ![VM 料金ページの UI](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> または、[料金計算ツール](https://azure.microsoft.com/pricing/#explore-cost)を使用して、仮想マシンを追加して構成します。 

## <a name="free-licensed-sql-server-editions"></a>無料ライセンスの SQL Server エディション

概念実証の開発、テスト、または構築を行う場合は、無料ライセンスの **SQL Server Developer Edition** を使用してください。 このエディションには、SQL Server Enterprise Edition のすべての機能が含まれるため、これを使用してどのようなアプリケーションでも構築できます。 ただし、運用環境では実行することはできません。 SQL Server Developer VM では、SQL Server のライセンスではなく、VM のコストに対してのみ料金が発生します。

運用環境で軽量ワークロード (コア 4 未満、メモリ 1 GB 未満、データベース < 10 GB 未満) を実行しようとしている場合は、無料ライセンスの **SQL Server Express Edition** を使用してください。 SQL Express VM では、SQL のライセンスではなく、VM のコストに対してのみ料金が発生します。

このような開発/テストまたは軽量運用ワークロードの場合、そのワークロードに応じて小さい VM サイズを選択することでも費用を節約できます。 このようなワークロードでは DS1v2 をお勧めします。

このようなイメージの 1 つを使用して SQL Server 2017 Azure VM を作成するには、次のリンクを参照してください。

| プラットフォーム | 自由にライセンスされたイメージ |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>有料の SQL Server エディション

運用ワークロードが軽量でない場合は、次のいずれかの SQL Server エディションを使用します。

| SQL Server のエディション | ワークロード |
|-----|-----|
| Web | 小規模 Web サイト |
| 標準 | 少量から中量のワークロード |
| Enterprise | 大量のワークロードまたはミッションクリティカルなワークロード|

これらのエディションの SQL Server ライセンス料金には 2 つのオプションがあります。"*使用した分を支払う*" 方法と、"*自分のライセンスを持ち込む (BYOL)*" 方法です。

### <a name="pay-per-usage"></a>使用した分を支払う

**使用した分の SQL Server ライセンス料金を支払う**方法では、Azure VM を実行する秒単位のコストに SQL Server ライセンスのコストが含まれます。 さまざまな SQL Server エディション (Web、Standard、Enterprise) の料金は、[Azure VM の料金に関するページ](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)で確認できます。 コストはすべてのバージョンの SQL Server (2012 SP3 から 2017 まで) で同じです。 一般的な SQL Server ライセンスと同様に、秒あたりのライセンス コストは VM コアの数によって変わります。

使用した分だけ SQL Server ライセンス料金を支払う方法は、次の場合にお勧めします。

- 一時的なワークロードまたは定期的なワークロード。 たとえば、毎年 2 か月間行われるイベントや毎週月曜日に行われるビジネス分析をサポートする必要があるアプリです。
- 有効期間または規模が不明なワークロード。 たとえば、数か月で不要になるアプリ、または要求によって必要な処理能力が増減するアプリです。

このような使用した分を支払うイメージの 1 つを使用して SQL Server 2017 Azure VM を作成するには、次のリンクを参照してください。

| プラットフォーム | ライセンスされたイメージ |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> ポータルで SQL Server 仮想マシンを作成するとき、**[サイズの選択]** ウィンドウには見積もりコストが表示されます。 この見積もりに含まれるのは、VM を実行するためのコンピューティング コストと、Windows VM のすべての Windows ライセンス コストのみであることに注意してください。 Web、Standard、および Enterprise エディションに対する追加の SQL Server ライセンス コストは含まれません。 また、Linux VM のサード パーティ製の Linux オペレーティング システムに対する追加のライセンス コストも含まれません。 正確な価格の見積もりを確認するには、[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) と [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) の価格ページで、ご自身のオペレーティング システムと SQL Server エディションを選択してください。
>
> ![VM サイズを選択するブレード](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)

### <a name="bring-your-own-license-byol"></a>ライセンスを持ち込む (BYOL)

**ライセンス モビリティを使用した自分の SQL Server ライセンスの持ち込み** (**BYOL**) は、Azure VM のソフトウェア アシュアランスで既存の SQL Server ボリューム ライセンスを使用することです。 BYOL を使用した SQL Server VM の料金は、SQL Server のライセンスではなく、VM を実行するコストに対してのみ発生します。これは、ボリューム ライセンス プログラムを介して既にライセンスとソフトウェア アシュアランスを取得している場合が対象になります。

> [!NOTE]
> BYOL イメージは、現在、Windows 仮想マシンでのみご利用いただけます。 ただし、SQL Server は Linux 専用の VM に手動でインストールできます。 [Linux SQL VM のよくあるご質問](../../linux/sql/sql-server-linux-faq.md)に関するページをご覧ください。

ライセンス モビリティによる自分の SQL ライセンスの持ち込みは、次の場合にお勧めします。

- 継続的なワークロード。 たとえば、年中無休の業務をサポートする必要があるアプリ。
- 有効期間と規模がわかっているワークロード。 たとえば、1 年にわたって必要で、要求が予測されているアプリ。

BYOL を SQL Server VM で使用するためには、SQL Server Standard または Enterprise と[ソフトウェア アシュアランス](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1) ([ボリューム ライセンス](https://www.microsoft.com/en-us/download/details.aspx?id=10585) プログラムの必須オプション、他の製品では必要に応じて購入可能) が必要です。  ボリューム ライセンス プログラムで提供される料金レベルは、契約のタイプ、SQL Server の数やコミットメントによってさまざまです。 ただし、経験則として、継続的な運用環境のワークロードに自分のライセンスを持ち込むメリットは次のとおりです。

| BYOL のメリット | [説明] |
|-----|-----|
| **コスト削減** | 自分の SQL Server ライセンスの持ち込みが、使用した分だけ支払うよりもコスト効果が高くなるのは、ワークロードによって SQL Server Standard または Enterprise が "*10 か月を超えて*" 実行され続ける場合です。 |
| **長期的なコスト削減** | 平均すると、当初 3 年間は SQL Server ライセンスを購入または更新する方が "*1 年あたり 30% のコストダ ウン*" です。 3 年経過した後は、ライセンスを更新する必要がなくなり、ソフトウェア アシュアランスのみの支払いになります。 その時点で、"*200% のコスト ダウン*" になります。 |
| **無料のパッシブ セカンダリ レプリカ** | 自分のライセンスを持ち込むもう 1 つのメリットは、高可用性を実現するために SQL Server 1 つにつき [1 つのパッシブ セカンダリ レプリカの無料ライセンス](https://azure.microsoft.com/pricing/licensing-faq/)を得られることです。 これによって、高可用性 SQL Server デプロイ (Always On 可用性グループの使用など) のライセンス コストを半減できます。 パッシブ セカンダリを実行する権限は、フェールオーバー サーバーのソフトウェア アシュアランス特典で提供されます。 |

自分のライセンスを持ち込むイメージの 1 つを使用して SQL Server 2016 Azure VM を作成するには、プレフィックスに "{BYOL}" が付いた VM を参照してください。

- [SQL Server 2016 Enterprise Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [SQL Server 2016 Standard Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!IMPORTANT]
> Azure で使用する SQL Server ライセンス数を 10 日以内にお知らせください。 前のイメージのリンクに、この方法が記載されています。

> [!NOTE]
> SQL Server VM のライセンス モデルを秒単位のライセンスから既存のライセンスに変更することはできません。 既存のライセンスを利用する場合は、BYOL VM を新規作成して、データベースを新しい VM に移行する必要があります。 

## <a name="avoid-unnecessary-costs"></a>不要なコストの回避

不要なコストがかからないようにするには、最適な仮想マシンのサイズを選択し、ワークロードが継続して実行されないよう断続的にシャットダウンすることを検討してください。

### <a id="machinesize"></a> VM の適切なサイズ設定

SQL Server のライセンス コストはコアの数に直接関連します。 CPU、メモリ、記憶域、および I/O 帯域幅の予想されるニーズに合った VM サイズを選択してください。 マシンのサイズ オプションの一覧については、[Windows VM のサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)と [Linux VM のサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをそれぞれご覧ください。

特定の種類の SQL Server ワークロードで適切に動作する新しいマシン サイズがあります。 こうしたマシン サイズでは、メモリ、記憶域、および I/O 帯域幅が高いレベルで維持されますが、仮想化されたコア数は少なくなります。 たとえば、次の例を考えてみましょう。

| VM サイズ | vCPU 数 | メモリ | ディスクの最大数 | 最大 I/O スループット | SQL のライセンス コスト | 合計コスト (コンピューティング + ライセンス) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS または 768 MB/秒 | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51,200 IOPS または 768 MB/秒 | 75% 削減 | 57% 削減 |

> [!IMPORTANT]
> これは、特定の時点の例です。 最新の仕様については、マシンのサイズに関する記事と、[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) および [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) それぞれの Azure 価格ページをご覧ください。

前の例で、**Standard_DS14v2** と **Standard_DS14-4v2** の仕様は、vCPU を除いて同じであることがわかります。 **Standard_DS14-4v2** マシン サイズの末尾のサフィックス **-4v2** は、アクティブな vCPU の番号を示します。 SQL Server のライセンス コストはコア数と関連するため、追加 vCPU が不要なシナリオでは、これにより VM のコストが大幅に削減されます。 これは 1 つの例であり、このサフィックス パターンで特定される、vCPU が制約されたマシン サイズは多数あります。 詳細については、[データベース作業のコスト効率を高めるための新しい Azure VM サイズについての発表](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)に関するページをご覧ください。

### <a name="shut-down-your-vm-when-possible"></a>可能な場合は VM をシャットダウン

使用しているワークロードが継続して実行されない場合は、活動しない期間に仮想マシンを停止することを検討してください。 料金は使用した分だけになります。

たとえば、Azure VM で SQL Server を試しているだけの場合、誤って実行したままにして数週間分の料金が発生するのは望ましくありません。 1 つの解決方法としては、[自動シャットダウン機能](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)を使用します。

![SQL VM 自動シャットダウン](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

自動シャットダウンは [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab) で提供されます。他にも多数の類似した機能があります。

他のワークロードでは、[Azure Automation](https://azure.microsoft.com/services/automation/) など、スクリプト ソリューションによる Azure VM の自動シャットダウンと再起動を検討してください。

> [!IMPORTANT]
> 課金を避けるためには、VM をシャットダウンして割り当て解除するしかありません。 停止するだけ、または電源オプションを使用した VM のシャットダウンでは、使用料金は発生します。

## <a name="next-steps"></a>次の手順

Azure の一般的な料金ガイダンスについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](../../../billing/billing-getting-started.md)」をご覧ください。

SQL Server を含め、仮想マシンの最新の料金については、[Azure VM の料金に関するページ](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)をご覧ください。

[SQL Server Windows VM](virtual-machines-windows-sql-server-iaas-overview.md) と [SQL Server Linux VM](../../linux/sql/sql-server-linux-virtual-machines-overview.md) の両方について、SQL Server 仮想マシンの詳細を確認します。
