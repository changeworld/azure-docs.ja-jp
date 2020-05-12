---
title: Azure Confidential Computing FAQ
description: Confidential Computing についてよく寄せられる質問の回答です。
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: e9cb691ef60f612672078a9ef84db904c79cbc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189447"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure Confidential Computing についてよく寄せられる質問

この記事では、[Azure で Confidential Computing ワークロード](overview.md)を実行することに関して特に多く寄せられる質問への回答を示します。

この記事で Azure の問題に対処できない場合は、[MSDN および Stack Overflow の Azure 関連フォーラム](https://azure.microsoft.com/support/forums/)を参照してください。 問題をこれらのフォーラムまたは [Twitter の @AzureSupport](https://twitter.com/AzureSupport) に投稿できます。 Azure サポート要求を送信することもできます。 サポート要求を送信するには、[[Azure サポート]](https://azure.microsoft.com/support/options/) ページで [サポートを受ける] を選択します。

## <a name="confidential-computing-virtual-machines"></a>Confidential Computing 仮想マシン <a id="vm-faq"></a>

1. **DCsv2 シリーズ VM のデプロイはどのような方法で開始しますか?**

   DCsv2 VM は次のような方法でデプロイできます。
   - [Azure Resource Manager テンプレート](../virtual-machines/windows/template-description.md)の使用
   - [Azure Portal](https://portal.azure.com/#create/hub) から
   - [Azure Confidential Computing (仮想マシン)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) マーケットプレース ソリューション テンプレートで。 サポートされているシナリオ (リージョン、イメージ、可用性、ディスク暗号化) だけを顧客に利用してもらうとき、マーケットプレース ソリューション テンプレートが役立ちます。 

1. **すべての OS イメージが Azure Confidential Computing と連動しますか?**

   いいえ。 仮想マシンは Generation 2 仮想マシンにのみデプロイできます。 Ubuntu Server 18.04、Ubuntu Server 16.04、Windows Server 2016 Datacenter には Generation 2 サポートを提供しています。 [Linux](../virtual-machines/linux/generation-2.md) と [Windows](../virtual-machines/windows/generation-2.md) の Gen 2 VM に関する詳細をご覧ください。

1. **DCsv2 仮想マシンがポータルで淡色表示され、選択できません**

   VM の横に付いている情報バブルに基づき、さまざまなアクションを行います。
    -   **UnsupportedGeneration**:仮想マシン イメージの生成を "Gen2" に変更します。
    -   **NotAvailableForSubscription** :お使いのサブスクリプションではこのリージョンを利用できません。 使用可能なリージョンを選択します。
    -   **InsufficientQuota**:[クォータを増やすためのサポート要求を作成します](../azure-portal/supportability/per-vm-quota-requests.md)。 無料試用版サブスクリプションの場合、Confidential Computing VM のクォータがありません。 

1. **ポータルのサイズ セレクターで見つけようとしても、DCsv2 仮想マシンが表示されません**

   利用できるリージョンを選択していることを確認してください。 また、サイズ セレクターでは必ず "すべてのフィルターをクリア" を選択してください。 

1. **DCsv2-Series VM と DC-Series VM の違いは何ですか?**

   DC-Series VM は、Intel SGX を備えた以前の 6 コア Intel プロセッサで実行されます。 合計メモリと EPC (Enclave Page Cage) メモリが少なく、利用できるリージョンが少ないです。 これらの VM は米国東部と欧州西部でのみ利用できます。利用できるサイズは、Standard_DC2s と Standard_DC4s の 2 つです。 GA になることはなく、[Confidential Compute DC-Series VM [プレビュー]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace インスタンスでのみデプロイできます。

1. **DCsv2 仮想マシンは世界中で利用できますか?**

   いいえ。この仮想マシンは一部のリージョンでのみ利用できます。 利用できるリージョンの最新情報については、[リージョン別製品ページ](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)をご覧ください。 

1. **Open Enclave SDK はどのような方法でインストールできますか?**
   
   Azure とオンプレミスを問わず、マシンに OE SDK をインストールする方法については、[Open Enclave SDK GitHub](https://github.com/openenclave/openenclave) の指示に従ってください。
     
   OS 固有のインストール手順については、Open Enclave SDK GitHub を参照しても構いません。
     - [Windows に OE SDK をインストールする](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Ubuntu 18.04 に OE SDK をインストールする](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Ubuntu 16.04 に OE SDK をインストールする](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
