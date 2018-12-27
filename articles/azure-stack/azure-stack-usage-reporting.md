---
title: Azure Stack 使用状況データの Azure への報告 | Microsoft Docs
description: Azure Stack で使用状況データ レポートを設定する方法について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: cf6604730ab3bd9ee04fac36e703022f63dcb949
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090367"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Azure Stack 使用状況データの Azure への報告 

使用状況データ (使用量データとも呼ばれる) は、使用されたリソースの量を表します。 

使用量ベースの課金モデルを採用する Azure Stack マルチモード システムは、課金のために使用状況データを Azure に報告する必要があります。  Azure Stack オペレーターは、使用状況データを Azure に報告するように Azure Stack インスタンスを構成する必要があります。

> [!NOTE]
> 使用状況データの報告は、従量課金モデルのライセンスを持つ Azure Stack マルチモード システムのユーザーにとっては必須事項です。 一方、容量モデルのライセンスを持つユーザーはこの報告を省略できます ([購入方法](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)に関するページを参照してください)。 Azure Stack Development Kit ユーザーの場合は、Azure Stack オペレーターが使用状況データを報告し、機能をテストできます。 ただし、ユーザーの使用に対して課金されることはありません。 


![課金のフロー](media/azure-stack-usage-reporting/billing-flow.png)

使用状況データは、Azure ブリッジ経由で Azure Stack から Azure に送信されます。 Azure では、コマース システムが使用状況データを処理し、課金を生成します。 課金が生成された後、Azure サブスクリプションの所有者はそれを表示したり、[Azure アカウント センター](https://account.windowsazure.com/Subscriptions)からダウンロードしたりできます。 Azure Stack がどのようにライセンス供与されるかについては、「[Azure Stack packaging and pricing document (Azure Stack のパッケージ化および価格設定ドキュメント)](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409)」を参照してください。

## <a name="set-up-usage-data-reporting"></a>使用状況データ レポートの設定

使用状況データ レポートを設定するには、[Azure Stack インスタンスを Azure に登録する](azure-stack-register.md)必要があります。 登録プロセスの一部として、Azure Stack を Azure に接続し、使用状況データを送信する Azure Stack の Azure Bridge コンポーネントが構成されます。 次の使用状況データが Azure Stack から Azure に送信されます。

- **メーター ID** – 使用されたリソースの一意の ID。
- **数量** – リソース使用状況の量。
- **場所** – 現在の Azure Stack リソースがデプロイされている場所。
- **リソース URI** – 使用状況が報告されているリソースの完全修飾 URI。
- **サブスクリプション ID** – ローカルの (Azure Stack) サブスクリプションである、Azure Stack ユーザーのサブスクリプション ID。
- **時間** – 使用状況データの開始および終了時間。 これらのリソースが Azure Stack で使用された時間と、使用状況データがコマースに報告された時間の間にはある程度の遅延が存在します。 Azure Stack は 24 時間ごとに使用状況データを集計し、その使用状況データを Azure のコマース パイプラインに報告するにはさらに数時間かかります。 そのため、午前 0 時の少し前に発生した使用状況は翌日 Azure に表示される可能性があります。

## <a name="generate-usage-data-reporting"></a>使用状況データ レポートの生成

1. 使用状況データ レポートをテストするには、Azure Stack でいくつかのリソースを作成します。 たとえば、コアの使用状況がどのように報告されるかを確認するには、Basic および Standard SKU で[ストレージ アカウント](azure-stack-provision-storage-account.md)、[Windows Server VM](azure-stack-provision-vm.md)、および Linux VM を作成できます。 異なる種類のリソースの使用状況データは、別のメーターのもとに報告されます。

2. リソースを数時間実行されたままにします。 使用状況の情報は、1 時間ごとに約 1 回収集されます。 収集の後、このデータは Azure に送信され、Azure コマース システムに処理されます。 このプロセスは、最大数時間かかることがあります。

## <a name="view-usage---csp-subscriptions"></a>使用状況の表示 - CSP サブスクリプション

Azure Stack を CSP サブスクリプションを使用して登録した場合は、Azure の消費量を表示するのと同じ方法で、使用状況と請求金額を表示できます。 Azure Stack の使用状況は請求書と調整ファイルに含まれます。これらは[パートナー センター](https://partnercenter.microsoft.com/partner/home)を通して入手できます。 調整ファイルは月単位で更新されます。 Azure Stack の最近の使用状況情報にアクセスする必要がある場合は、パートナー センター API を使用できます。

   ![パートナー センター](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>使用状況の表示 - Enterprise Agreement サブスクリプション

Azure Stack を Enterprise Agreement サブスクリプションを使用して登録した場合は、[EA ポータル](https://ea.azure.com/)で使用状況と請求金額を確認できます。 Azure Stack の使用状況は、EA ポータルの [レポート] セクションで、高度なダウンロードの中にAzure の使用状況と共に含まれています。 

## <a name="view-usage--other-subscriptions"></a>使用状況の表示 - その他のサブスクリプション

Azure Stack をその他のサブスクリプションの種類 (従量課金制など) を使用して登録した場合は、Azure アカウント センターで使用状況と請求金額を確認できます。 Azure アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインし、Azure Stack を登録するために使用される Azure サブスクリプションを選択します。 次の図に示すように、Azure Stack 使用状況データ (使用された各リソースの課金される量) を表示できます。

   ![課金のフロー](media/azure-stack-usage-reporting/pricing-details.png)

Azure Stack Development Kit の場合、Azure Stack リソースは課金されないため、価格は $0.00 として示されます。

## <a name="which-azure-stack-deployments-are-charged"></a>どの Azure Stack デプロイが課金されますか?

リソース使用状況は、Azure Stack Development Kit では無料です。 Azure Stack マルチノード システムの場合は、ワークロード VM、ストレージ サービス、および App Services が課金されます。

## <a name="are-users-charged-for-the-infrastructure-vms"></a>ユーザーはインフラストラクチャ VM に対して課金されますか?

いいえ。 一部の Azure Stack リソース プロバイダー VM の使用状況データは Azure に報告されますが、これらの VM に対する課金はなく、Azure Stack インフラストラクチャを有効にするためにデプロイ中に作成された VM も課金の対象になりません。  

課金は、テナント サブスクリプションの下で実行されている VM に対してのみ行われます。 すべてのワークロードは、Azure Stack のライセンス条項に従って、テナント サブスクリプションでデプロイする必要があります。

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Windows Server ライセンスを持っています。Azure Stack で使用したいのですが、どうすればよいですか。

既存のライセンスを使用すると、使用状況メーターの生成を回避できます。 既存の Windows Server ライセンスを Azure Stack で使用できます。「[Azure Stack Licensing Guide](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409)(Azure Stack ライセンス ガイド) の「Using existing software with Azure Stack」(Azure Stack で既存のソフトウェアを使用する) を参照してください。 既存のライセンスを使用するには、「[Windows Server 向け Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)」の記事で説明されているように、Windows Server 仮想マシンをデプロイする必要があります。

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>使用されたリソースに対してどのサブスクリプションが課金されますか?
[Azure Stack を Azure に登録する](azure-stack-register.md)ときに指定されたサブスクリプションが課金されます。

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>使用状況データ レポートでは、どのような種類のサブスクリプションがサポートされますか?

Azure Stack マルチモード システムでは、Enterprise Agreement (EA) と CSP サブスクリプションがサポートされます。 Azure Stack Development Kit の場合、Enterprise Agreement (EA)、従量課金制、CSP、MSDN サブスクリプションで、使用状況データの報告がサポートされます。

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>使用状況データ レポートはソブリン クラウドで機能しますか?

Azure Stack Development Kit では、使用状況データ レポートにはグローバルな Azure システムで作成されたサブスクリプションが必要です。 ソブリン クラウド (Azure Government、Azure Germany、および Azure China クラウド) のいずれかで作成されたサブスクリプションは Azure に登録できないため、使用状況データ レポートをサポートしません。

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Azure Stack で報告された使用状況が Azure アカウント センターから生成されたレポートと一致しないのはなぜですか?

Azure Stack 使用状況 API を使用して使用状況データを報告する場合と、Azure アカウント センターを使用して使用状況データを報告する場合とでは常に時間差があります。 この遅延は、使用状況データを Azure Stack から Azure コマースにアップロードするために必要な時間です。 この遅延のために、午前 0 時の少し前に発生した使用状況は翌日 Azure に表示される可能性があります。 [Azure Stack 使用状況 API](azure-stack-provider-resource-api.md) を使用し、その結果を Azure 課金ポータルで報告された使用状況と比較すると、違いを確認できます。

## <a name="next-steps"></a>次の手順

* [プロバイダー使用量 API](azure-stack-provider-resource-api.md)  
* [テナント使用量 API](azure-stack-tenant-resource-usage-api.md)
* [使用量に関する FAQ](azure-stack-usage-related-faq.md)
* [クラウド サービス プロバイダーとして使用状況と課金を管理する](azure-stack-add-manage-billing-as-a-csp.md)
