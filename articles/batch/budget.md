---
title: コスト分析と予算
description: Batch ワークロードの実行に使用される、基になるコンピューティング リソースとソフトウェア ライセンスについて、コスト分析を取得して予算を設定し、コストを削減する方法について説明します。
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679317"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch のコスト分析と予算

Azure Batch 自体の使用にコストはかかりませんが、Batch ワークロードの実行に使用される、基になるコンピューティング リソースとソフトウェア ライセンスには料金が発生する場合があります。 コストは、プール内の仮想マシン (VM)、VM からのデータ転送、またはクラウドに格納されている任意の入力または出力データから発生します。 このトピックは、コストの発生元、Batch プールまたはアカウントの予算の設定方法、Batch ワークロードのコストを削減する方法を理解するのに役立ちます。

## <a name="batch-resources"></a>Batch リソース

仮想マシンは、Batch 処理に使用される最も重要なリソースです。 Batch に VM を使用するコストは、使用の種類、量、および期間に基づいて計算されます。 VM の課金オプションには、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)または[予約](../cost-management-billing/reservations/save-compute-costs-reservations.md) (前払い) があります。 どちらの支払いオプションにも、コンピューティング ワークロードに応じてさまざまなメリットがあり、これらのオプションが請求に与える影響は異なります。

[アプリケーション パッケージ](batch-application-packages.md)を使用してアプリケーションを Batch ノード (VM) デプロイすると、アプリケーション パッケージが使用する Azure Storage リソースに対して課金されます。 また、リソース ファイルやその他のログデータなどの入力ファイルまたは出力ファイルのストレージに対しても課金されます。 一般に、Batch に関連付けられているストレージ データのコストは、コンピューティング リソースのコストよりはるかに低くなります。 [仮想マシンの構成](nodes-and-pools.md#virtual-machine-configuration)を使用して作成されたプール内の各 VM には、Azure マネージド ディスクを使用する関連 OS ディスクがあります。 Azure マネージド ディスクには追加のコストがかかり、他のディスク パフォーマンス層にも異なるコストがあります。

Batch プールにはネットワーク リソースが使用されます。 特に、**VirtualMachineConfiguration** プールの場合は、静的 IP アドレスを必要とする Standard Load Balancer が使用されます。 Batch に使用されるロード バランサーは、**ユーザー サブスクリプション** アカウントには表示されますが、**Batch サービス** アカウントには表示されません。 Standard ロード バランサーでは、Batch プール VM との間でやり取りされるすべてのデータに対して料金が発生します。プール ノード (タスク/ノード ファイルの取得など)、タスク アプリケーション パッケージ、リソース/出力ファイル、およびコンテナー イメージからデータを取得する一部の Batch API には料金が発生します。

### <a name="additional-services"></a>その他のサービス

VM とストレージが含まれないサービスでは、Batch アカウントのコストを考慮に入れることができます。

Batch と共に一般的に使用される他のサービスには、次のものがあります。

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- グラフィック アプリケーションを使用する VM

お客様が Batch ソリューションと共に使用するサービスによっては、追加料金が発生する可能性があります。 各追加サービスのコストを確認するには、「[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)」を参照してください。

## <a name="cost-analysis-and-budget-for-a-pool"></a>プールのコスト分析と予算

Azure portal で、Batch プールまたは Batch アカウントの予算と支出アラートを作成できます。 予算とアラートは、予算オーバーのリスクを関係者に通知するのに役立ちますが、支出アラートの遅延が生じたり、予算をわずかに超過したりする危険性があります。

この例では、個々の Batch プールのコスト分析を確認します。

1. Azure portal で、**Cost Management + Billing** を入力するか、選択します。
1. **[課金スコープ]** セクションでご使用のサブスクリプションを選択します。
1. **[コスト管理]** で、 **[コスト分析]** を選択します。
1. **[フィルターの追加]** を選択します 最初のドロップダウンで、 **[リソース]** を選択します
1. 2 番目のドロップダウンで、Batch プールを選択します。 プールを選択すると、次に示す例のようなプールのコスト分析が表示されます。
    ![Azure portal のプールのコスト分析を示すスクリーンショット。](./media/batch-budget/pool-cost-analysis.png)

結果のコスト分析には、プールのコストと、そのコストの原因であるリソースが表示されます。 この例では、プールで使用されている VM が最もコストのかかるリソースです。

プールの予算を作成するには、 **[Budget: none]\(予算: なし\)** を選択してから **[新しい予算の作成 >]** を選択します。 次に、ウィンドウを使用して、お使いのプールに合わせて[予算を構成](../cost-management-billing/costs/tutorial-acm-create-budgets.md)します。

> [!NOTE]
> Azure Batch は Azure Cloud Services と Azure Virtual Machines テクノロジに基づいて構築されています。 **Cloud Services 構成** を選択した場合は、Cloud Services の料金体系に基づいて課金されます。 **仮想マシンの構成** を選択した場合は、Virtual Machines の料金体系に基づいて課金されます。 このページの例では、 **[仮想マシンの構成]** を使用します。ほとんどの Batch プールには、これを推奨します。

## <a name="minimize-cost"></a>コストを最小限に抑える

長期間にわたって複数の VM および Azure サービスを使用すると、コストが高くなる可能性があります。 ワークロードの効率を最大化し、コストを削減するには、以下の戦略の使用を検討してください。

### <a name="low-priority-virtual-machines"></a>優先順位の低い仮想マシン

[優先順位の低い VM](batch-low-pri-vms.md) を使用すると、Azure の余剰コンピューティング容量が利用されるので、Batch ワークロードのコストが削減されます。 プールで優先順位の低い VM を指定すると、Batch ではこの余剰を使用してワークロードが実行されます。 専用 VM の代わりに優先順位の低い VM を使用すると、コストを大幅に節約できます。

### <a name="virtual-machine-os-disk-type"></a>仮想マシン OS ディスクの種類

Azure には、[VM OS ディスクの種類](../virtual-machines/disks-types.md)が複数用意されています。 ほとんどの VM シリーズには、Premium ストレージと Standard ストレージの両方をサポートするサイズがあります。 プールに VM サイズとして 's' が選択されている場合、Batch では Premium SSD OS ディスクが構成されます。 's 以外' の VM サイズを選択すると、より低コストの Standard HDD ディスクの種類が使用されます。 たとえば、Premium SSD OS ディスクは `Standard_D2s_v3` に使用され、Standard HDD OS ディスクは `Standard_D2_v3` に使用されます。

Premium SSD OS ディスクはより高価ですが、高いパフォーマンスが得られます。 Premium ディスクを使用する VM は、Standard HDD OS ディスクを使用する VM よりも多少速く起動できます。 アプリケーションとタスク ファイルは VM の一時 SSD ディスク上にあるため、Batch では、多くの場合、OS ディスクはそれほど頻繁に使用されません。 このため、多くの場合、's 以外' の VM サイズを選択して、's' の VM サイズを指定したときにプロビジョニングされる Premium SSD の増加コストの支払を回避することができます。

### <a name="reserved-virtual-machine-instances"></a>予約仮想マシン インスタンス

長期間 Batch を使用する予定であれば、ワークロードに [Azure の予約](../cost-management-billing/reservations/save-compute-costs-reservations.md)を使用することで VM のコストを削減できます。 予約料金は、従量課金制の料金よりもかなり低価です。 予約なしで使用された仮想マシン インスタンスは、従量課金制で料金が発生します。 予約を購入すると、予約割引が適用されます。

### <a name="automatic-scaling"></a>自動スケーリング

[自動スケーリング](batch-automatic-scaling.md)では、現在のジョブの要求に基づいて、Batch プール内の VM 数が動的にスケーリングされます。 自動スケーリングでは、ジョブの有効期間に基づいてプールがスケールされるので、実行するジョブがある場合にのみ VM がスケールアップされ、使用されます。 ジョブが完了したとき、またはジョブがないときは、コンピューティング リソースを節約するために VM は自動的にスケールダウンされます。 スケーリングを使用すると、必要なリソースのみを使用して Batch ソリューションの全体的なコストを削減できます。

## <a name="next-steps"></a>次のステップ

- Batch ソリューションの構築と監視に使用できる [Batch API とツール](batch-apis-tools.md)の詳細を確認します。  
- [Batch での優先順位の低い VM の使用](batch-low-pri-vms.md)について確認します。
