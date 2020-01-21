---
title: Azure VM シリーズあたりの vCPU クォータ制限の引き上げを要求する | Microsoft Docs
description: この記事では、VM vCPU あたりのクォータ制限の引き上げを要求する方法について説明します。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898847"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>標準クォータ: VM シリーズでの制限の引き上げ

Azure Resource Manager は、仮想マシン向けに次の 2 タイプの vCPU クォータをサポートしています。
* *従量課金制 VM* と*予約 VM インスタンス*には、*標準 vCPU クォータ*が適用されます。
* *スポット VM* には、*スポット vCPU クォータ*が適用されます。 

従量課金制の標準 vCPU クォータと予約仮想マシン インスタンスは、各リージョンのサブスクリプションごとに 2 つのレベルで適用されます。
* 1 つ目のレベルは、*リージョンの vCPU の合計の制限* (すべての VM シリーズ) です。
* 2 つ目のレベルは、*VM シリーズあたりの vCPU の制限* (Dv3 シリーズの vCPU など) です。 

新しいスポット VM をデプロイする場合、その VM シリーズの新規および既存の vCPU 使用量の合計が、その特定の VM シリーズの承認されたスポット vCPU クォータを超えることできません。 さらに、すべての VM シリーズにわたってデプロイされる新規および既存の vCPU の合計数が、サブスクリプションに対して承認されているリージョンの vCPU の合計のクォータを超えることはできません。 これらのクォータのいずれかを超えている場合には、VM のデプロイは許可されません。

Azure portal を使用して、VM シリーズの vCPU クォータ制限の引き上げを要求できます。 VM シリーズのクォータを増やすと、リージョンの vCPU の合計の制限が同じ量だけ自動的に増えます。 

標準 vCPU クォータの詳細については、「[仮想マシンの vCPU クォータ](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)」および「[Azure サブスクリプションとサービスの制限](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)」に関するページを参照してください。 

リージョンごとの vCPU 制限を標準クォータに引き上げる方法については、[「標準クォータ: リージョンごとの制限の引き上げ」](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)を参照してください。 

Spot VM vCPU の制限の引き上げの詳細については、「[スポット クォータ: すべての VM シリーズでの制限の引き上げ](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)」を参照してください。

次のセクションで説明するように、2つの方法のいずれかで、VM シリーズあたりの標準 vCPU クォータ制限の引き上げを要求できます。

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>[ヘルプとサポート] ウィンドウから標準クォータの引き上げを要求する

**[ヘルプとサポート]** ウィンドウから VM シリーズごとの標準 vCPU クォータの増加を要求するには、次の手順を実行します。 

> [!NOTE]
> また、1 つのサポート ケースを使用して、複数のリージョンのクォータ制限の増加を要求することもできます。 詳細については、ステップ 8 を参照してください。

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

1. **[詳細]** タブの **[問題の詳細]** で、[**詳細を入力]** を選択し、要求の処理に役立つ追加情報を入力します。

   ![[詳細の指定] リンク](./media/resource-manager-core-quotas-request/provide-details.png)

1. 右上の **[クォータの詳細]** ウィンドウで、次の操作を行います。

   ![[クォータの詳細] ウィンドウ](./media/resource-manager-core-quotas-request/1-7.png)

   a. **[デプロイ モデル]** ドロップダウン リストで、適切なモデルを選択します。

   b. **[場所]** ドロップダウン リストで、場所を選択します。 選択した場所に対して、 **[タイプ]** の **[タイプの選択]** ボックスに **[Standard]** と入力します。

   ![[クォータの詳細] ウィンドウ - クォータのタイプ](./media/resource-manager-core-quotas-request/1-8.png)

   **[タイプ]** では、複数選択サポートを使用して、1 つのサポート ケースから標準とスポットの両方のクォータのタイプを要求できます。
   
   スポットクォータ制限の増加の詳細については、「[仮想マシンスケールセット用の Azure スポット VM](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)」を参照してください。

   c. **[Standard]** ドロップダウン リストで、クォータを増やす SKU シリーズを選択します。

   ![[クォータの詳細] ウィンドウ - SKU シリーズ](./media/resource-manager-core-quotas-request/1-9.png)

   d. このサブスクリプションに適用する新しいクォータ制限を入力します。 SKU を一覧から削除するには、SKU の横にあるチェックボックスをオフにするか、または、[**削除** (X)] アイコンを選択します。 

   ![[新しい vCPU 制限] テキスト ボックス](./media/resource-manager-core-quotas-request/1-10.png)

1. 複数の場所のクォータの増加を要求するには、ドロップダウン リストで追加の場所を選択してから、適切な VM のタイプを選択します。 その後に、追加の場所に適用する制限を入力できます。

   ![[クォータの詳細] ウィンドウ内の追加の場所](./media/resource-manager-core-quotas-request/1-11.png)
   
1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>[サブスクリプション] ウィンドウから標準クォータの増加を要求する

**[サブスクリプション]** ウィンドウから VM シリーズごとの標準 vCPU クォータの増加を要求するには、次の手順を実行します。

> [!NOTE]
> また、1 つのサポート ケースを使用して、複数のリージョンのクォータ制限の増加を要求することもできます。 詳細については、ステップ 7 を参照してください。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[サブスクリプション]** を選択します。

   ![[サブスクリプション] リンク](./media/resource-manager-core-quotas-request/subscriptions.png)

1. クォータを増やしたいサブスクリプションを選択します。

   ![[サブスクリプション] ウィンドウ](./media/resource-manager-core-quotas-request/select-subscription.png)

1. **\<[サブスクリプション名]** ページの左側のウィンドウで、 **[使用量 + クォータ]** を選択します。

   ![[使用量 + クォータ] リンク](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. 右上の **[引き上げを要求する]** を選択します。

   ![引き上げを依頼する](./media/resource-manager-core-quotas-request/request-increase.png)

1. **[クォータの種類]** ドロップダウン リストで、 **[Compute-VM (cores-vCPU) サブスクリプションの制限の増加]** を選択します。

   ![[クォータの種類] ドロップダウン リスト](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. 右上の **[クォータの詳細]** ウィンドウで、次の操作を行います。

   ![[クォータの詳細] ウィンドウ](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. **[デプロイ モデル]** ドロップダウン リストで、適切なモデルを選択します。

   b. **[場所]** ドロップダウン リストで、場所を選択します。 
   
   c. 選択した場所に対して、**タイプ]** [ から ] **[タイプの選択** を選択し、 **[Standard]** のチェックボックスをオンにします。

   ![[Standard] チェックボックス](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   **[タイプ]** フィールドで複数選択サポートを使用して、1つのサポート ケースから標準と低優先度の両方のクォータ タイプを要求できます。
   
   スポットクォータ制限の増加の詳細については、「[仮想マシンスケールセット用の Azure スポット VM](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)」を参照してください。

   d. **[Standard]** ドロップダウン リストで、クォータを増やす SKU シリーズを選択します。

   ![[クォータの詳細] ウィンドウ - SKU シリーズ](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. このサブスクリプションに適用する新しいクォータ制限を入力します。 SKU を一覧から削除するには、SKU の横にあるチェックボックスをオフにするか、または、[**削除** (X)] を選択します。 

   ![[新しい vCPU 制限] テキスト ボックス](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. 複数の場所のクォータの増加を要求するには、ドロップダウン リストで追加の場所を選択してから、適切な VM のタイプを選択します。 

   このステップでは、これ以前の場所であなたが選択した SKU シリーズがプリロードされます。 追加のシリーズに適用したいクォータ制限を入力します。
   
   ![[クォータの詳細] ウィンドウ内の追加の場所](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. サポート要求の作成を続行するには、 **[保存して続行]** を選択します。
