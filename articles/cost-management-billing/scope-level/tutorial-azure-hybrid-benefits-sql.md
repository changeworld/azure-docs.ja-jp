---
title: チュートリアル - スコープ レベルの Azure ハイブリッド特典を SQL Server に合わせて最適化する
description: このチュートリアルでは、Azure で SQL Server のライセンスを事前に割り当て、Azure ハイブリッド特典を管理および最適化する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: fa928181b20a5975f4c6e42079695aede223ddac
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547482"
---
# <a name="tutorial-optimize-scope-level-azure-hybrid-benefit-for-sql-server"></a>チュートリアル: スコープ レベルの Azure ハイブリッド特典を SQL Server に合わせて最適化する

このチュートリアルでは、Azure で SQL Server のライセンスを事前に割り当て、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を管理および最適化する方法について説明します。 特典を最適化することで、Azure SQL の運用コストを削減できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ライセンスの使用状況と可用性の詳細を収集する
> * 必要に応じてライセンスを追加購入する
> * ライセンスを Azure に割り当てる
> * 使用状況を監視して調整する
> * 管理スケジュールを確立する

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。

「[Azure ハイブリッド特典のスコープレベルの管理とは](overview-azure-hybrid-benefit-scope.md)」の記事を読み、理解していること。 この記事では、Azure ハイブリッド特典の対象となる SQL Server ライセンスの種類について説明します。また、サブスクリプションまたは全体の課金アカウント レベルで選択したスコープで Azure ハイブリッド特典を有効にして使用する方法についても説明します。

> [!NOTE]
> スコープを指定して Azure ハイブリッド特典を使用することは、現在パブリック プレビュー段階であり、エンタープライズのお客様に限定されています。

この新しいエクスペリエンスの使用を開始する前に、Azure で SQL Server を実行する自己インストールした仮想マシンが登録されていることを確認してください。 そうすると、SQL Server を実行している Azure リソースがお客様と Azure から見えるようになります。 Azure に SQL VM を登録する方法については、「[SQL Server VM を SQL IaaS Agent 拡張機能に登録する](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)」と「[Azure の複数の SQL VM を SQL IaaS Agent 拡張機能に登録する](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md)」を参照してください。

## <a name="gather-license-usage-and-availability-details"></a>ライセンスの使用状況と可用性の詳細を収集する

"_最初の手順は準備です。_ " 組織内の他部署と連携して、次の 2 点を理解します。

- 現在と今後 1 年間で予定されている Azure SQL と SQL Server の使用状況はどのくらいですか。
- Azure に割り当てることができる SQL Server コア ライセンスの数はいくつありますか。

次に、現在と、今後 1 年間または他の延長期間 (少なくとも 1 か月間) で "_予定されている_" Azure SQL の使用状況を判断します。

Azure SQL 使用状況の詳細は、[Azure ハイブリッド特典用の SQL Server ライセンスの割り当てを作成する](create-sql-license-assignments.md)と表示されます。

組織の適切な担当者に相談して上記の情報を検証することをお勧めします。 たとえば、"_予定されている_" 使用状況と予想される SQL Server の使用状況の増加などです。 このような計画を担当する個人やグループが集中している場合もあれば、複数のチームに分散している場合もあります。

オプションではありますが、Azure SQL の使用状況 (リソース レベルでの Azure ハイブリッド特典の使用状況を含む) を調査する便利な方法として、Azure ハイブリッド特典の [sql-license-usage PowerShell スクリプト](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit)を使用することができます。 これにより、特定のサブスクリプションまたはアカウント全体のすべての SQL リソースの合計の SQL Server ライセンス使用状況が分析され、追跡されます。

### <a name="determine-the-number-of-sql-server-core-licenses-available-to-assign-to-azure"></a>Azure に割り当てることができる SQL Server コア ライセンスの数を決定する

数量は、購入したライセンス数と、オンプレミス サーバーと Azure VM の間で既に使用されているライセンス数によって変わります。

確実かつシームレスに移行を行うために、180 日間は SQL Server ライセンスの二重使用権があります。 180 日の期間を過ぎると、SQL Server ライセンスは Azure でのみ使用できるようになります。 ライセンスの可用性を計画するときには、その点を考慮してください。 たとえば、移行するライセンスは、割り当て可能なライセンスとして考えることができます。

ソフトウェア調達部門やソフトウェア資産管理部門がこのような情報を持っていると考えられます。

## <a name="buy-more-licenses-if-needed"></a>必要に応じてライセンスを追加購入する

収集した情報を確認した上で、使用できる SQL Server ライセンスの数が予定されている Azure SQL の使用状況をカバーするには不十分であることが確実な場合は、ソフトウェア アシュアランス (またはサブスクリプション ライセンス) 付きの SQL Server コア ライセンスを追加購入するように調達部門に相談します。

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

あなたは、アカウント全体のレベルで SQL Server の Azure ハイブリッド特典を管理できることを調達部門から知らされました。 調達部門は、Microsoft アカウント チームからこの情報を知りました。 あなたは、Azure ハイブリッド特典を管理するのが最近大変だったので、興味を持ちました。 その理由の 1 つは、開発者が互いにスクリプトを共有するときに、リソース上で任意に特典を有効 (または無効) にしていたからです。

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
1. 次に、1,800 個の SQL Server Enterprise Edition と 2,000 個の SQL Server Standard Edition を Azure に割り当てます。 そのアクションの結果、毎時実行される Azure SQL リソースに適用できる 9,200 個の正規化されたコア ライセンスになります。 必要以上のライセンスを割り当てることで、使用状況が予想よりも早く増加した場合のバッファーを確保できます。

その後、割り当てられたライセンスの使用状況を定期的に (理想的には毎月) 監視します。 10 か月後、使用率は 95% に近づき、Azure SQL の使用状況が予想以上に増加していることがわかりました。 あなたは調達チームに相談し、ライセンスを追加して割り当てられるようにします。

最後に、年に一度のライセンス レビュー スケジュールを採用します。 レビュー プロセスでは、次のことを行います。

- ライセンスの使用状況のデータを収集して分析する。
- ライセンスの可用性を確認する。
- 必要に応じて、調達チームと連携してライセンスを増やす。
- ライセンスの割り当てを更新する。
- 時系列で監視する。

## <a name="next-steps"></a>次のステップ

- 「[Azure ハイブリッド特典のスコープレベルの管理に移行する](transition-existing.md)」方法について確認します。
- 「[スコープ レベルの Azure ハイブリッド特典の管理に関する FAQ](faq-azure-hybrid-benefit-scope.yml)」を確認します。