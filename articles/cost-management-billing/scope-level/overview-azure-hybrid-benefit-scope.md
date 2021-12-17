---
title: 一元管理された Azure ハイブリッド特典とは
description: Azure ハイブリッド特典は、オンプレミスのコア ベースの Windows Server と SQL Server のライセンスを、アクティブなソフトウェア アシュアランス (またはサブスクリプション) と一緒に Azure に移行できるようにするライセンス特典です。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/11/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: fdf2bbae7ec9a7c20e79298561509d0d820103ab
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708350"
---
# <a name="what-is-centrally-managed-azure-hybrid-benefit"></a>一元管理された Azure ハイブリッド特典とは

Azure ハイブリッド特典は、クラウドでワークロードを実行するコストを大幅に削減するのに役立つライセンス特典です。 これは、オンプレミスのソフトウェア アシュアランスまたはサブスクリプション対応の Windows Server および SQL Server ライセンスを Azure で使用できるようにすることで機能します。 詳細については、「<bpt id="p1">[</bpt>Azure ハイブリッド特典<ept id="p1">](https://azure.microsoft.com/pricing/hybrid-benefit/)</ept>」を参照してください。

SQL Server の Azure ハイブリッド特典は、Azure サブスクリプション全体または課金アカウント全体のスコープで一元的に管理できます。 大まかに言うと、次のようなしくみです。

1. まず、自己インストールした SQL サーバー イメージの IaaS 拡張機能への自動登録を有効にして、あなたと Azure からすべての SQL サーバー VM が見えることを確認します。 詳細については、「[Azure の複数の SQL VM を SQL IaaS Agent 拡張機能に登録する](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md)」を参照してください。
1. Azure portal の **[コストの管理と請求]** で、あなた (請求管理者) は、スコープと、スコープ内のリソースをカバーするために割り当てる対象ライセンスの数を選択します。  
    :::image type="content" source="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" alt-text="スコープの設定とライセンスの割り当てを示すスクリーンショット。" lightbox="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" :::

前の例では、対象となるすべての Azure SQL リソースに対応するために、108 個の正規化されたコア ライセンスの使用状況を検出する必要があります。 個々のリソースに対して検出された使用状況は、56 個の正規化されたコア ライセンスでした。 この例では、60 個の Standard コア ライセンスと 12 個の Enterprise コア ライセンス (12 * 4 = 48) が示されています。 したがって、60 + 48 = 108 になります。 正規化されたコア ライセンスの値については、「[Azure リソースにライセンスを適用する方法](#how-licenses-apply-to-azure-resources)」セクションで詳しく説明します。

- 毎時間、スコープ内のリソースが実行されると、Azure によって自動的にライセンスが割り当てられ、コストが正しく割り引かれます。 1 時間ごとに、異なるリソースをカバーすることができます。
- 割り当てられたライセンス数を超える使用量になると、通常の従量課金制料金で請求されます。
- スコープ レベルでライセンスを割り当てて特典を管理することを選択した場合、スコープ内の個々のリソースを管理することはできなくなります。

Azure ハイブリッド特典を有効にする元々のリソース レベルの方法は、SQL Server で引き続き使用可能であり、Windows Server では現在のところ唯一の選択肢です。 これには、個々のリソース (SQL Database や Windows Server VM など) を作成または管理するときに、そのリソースの特典を選択する DevOps ロールが必要です。 その結果、そのリソースの時間当たりのコストが割り引かれます。 詳細については、「<bpt id="p1">[</bpt>Windows Server 向け Azure ハイブリッド特典<ept id="p1">](../../azure-sql/azure-hybrid-benefit.md)</ept>」を参照してください。

サブスクリプションまたはアカウント スコープ レベルで SQL Server の Azure ハイブリッド特典の一元的な管理を有効にすることは、現在プレビュー段階です。 これは、企業のお客様や、Azure.com から Microsoft 顧客契約を結んで直接購入されたお客様にご利用いただけます。 Windows サーバーやその他のお客様に機能を拡張できるように取り組んでいます。

## <a name="qualifying-sql-server-licenses"></a>対象となる SQL Server ライセンス

ソフトウェア アシュアランスが有効な SQL Server Enterprise および Standard コア ライセンスがこの特典の対象となります。 さらに、SQL Server コア ライセンスのサブスクリプションです。

## <a name="qualifying-azure-resources"></a>対象となる Azure リソース

スコープ レベルでAzure ハイブリッド特典を一元的に管理すると、次の一般的な Azure SQL リソースが対象になります。

- SQL データベース
- SQL マネージド インスタンス
- SQL エラスティック プール
- Azure VM での SQL Server

リソースレベルの Azure ハイブリッド特典管理は、上記のすべてをカバーすることもできます。 現在、これは次のリソースをカバーする唯一のオプションです。

- Azure 専用ホスト
- Azure Data Factory SQL Server Integration Services (SSIS)

## <a name="centralized-scope-level-management-advantages"></a>スコープレベルでの一元的な管理の利点

次のような点があります。

- **より詳細に制御できるシンプルでスケーラブルなアプローチ** - 請求管理者は、使用できるライセンスを 1 つ以上の Azure スコープに直接割り当てます。 元々のアプローチでは、大規模な場合、多くのリソースと DevOps 所有者にわたって Azure ハイブリッド特典の使用状況を調整する必要があります。
- **コストを最適化するための使いやすい方法** - 管理者は、Azure ハイブリッド特典の使用状況を監視し、Azure に割り当てられたライセンスを直接調整することができます。 たとえば、管理者は、Azure に割り当てるライセンスを増やすことで、より多くのコストを削減できる可能性を確認したとします。 そこで、調達部門と話し合ってライセンスの可用性を確認します。 最後に、簡単に Azure にライセンスを割り当て、コスト削減を始めることができます。
- **使用量の急増時に推奨されるコストの管理方法** - 一時的な急増時に、簡単に同じリソースをスケールアップすることや、リソースを追加することができます。 さらに多くの SQL Server ライセンスを割り当てる必要はありません (たとえば、決算期やホリデー ショッピングの増加など)。 短期間のワークロードの急増の場合、追加容量の従量課金料金は、容量のために Azure ハイブリッド特典を使用するためのライセンスを追加購入するよりもコストがかからない場合があります。 リソースレベルではなく、スコープで特典を管理することは、使用状況の集計に基づいて決定するのに役立ちます。
- **コンプライアンスを維持するための明確な職務の分離** - リソースレベルの Azure ハイブリッド特典モデルでは、使用可能なライセンスがない場合にリソース所有者が Azure ハイブリッド特典を選択することがあります。 または、使用可能なライセンスが "ある" ときに特典を選択 "しない" こともあります。  Azure ハイブリッド特典のスコープレベルの管理により、この状況が解決されます。 特典を一元管理する請求管理者は、Azure に割り当てることができるライセンス数を調達部門とソフトウェア資産管理部門に確認する立場にあります。 この点を示したのが次の図です。

:::image type="content" source="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg" alt-text="職務の分離を示す図。" border="false" lightbox="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg":::

## <a name="how-licenses-apply-to-azure-resources"></a>Azure リソースにライセンスを適用する方法

SQL Server Enterprise (コア) と SQL Server Standard (コア) のソフトウェア アシュアランス付きライセンスは、どちらも対象となりますが、[Microsoft Product Terms](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS) のページに記載されているように、Azure ハイブリッド特典を使用して Azure に持ち込むときには、異なる変換比率が適用されます。

理解すべき規則が 1 つあります。すべての適格な Azure SQL リソースの種類において、1 つの SQL Server Enterprise Edition ライセンスは _4_ つの SQL Server Standard Edition ライセンスと同じカバレッジであるということです。

このしくみを説明するために、_正規化されたコア ライセンス_ (NCL) という用語が使用されます。 上記の規則に沿うと、1 つの SQL Server Standard コア ライセンスにより、1 つの NCL が生成されます。 1 つの SQL Server Enterprise コア ライセンスにより、4 つの NCL が生成されます。 たとえば、4 つの SQL Server Enterprise コア ライセンスと 7 つの SQL Server Standard コア ライセンスを割り当てた場合、カバー率と Azure ハイブリッド特典の割引率の合計は 23 NCL (4\*4+7\*1) に相当します。

次の表は、異なるリソースの種類に関する SQL Server ライセンスのコストを最大限割り引くために必要な NCL 数をまとめたものです。 Azure ハイブリッド特典のスコープレベルの管理では、以下にまとめた製品の契約条項の規則が厳密に適用されます。

| **Azure Data Service** | <bpt id="p1">**</bpt>サービス レベル<ept id="p1">**</ept> | **必要な NCL の数** |
| --- | --- | --- |
| SQL Managed Instance またはインスタンス プール | Business Critical | 仮想コアあたり 4 |
| SQL Managed Instance またはインスタンス プール | General Purpose | 仮想コアあたり 1 |
| SQL Database またはエラスティック プール<sup>1</sup> | Business Critical | 仮想コアあたり 4 |
| SQL Database またはエラスティック プール<sup>1</sup> | General Purpose | 仮想コアあたり 1 |
| SQL Database またはエラスティック プール<sup>1</sup> | ハイパースケール | 仮想コアあたり 1 |
| Azure Data Factory SQL Server Integration Services | エンタープライズ | 仮想コアあたり 4 |
| Azure Data Factory SQL Server Integration Services | Standard | 仮想コアあたり 1 |
| SQL Server 仮想マシン<sup>2</sup> | エンタープライズ | vCPU あたり 4 |
| SQL Server 仮想マシン<sup>2</sup> | Standard | vCPU あたり 1 |

<sup>1</sup> *Azure ハイブリッド特典は、Azure SQL Database のサーバーレス コンピューティング レベルには使用できません。*

<sup>2</sup> *仮想マシンごとに最低 4 つの仮想コア ライセンスが必要です。*

## <a name="ongoing-scope-level-management"></a>継続的なスコープレベルの管理

Azure ハイブリッド特典を一元管理する場合は、次のタスクと順序のような、プロアクティブな手順を確立することをお勧めします。

- 組織内で、来月、四半期、または 1 年間に使用される Azure SQL リソースと仮想コアの数を把握します。
- 調達部門およびソフトウェア資産管理部門と連携して、十分なソフトウェア アシュアランス付き SQL コア ライセンスを使用できるかどうかを確認します。 この特典では、移行するワークロードをサポートするライセンスを、オンプレミスと Azure の両方で最大 180 日間使用することができます。 そのため、これらのライセンスは使用可能なものとしてカウントできます。
- 使用できるライセンスを、現在の使用状況 "および" 今後の期間で想定される使用状況の増加をカバーするように割り当てます。
- 割り当てられたライセンスの使用状況を監視します。
  - 100% に近づいたら、組織内の他の人に相談し、予想される使用量を把握します。 使用可能なライセンスを確認し、そのスコープにさらにライセンスを割り当てます。
  - 使用率が 100% の場合は、割り当てられたライセンス数を超えてリソースを使用している可能性があります。 [ライセンス割り当ての作成エクスペリエンス](create-sql-license-assignments.md)に関するページに戻り、Azure に表示される使用状況を確認します。 次に、スコープに追加の使用可能なライセンスを割り当て、対象範囲を拡大します。
- この事前プロセスを定期的に繰り返します。

## <a name="next-steps"></a>次のステップ

- [SQL Server の Azure ハイブリッド特典の管理と最適化](tutorial-azure-hybrid-benefits-sql.md)に関するチュートリアルに従います。
- [一元管理された Azure ハイブリッド特典への移行](transition-existing.md)方法について確認します。
- [一元管理された Azure ハイブリッド特典の FAQ](faq-azure-hybrid-benefit-scope.yml) を確認します。