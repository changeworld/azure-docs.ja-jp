---
title: チュートリアル - SQL Server に対して一元的に管理された Azure ハイブリッド特典を最適化する
description: このチュートリアルでは、Azure で SQL Server のライセンスを事前に割り当て、Azure ハイブリッド特典を管理および最適化する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 11/11/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: a8db95d33ae6398898108dfbf62a57b5b5f48d9d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708312"
---
# <a name="tutorial-optimize-centrally-managed-azure-hybrid-benefit-for-sql-server"></a>チュートリアル: SQL Server に対して一元的に管理された Azure ハイブリッド特典を最適化する

このチュートリアルでは、Azure で SQL Server のライセンスを積極的に割り当て、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を一元的に管理しながら最適化する方法について説明します。 特典を最適化することで、Azure SQL の運用コストを削減できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ライセンスの使用状況と可用性の詳細を収集する
> * 必要に応じてライセンスを追加購入する
> * ライセンスを Azure に割り当てる
> * 使用状況を監視して調整する
> * 管理スケジュールを確立する

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。

「[一元管理された Azure ハイブリッド特典とは](overview-azure-hybrid-benefit-scope.md)」の記事を読み、理解していること。 この記事では、Azure ハイブリッド特典の対象となる SQL Server ライセンスの種類について説明します。 また、選択したサブスクリプションまたは課金アカウントのスコープで特典を有効にする方法についても説明します。

> [!NOTE]
> スコープ レベルで Azure ハイブリッド特典を一元的に管理する方法は、現在パブリック プレビュー中であり、企業のお客様と、Microsoft 顧客契約を結んで Azure.com から直接購入されるお客様に限定されています。

この新しいエクスペリエンスの使用を開始する前に、Azure で SQL Server を実行する自己インストールした仮想マシンが登録されていることを確認してください。 そうすると、SQL Server を実行している Azure リソースがお客様と Azure から見えるようになります。 Azure に SQL VM を登録する方法については、「[SQL Server VM を SQL IaaS Agent 拡張機能に登録する](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)」と「[Azure の複数の SQL VM を SQL IaaS Agent 拡張機能に登録する](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md)」を参照してください。

## <a name="gather-license-usage-and-availability-details"></a>ライセンスの使用状況と可用性の詳細を収集する

"_最初の手順は準備です。_ " 組織内の他部署と連携して、次の 2 点を理解します。

- 今後の計画期間中に Azure でどの程度の SQL Server の使用量が予想されますか?
- ソフトウェア アシュアランスのある (またはサブスクリプション内の) SQL Server コア ライセンスをいくつ購入し、Azure に割り当て可能ですか?

システムにより検出された最近の Azure SQL 使用状況の詳細は、[Azure ハイブリッド特典用の SQL Server ライセンスの割り当てを作成する](create-sql-license-assignments.md)ときに表示されます。

組織内の適切な担当者に相談して、その情報を検証し、計画されている SQL Server の使用量の増加について確認することをお勧めします。

オプションではありますが、Azure SQL の使用状況 (リソース レベルでの Azure ハイブリッド特典の使用状況を含む) を調査する便利な方法として、Azure ハイブリッド特典の [sql-license-usage PowerShell スクリプト](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit)を使用することができます。 これにより、特定のサブスクリプションまたはアカウント全体のすべての SQL リソースの合計の SQL Server ライセンス使用状況が分析され、追跡されます。

### <a name="determine-the-number-of-eligible-sql-server-core-licenses-available-to-assign-to-azure"></a>Azure に割り当てることができる SQL Server コア ライセンスの数を決定する

数量は、購入したライセンスの数 (ソフトウェア アシュアランスまたはサブスクリプションを使用) と、Azure の外部 (通常はオンプレミス) で既に使用されているライセンスの数によって決まります。

ソフトウェア調達部門やソフトウェア資産管理部門がこのような情報を持っていると考えられます。

> [!TIP]
> オンプレミスから Azure にワークロードを移行すると、関連付けられているライセンスを Azure に割り当てることができます。 これは、Azure ハイブリッド特典を使用しているためで、移行中は SQL Server ライセンスに対して 180 日間の二重使用権 (Azure とオンプレミス) が付与されるためです。 これにより、シームレスな実行を実現できます。

## <a name="buy-more-licenses-if-needed"></a>必要に応じてライセンスを追加購入する

収集した情報を確認した上で、使用できる SQL Server ライセンスの数が予定されている Azure SQL の使用状況をカバーするには不十分であることが判明した場合は、ソフトウェア アシュアランス (またはサブスクリプション ライセンス) 付きの SQL Server コア ライセンスを追加購入するように調達部門に相談します。

SQL Server のライセンスを購入して Azure ハイブリッド特典を適用する方が、Azure で SQL Server に時間単位で支払うよりも低コストです。 予定されているすべての Azure SQL の使用をカバーするのに十分な数のライセンスを購入することで、組織は特典によるコスト削減を最大化することができます。

## <a name="assign-licenses-to-azure"></a>ライセンスを Azure に割り当てる

1. Azure portal とドキュメントの指示に従って、少なくとも 1 つのスコープを選択し、それらに SQL Server ライセンスを割り当てます。 詳細については、「[Azure ハイブリッド特典用に SQL Server ライセンス割り当てを作成する](create-sql-license-assignments.md)」を参照してください。
2. ライセンスを割り当てるときに、検出された Azure SQL の使用状況を再度確認し、詳細が収集した他の情報と一致していることを確認します。

## <a name="monitor-usage-and-adjust"></a>使用状況を監視して調整する

1. **[コストの管理と請求]**  >  **[Reservations + Hybrid Benefits]\(予約とハイブリッドの特典\)** に移動します。
1. 割り当てた Azure ハイブリッド特典のライセンスと、それぞれの使用率を含む表が表示されます。
1. 使用率のいずれかが 100% である場合、組織は一部の SQL Server リソースに対して時間単位の料金を支払っていることを意味します。 組織内の他のグループと再度話し合って、現在の使用状況レベルが一時的であるか、それとも今後も続くかを確認します。 後者の場合、組織のコストを削減するには、ライセンスを追加購入し、それらを Azure に割り当てる必要があります。
1. 使用率が 100% に近づいても超えない場合は、近い将来に使用率が増加する見込みがあるかどうかを判断します。 そうであれば、事前にライセンスを追加購入して割り当てることができます。

## <a name="establish-a-management-schedule"></a>管理スケジュールを確立する

前のセクションでは、継続的な監視について説明しました。 繰り返し従う年単位または四半期単位のスケジュールを設定することもお勧めします。 このスケジュールには、この記事で説明されている主な手順が含まれます。

- ライセンスの使用状況と可用性の詳細を収集する。
- 今後の使用状況の増加に備えて、必要に応じてライセンスを追加購入する。
- ライセンスを Azure に割り当てる。
- 使用状況を監視し、必要に応じてその場で調整する。
- 毎年、またはニーズに合わせて最適な頻度でこのプロセスを繰り返す。

## <a name="example-walkthrough"></a>例のチュートリアル

次の例では、あなたが Contoso Insurance 社の請求管理者であると仮定してください。 あなたは Contoso の SQL Server の Azure ハイブリッド特典を管理しています。

あなたは、アカウント全体のレベルで SQL Server の Azure ハイブリッド特典を一元的に管理できることを調達部門から知らされました。 調達部門は、Microsoft アカウント チームからこの情報を知りました。 あなたは、Azure ハイブリッド特典を管理するのが最近大変だったので、興味を持ちました。 その理由の 1 つは、開発者が互いにスクリプトを共有するときに、リソース上で任意に特典を有効 (または無効) にしていたからです。

新しい Azure ハイブリッド特典エクスペリエンスは、Azure portal の [コストの管理と請求] 領域にあります。

あなたはこの記事の前述の手順を読み、次のことを理解しました。

  - Contoso は、他のアクションを実行する前に、SQL Server VM を登録する必要があります。
  - この新しい機能を使用する理想的な方法は、予想される使用状況をカバーするために事前にライセンスを割り当てることです。

その後、次の手順を実行します。

1. 前述の手順で、自己インストールした SQL VM が登録されていることを確認します。 これには、あなたが十分なアクセス許可を持っていないサブスクリプションについて登録を完了するために、サブスクリプション所有者に相談することも含まれます。
1. あなたは、ここ数か月間の Azure リソースの使用状況データを確認し、Contoso の他の社員と話しました。 今後 1 年間に予想される Azure SQL の使用状況をカバーするには、2,000 個の SQL Server Enterprise Edition と 750 個の SQL Server Standard Edition のコア ライセンス (つまり 8,750 個の正規化されたコア ライセンス) が必要であると判断しました。 予想される使用状況には、移行するワークロード (1,500 個の SQL Server Enterprise Edition + 750 個の SQL Server Standard Edition = 正規化された 6,750 個) と、正味の新しい Azure SQL ワークロード (さらに 500 個の SQL Server Enterprise Edition、つまり 2,000 個の正規化されたコア ライセンス) も含まれます。
1. 次に、必要なライセンスが既に使用できるか、間もなく購入されるかを調達チームに確認します。 この確認により、確実にライセンスを Azure に割り当てられるようにします。
   - オンプレミスで使用しているライセンスは、関連付けられているワークロードが Azure に移行されている場合、Azure に割り当てることができると考えられます。 前述のとおり、Azure ハイブリッド特典では、最大 180 日間の二重使用が可能です。
   - あなたは、Azure に割り当てることができる SQL Server Enterprise Edition ライセンスは 1,800 個、SQL Server Standard Edition ライセンスは 2,000 個あると判断します。 使用できるライセンスは、9,200 個の正規化されたコア ライセンスに相当します。 これは、必要な 8,750 個 (2,000 x 4 + 750 = 8,750) を少し上回る数です。
1. 次に、1,800 個の SQL Server Enterprise Edition と 2,000 個の SQL Server Standard Edition を Azure に割り当てます。 そのアクションの結果、毎時実行される Azure SQL リソースにシステムで適用できる正規化されたコア ライセンスは 9,200 個になります。 必要以上のライセンスを割り当てることで、使用状況が予想よりも早く増加した場合のバッファーを確保できます。

その後、割り当てられたライセンスの使用状況を定期的に (理想的には毎月) 監視します。 10 か月後、使用率は 95% に近づき、Azure SQL の使用状況が予想以上に増加していることがわかりました。 あなたは調達チームに相談し、ライセンスを追加して割り当てられるようにします。

最後に、年に一度のライセンス レビュー スケジュールを採用します。 レビュー プロセスでは、次のことを行います。

- ライセンスの使用状況のデータを収集して分析する。
- ライセンスの可用性を確認する。
- 必要に応じて、調達チームと連携してライセンスを増やす。
- ライセンスの割り当てを更新する。
- 時系列で監視する。

## <a name="next-steps"></a>次のステップ

- [一元管理された Azure ハイブリッド特典への移行](transition-existing.md)方法について確認します。
- [一元管理された Azure ハイブリッド特典の FAQ](faq-azure-hybrid-benefit-scope.yml) を確認します。