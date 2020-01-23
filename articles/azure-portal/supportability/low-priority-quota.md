---
title: スポット仮想マシンのクォータ | Microsoft Docs
description: スポット クォータ要求を作成してクォータ制限を増やす
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9567df95e4e66d9c6b82b29cb9bba9adaf39349d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898863"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>スポット クォータ: すべての VM シリーズの制限を増やす

スポット仮想マシン (VM) は、Azure の使用法の別のモデルを提供します。 これを使用すると、従量課金制または予約 VM インスタンスのデプロイに応じて Azure が必要に応じて VM を削除できるようにする代わりに、コストの削減を想定できます。 スポット VM の詳細については、「[仮想マシン スケール セット用の Azure スポット VM](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)」を参照してください。

Azure Resource Manager は、仮想マシンの次の 2 種類の vCPU クォータをサポートしています。
* *従量課金制 VM* と*予約 VM インスタンス*は、*標準 vCPU クォータ*に従います。
* *スポット VM* は、*スポット vCPU クォータ*に従います。 

*スポット vCPU クォータ*の種類の場合、Resource Manager の vCPU クォータは、単一のリージョン制限として使用可能なすべての VM シリーズにわたって適用されます。

新しいスポット VM をデプロイする場合は常に、すべてのスポット VM インスタンスの新規および既存の vCPU 使用量の合計が、承認されたスポット vCPU クォータ制限を超えてはいけません。 スポット クォータを超えた場合、スポット VM のデプロイは許可されません。 

この記事では、Azure portal を使用してスポット vCPU クォータ制限の増加を要求する方法について説明します。 

標準 vCPU クォータの詳細については、「[仮想マシンの vCPU クォータ](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)」および[Azure サブスクリプションとサービスの制限](https://aka.ms/quotalimits)に関するページを参照してください。 

リージョンごとに vCPU の制限を増やす方法については、[標準クォータ: リージョンごとに vCPU の制限を増やす方法](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)に関するページを参照してください。

## <a name="request-a-quota-limit-increase-from-the-help--support-pane"></a>[ヘルプとサポート] ウィンドウからクォータ制限の増加を要求する 

**[ヘルプとサポート]** ウィンドウからすべての VM シリーズのスポット クォータ制限の増加を要求するには、次の操作を行います。

> [!NOTE]
> 1 つのサポート ケースを使用して、複数のリージョンのクォータ制限の増加を要求することもできます。 詳細については、手順 8 を参照してください。 

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート] リンク](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. **[ヘルプとサポート]** ウィンドウで、 **[新しいサポート要求]** を選択します。 

    ![新しいサポート要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. **[問題の種類]** ドロップダウン リストで、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![[問題の種類] ドロップダウン リスト](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. **[サブスクリプション]** ドロップダウン リストで、クォータを増やすサブスクリプションを選択します。

   ![[サブスクリプション] ドロップダウン リスト](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. **[クォータの種類]** ドロップダウン リストで、 **[Compute-VM (cores-vCPU) サブスクリプションの制限の増加]** を選択します。 

   ![[クォータの種類] ドロップダウン リスト](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **[詳細]** タブを選択してから、 **[問題の詳細]** で **[詳細の指定]** を選択し、要求の処理に役立つ追加情報を入力します。

   ![[詳細の指定] リンク](./media/resource-manager-core-quotas-request/provide-details.png)
   
1. 右上の **[クォータの詳細]** ウィンドウで、次の操作を行います。

   ![[クォータの詳細] ウィンドウ](./media/resource-manager-core-quotas-request/3-7.png)

   a. **[デプロイ モデル]** ドロップダウン リストで、適切なモデルを選択します。

   b. **[場所]** ドロップダウン リストで、場所を選択します。 選択した場所について、 **[種類]** の **[種類の選択]** ボックスで **[スポット]** を入力します。 
   
   ![[新しいサポート要求] の [詳細] タブ](./media/resource-manager-core-quotas-request/3-8.png)

    **[種類]** では、複数選択サポートを使用して、1 つのサポート ケースから標準とスポットの両方のクォータの種類を要求できます。 
    
    詳細については、[標準クォータ: VM シリーズごとに vCPU の制限を増やす方法](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)に関するページを参照してください。

   c. このサブスクリプションに適用する新しいクォータ制限を入力します。 
 
   ![[新しい vCPU 制限] テキスト ボックス](./media/resource-manager-core-quotas-request/3-9.png)

1. 複数の場所のクォータの増加を要求するには、ドロップダウン リストで追加の場所を選択してから、適切な VM の種類を選択します。 その後に、追加の場所に適用される制限を入力できます。

   ![[クォータの詳細] ウィンドウ内の追加の場所](./media/resource-manager-core-quotas-request/3-10.png)

1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>[サブスクリプション] ウィンドウからクォータ制限の増加を要求する

**[サブスクリプション]** ウィンドウからすべての VM シリーズのスポット クォータ制限の増加を要求するには、次の操作を行います。

> [!NOTE]
> 1 つのサポート ケースを使用して、複数のリージョンのクォータ制限の増加を要求することもできます。 詳細については、手順 7 を参照してください。 

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[サブスクリプション]** を選択します。

   ![[サブスクリプション] リンク](./media/resource-manager-core-quotas-request/subscriptions.png)

1. クォータを増やすサブスクリプションを選択します。

   ![[サブスクリプション] ウィンドウ](./media/resource-manager-core-quotas-request/select-subscription.png)

1. **[\<サブスクリプション名>]** ページの左側のウィンドウで、 **[使用量 + クォータ]** を選択します。

   ![[使用量 + クォータ] リンク](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. 右上の **[引き上げを依頼する]** を選択します。

   ![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

1. **[クォータの種類]** ドロップダウン リストで、 **[Compute-VM (cores-vCPU) サブスクリプションの制限の増加]** を選択します。

   ![[クォータの種類] ドロップダウン リスト](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 右上の **[クォータの詳細]** ウィンドウで、次の操作を行います。

   ![[クォータの詳細] ウィンドウ](./media/resource-manager-core-quotas-request/3-2-6.png)
 
   a. **[デプロイ モデル]** ドロップダウン リストで、適切なモデルを選択します。

   b. **[場所]** ドロップダウン リストで、場所を選択します。 
   
   c. 選択した場所について、 **[種類]** の **[種類の選択]** ボックスで **[スポット]** を入力します。

   ![[クォータの詳細] ウィンドウ](./media/resource-manager-core-quotas-request/3-2-7.png)

   詳細については、[標準クォータ: VM シリーズごとに vCPU の制限を増やす方法](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)に関するページを参照してください。

   d. このサブスクリプションに適用する新しいクォータ制限を入力します。

   ![[新しい vCPU 制限] テキスト ボックス](./media/resource-manager-core-quotas-request/3-2-8.png)
 
1. 複数の場所のクォータの増加を要求するには、ドロップダウン リストで追加の場所を選択してから、適切な VM の種類を選択します。 その後に、追加の場所に適用される制限を入力できます。

   ![[クォータの詳細] ウィンドウ内の追加の場所](./media/resource-manager-core-quotas-request/3-2-9.png)
 
1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。


