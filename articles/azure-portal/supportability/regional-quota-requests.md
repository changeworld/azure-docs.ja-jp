---
title: Azure リージョンの vCPU クォータ制限の引き上げを要求する | Microsoft Docs
description: リージョン クォータの引き上げ要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e262651a6e040c40dbe240ad3437eff1914aa3e5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898691"
---
# <a name="standard-quota-increase-limits-by-region"></a>標準クォータ: リージョンごとの制限の引き上げ 

Azure Resource Manager は、仮想マシンの次の 2 種類の vCPU クォータをサポートしています。
* *従量課金制 VM* と*予約 VM インスタンス*は、*標準 vCPU クォータ*に従います。
* *スポット VM* は、*スポット vCPU クォータ*に従います。 

従量課金制の標準 vCPU クォータと予約仮想マシン インスタンスは、各リージョンのサブスクリプションごとに 2 つのレベルで適用されます。
* 1 つ目のレベルは*リージョンの vCPU の合計の制限* (すべての VM シリーズ) です。
* 2 つ目のレベルは *VM シリーズあたりの vCPU の制限* (D シリーズの vCPU など) です。
 
新しいスポット VM をデプロイする場合は常に、その VM シリーズの新規および既存の vCPU 使用量の合計が、その特定の VM シリーズの承認されたスポット vCPU クォータを超えてはいけません。 さらに、すべての VM シリーズにわたってデプロイされる新規および既存の vCPU の合計数が、サブスクリプションに対して承認されているリージョンの vCPU の合計のクォータを超えてはなりません。 これらのクォータのいずれかを超えている場合、VM のデプロイは許可されません。 

VM シリーズの vCPU クォータ制限の引き上げは、Azure portal を使用して要求できます。 VM シリーズのクォータを増やすと、リージョンの vCPU の合計の制限が同じ量だけ自動的に増えます。

新しいサブスクリプションを作成するとき、リージョンの vCPU の合計の既定値が、すべての個別 VM シリーズに対する既定の vCPU クォータの合計と等しくない場合があります。 この不一致により、サブスクリプションで、デプロイしたい個別の VM シリーズごとのクォータは十分になる可能性があります。 一方、すべてのデプロイに対するリージョンの vCPU の合計に対応するクォータは十分でない可能性があります。 この場合、リージョンの vCPU の合計数の制限を明示的に増やすための要求を送信する必要があります。 リージョンの vCPU の合計の制限が、そのリージョンのすべての VM シリーズにわたって承認されたクォータの合計を超えることはできません。

標準 vCPU クォータの詳細については、「[仮想マシンの vCPU クォータ](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)」および [Azure サブスクリプションとサービスの制限](https://aka.ms/quotalimits)に関するページを参照してください。

Spot VM vCPU の制限の引き上げの詳細については、「[スポット クォータ: すべての VM シリーズでの制限の引き上げ](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)」を参照してください。

次のセクションで説明するように、2 つの方法のいずれかで、リージョンごとの vCPU の標準クォータ制限の引き上げを要求できます。

## <a name="request-a-quota-increase-by-region-from-the-help--support-pane"></a>[ヘルプとサポート] ウィンドウからリージョンごとにクォータの引き上げを要求する

**[ヘルプとサポート]** ウィンドウからリージョンごとに vCPU クォータの引き上げを要求するには、次の手順を実行します。 

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[ヘルプとサポート]** を選択します。

   ![[ヘルプとサポート] リンク](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. **[ヘルプとサポート]** ウィンドウで、 **[新しいサポート要求]** を選択します。 

    ![新しいサポート要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. **[問題の種類]** ドロップダウン リストで、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

   ![[問題の種類] ドロップダウン リスト](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. **[サブスクリプション]** ドロップダウン リストで、クォータを増やすサブスクリプションを選択します。

   ![[サブスクリプション] ドロップダウン リスト](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. **[クォータの種類]** ドロップダウンで **[その他の要求]** を選択します。

   ![[クォータの種類] ドロップダウン リスト](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **[問題の詳細]** ウィンドウの **[説明]** ボックスで、次の追加情報を入力します。 

    a. **[デプロイ モデル]** には、 **[Resource Manager]** を指定します。  
    b. **[リージョン]** には、必要なリージョン (例: **[米国東部 2]** ) を指定します。  
    c. **[新しい制限]** には、そのリージョンに対する新しい vCPU の制限を指定します。 この値は、このサブスクリプションに対する個々の SKU シナリオの承認済みクォータの合計を超えてはなりません。

    ![[問題の詳細] ウィンドウ](./media/resource-manager-core-quotas-request/regional-details.png)

1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。

## <a name="request-a-quota-increase-by-region-from-the-subscriptions-pane"></a>[サブスクリプション] ウィンドウからリージョンごとにクォータの引き上げを要求する

**[サブスクリプション]** ウィンドウからリージョンごとに vCPU クォータの引き上げを要求するには、次の手順を実行します。 

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[サブスクリプション]** を選択します。

   ![[サブスクリプション] リンク](./media/resource-manager-core-quotas-request/subscriptions.png)

1. クォータを増やすサブスクリプションを選択します。

   ![[サブスクリプション] ウィンドウ](./media/resource-manager-core-quotas-request/select-subscription.png)

1. **[\<サブスクリプション名>]** ページの左側のウィンドウで、 **[使用量 + クォータ]** を選択します。

   ![[使用量 + クォータ] リンク](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. 右上の **[引き上げを依頼する]** を選択します。

   ![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

1. **[クォータの種類]** ドロップダウンで **[その他の要求]** を選択します。

   ![[クォータの種類] ドロップダウン リスト](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **[問題の詳細]** ウィンドウの **[説明]** ボックスで、次の追加情報を入力します。 

    a. **[デプロイ モデル]** には、 **[Resource Manager]** を指定します。  
    b. **[リージョン]** には、必要なリージョン (例: **[米国東部 2]** ) を指定します。  
    c. **[新しい制限]** には、そのリージョンに対する新しい vCPU の制限を指定します。 この値は、このサブスクリプションに対する個々の SKU シナリオの承認済みクォータの合計を超えてはなりません。

    ![[問題の詳細] ウィンドウ](./media/resource-manager-core-quotas-request/regional-details.png)

1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。

